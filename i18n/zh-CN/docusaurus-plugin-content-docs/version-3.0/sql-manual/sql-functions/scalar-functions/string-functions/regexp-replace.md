---
{
    "title": "REGEXP_REPLACE",
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

## regexp_replace
## 描述
## 语法

`VARCHAR regexp_replace(VARCHAR str, VARCHAR pattern, VARCHAR repl)`

对字符串 str 进行正则匹配, 将命中 pattern 的部分使用 repl 来进行替换

字符集匹配需要使用 Unicode 标准字符类型。例如，匹配中文请使用 `\p{Han}`。

## 举例

```sql
mysql> SELECT regexp_replace('a b c', " ", "-");
+-----------------------------------+
| regexp_replace('a b c', ' ', '-') |
+-----------------------------------+
| a-b-c                             |
+-----------------------------------+

mysql> SELECT regexp_replace('a b c','(b)','<\\1>');
+----------------------------------------+
| regexp_replace('a b c', '(b)', '<\1>') |
+----------------------------------------+
| a <b> c                                |
+----------------------------------------+

mysql> select regexp_replace('这是一段中文This is a passage in English 1234567', '\\p{Han}+', '123');
+---------------------------------------------------------------------------------------------+
| regexp_replace('这是一段中文This is a passage in English 1234567', '\p{Han}+', '123')       |
+---------------------------------------------------------------------------------------------+
| 123This is a passage in English 1234567                                                     |
+---------------------------------------------------------------------------------------------+
```

### keywords
    REGEXP_REPLACE,REGEXP,REPLACE
