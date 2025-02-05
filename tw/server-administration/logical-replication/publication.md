# 30.1. 發佈（Publication）

可以在任何實體複寫主機上定義 PUBLICATION \(發佈\)。 定義 PUBLICATION 的節點稱為 Publisher \(發佈者\)。PUBLICATION 是從一個資料表或一組資料表中所産生的一組變更，也可能被描述為變更集合或複寫集合。每個發佈僅能存在於一個資料庫中。

發佈與綱要\(Schema\)不同，它並不會影響資料表的存取方式。如果需要，每個資料表可以加到多個發佈之中。 發佈目前可能只包含資料表。物件必須明確加入，除非為 ALL TABLES 建立發佈。

發佈可以選擇將其產生的變更限制為 INSERT，UPDATE 和 DELETE 的任意組合，類似於特定事件類型觸發器的方式。預設情況下，所有操作類型都被複寫。

發佈的資料表必須配置「副本識別」，以便能夠複寫 UPDATE 和 DELETE 操作，使在訂閱端可以識別更新或刪除適當的資料列。預設情況下，這是主鍵，如果有的話。另一個是唯一索引（具有某些附加要求）也可以設定為副本識別。如果該資料表沒有任何合適的方式，則可以將其設定為副本識別「full」，這意味著整個資料列都作為識別。但是，這效能是非常低的，只有在沒有其他解決方案可行的情況下才可以這樣使用。如果在發佈方設定了除「full」之外的副本識別，則還必須在訂閱戶設定包含相同或更少欄位的副本標別。有關如何設定副本識別的詳細訊息，請參閱 [REPLICA IDENTITY](../../reference/sql-commands/alter-table.md#replica-identity)。如果沒有副本識別的資料表被加到複寫 UPDATE 或 DELETE 操作的發佈中，則隨後的 UPDATE 或 DELETE 操作將導致發佈者出錯。不管任何副本識別，INSERT 操作都可以繼續進行。

每個發佈可以有多個訂閱者。

使用 [CREATE PUBLICATION](../../reference/sql-commands/create-publication.md) 指令建立發佈，稍後可以使用相應的命令變更或移除發佈。

可以使用 [ALTER PUBLICATION](../../reference/sql-commands/alter-publication.md) 動態加入和移除單個資料表。ADD TABLE 和 DROP TABLE 操作都是交易安全的；所以一旦交易事務提交後，資料表就會在正確的快照上，並且啟動或停止複寫。

