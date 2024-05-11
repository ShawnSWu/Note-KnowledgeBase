## 一個 **Step** 代表一個”**階段”**的製造流程, 在一個 **Step** 中,會將 **Material** 送進某一個**Resource**當中處理, Step也是 **Material** 的最小可追蹤流程單位。



一個 **Step** 可以定義好的可能的發生的意外事件的原因(**[[Reason]]**) 大致上分成以下幾種類型的原因

- Loss → 數量損失原因

- Bouns → 數量增加原因

- Off-Flow→ 下線的原因

- hold reasons → 暫停的原因

一個 **Step** 也定義了Primary quantity與Secondary quantity.

_**一個Step可以被設定Pass-Through, 即代表此Step不提供任何服務, 例如暫時存放等等的**_