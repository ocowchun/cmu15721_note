## H. Garcia-Molina, et al., [Main Memory Database Systems: An Overview](http://15721.courses.cs.cmu.edu/spring2016/papers/garciamolina-tkde1992.pdf)

mmdb(main memory database)
drdb(disk resident database)

有很大 cache 的 drdb 跟 mmdb 還是有些差別的，例如 index,當你使用 drdb 讀取資料的時候，就算資料已經在 memory裡面了，還是要先讀取 index 確認資料所在的 block 然後才能去確認說 這個 block 已經被讀取到 memory，相較之下 mmdb 可以直接讀取資料的 address 

相較於傳統的 db, mmdb 對於 backup的要求會更高，需要更頻繁的 backup資料到硬碟，畢竟只要系統 crash，你在 memory上的全部資料就都消失了。


concurrency control
mmdb 的 transaction 會比 drdb 來得快，這表示在 lock control 上, lock的時間會縮短，相較於 drdb 會將 lock 的粒度訂在 fields 或是 row 來避免 lock 時間過長影響到其他交易, 既然 mmdb 的 lock 時間縮短了，上述避免 lock 時間過長影響其他交易的優勢就會跟著下降，mmdb 可能可以將粒度訂在 relations,甚至更極端一點整個 db(等於說 transaction 會變成 sequential 執行)，當然這樣的做法並不實際(考慮到multi core,long transaction...etc)。
mmdb 實作 lock 的機制跟 drdb 也會有很大的不同，可以考慮直接將 lock 的狀態記錄在資料上(i.e. 在每一筆資料上用一個 bit 來表示 lock與否)。


## 個人想法
mmdb 的優勢在於 write 上會比 read 來的明顯，當我所有的 transaction 都可以在 memory 完成的時候，一定會比需要將 log 寫入到 disk 來得寫，不過如何確保 transaction 的 durability 會是 mmdb 所要面臨的最大問題


