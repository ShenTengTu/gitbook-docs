# 28.2. 磁碟空間不足錯誤

資料庫管理員最重要的磁碟監控任務是確保磁碟空間是足夠的。充滿資料的資料磁碟不會導致資料損壞，但是可能限制繼續進行資料處理的活動。如果儲存 WAL 檔案的磁碟空間已滿，則資料庫伺服器會出現混亂，並因此而導致服務中斷。

如果無法透過刪除其他內容來釋放磁碟上的其他空間，則可以透過使用資料表空間將某些資料庫檔案移至其他檔案系統。有關更多資訊，請參閱[第 22.6 節](../managing-databases/tablespaces.md)。

**注意**  
有一些檔案系統在幾乎全滿時效能會很差，因此不要等到磁碟完全滿之後才採取措施。

如果您的系統支援使用者的磁碟配額，那麼資料庫自然會受到伺服器作為其執行使用者的配額限制。超過配額將帶來與完全用盡磁碟空間相同的不良影響。

