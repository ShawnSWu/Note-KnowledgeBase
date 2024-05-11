
# 1.  一個Broker裏面長怎樣?

> [!info] 概念
> 一個Broker會管理著不同的Topic，而每個Topic都會有以下兩個概念
> 1. Partition
> 2. Replica

> 想像一家麥當勞，漢堡就相當於Broker中的資料(Message)。

**Partition** 就好比麥當勞的服務窗口。如果只有一個窗口,當顧客太多時,排隊的人就會排得很長很長。所以麥當勞會開多個服務窗口(**Partition**), 這樣顧客就可以平均分散到不同窗口排隊,加快了服務效率。

生產者就好比麥當勞的廚房,它們生產出漢堡(**Message**),並均勻地分配到不同窗口。消費者就相當於顧客,他們只需要去自己排隊的那個窗口取餐,不同窗口的顧客是並行的,誰也不會影響別人。增加窗口數量,就能提高整體的服務能力。

另一方面,有些窗口可能會因故暫時關閉維修,那麼所有的顧客都只能去其他還開著的窗口取餐了。為了應付這種情況，麥當勞會為每個窗口(**Partition**)備份幾個一模一樣的窗口(**Replica**)。
當有一個主窗口(Leader Replica)關閉時,它旁邊的備份窗口(Follower Replica)就會立即接手工作，繼續為顧客服務,這樣顧客們就不會因為某個窗口關閉而受影響了。

### 1. Partition

Partition是Kafka中實現分區和並行處理的基本單位。每個Topic會被分為一個或多個Partition,生產者發送消息時會被攤平分散到不同的Partition中,消費者則根據Partition並行地訂閱和消費數據。

有以下特點:
- **每個Partition都是一個有序、不可變的消息序列**，簡單來說是Queue的概念，所以取資料時也是First in First out，也正因如此 會需要紀錄現在Partition被消費到了哪個位置，以便於之後繼續從這個位置開始讀取Message，記錄這個位置的名詞叫做Offset(偏移量)。

	**偏移量 (Offset)**：是指Message在Topic中的位置。offset是Kafka用來記錄消息消費進度的一種機制。通常是一個單調遞增的序列號，指明了消費者在 該Partition內 已經消費到了哪個位置
  
- 一個Partition只能被一個消費者群組內的一個消費者消費。

- 合理地增加Partition數量,可以提高Kafka的吞吐量和並行能力
  
  以下是一個Topic中的Partition示意圖：
  ![[Screenshot 2024-05-12 at 2.21.58 AM.png]]

### 2. Replica

為了實現容錯性,Kafka為每個Partition維護了多個Replica副本,分布在不同的Broker上，

概念上像這樣：
![[Screenshot 2024-05-12 at 2.29.16 AM.png]]
在 **Broker 101的Partition 0** 會被備份在 **Broker 102**，**Broker 101的Partition 1** 會被備份在 **Broker 103**，

有以下2種Replica角色:

- Leader Replica: 每個Partition有且只有一個Leader,它處理來自生產者的所有寫入請求,以及來自消費者的讀取請求。
- ISR Replica(Follower): Follower是Leader的備份副本,定期從Leader複製數據,保持與Leader的數據一致。如果Leader失效,其中一個Follower會被選為新的Leader。

所有的生產請求都由Leader處理,然後**由Leader將消息複製到所有的Follower副本上**。如果Follower與Leader之間的數據發生不一致,就會被認為是過時的,需要被Leader替換掉。


> [!important] 
> 正是透過 **Partition**和**Replica**的設計,Kafka實現了水平擴展、負載均衡和容錯機制。
**Partition**是水平礦展消息處理的基礎,而**Replica**則提供了冗餘備份和自動故障轉移能力。
> 
> 透過這種方式，一個Broker本身不只紀錄自己的資料，也需要幫別的Broker備份資料，這就是為什麼有其中幾個節點掛掉後，系統本身還可以正常運作，因為其他Broker可以把責任給扛起來


# 2. ZooKeeper

**ZooKeeper的作用** ZooKeeper主要被用於執行集群管理、Leader選舉、檢測加入/離開節點等關鍵任務,保證Kafka集群的穩定。

