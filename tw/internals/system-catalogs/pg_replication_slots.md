# 51.80. pg\_replication\_slots

The `pg_replication_slots` view provides a listing of all replication slots that currently exist on the database cluster, along with their current state.

For more on replication slots, see [Section 26.2.6](https://www.postgresql.org/docs/13/warm-standby.html#STREAMING-REPLICATION-SLOTS) and [Chapter 48](https://www.postgresql.org/docs/13/logicaldecoding.html).

#### **Table 51.81. `pg_replication_slots` Columns**

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
        <p><code>slot_name</code>  <code>name</code>
        </p>
        <p>A unique, cluster-wide identifier for the replication slot</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>plugin</code>  <code>name</code>
        </p>
        <p>The base name of the shared object containing the output plugin this logical
          slot is using, or null for physical slots.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>slot_type</code>  <code>text</code>
        </p>
        <p>The slot type: <code>physical</code> or <code>logical</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>datoid</code>  <code>oid</code> (references <a href="https://www.postgresql.org/docs/13/catalog-pg-database.html"><code>pg_database</code></a>.<code>oid</code>)</p>
        <p>The OID of the database this slot is associated with, or null. Only logical
          slots have an associated database.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>database</code>  <code>name</code> (references <a href="https://www.postgresql.org/docs/13/catalog-pg-database.html"><code>pg_database</code></a>.<code>datname</code>)</p>
        <p>The name of the database this slot is associated with, or null. Only logical
          slots have an associated database.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>temporary</code>  <code>bool</code>
        </p>
        <p>True if this is a temporary replication slot. Temporary slots are not
          saved to disk and are automatically dropped on error or when the session
          has finished.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>active</code>  <code>bool</code>
        </p>
        <p>True if this slot is currently actively being used</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>active_pid</code>  <code>int4</code>
        </p>
        <p>The process ID of the session using this slot if the slot is currently
          actively being used. <code>NULL</code> if inactive.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>xmin</code>  <code>xid</code>
        </p>
        <p>The oldest transaction that this slot needs the database to retain. <code>VACUUM</code> cannot
          remove tuples deleted by any later transaction.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>catalog_xmin</code>  <code>xid</code>
        </p>
        <p>The oldest transaction affecting the system catalogs that this slot needs
          the database to retain. <code>VACUUM</code> cannot remove catalog tuples
          deleted by any later transaction.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>restart_lsn</code>  <code>pg_lsn</code>
        </p>
        <p>The address (<code>LSN</code>) of oldest WAL which still might be required
          by the consumer of this slot and thus won&apos;t be automatically removed
          during checkpoints unless this LSN gets behind more than <a href="https://www.postgresql.org/docs/13/runtime-config-replication.html#GUC-MAX-SLOT-WAL-KEEP-SIZE">max_slot_wal_keep_size</a> from
          the current LSN. <code>NULL</code> if the <code>LSN</code> of this slot has
          never been reserved.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>confirmed_flush_lsn</code>  <code>pg_lsn</code>
        </p>
        <p>The address (<code>LSN</code>) up to which the logical slot&apos;s consumer
          has confirmed receiving data. Data older than this is not available anymore. <code>NULL</code> for
          physical slots.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>wal_status</code>  <code>text</code>
        </p>
        <p>Availability of WAL files claimed by this slot. Possible values are:</p>
        <ul>
          <li><code>reserved</code> means that the claimed files are within <code>max_wal_size</code>.</li>
          <li><code>extended</code> means that <code>max_wal_size</code> is exceeded but
            the files are still retained, either by the replication slot or by <code>wal_keep_size</code>.</li>
          <li><code>unreserved</code> means that the slot no longer retains the required
            WAL files and some of them are to be removed at the next checkpoint. This
            state can return to <code>reserved</code> or <code>extended</code>.</li>
          <li><code>lost</code> means that some required WAL files have been removed
            and this slot is no longer usable.</li>
        </ul>
        <p>The last two states are seen only when <a href="https://www.postgresql.org/docs/13/runtime-config-replication.html#GUC-MAX-SLOT-WAL-KEEP-SIZE">max_slot_wal_keep_size</a> is
          non-negative. If <code>restart_lsn</code> is NULL, this field is null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><code>safe_wal_size</code>  <code>int8</code>
        </p>
        <p>The number of bytes that can be written to WAL such that this slot is
          not in danger of getting in state &quot;lost&quot;. It is NULL for lost
          slots, as well as if <code>max_slot_wal_keep_size</code> is <code>-1</code>.</p>
      </td>
    </tr>
  </tbody>
</table>

