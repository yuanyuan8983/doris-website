---
{
    "title": "IS_IPV4_STRING",
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


## Description
Determine whether an IPv4-type address is a valid IPv4 address.

## Syntax
```sql
IS_IPV4_STRING(<ipv4_str>)
```

## Parameters
| Parameter | Description                                      |
|-----------|--------------------------------------------------|
| `<ipv4_str>`      | An IPv4 address of type String |


## Return Value
If it is a correctly formatted and valid IPv4 address, return true; On the contrary, return false.
- If input is NULL, the function returns NULL.


## Example
```sql
CREATE TABLE `test_is_ipv4_string` (
      `id` int,
      `ip_v4` string
    ) ENGINE=OLAP
    DISTRIBUTED BY HASH(`id`) BUCKETS 4
    PROPERTIES (
    "replication_allocation" = "tag.location.default: 1"
    );
    
insert into test_is_ipv4_string values(0, NULL), (1, '0.0.0.'), (2, ''), (3, '.'), (4, '255.255.255.255');

select id, ip_v4, is_ipv4_string(ip_v4) from test_is_ipv4_string order by id;
```
```text
+------+-----------------+-----------------------+
| id   | ip_v4           | is_ipv4_string(ip_v4) |
+------+-----------------+-----------------------+
|    0 | NULL            |                  NULL |
|    1 | 0.0.0.          |                     0 |
|    2 |                 |                     0 |
|    3 | .               |                     0 |
|    4 | 255.255.255.255 |                     1 |
+------+-----------------+-----------------------+
```
