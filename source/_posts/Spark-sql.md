---
layout: posts
title: Spark Sql
date: 2021-03-18 18:18:18
tags: spark
categories:
- bigdata
- spark
---

## Spark Sql Techniques

### Pivot and Unpivot
`行转列 and 列转行`

列转行一般需要将该表的几个字段（列）所属同一种类的字段进行总结，并且新的字段的值就是之前的字段名
行转列一般需要将该表的某一字段下的数据进行汇总并且总结成新的多个字段

All of this two techniques need to use 

## SQL 

## Keywords

WITH
---

Referrence: [https://spark.apache.org/docs/latest/sql-ref-syntax-qry-select-cte.html](https://spark.apache.org/docs/latest/sql-ref-syntax-qry-select-cte.html)

It's called `Common Table Expression`

Syntax:

```
WITH common_table_expression [ , ... ]

While common_table_expression is defined as
expression_name [ ( column_name [ , ... ] ) ] [ AS ] ( query )
```

LATERAL VIEW
---

Referrence: [https://spark.apache.org/docs/3.1.1/sql-ref-syntax-qry-select-lateral-view.html#content](https://spark.apache.org/docs/3.1.1/sql-ref-syntax-qry-select-lateral-view.html#content)

Syntax:

```
LATERAL VIEW [ OUTER ] generator_function ( expression [ , ... ] ) [ table_alias ] AS column_alias [ , ... ]
```

This clause follows after `original table` or `view` to append each row which is generated by `generator_function` to `each original output row`. Then you original table will contains more columns to become a wide table for speeding up the searchs.

## Spark Functions

explode(array(...)
---

Referrence: [https://spark.apache.org/docs/latest/api/sql/index.html#explode](https://spark.apache.org/docs/latest/api/sql/index.html#explode)

Usually used to separate the elements in array or map into row formation. 

`cloumn_name` can also used as the parameter in `explode` function.

row_numer()
---

Referrence: [https://spark.apache.org/docs/latest/api/sql/#row_number](https://spark.apache.org/docs/latest/api/sql/#row_number)

Usually used with `PARTITION BY` keywords. E.g. `row_number() OVER (PARTITION BY col1 ORDER BY col2) FROM table1`

nvl(expr1, expr2)
---

Equals with: `Optional(expr1).getOrElse(expr2)`