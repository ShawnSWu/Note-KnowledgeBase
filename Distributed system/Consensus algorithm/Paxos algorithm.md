<<<<<<< HEAD
作為分佈式系統中達成一致性的經典算法之一,Paxos 背後所包含的數學理論和嚴密的證明過程,非常複雜,絕大多數現有的分佈式系統都或多或少地參考和借鑑了 Paxos 提出的核心思想, 但由於學術部分太複雜,但做為系統架構師,對Paxos 算法至少有個粗淺的理解還是必要的。
=======
作為分佈式系統中達成一致性的經典算法之一,Paxos 背後所包含的數學理論和嚴密的證明過程,非常複雜,絕大多數現有的分佈式系統都或多或少地參考和借鑑了 Paxos 提出的核心思想, 但由於學術部分太複雜,但做為系統架構師,對Paxos 算法至少有個粗淺的理解還是必要的。


Paxos 是一種用於達成分佈式系統中數據一致性的協議。它是由 Leslie Lamport 提出,被廣泛應用於諸如 Chubby、Zookeeper 等分佈式系統中。Paxos 算法由兩個階段組成:

### 第一階段 - Prepare 請求

1. **選舉 Proposer**
   一個 Acceptor 節點自我提名為 Proposer,並選擇一個 Proposal 編號 N (通常是一個遞增的數字)。

2. **發送 Prepare 請求**
   Proposer 向所有 Acceptor 節點發送帶有編號 N 的 Prepare 請求。
   
3. **Acceptor 回覆 Promise**
   每個 Acceptor 收到 Prepare 請求後,會檢查編號 N 是否比之前答應的任何 Proposal 編號都要大。如果是,則回覆一個 Promise 應答,其中包含了自己接受的最大 Proposal 編號,以及對應的值(如果有的話)。
   
4. **處理 Promise 響應**：
   如果 Proposer 收到了來自多數 Acceptor 的 Promise 應答,它就可以進入第二階段。否則,它需要重新開始第一階段,並選擇一個更大的 Proposal 編號。
   
   
5. **發送 Accept 請求**：
   Proposer 選擇一個值(可能來自 Promise 應答),並將該值和 Proposal 編號 N 發送給所有 Acceptor,請求它們接受該 Proposal。
   
   
6. **Acceptor 接受 Proposal**：
   如果一個 Acceptor 收到一個 Proposal 編號 N,且之前未應答過更大的編號,它就會接受該 Proposal,並向所有節點發送一個帶有該 Proposal 值的 Accepted 響應。
   
   
7. **處理 Accepted 響應**：
   一旦 Proposer 收到來自多數 Acceptor 的 Accepted 響應,該 Proposal 就被正式選定。Proposer 可以將該值應用到系統的狀態中。
   
   
8. **處理失敗情況**：
   如果 Proposer 在第二階段沒有收到多數 Accepted 響應,它可以重新從第一階段開始,選擇一個更大的 Proposal 編號。
   
   
通過上述過程,Paxos 算法保證了在任何時刻,系統中最多只有一個值被選定。這樣就實現了分佈式系統中數據的一致性。
>>>>>>> 9b7e7d688b9283d5bf935d2fbceca14a2671a8fe
