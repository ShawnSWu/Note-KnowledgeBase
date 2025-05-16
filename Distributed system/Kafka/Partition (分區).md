#kafka
Partition是Kafka中實現分區和並行處理的基本單位。每個Topic會被分為一個或多個Partition,生產者發送消息時會被攤平分散到不同的Partition中,消費者則根據Partition並行地訂閱和消費數據。

有以下特點:
- **每個Partition都是一個有序、不可變的消息序列**，簡單來說是Queue的概念，所以取資料時也是First in First out，也正因如此 會需要紀錄現在Partition被消費到了哪個位置，以便於之後繼續從這個位置開始讀取Message，記錄這個位置的名詞叫做Offset(偏移量)。

	**偏移量 (Offset)**：是指Message在Topic中的位置。offset是Kafka用來記錄消息消費進度的一種機制。通常是一個單調遞增的序列號，指明了消費者在 該Partition內 已經消費到了哪個位置
  
- 一個Partition只能被一個消費者群組內的一個消費者消費。

- 合理地增加Partition數量,可以提高Kafka的吞吐量和並行能力
  
  以下是一個Topic中的Partition示意圖：
  ![[Screenshot 2024-05-12 at 2.21.58 AM.png]]


### 詳細概念：

1. **每個分區都有一個唯一的識別符（partition ID）**
2. **消息被寫入分區後，可以按照消息的鍵（key）進行分區，也可以使用輪詢（round-robin）策略進行分區**
3. **消息在分區內是有序的**，但不同分區之間的消息順序不做保證。