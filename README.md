# posture-detect-using-IMU

## 引言

這篇文章是關於利用 IMU 偵測姿勢的構想（插一個諧音梗：IMU 的中文是「我是你」= I am you），會先以架構的設計開始，到後面會針對每個部分進行技術層面的介紹。

## 架構圖

### 架構圖先備知識

1. MQTT 是什麼？

   MQTT 是一種通訊協定，可將資料互相傳遞，比起現行網頁使用的 HTTP 協議更節省資源（傳遞之資料大小更小）。MQTT 在實務層面包含客戶端（client）及伺服端（server）：

   1. 客戶端（client）：不論是傳送資訊或接收資訊都稱為客戶端。
   2. 伺服端（server）：伺服端是各客戶端之間的橋樑，舉個例子：`A 客戶端` 欲發送資料給 `B 客戶端`，則實務上的傳送邏輯是 `A 客戶端` 發送資料到伺服端，`B 客戶端` 就會收到來自伺服端發送的 `A 客戶端` 資料。而伺服端的機器在 MQTT 協議中又稱為 `MQTT Broken`。

   ```mermaid
   graph LR
     A[(伺服端)]
     B[A 客戶端] --實務層面的發送資料--> A
     A --實務層面的接收資料--> C[B 客戶端]
     B -.邏輯層面的傳送資料.-> C
   ```
   
2. 有兩種形式的感測器，可偵測受測者的姿勢：
   1. 使用含有 Wi-Fi 晶片的單晶片（e.g. ESP32, D1 mini, etc.）及加速度計（e.g. GY-61, etc.）製作感測器。
   2. 利用手機作為感測器，利用內建的加速度計，透過網頁方式傳送相關數值。

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

## MQTT Broken（伺服端）之建置及測試

### 安裝前的先備知識

1. Terminal（終端機）：是一個 Mac 內建的應用程式，可在輸入指令換行後執行指令，是開發者的好朋友(?

### 利用 MacOS 建置 MQTT Broken

1. 安裝 Homebrew（一個套件管理工具），以方便安裝 MQTT Broken。請在 Terminal 中輸入以下指令，若視窗要求輸入密碼，請輸入電腦之開機密碼，輸入時視窗不會有任何變化，輸入後按下 Enter 即可：
   ```shell
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
2. 透過 Homebrew 安裝 MQTT Broken。請在