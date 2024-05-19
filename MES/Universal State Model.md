## 每個在CM的Object都適用**Universal State Model, 主要有**

- **Created**

- **Active**

- **Effective**

- **Terminated**

- **Frozen**

**這幾種狀態, 且分成兩種Object**

- **Non-Versioned objects** - do not require change control.
    也稱為Normal objects, 不受更改控制。它們在「**Active**」狀態下創建，一旦終止，它們就會進入「**Terminated**」 狀態。
    一個被**Terminated** object只有在尚未從資料庫中清除時才能夠被unterminated 。

- **Versioned objects** - require change control

## 一個Object在CM中由兩部分組成：

- **Global Component**（全局組件）：這部分對所有版本的Object都適用，並且在首次創建Object時建立，可以在不經過變更控制流程的情況下進行更改。

- **Versioned Portion**（版本化部分）：這部分僅指特定版本。

**版本零始終代表當前有效版本(Effective version), 或者如果沒有有效版本(Effective version)，則引用最高的非終止版本(Non-terminated version)，  
**

**如何修改Object?　　 透過[[Change set]]**