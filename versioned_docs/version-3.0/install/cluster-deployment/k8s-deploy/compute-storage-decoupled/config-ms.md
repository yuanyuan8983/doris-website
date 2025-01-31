---
{
"title": "Config Metaservice",
"language": "en"
}
---

<!-- 
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

MetaService is a component of Doris designed for the separation of storage and computation, providing metadata management and query services. The MetaService is a stateless service, typically deployed in a high-availability master-slave configuration. This document provides detailed instructions for configuring MetaService within a DorisDisaggregatedCluster.

## Image configuration
In the provided deployment example, the configured MetaService image may not be the latest version. When using a custom image, configure it as follows:
```yaml
spec:
  metaService:
    image: ${msImage}
```
Here, ${msImage} refers to the image of MetaService that you wish to deploy. The official [MetaService image](https://hub.docker.com/repository/docker/selectdb/doris.ms-ubuntu/general) is available on DockerHub.

## Resource configuration
To limit the resources available to MetaService to 4 CPU cores and 4 Gi of memory, use the following configuration:
```yaml
spec:
  metaService:
    requests:
      cpu: 4
      memory: 4Gi
    limits:
      cpu: 4
      memory: 4Gi
```
Update the configuration in the required [DorisDisaggregatedCluster resource](install-quickstart.md#step-3-deploy-the-compute-storage-decoupled-cluster).

## FoundationDB access configuration
- Configure the ConfigMap with FoundationDB access information  
  The FoundationDB cluster is deployed using the fdb-kubernetes-operator. You can directly configure the ConfigMap generated by the fdb-kubernetes-operator to include the FoundationDB access information. The configuration is as follows:
  ```yaml
  spec:
    metaService:
      image: {msImage}
      fdb:
        configMapNamespaceName:
          name: {foundationdbConfigmap}
          namespace: {namespace}
  ```
  Here, ${foundationdbConfigMapName} is the name of the ConfigMap, and ${namespace} is the namespace where FoundationDB is deployed. For details on locating the ConfigMap with FoundationDB access information, refer to the section on [Retrieving the ConfigMap with FoundationDB access information](install-fdb.md#retrieve-the-configmap-containing-foundationdb-access-information).  
- Configure the FoundationDB access endpoint  
  If FoundationDB is deployed on physical machines, you can directly configure the access endpoint in MetaService as follows:
  ```yaml
  spec:
    metaService:
      fdb:
        address: ${fdbEndpoint}
  ```
  ${fdbEndpoint} refers to the access address of FoundationDB. For physical deployments, refer to the FoundationDB documentation on [cluster-file](https://apple.github.io/foundationdb/administration.html#foundationdb-cluster-file) for details on how to retrieve the access information.
  
## Custom startup configuration
In Kubernetes, configuration files can be mounted via ConfigMap. You can customize the startup configuration of the Doris storage-computation separation component through a ConfigMap. When customizing the startup configuration, Doris Operator automatically handles the configuration required for mutual awareness between components. As a result, when customizing the MetaService startup configuration, there is no need to include FoundationDB-related configurations.

1. Create a Custom ConfigMap with Startup Configuration.  
  The following is an example of a custom ConfigMap that includes startup configuration:
  ```yaml
  apiVersion: v1
  data:
    doris_cloud.conf: |
      # // meta_service
      brpc_listen_port = 5000
      brpc_num_threads = -1
      brpc_idle_timeout_sec = 30
      http_token = greedisgood9999
  
      # // doris txn config
      label_keep_max_second = 259200
      expired_txn_scan_key_nums = 1000
  
      # // logging
      log_dir = ./log/
      # info warn error
      log_level = info
      log_size_mb = 1024
      log_filenum_quota = 10
      log_immediate_flush = false
      # log_verbose_modules = *
  
      # //max stage num
      max_num_stages = 40
  kind: ConfigMap
  metadata:
    name: doris-metaservice
    namespace: default
  ```
2. Update the DorisDisaggregatedCluster Resource.
  ```yaml
  spec:
    metaService:
      image: {msImage}
      configMaps:
        - name: doris-metaservice
          mountPath: /etc/doris
  ```
  In this example, the `doris-metaservice` ConfigMap is used. Update the [DorisDisaggregatedCluster resource](install-quickstart.md#step-3-deploy-the-compute-storage-decoupled-cluster). The MetaService service uses the doris_cloud.conf file for its startup configuration, and the corresponding key in the ConfigMap must be doris_cloud.conf. The mount path for the ConfigMap must be /etc/doris.
