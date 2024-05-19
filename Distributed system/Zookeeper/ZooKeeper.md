#kafka
ZooKeeper**是一個分佈式協調服務**，它提供了可靠的協調和管理功能，包括分布式應用程序中的Leader選舉。


Leader選舉機制大致如下：

1. **每個分區的副本管理**：在Kafka中，每個 Partition (分區) 都有一個或多個 Replica (副本)。ZooKeeper會監視這些 Replica (副本) 的狀態。
    
2. **選舉觸發**：當Leader失效或者新的 Partition (分區) 需要Leader時，ZooKeeper會觸發Leader選舉。這可能是由於Leader節點崩潰或者新的 Partition (分區) 被創建。
    
3. **選舉過程**：在選舉過程中，Replica (副本) 之間會通過ZooKeeper進行協調。每個 Replica (副本) 會嘗試參與選舉，它們會通過與其他副本進行通信，進行投票並確定新的Leader。ZooKeeper確保了在選舉過程中只有一個Leader被選舉出來。
    
4. **Leader選舉結果**：一旦Leader被選舉出來，ZooKeeper會通知所有參與者關於新的Leader的信息。這樣，客戶端就可以向新的Leader進行讀寫操作了。
    

這個Leader選舉機制確保了Kafka在發生故障或者需要新的Leader時能夠快速地恢復，從而保證了系統的高可用性和可靠性。

**ZooKeeper在Kakfa當主的作用** 主要被用於執行集群管理、Leader選舉、檢測加入/離開節點等關鍵任務,保證Kafka集群的穩定。
