## **廣義上來說，Resource代表任何參與處理或儲存Material的實體，意義上來說是機台設備，**


**<font color="#F7A004">例如麵包工場的烤箱,洗衣工廠的洗衣機</font>**
### **Resource可以有很多種類型**:

1.  **Component** - 元件; **可以有很多multiple parents(只有Component可以有)**, 且可被共享(畢竟已經被做成元件,很多設備可能都能用) 如果是Component就代表他不提供Service
2. **Consumable Feed** -耗材設備; 用於為其他Resource提供消耗品的設備

3. **Instrument** - 測量設備

4. **Load Port** - 用來提供的輸入輸出的設備

5. **Process** - 用來處理Material的設備
   
6. **Storage** - 用來保存Material的設備
   
7. **Transport** - 用來運輸的設備
   
8. **Line** - 代表這個Resource是由幾個sub-Resource組成的
   
    **這些Sub-Resource的type都是process, 概念上有點像一個Resource是由幾個Sub-Resource線性組成的**

## **一個Resource能**提供不同的[[Service]]

例如:
* 烤箱 (Resource) 能提供烘烤服務 (Service)
* 洗衣機(Resource) 能提供洗衣 (Service) 與烘乾 (Service) 兩種服務

> **每個Resource都有自己的極限乘載量(capacity ), 例如一台洗衣機一次就只能洗20Kg**