---
{
    "title": "EXPLODE_SPLIT_OUTER",
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

The `explode_split_outer` function is similar to `explode_split`, also splitting a string into multiple substrings and expanding them into multiple rows. However, unlike `explode_split`, it behaves differently when handling empty or NULL values, being able to handle empty or NULL strings.

## Syntax

`explode_split(str, delimiter)`

## Parameters

| Parameter | Description |
| -- | -- |
| `str` | String type |
| `delimiter` | Delimiter |

## Return Value

Returns a sequence of the split substrings. If the string is empty or NULL, no rows are returned.

## Examples

```sql
select * from example1 order by k1;
```
```text
+------+---------+
| k1   | k2      |
+------+---------+
|    1 |         |
|    2 | NULL    |
|    3 | ,       |
|    4 | 1       |
|    5 | 1,2,3   |
|    6 | a, b, c |
+------+---------+
```

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