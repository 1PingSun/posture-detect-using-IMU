# posture-detect-using-IMU

## 引言

這篇文章是關於利用 IMU 偵測姿勢的構想，會先以架構的設計開始，到後面會針對每個部分進行技術層面的介紹。

## 架構圖

### 

1. IMU 感測器：安裝於受測者身上，

```mermaid
graph LR
  A[受測者]
  A --> B[/背部 IMU 感測器/]
  A --> C[/肩部 IMU 感測器/]
  A --> D[/頭部 IMU 感測器/]
  E[(MQTT Broken（MQTT 伺服端）)]
  B --透過 MQTT 協議傳送--> E
  C --透過 MQTT 協議傳送--> E
  D --透過 MQTT 協議傳送--> E
  E --透過 MQTT 協議傳送--> F[計算與監控端]
```
