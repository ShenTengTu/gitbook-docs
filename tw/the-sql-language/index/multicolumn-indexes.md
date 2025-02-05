# 11.3. 多欄位索引

可以在資料表的多個欄位上定義索引。例如，如果您有此形式的資料表：

```text
CREATE TABLE test2 (
  major int,
  minor int,
  name varchar
);
```

（比如，你將 /dev 目錄內容儲存在資料庫中......）並經常發出如下查詢：

```text
SELECT name FROM test2 WHERE major = constant AND minor = constant;
```

那麼在 major 和 minor 欄位上定義一個索引可能是合適的，例如：

```text
CREATE INDEX test2_mm_idx ON test2 (major, minor);
```

目前，只有 B-tree、GiST、GIN 和 BRIN 索引型別支援多欄位索引。最多可以指定 32 個欄位。（編譯 PostgreSQL 時可以變更此限制；請參閱檔案 pg\_config\_manual.h。）

多欄位 B-tree 索引可以與涉及索引欄位的任何子集的查詢條件一起使用，但是當前導（最左側）欄位存在限制條件時，索引最有效。確切的規則是對前導欄位的等式限制條件以及第一欄位上沒有等式限制條件的任何不等式限制條件將用於索引的條件掃描。在索引中檢查對這些欄位右側的欄位限制條件，因此它們可以儲存對資料表的正確存取，而不會減少必須掃描的索引部分。例如，給定 \(a, b, c\) 上的索引和查詢條件 WHERE a = 5 AND b &gt;= 42 AND c &lt; 77，必須從第一個項目掃描索引，其中 a = 5 且 b = 42，直到最後一個項目 a = 5。將跳過 c &gt;= 77 的索引條目，但仍然需要掃描它們。該索引原則上可以用於對 b 或 c 有限制條件，但對 a 沒有限制條件的查詢 - 只是必須掃描整個索引，因此在大多數情況下，查詢規劃程序更喜歡使用索引進行循序資料表掃描。

多欄位 GiST 索引可以與涉及索引欄位的任何子集的查詢條件一起使用。其他欄位的條件限制索引回傳的項目，但第一欄位的條件是確定需要掃描多少索引的最重要條件。如果 GiST 索引的第一欄位只有幾個不同的值，即使其他欄位中有許多不同的值，它也會相對無效。

多欄位 GIN 索引可以與涉及索引欄位的任何子集的查詢條件一起使用。與 B-tree 或 GiST 不同，無論查詢條件使用哪個索引欄位，索引搜尋的有效性都是相同的。

多欄位 BRIN 索引可以與涉及索引欄位的任何子集的查詢條件一起使用。與 GIN 類似，與 B-tree 或 GiST 不同，無論查詢條件使用哪個索引欄位，索引搜尋有效性都是相同的。在單個資料表上具有多個 BRIN 索引而不是一個多欄位 BRIN 索引的唯一原因是具有不同的 pages\_per\_range 儲存參數。

當然，每個欄位必須與適合索引類型的運算子一起使用；涉及其他運算子的子句將不予考慮。

應謹慎使用多欄位索引。在大多數情況下，單個欄位上的索引就足夠了，節省了空間和時間。除非資料表的使用非常特殊，否則具有三個欄位以上的索引不太可能有用。有關不同索引配置的優點的一些討論，另請參閱[第 11.5 節](combining-multiple-indexes.md)和[第 11.9 節](index-only-scans-and-covering-indexes.md)。

