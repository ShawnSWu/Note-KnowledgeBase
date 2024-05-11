## 處理Materiial的固定過程, **Flow**是由**Step**或其他**Flow**組成的, 也就是說


* ***Flow 的內容可以是 ⇒ [[Step]]

* ***Flow 的內容也可以是⇒ Flow(子Flow)**

### 主要有4種Flow:

- **Sequential Flow Step →** 必須照著順序執行

- **Non Sequential flow Step** → 可以用任何順序執行

- **Alternate Flow Material →** 可以選擇任何可能的可用路徑

- **Line Flow**
    用在某個”Line Step”裡的Flow, 代表著一個Step中包含一組Step才能處理好所需要的需求, 這個Step也稱作”Line Step”

![[cef6342a-7ff7-406a-aa17-51b9534fa18a.png]]


**Alternate Flow 對於其他shop floor 場景非常有用，例如，當同一生產流程的高產量和低產量路徑有不同的方法時**