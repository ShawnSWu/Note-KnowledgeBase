
## 意義上來說是”檢查清單”，用來在某些情況下檢查是否完成了哪些事

### **Checklist**可以手動或自動觸發的動作。

有以下配置可以設定

- **執行順序**: 項目可以是 **Sequential**（按順序執行）或 **Floating**（隨時執行，自動動作除外）。  

- **必要性**: 項目可以是 **Optional**（可選，自動動作除外）或 **Mandatory**（必須執行）。

- **執行模式**: **Checklist** 可以是 
    
    1. **Immediate**（一次性執行完畢）
    2. **Long Running**（長時間執行）。


Checklist建立後，可以透過SmartTable綁定觸發時機,可以跟以下兩個物件綁定

1. **[[Material]]**

2. **[[Resource]]**

當條件滿足時(例如Track-In, Track-out, Dispatch)時即會自動跳出Checklist.  
即代表此階段需要檢查是否完成了清單上的任務