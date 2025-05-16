<<<<<<< HEAD
不同於Paxos, Raft使用Leader(領導者)、Follower(追隨者)等更直觀的術語
並且簡化了複雜的流程,以下方便說明,使用三種角色代表圖
![[rule.png]]

## Leader Election 

 1. **初始化狀態**： 所有節點初始狀態都是Follower, 沒有Leader存在時,所有節點不會收到心跳消息
   ![[Pasted image 20240517101007.png]]

 
 2. **超時觸發選舉**： - 任何一個Follower節點在一定時間內沒有收到來自Leader的心跳訊號（Heartbeat），它會自己轉變為Candidate
 
  
  
 3. **發送投票請求**： - Candidate節點向其他所有節點發送RequestVote請求，並附帶其當前的日誌索引和任期號。 
  
  
  
 4. **接受投票**： - 其他節點（Followers）收到RequestVote請求後，會根據Candidate的日誌索引和任期號決定是否投票。如果該Candidate的日誌比自己更新，且尚未投票給其他Candidate，則會投票給該Candidate。 
  
  
  
 5. **當選為Leader**： - Candidate節點如果獲得多數節點的投票（超過半數），則成為Leader。當選後，它會立即向其他節點發送心跳訊號，通知其成為新的Leader。 
  
  
  
 6. **處理失敗情況**： - 如果Candidate在一定時間內沒有獲得足夠的投票，它會重新進入Follower狀態，並等待下一次選舉超時再次發起選舉。
  
  
  
## 初始狀態


 在Raft中有兩種timeout設定
		a. Election timeout.
			150ms and 300ms.
		b. se








- 
- 選舉超時導致發起選舉
    - 如果一個Follower節點在選舉超時時間內沒有收到任何節點的消息(心跳或日誌條目)
    - 該節點就會進入Candidate狀態,發起新一輪的領導者選舉
- 多個節點同時成為Candidate
    - 有可能發生所有節點在同一時間內都因為選舉超時而進入Candidate狀態
    - 可能原因包括:集群啟動、網絡分區、領導者突然宕機、選舉定時器不一致等
- 競爭領導權
    - 多個Candidate同時向其他節點發送請求投票消息
    - 消息包含該節點的任期號和最後日誌索引號
    - 其他節點會先檢查發送請求節點的日誌是否至少與自己的一致,如果不落後則投贊成票
- 選舉完成
    - 一個Candidate節點獲得超過集群半數節點的贊成票後,就能成功當選為Leader
    - Leader立即向所有節點發送心跳消息,並附帶從上一任期已提交日誌開始的日誌條目
    - 其他節點確認Leader身份,重置選舉超時計時器,進入正常運行狀態
- 日誌複製
    - 客戶端向Leader發送指令
    - Leader先臨時添加指令到本地日誌
    - 並行發送日誌條目給其他節點
    - 超過半數節點成功追加後,Leader提交並通知全部節點
- 領導權轉移
    - 如果Leader宕機或與過半節點失去聯繫
    - 其他節點在選舉超時後會重新進入Candidate狀態觸發新一輪選舉
=======
不同於Paxos，Raft使用Leader(領導者)、Follower(追隨者)等更直觀的術語
並且簡化了複雜的流程，主要還是有三個流程

1. **Leader Election 領導者選舉**
2. **Log Replication 日誌複製**
3. **Log Re日誌一致性**

在Raft中，有以下三個角色代表不同節點
1. Follower
2. Candidate
3. Leader
![[Pasted image 20240518180617.png]]

## 1. Leader Election

