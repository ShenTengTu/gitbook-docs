# 51.88. pg\_stats

The view `pg_stats` provides access to the information stored in the [`pg_statistic`](https://www.postgresql.org/docs/13/catalog-pg-statistic.html) catalog. This view allows access only to rows of `pg_statistic` that correspond to tables the user has permission to read, and therefore it is safe to allow public read access to this view.

`pg_stats` is also designed to present the information in a more readable format than the underlying catalog — at the cost that its schema must be extended whenever new slot types are defined for `pg_statistic`.

#### **Table 51.89. `pg_stats` Columns**

<table>
  <thead>
    <tr>
      <th style="text-align:left">
        <p>Column Type</p>
        <p>Description</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p><code>schemaname</code>  <code>name</code> (references <a href="https://www.postgresql.org/docs/13/catalog-pg-namespace.html"><code>pg_namespace</code></a>.<code>nspname</code>)</p>
        <p>Name of schema containing table</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>tablename</code>  <code>name</code> (references <a href="https://www.postgresql.org/docs/13/catalog-pg-class.html"><code>pg_class</code></a>.<code>relname</code>)</p>
        <p>Name of table</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>attname</code>  <code>name</code> (references <a href="https://www.postgresql.org/docs/13/catalog-pg-attribute.html"><code>pg_attribute</code></a>.<code>attname</code>)</p>
        <p>Name of the column described by this row</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>inherited</code>  <code>bool</code>
        </p>
        <p>If true, this row includes inheritance child columns, not just the values
          in the specified table</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>null_frac</code>  <code>float4</code>
        </p>
        <p>Fraction of column entries that are null</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>avg_width</code>  <code>int4</code>
        </p>
        <p>Average width in bytes of column&apos;s entries</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>n_distinct</code>  <code>float4</code>
        </p>
        <p>If greater than zero, the estimated number of distinct values in the column.
          If less than zero, the negative of the number of distinct values divided
          by the number of rows. (The negated form is used when <code>ANALYZE</code> believes
          that the number of distinct values is likely to increase as the table grows;
          the positive form is used when the column seems to have a fixed number
          of possible values.) For example, -1 indicates a unique column in which
          the number of distinct values is the same as the number of rows.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>most_common_vals</code>  <code>anyarray</code>
        </p>
        <p>A list of the most common values in the column. (Null if no values seem
          to be more common than any others.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>most_common_freqs</code>  <code>float4[]</code>
        </p>
        <p>A list of the frequencies of the most common values, i.e., number of occurrences
          of each divided by total number of rows. (Null when <code>most_common_vals</code> is.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>histogram_bounds</code>  <code>anyarray</code>
        </p>
        <p>A list of values that divide the column&apos;s values into groups of approximately
          equal population. The values in <code>most_common_vals</code>, if present,
          are omitted from this histogram calculation. (This column is null if the
          column data type does not have a <code>&lt;</code> operator or if the <code>most_common_vals</code> list
          accounts for the entire population.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>correlation</code>  <code>float4</code>
        </p>
        <p>Statistical correlation between physical row ordering and logical ordering
          of the column values. This ranges from -1 to +1. When the value is near
          -1 or +1, an index scan on the column will be estimated to be cheaper than
          when it is near zero, due to reduction of random access to the disk. (This
          column is null if the column data type does not have a <code>&lt;</code> operator.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>most_common_elems</code>  <code>anyarray</code>
        </p>
        <p>A list of non-null element values most often appearing within values of
          the column. (Null for scalar types.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>most_common_elem_freqs</code>  <code>float4[]</code>
        </p>
        <p>A list of the frequencies of the most common element values, i.e., the
          fraction of rows containing at least one instance of the given value. Two
          or three additional values follow the per-element frequencies; these are
          the minimum and maximum of the preceding per-element frequencies, and optionally
          the frequency of null elements. (Null when <code>most_common_elems</code> is.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>elem_count_histogram</code>  <code>float4[]</code>
        </p>
        <p>A histogram of the counts of distinct non-null element values within the
          values of the column, followed by the average number of distinct non-null
          elements. (Null for scalar types.)</p>
      </td>
    </tr>
  </tbody>
</table>

The maximum number of entries in the array fields can be controlled on a column-by-column basis using the `ALTER TABLE SET STATISTICS` command, or globally by setting the [default\_statistics\_target](https://www.postgresql.org/docs/13/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) run-time parameter.

