# 9.23. 子查詢

This section describes the SQL-compliant subquery expressions available in PostgreSQL. All of the expression forms documented in this section return Boolean \(true/false\) results.

## 9.23.1. `EXISTS`

```text
EXISTS (subquery)
```

The argument of `EXISTS` is an arbitrary `SELECT` statement, or _subquery_. The subquery is evaluated to determine whether it returns any rows. If it returns at least one row, the result of `EXISTS` is “true”; if the subquery returns no rows, the result of `EXISTS` is “false”.

The subquery can refer to variables from the surrounding query, which will act as constants during any one evaluation of the subquery.

The subquery will generally only be executed long enough to determine whether at least one row is returned, not all the way to completion. It is unwise to write a subquery that has side effects \(such as calling sequence functions\); whether the side effects occur might be unpredictable.

Since the result depends only on whether any rows are returned, and not on the contents of those rows, the output list of the subquery is normally unimportant. A common coding convention is to write all `EXISTS` tests in the form `EXISTS(SELECT 1 WHERE ...)`. There are exceptions to this rule however, such as subqueries that use `INTERSECT`.

This simple example is like an inner join on `col2`, but it produces at most one output row for each `tab1` row, even if there are several matching `tab2` rows:

```text
SELECT col1
FROM tab1
WHERE EXISTS (SELECT 1 FROM tab2 WHERE col2 = tab1.col2);
```

## 9.23.2. `IN`

```text
expression IN (subquery)
```

The right-hand side is a parenthesized subquery, which must return exactly one column. The left-hand expression is evaluated and compared to each row of the subquery result. The result of `IN` is “true” if any equal subquery row is found. The result is “false” if no equal row is found \(including the case where the subquery returns no rows\).

Note that if the left-hand expression yields null, or if there are no equal right-hand values and at least one right-hand row yields null, the result of the `IN` construct will be null, not false. This is in accordance with SQL's normal rules for Boolean combinations of null values.

As with `EXISTS`, it's unwise to assume that the subquery will be evaluated completely.

```text
row_constructor IN (subquery)
```