1. **初始化狀態**： - 系統中的所有節點開始時都處於Follower狀態。 
	   ![[Pasted image 20240518180913.png]]
	   
	2. **超時觸發選舉**： - 每個Follower節點在一定時間內沒有收到來自Leader的心跳訊號(Heartbeat)，它會轉變為Candidate並發起選舉，
	   
	   以下舉例為節點初始化時的狀態(沒任何Leader 傳 heaerbeat)，每個節點的進度條則代表沒收到心跳訊號的時間
	   ![[leader election.gif]]

	3. **發送投票請求**： - Candidate節點向其他所有節點發送RequestVote請求，並附帶其當前的日誌索引和任期號。 
	   ![[ezgif-4-476e38a096.gif]]

	4. **接受投票**： - 其他節點（Followers）收到RequestVote請求後，會根據Candidate的日誌索引和任期號決定是否投票
	   
	   **如果該Candidate的日誌比自己更新，且尚未投票給其他Candidate，則會投票給該Candidate(這裡算是算法的核心之一)。
	   
	   像上面gif顯示的結尾部分，Candidate變成Leader，
	   
	1. **當選為Leader**： - Candidate節點如果獲得多數節點的投票（超過半數），則成為Leader。
	   當選後，它會立即向其他節點發送心跳訊號，通知其成為新的Leader，以下為正常Leader持續傳送心跳訊號的樣子
	   ![[ezgif-4-dc37de552c.gif]]
	   
	6. **處理失敗情況**： - 如果Candidate在一定時間內沒有獲得足夠的投票，它會重新進入Follower狀態，並等待下一次選舉超時再次發起選舉。
	   
	   以下是當Leader掛掉 不再傳送心跳訊號時時，各節點會再重新選出新Leader的選舉機制（意義上就是回到 **1.初始化狀態** 開始)
	   
	   ![[fails 1.gif]]
#### 以下影片為多節點觸發投票機制的情況	
	![[normal.mp4]]

## 2. Log Replication 日誌複製

當有了領導者之後，一旦系統有發生改變時，我們就需要將系統的所有變更複製到所有節點

1. **日誌條目追加**：
   當 Leader 接收到一個新的數據變更請求(例如,增加一筆資料),它會將該變更記錄為一個新的日誌條目,並將其追加到其本地日誌中。
   
   以下例子 (綠色節點=Client) 是Client 傳一筆資料 = 5 的資料給 Leader 
    ![[ezgif-5-faede10599.gif]]
   
2. **發送 AppendEntries 請求**：
   Leader 會並行地將新的日誌條目發送給所有 Follower 節點,通過發送 AppendEntries RPC 請求。每個 Follower 在收到 AppendEntries 請求後,會將新的日誌條目附加到其本地日誌中
![[ezgif-5-f7ecb4352b.gif]]

3. **回覆成功或失敗**：
   Follower 處理完 AppendEntries 請求後,會向 Leader 回覆是否成功附加了新的日誌條目。
   ![[ezgif-7-ccb297247a.gif]]
   如果大多數(>50%)Follower 成功複製了日誌條目，則該條目被認為已經被認同了，並將回應傳送給Client端，狀態為已提交(Committed)。

4. **應用日誌條目**：
   一旦某個日誌條目被提交,Leader 會通知所有 Follower 應用該條目到狀態機器(State Machine)中。這樣,所有節點的數據狀態就保持一致。
      ![[ezgif-7-770968f165.gif]]
      
      
5. **處理網絡分區**：
   ### Raft 甚至可以在網路分區時保持一致性
   如果在日誌複製過程中出現網絡分區,導致 Leader 無法與部分 Follower 通信,則 Leader 會無限期等待這些 Follower 重新上線。一旦重新建立連接,Leader 會自動將它們的日誌複製過來。
   
   #### 讓我們新增一個分割區來將 A 和 B 與 C、D 和 E 分開例子：
   由於我們的分裂，我們現在有兩位不同任期的Leader
   ![[ezgif-7-0934a7bc3f.gif]]
   
   #### 我們新增另一個客戶端並嘗試更新兩個Leader的資料
   ![[Pasted image 20240519215407.png]]

   #### 各分區開始各做各的
   
   圖中下面的一個Client端將嘗試將節點 B 的值設為『3』，但由於節點 B 因為節點數量不夠多而無法使選舉機制成功，所以無法複製資料到多數節點，因此其日誌條目一直保持未提交狀態   
   (專注看下面的分區)
   ![[ezgif-7-02da4871a3.gif]]
   
   再來看上面的Client端分區
   Client 將嘗試將節點E的值設為“8”，因為結點多到可以使選舉機制成功，所以其他Follwer也會更新
   
   (專注看上面的分區)
   ![[ezgif-7-30b4258bbb.gif]]
   

   #### 當我們修復網路分割區時

   節點 B 將會看到更新的『選舉任期』，所以無條件接受別人的資料版本，並接受新領導者的日誌，接著，我們的日誌在整個叢集中就變成是一致的了。
	![[ezgif-7-b8b748af19.gif]]


通過以上過程,Raft 確保了在任何時候,大多數節點的日誌都是完全一致的。這樣就保證了系統的數據一致性和容錯性。
>>>>>>> 9b7e7d688b9283d5bf935d2fbceca14a2671a8fe
