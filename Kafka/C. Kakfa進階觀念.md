
## Consumer
Consumer使用group.id屬性將自己訂閱到Consumer群組時,此時Consumer是無法指定要消費的partition的。

這是因為Kafka中的Consumer群組有以下規則:

Consumer群組保證每個分區只能分配給組內一個Consumer消費,避免重複消費
* 組內Consumer自己不能指定消費分區,分區分配由組協調者管理
* 如果Consumer數大於分區數,則會有Consumer接收不到分區數據
* 如果Consumer數小於分區數,則有分區資料沒有Consumer消費
* 所以當使用了groupId後,應用程式只能被動接收被分配的分區,而無法主動指定需消費的分區。

如果要指定分區,可以不使用groupId,而是直接訂閱對應topic的指定分區。 但這種方式就無法利用Consumer群組的負載平衡等特性了。

這在Kafka的Consumer group的設計上是一種trade off。

所以簡而言之,使用了groupId就只能被動接收分配的分區。 要主動指定分區就需要單獨訂閱。

### Consumer group內的平衡策略(Consumer groups Partition Rebalance)

* Range 指派策略 (RangeAssignor)：

> 描述： 預設的分區分配策略，盡可能平均分配一定範圍的連續分區給消費者。
> 例： 假設有 3 個消費者，而主題有 9 個分區。 RangeAssignor 將嘗試將分區 0-2 分配給消費者 A，3-5 分配給消費者 B，6-8 分配給消費者 C。 這樣，每個消費者負責一定範圍的分區，以實現負載平衡。

* Round Robin 指派策略 (RoundRobinAssignor)：

> 描述： 將分區逐一分配給消費者，循環往復。
> 例： 假設有 3 個消費者，而主題有 5 個分區。 RoundRobinAssignor 將嘗試將分區 0 分配給消費者 A，1 分配給消費者 B，2 分配給消費者 C，然後再次循環。 這樣，每個消費者依序處理一個分區，實現均勻分配。

* Sticky 策略 (StickyAssignor)：

> 描述： 盡量保持相同的分區分配給相同的消費者，在一定時間內減少分區的重新分配。
> 範例： 如果有 3 個消費者，主題有 9 個分割區，StickyAssignor 會嘗試在一定時間內保持相同的分割區分配給相同的消費者，以減少重新分配的頻率。 這對於某些場景，如避免狀態喪失，可能是有益的。

* Cooperative Sticky 策略 (CooperativeStickyAssignor):
> 描述: 在Sticky的基礎上增加了「協作再均衡」機制:
> 當有消費者下線時,
> 1.它會協調群組裡的存活消費者,讓它們提交並暫停消費至最近已提交的offset位置，
> 2.然後將下線消費者的分區安全重新分配給存活consumer，
> 3.等重新分配完成後,通知存活消費者繼續消費。
> 
> **與Sticky的區別**
> 
> Sticky僅僅追求最大化快取重複使用和最小化重新分配。
> 而Cooperative考慮到了分區重分配時可能出現的資料遺失問題,透過協作機制進行了最佳化。
> 
> 所以CooperativeSticky在Sticky的基礎上,提供了再均衡時的資料安全機制,防止重複消費和訊息遺失。


### Consumer 的offset提交策略(Auto Offset Commit Behavior)

在Kafka中，Auto Offset Commit Behavior 是指消費者是否自動提交偏移量（offsets）的行為。 偏移量是用來追蹤消費者在分區中讀取訊息的位置的標識。 在 Kafka 中，消費者可以選擇自動提交偏移量，也可以選擇手動管理偏移量。

以下是有關 Auto Offset Commit Behavior 的詳細說明：

* Auto Commit（自動提交）：

> 啟用方式： 透過設定消費者設定參數 enable.auto.commit 為 true，消費者將自動提交偏移量。
> 提交頻率： 根據配置參數 auto.commit.interval.ms，決定了自動提交的頻率。
> 作用： 每隔一段時間，消費者會自動將最新的偏移量提交到 Kafka 伺服器。 這樣，Kafka 就知道了消費者在每個分區的目前位置。

* Manual Commit（手動提交）：

> 啟用方式： 將 enable.auto.commit 設定為 false，然後透過呼叫 commitSync() 或 commitAsync() 方法手動提交偏移量。
> 手動提交時機： 消費者可以在適當的時機手動提交偏移量，例如在處理完一批訊息後。
> 作用： 手動提交使得消費者有更細粒度的控制，可以確保只有在訊息被成功處理後才提交偏移量，防止資料的重複處理。


```
At-Most-Once 和 At-Least-Once 語意：
Auto Commit： 自動提交偏移量可能導致訊息被處理一次（At-Most-Once），但也可能導致訊息被處理多次（At-Least-Once）。
Manual Commit： 手動提交偏移量可以實現更可靠的 Exactly-Once 語義，確保訊息僅被處理一次。
```


```yaml
# 啟用自動提交
enable.auto.commit=true
# 自動提交的時間間隔
auto.commit.interval.ms=1000
```

對於 Auto Offset Commit Behavior 的選擇，需要根據應用的語義和要求來決定。 如果應用程式可以容忍一定的重複處理，並且希望簡化偏移管理，可以選擇啟用自動提交。 如果需要更精細的控制，確保訊息被處理一次，那麼手動提交偏移量可能更合適。