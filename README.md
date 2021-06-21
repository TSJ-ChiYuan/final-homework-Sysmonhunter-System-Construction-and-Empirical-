# Final-homework-Sysmonhunter System Construction and Empirical_ 107362537陳麒元/107362543林建亨

這次的作業會針對Sysmonhunter中的ATT&CK對攻擊類型的檢測與一些攻擊事件分析。

ATT&CK是安全行業的一個備受矚目的火熱概念，很多組織和廠商发布了文章闡釋各自對於它的理解，不僅向安全廠商咨詢其在這方面的研究成果，似乎也有意將其當做衡量廠商產品能力的一個維度——安全圈一時間頗有些“平生不識此概念，縱做安全也枉然”的氛圍。

當然，這個現象很正常，ATT&CK作為一項來自國外的新技術概念，安全行業理所應當對其進行研究分析。


# Sysmon介紹

Sysmon是微軟的一款免費的輕量級系統監控工具，最開始是由Sysinternals開发的，後來Sysinternals被微軟收購，現在屬於Sysinternals系列工具（帶有微軟代碼簽名）。它通過系統服務和驅動程序實現記錄進程創建，網絡連接以及文件創建時間更改的詳細信息，並把相關的信息寫入並展示在windows的日志事件里。經常有安全人員使用這款工具去記錄並分析系統進程的活動來識別惡意或者異常活動。

Sysmon安裝後分為用戶態系統服務，驅動兩部分，用戶態通過ETW(Event Tracing for Windows)實現對網絡數據記錄，通過EventLog對驅動返回的數據進行解析，驅動部分則通過進、線程，模塊的回調函數收集進程相關的信息，通過Minifilter文件過濾驅動和注冊表回調函數記錄訪問文件、注冊表的數據。

從功能上來講，Sysmon一旦安裝在系統上，在駐留系統期間，可以監視系統活動並將其記錄到Windows事件日志中。 與一般檢測工具相比，Sysmon可以執行系統活動深度監視，並記錄高級攻擊的高可信度指標，是一款優秀的HIDS、EDR的主機入侵檢測引擎。

穩定性方面超過大部分自研的驅動，功能完善，對性能影響較小，雖然功能強大但卻有很多監控盲區。若加以自研Agent與其配之，便可彌補自身監控盲區及非查詢功能等其他需求。

執行sysmon程序進行安裝，用於生成日志監測數據。

![1578991002_5e1d7d9ad07ef](https://user-images.githubusercontent.com/82754889/122735117-501c2c00-d2b1-11eb-83ec-e3406e492b84.png)

模擬攻擊環境，編寫批處理文件，執行minikatz惡意程序（可用於從內存獲取明文密碼、黃金票據和****等）。
![1578991013_5e1d7da567fdd](https://user-images.githubusercontent.com/82754889/122735350-88236f00-d2b1-11eb-899d-7ce4355778ff.png)


# Sysmonhunter分析

利用Empire工具進行模擬測試，Empire是一款內網滲透測試利器，其跨平台的特性類似於Metasploit，有豐富的模塊和接口，用戶可以自行添加模塊和功能，是針對PowerShell 利用較好的平台。
通過對終端監測的數據進行特征匹配處理，並將結果推送到Elasticsearch數據庫，查看數據的錄入情況。

![1578991099_5e1d7dfb8020f](https://user-images.githubusercontent.com/82754889/122735954-17308700-d2b2-11eb-9146-fdb6cf31576b.png)

通過分析查看dll的調用次數，分析文件是不是存在被正常程序調用的情況。

![1578991102_5e1d7dfecf170](https://user-images.githubusercontent.com/82754889/122736961-2532d780-d2b3-11eb-82f7-71aa79b0dd1b.png)
