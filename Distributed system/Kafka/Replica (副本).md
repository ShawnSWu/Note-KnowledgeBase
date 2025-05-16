#kafka 
為了實現容錯性, Kafka在建立topic時，可以設定 Replica 的數量，中文翻為『副本數 (Replica factor)』，
實質上的意義為『包含自己』共有幾份相同的資料，例如 Replica factor設為3，代表同一份資料會有3份，以避免當網路發生問題或broker down時，資料會因此消失

概念上像這樣：
![[Screenshot 2024-05-12 at 2.29.16 AM.png]]
在 **Broker 101的Partition 0** 會被備份一份在 **Broker 102**，
**Broker 101的Partition 1** 會被備份一份在 **Broker 103**


有以下2種Replica角色:

- **Leader Replica**: 透過選舉機制產生，每個Partition有且只有一個Leader,它處理來自生產者的所有寫入請求,以及來自消費者的讀取請求。
- **ISR (In-Sync Replicas)**:  也稱作Follower，是Leader的備份副本,定期從Leader複製數據,保持與Leader的數據一致。如果Leader失效,其中一個Follower會被選為新的Leader。

#### 為何需要從每個Replica選出Leader?

透過『**選舉機制**』選出Leader的目的在於協調消息的讀寫操作。當用戶寫入消息(Message)時，它們會被寫入Leader，然後Leader會負責將消息複製到其他Followers。而當用戶讀取消息時，它們也會從Leader那裡讀取。

這種設計帶來了幾個好處：

1. **單一讀取點**：有了Leader，用戶可以直接向它發送讀取請求，無需關心其他副本的位置，只需要知道誰是Leader，這樣可以簡化用戶端的實現和管理。
    
2. **一致性保證**：由於所有的寫入都發生在Leader上，因此只需確保Leader的一致性即可。Followers只需從Leader同步消息，這樣可以簡化一致性控制的實現。
    
3. **高可用性**：即使Leader節點失效，系統仍然可以繼續運行，Kafka會自動選舉一個新的Leader Replica，這樣可以確保系統的高可用性。


### 那麼 選舉機制是怎麼做到的？

Kafka使用的是基於 [[ZooKeeper]] **的Leader選舉機制來確定分區中的Leader。

ZooKeeper**是一個分佈式協調服務**，它並非為了Kafka而生，只是剛好這個工具提供了以下幾種功能，很適合作為Kafka中每個節點的管理：

1. **配置管理**：ZooKeeper可用於存儲和管理分佈式系統的配置信息，並確保配置的一致性和可用性。
2. **命名服務**：ZooKeeper提供了分佈式命名服務，用於註冊、查詢和管理分佈式系統中的節點。
3. **集群成員身份**：ZooKeeper可以用於管理和追蹤分佈式系統中的節點和成員身份，例如檢測節點的加入和離開。
4. **分布式鎖**：ZooKeeper提供了分佈式鎖機制，用於確保在分佈式系統中的資源訪問的互斥性和同步性。
5. **通知機制**：ZooKeeper可以向客戶端發送通知，以通知它們有關系統狀態的更改。


ZooKeeper通常被用於管理和協調分佈式系統中的各種資源，包括Hadoop、Kafka、HBase等。

ZooKeeper作為一個關鍵的分佈式協調服務，在分佈式系統中發揮著至關重要的作用，確保系統的一致性、可靠性和高效性。


> [!bug] 注意
> Zookeeper畢竟是外部服務，且需要單獨部署和管理，並且在一些高吞吐量的場景下性能可能不夠滿足需求，且傳出有安全性問題
> 
> 正因如此 Zookeeper 正在逐漸被淘汰，取而代之的是 [[Raft algorithom]]，
> 然而，這並不意味著ZooKeeper完全被淘汰，因為在某些情況下，仍然可能選擇使用ZooKeeper，特別是當一些特定功能或需求需要使用ZooKeeper時