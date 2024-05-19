#kafka
## Kafka 提供了什麼?

Kafka非常適合作為這種大型分散式系統的傳輸層,來有效解決上述核心問題:

1. 通過**分區(Partition)和複製(Replication)機制, 實現了資料的水平擴展和高可用性**。
   
2. 使用**日誌機制**來持久化和複製數據, **保證了資料的一致性**。
   
3. **資料存儲/消費分離** - 生產者只管產生數據,消費者只管讀取處理數據, 解耦合提高系統靈活性。
   
4. 生產者只需把數據發給Kafka, 無需關心消費端的情況。消費者可以根據需要從Kafka拉取數據進行處理，這行為稱為**Pull模式**，代表的是消費者主動從Kafka取資料，而非Kafka主動推送資料， 這也**解耦了兩者之間的依賴**，提高系統靈活性。
   
   > [!tldr] 延伸
>	有Pull模式 就有 Push模式，RabbitMQ走的就是Push模式的Broker


 通過Kafka作為分散式系統之間的數據溝通管道, 解耦了數據生產者和消費者,提高了整個系統的彈性和可擴展性。

![[0_WoORkberZyCQs7GR.png]]
# 2. Kafka 出現的角色

###     1. Topic(主題)
   Kafka走的是訂閱發布機制(publish–subscribe pattern)，就像是Youtube頻道的樣子，你訂閱了某個頻道，當這頻道有更新時，系統只會通知有訂閱的人，而Topic在這例子中對應的就是頻道名稱，由Producer發布並由Consumer訂閱和消費，
   但是是Consumer主動消費，而非Kafka主動推送資料過來。

###     2. Producer(生產者)
   資料產生者，負責不斷地向Kafka的Topic發送數據,每條數據在Kafka中被視為一條消息記錄(record)。Producer可以是各種應用程序,比如監控系統、網站伺服器日誌、工廠機台即時資料等。

###     3. Consumer(消費者) 
   Consumer則是向Kafka的Topic發出訂閱請求,並拉取(pull)訂閱主題的消息記錄進行處理的角色。  Consumer可以獨立運作,也可以組成消費者群組(Consumer Group),在群組內按分區劃分消費任務。
   
###     4. Broker
簡單來說就是Kafka節點之一，Broker負責接收、儲存、同步資料，每個Broker不僅為**Consumer**提供服務,還會與其他**Broker**通訊並進行資料備份,  形成一個分散式的集群

在Kafka中把這些資料稱作『Message』

概念圖如下：

![[image_589142173211625734253276.png]]