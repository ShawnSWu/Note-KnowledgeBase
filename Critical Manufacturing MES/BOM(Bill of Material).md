## 常見名稱如：物料清單、料表、配方表

### 要組裝一個[[Product]]所需的配方表, 需要透過BOM來記錄下此Product需要的Material有哪些。


### _基本上會綁定在某個[[Step]]中,意義上是  

> 在此Step需要組成某一個Product, 這個Product可以是一個新的Product也可以只是產品的一部分

## 組合的關鍵字叫做 **Assembly**

可以透過四種方式Assembly

1. **Automatic at Track-In  
    →** 當**Material**被**Track-In**到**Resource**內部後，它會自動消耗BOM中的Material來Assembly

2. **Automatic at Track-Out  
	→** 與**Automatic at Track-In**類似**, BOM**的組裝發生在**Track-Out**

3. **Explicit**   
    → 待確認是甚麼意義

4. **Explicit-Add** 
    → 待確認是甚麼意義

結合後的Material是Assembled Material，被消耗的是Consumed Material