---
{
    "title": "DROP INDEX",
    "language": "zh-CN"
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

## 描述

该语句用于从一个表中删除指定名称的索引，目前仅支持 bitmap 索引。

## 语法

```sql
DROP INDEX [ IF EXISTS ] <index_name> ON [ <db_name> . ] <table_name>;
```

## 必选参数

**1. `<index_name>`**：索引名称。

**2. `<table_name>`**：索引归属的表名。

## 可选参数  

**1. `<db_name>`**：库名，选填，不填默认当前库。

## 权限控制

执行此 SQL 命令的用户必须至少具有以下权限：

| 权限         | 对象       | 说明                      |
|:-----------|:---------|:------------------------|
| ALTER_PRIV | 表（Table） | DROP INDEX 属于表 ALTER 操作 |

## 示例

- 删除索引

   ```sql
   DROP INDEX IF NOT EXISTS index_name ON table1 ;
   ```

