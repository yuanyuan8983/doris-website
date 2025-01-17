---
{
    "title": "EXPLODE_BITMAP_OUTER",
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

`explode_bitmap_outer` 与 `explode_bitmap` 类似，接受一个位图（bitmap）类型的数据，将位图中的每个 bit（位）映射为单独的行。但在处理空值或 NULL 时，行为有所不同。它允许空位图或 NULL 位图的记录存在，并在返回结果中将空位图或者 NULL 位图展开为 NULL 行。配合 LATERAL VIEW 使用。

## 语法

`explode_bitmap_outer(<bitmap>)`

## 参数

| 参数 | 说明 |
| -- | -- |
| `<bitmap>` | bitmap 类型 |

## 返回值

返回位图中每一位对应的行，如果位图为空或为 NULL，会返回一行并将展开列的值设为 NULL。

## 举例

```sql
CREATE TABLE example (
    k1 INT,
    v1 bitmap
)DUPLICATE KEY(k1)
DISTRIBUTED BY HASH(k1) BUCKETS AUTO
PROPERTIES (
"replication_allocation" = "tag.location.default: 1");
```
```sql
insert into example values(1,to_bitmap('10101')),(2,to_bitmap('0')),(3,to_bitmap(NULL));
```
```sql
SELECT id, k, v
FROM example
LATERAL VIEW explode_json_object(value_json) exploded_table AS k , v;
```
```text
+------+-------+
| k1   | bit   |
+------+-------+
|    2 |     0 |
|    1 | 10101 |
+------+-------+
```
```sql
SELECT id, k, v
FROM example
LATERAL VIEW explode_json_object_outer(value_json) exploded_table AS k, v;
```
```text
+------+-------+
| k1   | bit   |
+------+-------+
|    2 |     0 |
|    1 | 10101 |
|    3 |  NULL |
+------+-------+
```