The left-hand side of this form of `IN` is a row constructor, as described in [Section 4.2.13](https://www.postgresql.org/docs/13/sql-expressions.html#SQL-SYNTAX-ROW-CONSTRUCTORS). The right-hand side is a parenthesized subquery, which must return exactly as many columns as there are expressions in the left-hand row. The left-hand expressions are evaluated and compared row-wise to each row of the subquery result. The result of `IN` is “true” if any equal subquery row is found. The result is “false” if no equal row is found \(including the case where the subquery returns no rows\).

As usual, null values in the rows are combined per the normal rules of SQL Boolean expressions. Two rows are considered equal if all their corresponding members are non-null and equal; the rows are unequal if any corresponding members are non-null and unequal; otherwise the result of that row comparison is unknown \(null\). If all the per-row results are either unequal or null, with at least one null, then the result of `IN` is null.

## 9.23.3. `NOT IN`

```text
expression NOT IN (subquery)
```

The right-hand side is a parenthesized subquery, which must return exactly one column. The left-hand expression is evaluated and compared to each row of the subquery result. The result of `NOT IN` is “true” if only unequal subquery rows are found \(including the case where the subquery returns no rows\). The result is “false” if any equal row is found.

Note that if the left-hand expression yields null, or if there are no equal right-hand values and at least one right-hand row yields null, the result of the `NOT IN` construct will be null, not true. This is in accordance with SQL's normal rules for Boolean combinations of null values.

As with `EXISTS`, it's unwise to assume that the subquery will be evaluated completely.

```text
row_constructor NOT IN (subquery)
```

The left-hand side of this form of `NOT IN` is a row constructor, as described in [Section 4.2.13](https://www.postgresql.org/docs/13/sql-expressions.html#SQL-SYNTAX-ROW-CONSTRUCTORS). The right-hand side is a parenthesized subquery, which must return exactly as many columns as there are expressions in the left-hand row. The left-hand expressions are evaluated and compared row-wise to each row of the subquery result. The result of `NOT IN` is “true” if only unequal subquery rows are found \(including the case where the subquery returns no rows\). The result is “false” if any equal row is found.

As usual, null values in the rows are combined per the normal rules of SQL Boolean expressions. Two rows are considered equal if all their corresponding members are non-null and equal; the rows are unequal if any corresponding members are non-null and unequal; otherwise the result of that row comparison is unknown \(null\). If all the per-row results are either unequal or null, with at least one null, then the result of `NOT IN` is null.

## 9.23.4. `ANY`/`SOME`

```text
expression operator ANY (subquery)
expression operator SOME (subquery)
```

The right-hand side is a parenthesized subquery, which must return exactly one column. The left-hand expression is evaluated and compared to each row of the subquery result using the given _`operator`_, which must yield a Boolean result. The result of `ANY` is “true” if any true result is obtained. The result is “false” if no true result is found \(including the case where the subquery returns no rows\).

`SOME` is a synonym for `ANY`. `IN` is equivalent to `= ANY`.

Note that if there are no successes and at least one right-hand row yields null for the operator's result, the result of the `ANY` construct will be null, not false. This is in accordance with SQL's normal rules for Boolean combinations of null values.

As with `EXISTS`, it's unwise to assume that the subquery will be evaluated completely.

```text
row_constructor operator ANY (subquery)
row_constructor operator SOME (subquery)
```

The left-hand side of this form of `ANY` is a row constructor, as described in [Section 4.2.13](https://www.postgresql.org/docs/13/sql-expressions.html#SQL-SYNTAX-ROW-CONSTRUCTORS). The right-hand side is a parenthesized subquery, which must return exactly as many columns as there are expressions in the left-hand row. The left-hand expressions are evaluated and compared row-wise to each row of the subquery result, using the given _`operator`_. The result of `ANY` is “true” if the comparison returns true for any subquery row. The result is “false” if the comparison returns false for every subquery row \(including the case where the subquery returns no rows\). The result is NULL if no comparison with a subquery row returns true, and at least one comparison returns NULL.

See [Section 9.24.5](https://www.postgresql.org/docs/13/functions-comparisons.html#ROW-WISE-COMPARISON) for details about the meaning of a row constructor comparison.

## 9.23.5. `ALL`

```text
expression operator ALL (subquery)
```

The right-hand side is a parenthesized subquery, which must return exactly one column. The left-hand expression is evaluated and compared to each row of the subquery result using the given _`operator`_, which must yield a Boolean result. The result of `ALL` is “true” if all rows yield true \(including the case where the subquery returns no rows\). The result is “false” if any false result is found. The result is NULL if no comparison with a subquery row returns false, and at least one comparison returns NULL.

`NOT IN` is equivalent to `<> ALL`.

As with `EXISTS`, it's unwise to assume that the subquery will be evaluated completely.

```text
row_constructor operator ALL (subquery)
```

The left-hand side of this form of `ALL` is a row constructor, as described in [Section 4.2.13](https://www.postgresql.org/docs/13/sql-expressions.html#SQL-SYNTAX-ROW-CONSTRUCTORS). The right-hand side is a parenthesized subquery, which must return exactly as many columns as there are expressions in the left-hand row. The left-hand expressions are evaluated and compared row-wise to each row of the subquery result, using the given _`operator`_. The result of `ALL` is “true” if the comparison returns true for all subquery rows \(including the case where the subquery returns no rows\). The result is “false” if the comparison returns false for any subquery row. The result is NULL if no comparison with a subquery row returns false, and at least one comparison returns NULL.

See [Section 9.24.5](https://www.postgresql.org/docs/13/functions-comparisons.html#ROW-WISE-COMPARISON) for details about the meaning of a row constructor comparison.

## 9.23.6. Single-Row Comparison

```text
row_constructor operator (subquery)
```

The left-hand side is a row constructor, as described in [Section 4.2.13](https://www.postgresql.org/docs/13/sql-expressions.html#SQL-SYNTAX-ROW-CONSTRUCTORS). The right-hand side is a parenthesized subquery, which must return exactly as many columns as there are expressions in the left-hand row. Furthermore, the subquery cannot return more than one row. \(If it returns zero rows, the result is taken to be null.\) The left-hand side is evaluated and compared row-wise to the single subquery result row.

See [Section 9.24.5](https://www.postgresql.org/docs/13/functions-comparisons.html#ROW-WISE-COMPARISON) for details about the meaning of a row constructor comparison.

