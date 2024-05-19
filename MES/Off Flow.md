## 意義上Off flow意味著material需要到當前Flow以外的地方被處理, 會從[[Step]]被Off flow, 


**大概會有兩種情況：**

## **1. Temporary Off-Flow:**

### 用於將Material暫時發送到另一個Flow處理 就稱為off-flow。

當Material 被 off-flow 後且處理結束時，它一定會返回到原始處理點，並保持 Dispatched 或 Processed 狀態。且至少有一個Off-Flow [[Reason]]

以下為Material被發送到Repacking Flow的樣子
![[d027ffd7-5dbf-4107-bdff-47abccccc22b.png]]

## 2. Rework

> 當一個Material被Rework,通常意味著某個步驟需要重新做

一旦Rework完成，它將返回到Material當前流程中的預定處理點 所以要事先規劃好預定處理點，且需要至少有一個Rework _Reason,_ 當Material返回到預定處理點 時，它的State將取決於先前的State,因為能被Rework的State一定是Queued or Processed. 所以一定會是其中一個

![[bdc64e51-b98c-4af4-8094-90312fcd7830.png]]