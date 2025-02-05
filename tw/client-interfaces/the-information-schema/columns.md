# 36.17. columns

此檢視表欄位包含了有關資料庫中所有資料表欄位（或檢視表欄位）的資訊。不包括系統欄位（ctid 等）。僅顯示目前使用者有權存取的那些欄位（擁有者或具有某些權限）。

#### **Table 36.15. `columns` Columns**

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
        <p><code>table_catalog</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the database containing the table (always the current database)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>table_schema</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the schema containing the table</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>table_name</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the table</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>column_name</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the column</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>ordinal_position</code>  <code>cardinal_number</code>
        </p>
        <p>Ordinal position of the column within the table (count starts at 1)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>column_default</code>  <code>character_data</code>
        </p>
        <p>Default expression of the column</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>is_nullable</code>  <code>yes_or_no</code>
        </p>
        <p><code>YES</code> if the column is possibly nullable, <code>NO</code> if it
          is known not nullable. A not-null constraint is one way a column can be
          known not nullable, but there can be others.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>data_type</code>  <code>character_data</code>
        </p>
        <p>Data type of the column, if it is a built-in type, or <code>ARRAY</code> if
          it is some array (in that case, see the view <code>element_types</code>),
          else <code>USER-DEFINED</code> (in that case, the type is identified in <code>udt_name</code> and
          associated columns). If the column is based on a domain, this column refers
          to the type underlying the domain (and the domain is identified in <code>domain_name</code> and
          associated columns).</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>character_maximum_length</code>  <code>cardinal_number</code>
        </p>
        <p>If <code>data_type</code> identifies a character or bit string type, the
          declared maximum length; null for all other data types or if no maximum
          length was declared.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>character_octet_length</code>  <code>cardinal_number</code>
        </p>
        <p>If <code>data_type</code> identifies a character type, the maximum possible
          length in octets (bytes) of a datum; null for all other data types. The
          maximum octet length depends on the declared character maximum length (see
          above) and the server encoding.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>numeric_precision</code>  <code>cardinal_number</code>
        </p>
        <p>If <code>data_type</code> identifies a numeric type, this column contains
          the (declared or implicit) precision of the type for this column. The precision
          indicates the number of significant digits. It can be expressed in decimal
          (base 10) or binary (base 2) terms, as specified in the column <code>numeric_precision_radix</code>.
          For all other data types, this column is null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>numeric_precision_radix</code>  <code>cardinal_number</code>
        </p>
        <p>If <code>data_type</code> identifies a numeric type, this column indicates
          in which base the values in the columns <code>numeric_precision</code> and <code>numeric_scale</code> are
          expressed. The value is either 2 or 10. For all other data types, this
          column is null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>numeric_scale</code>  <code>cardinal_number</code>
        </p>
        <p>If <code>data_type</code> identifies an exact numeric type, this column
          contains the (declared or implicit) scale of the type for this column.
          The scale indicates the number of significant digits to the right of the
          decimal point. It can be expressed in decimal (base 10) or binary (base
          2) terms, as specified in the column <code>numeric_precision_radix</code>.
          For all other data types, this column is null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>datetime_precision</code>  <code>cardinal_number</code>
        </p>
        <p>If <code>data_type</code> identifies a date, time, timestamp, or interval
          type, this column contains the (declared or implicit) fractional seconds
          precision of the type for this column, that is, the number of decimal digits
          maintained following the decimal point in the seconds value. For all other
          data types, this column is null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>interval_type</code>  <code>character_data</code>
        </p>
        <p>If <code>data_type</code> identifies an interval type, this column contains
          the specification which fields the intervals include for this column, e.g., <code>YEAR TO MONTH</code>, <code>DAY TO SECOND</code>,
          etc. If no field restrictions were specified (that is, the interval accepts
          all fields), and for all other data types, this field is null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>interval_precision</code>  <code>cardinal_number</code>
        </p>
        <p>Applies to a feature not available in PostgreSQL (see <code>datetime_precision</code> for
          the fractional seconds precision of interval type columns)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>character_set_catalog</code>  <code>sql_identifier</code>
        </p>
        <p>Applies to a feature not available in PostgreSQL</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>character_set_schema</code>  <code>sql_identifier</code>
        </p>
        <p>Applies to a feature not available in PostgreSQL</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>character_set_name</code>  <code>sql_identifier</code>
        </p>
        <p>Applies to a feature not available in PostgreSQL</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>collation_catalog</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the database containing the collation of the column (always the
          current database), null if default or the data type of the column is not
          collatable</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>collation_schema</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the schema containing the collation of the column, null if default
          or the data type of the column is not collatable</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>collation_name</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the collation of the column, null if default or the data type
          of the column is not collatable</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>domain_catalog</code>  <code>sql_identifier</code>
        </p>
        <p>If the column has a domain type, the name of the database that the domain
          is defined in (always the current database), else null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>domain_schema</code>  <code>sql_identifier</code>
        </p>
        <p>If the column has a domain type, the name of the schema that the domain
          is defined in, else null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>domain_name</code>  <code>sql_identifier</code>
        </p>
        <p>If the column has a domain type, the name of the domain, else null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>udt_catalog</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the database that the column data type (the underlying type of
          the domain, if applicable) is defined in (always the current database)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>udt_schema</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the schema that the column data type (the underlying type of the
          domain, if applicable) is defined in</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>udt_name</code>  <code>sql_identifier</code>
        </p>
        <p>Name of the column data type (the underlying type of the domain, if applicable)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>scope_catalog</code>  <code>sql_identifier</code>
        </p>
        <p>Applies to a feature not available in PostgreSQL</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>scope_schema</code>  <code>sql_identifier</code>
        </p>
        <p>Applies to a feature not available in PostgreSQL</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>scope_name</code>  <code>sql_identifier</code>
        </p>
        <p>Applies to a feature not available in PostgreSQL</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>maximum_cardinality</code>  <code>cardinal_number</code>
        </p>
        <p>Always null, because arrays always have unlimited maximum cardinality
          in PostgreSQL</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>dtd_identifier</code>  <code>sql_identifier</code>
        </p>
        <p>An identifier of the data type descriptor of the column, unique among
          the data type descriptors pertaining to the table. This is mainly useful
          for joining with other instances of such identifiers. (The specific format
          of the identifier is not defined and not guaranteed to remain the same
          in future versions.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>is_self_referencing</code>  <code>yes_or_no</code>
        </p>
        <p>Applies to a feature not available in PostgreSQL</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>is_identity</code>  <code>yes_or_no</code>
        </p>
        <p>If the column is an identity column, then <code>YES</code>, else <code>NO</code>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>identity_generation</code>  <code>character_data</code>
        </p>
        <p>If the column is an identity column, then <code>ALWAYS</code> or <code>BY DEFAULT</code>,
          reflecting the definition of the column.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>identity_start</code>  <code>character_data</code>
        </p>
        <p>If the column is an identity column, then the start value of the internal
          sequence, else null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>identity_increment</code>  <code>character_data</code>
        </p>
        <p>If the column is an identity column, then the increment of the internal
          sequence, else null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>identity_maximum</code>  <code>character_data</code>
        </p>
        <p>If the column is an identity column, then the maximum value of the internal
          sequence, else null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>identity_minimum</code>  <code>character_data</code>
        </p>
        <p>If the column is an identity column, then the minimum value of the internal
          sequence, else null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>identity_cycle</code>  <code>yes_or_no</code>
        </p>
        <p>If the column is an identity column, then <code>YES</code> if the internal
          sequence cycles or <code>NO</code> if it does not; otherwise null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>is_generated</code>  <code>character_data</code>
        </p>
        <p>If the column is a generated column, then <code>ALWAYS</code>, else <code>NEVER</code>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>generation_expression</code>  <code>character_data</code>
        </p>
        <p>If the column is a generated column, then the generation expression, else
          null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>is_updatable</code>  <code>yes_or_no</code>
        </p>
        <p><code>YES</code> if the column is updatable, <code>NO</code> if not (Columns
          in base tables are always updatable, columns in views not necessarily)</p>
      </td>
    </tr>
  </tbody>
</table>

Since data types can be defined in a variety of ways in SQL, and PostgreSQL contains additional ways to define data types, their representation in the information schema can be somewhat difficult. The column `data_type` is supposed to identify the underlying built-in type of the column. In PostgreSQL, this means that the type is defined in the system catalog schema `pg_catalog`. This column might be useful if the application can handle the well-known built-in types specially \(for example, format the numeric types differently or use the data in the precision columns\). The columns `udt_name`, `udt_schema`, and `udt_catalog` always identify the underlying data type of the column, even if the column is based on a domain. \(Since PostgreSQL treats built-in types like user-defined types, built-in types appear here as well. This is an extension of the SQL standard.\) These columns should be used if an application wants to process data differently according to the type, because in that case it wouldn't matter if the column is really based on a domain. If the column is based on a domain, the identity of the domain is stored in the columns `domain_name`, `domain_schema`, and `domain_catalog`. If you want to pair up columns with their associated data types and treat domains as separate types, you could write `coalesce(domain_name, udt_name)`, etc.

