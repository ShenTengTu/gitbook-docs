# 13.1. 簡介

PostgreSQL 為開發者們提供了豐富的工具來管理資料的同時存取。資料的一致性在資料內部是以多重資料版本的方式維護（Multiversion Concurrency Control，MVCC），這表示無論目前資料的當下狀態如何，每個 SQL 指令會看見的是資料在一段時間前的快照（資料庫的某個版本）。這個機制可以避免指令看到由其他同時交易正在更新同個資料列所產生的資料不一致，也對每個資料庫的連線階段提供了_交易隔離_。MVCC 也藉由避開傳統資料庫系統的上鎖方式減少了鎖的競爭，以在多使用者的環境中提供合理的效能。

相較於鎖定的機制來說，一致性控制使用 MVCC 模式的主要優勢，是在於 MVCC 對於查詢（讀取）資料的鎖並不會和寫入資料的鎖發生衝突，因此讀取不會阻擋寫入、寫入也不會阻擋讀取。PostgreSQL 即使在提供最嚴格的交易隔離等級中，也會透過使用創新的_可序列化快照隔離_（Serializable Snapshot Isolation，SSI）等級來維持這個保證。

對於不需要完整的交易隔離、或者喜歡明確地管理特定衝突點的應用程式，PostgreSQL 也提供資料表和資料列等級的鎖定功能。然而，適當地使用 MVCC 一般能夠提供比鎖定功能更佳的效能。此外，應用程式定義的 advisory lock 提供了一種與交易事務無關的鎖定機制。

