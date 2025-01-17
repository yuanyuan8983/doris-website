---
{
    "title": "EXPLODE_SPLIT",
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

The `explode_split` table function is used to split a string into multiple substrings based on a specified delimiter and expand each substring into a separate row. Each substring is returned as an individual row, and it is typically used with LATERAL VIEW to break down long strings into individual parts for more granular queries.

## Syntax

`explode_split(<str>, <delimiter>)`

## Parameters

| Parameter | Description |
| -- | -- |
| `<str>` | String type |
| `<delimiter>` | Delimiter |

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
select k1, e1 from example1 lateral view explode_split(k2, ',') tmp1 as e1 where k1 = 1 order by k1, e1;
```
```text
+------+------+
| k1   | e1   |
+------+------+
|    1 |      |
+------+------+
```
```sql
select k1, e1 from example1 lateral view explode_split(k2, ',') tmp1 as e1 where k1 = 2 order by k1, e1;
Empty set
```
```sql
select k1, e1 from example1 lateral view explode_split(k2, ',') tmp1 as e1 where k1 = 3 order by k1, e1;
```
```text
+------+------+
| k1   | e1   |
+------+------+
|    3 |      |
+------+------+
```
```sql
select k1, e1 from example1 lateral view explode_split(k2, ',') tmp1 as e1 where k1 = 4 order by k1, e1;
```
```text
+------+------+
| k1   | e1   |
+------+------+
|    4 | 1    |
+------+------+
```
```sql
select k1, e1 from example1 lateral view explode_split(k2, ',') tmp1 as e1 where k1 = 5 order by k1, e1;
```
```text
+------+------+
| k1   | e1   |
+------+------+
|    5 | 2    |
|    5 | 3    |
|    5 | 1    |
+------+------+
```
```sql
select k1, e1 from example1 lateral view explode_split(k2, ',') tmp1 as e1 where k1 = 6 order by k1, e1;
```
```text
+------+------+
| k1   | e1   |
+------+------+
|    6 |  b   |
|    6 |  c   |
|    6 | a    |
+------+------+
```