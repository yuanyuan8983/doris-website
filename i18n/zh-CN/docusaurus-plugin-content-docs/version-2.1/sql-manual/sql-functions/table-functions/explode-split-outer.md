---
{
    "title": "EXPLODE_SPLIT_OUTER",
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

`explode_split_outer` 与 `explode_split` 类似，也是将字符串拆分为多个子字符串并展开为多行。但与 explode_split 不同的是，它在处理空值和 NULL 值时会有不同的行为，能够处理空的或 NULL 的字符串。

## 语法
`explode_split_outer(<str>, <delimiter>)`

## 参数

| 参数 | 说明 |
| -- | -- |
| `<str>` | 字符串类型 |
| `<delimiter>` | 分割符 |


## 返回值

会在字符串为空或 NULL 时保留一行并返回 NULL 值。

## 举例

原表数据：

```sql
CREATE TABLE example2 (
    id INT,
    str string null
)DUPLICATE KEY(id)
DISTRIBUTED BY HASH(`id`) BUCKETS AUTO
PROPERTIES (
"replication_allocation" = "tag.location.default: 1");
```
```sql
insert into example2 values (1,''),(2,NUll),(3,"1"),(4,"1,2,3"),(5,"a,b,c");
```
```sql
select id, e1 from example2 lateral view explode_split(str, ',') tmp1 as e1 where id = 2 order by id, e1;
Empty set (0.02 sec)
```
```sql
select id, e1 from example2 lateral view explode_split_outer(str, ',') tmp1 as e1 where id = 2 order by id, e1;
```
```text
+------+------+
| id   | e1   |
+------+------+
|    2 | NULL |
+------+------+
```
