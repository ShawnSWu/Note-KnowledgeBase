## **就是製造產品的原料**

Represents any raw material, inventory or work-in-progress (wafers, dies, modules, printed circuit boards, capacitors, etc.).

**All Materials in the system require a unique system wide identifier (ID).**

Material有兩個數量單位(Quantities):
1. Primary quantity
2. Secondary quantity. (**目前還不確定Second的意義是甚麼**)

這兩個數量的單位由Material當前所在的Step定義。由於Material可以有[[Sub-Material]]

### Material狀態follow以下流程. 粗線是傳統的流程

![[40b91a67-3706-43cd-bfe8-68896326efd3.png]]