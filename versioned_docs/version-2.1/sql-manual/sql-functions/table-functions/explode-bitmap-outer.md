---
{
    "title": "EXPLODE_BITMAP_OUTER",
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

`explode_bitmap_outer` is similar to `explode_bitmap`, accepting a bitmap type data and mapping each bit of the bitmap into a separate row. However, its behavior differs when handling null or empty values. It allows records with empty or null bitmaps and expands them as NULL rows in the result.

## Syntax

`explode_bitmap_outer(<bitmap>)`

## Parameters

| Parameter | Description |
| -- | -- |
| `<bitmap>` | bitmap type |

## Return Value

Returns a row for each bit in the bitmap. If the bitmap is empty or NULL, it returns one row with the expanded column set to NULL.

## Examples

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