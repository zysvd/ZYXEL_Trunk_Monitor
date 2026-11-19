# 🌐 ZYXEL Trunk Monitor

<!-- 專案狀態與規格標籤 (Badges)，彰顯專案的專業度與維護狀態 -->
![Version](https://img.shields.io/badge/Version-v1.0.6-00f0ff?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.8+-00ff9d?style=for-the-badge&logo=python&logoColor=black)
![Platform](https://img.shields.io/badge/Platform-Windows-blue?style=for-the-badge&logo=windows)
![Theme](https://img.shields.io/badge/Theme-Dark%20Cyber%20%2F%20NOC-121820?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-gray?style=for-the-badge)

**ZYXEL Trunk Monitor** 是一套專為 Zyxel 軟硬體驗證部門（SVD/QA）量身打造的 **SNMPv2c 多通道 Trunk 流量遙測監控與多連線負載壓測產生器**。

在網路設備驗證中，測試多 WAN 負載平衡（Active-Active）與備援容錯切換（Active-Passive）常需要頻繁切換介面。本系統將**「SNMP 流量實時監控」**與**「高速多執行緒 UDP 5-Tuple 發包引擎」**整合為單一綠色免安裝應用程式，並套用極具質感的「深海科技 NOC 儀表板」深色主題。

---

## 📌 核心功能 (Key Features)

* 🔍 **SNMP Walk 智慧巡覽與精準探測**：全面引進標準 SNMP GetNext (`0xa1`) 巡覽技術，自動遞迴遍歷 `ifName` 與 `ifDescr` 關聯。不論 Zyxel 設備實體 Port Index 如何跳號或非連續，皆能在 0.5 秒內精準識別並自動綁定 `名稱 (Index X)`。
* 🔄 **一鍵全自動熱覆蓋更新（Hot-Swap Auto Update）**：點選同意更新後，程式會自動在背景安全下載 `.exe`，並透過無窗中繼腳本在舊版安全退出後，自動完成無感覆蓋與重新啟動。
* 🔒 **零本機路徑洩露保護（Zero Path Leakage）**：下載與更新過程中，所有對話框、狀態列 HUD 與執行日誌皆**完全隱藏**任何本機暫存路徑（如 `C:\Users\...` 或 `Temp` 目錄），全面防範測試機台隱私洩露。
* 🛡️ **自訂 Active / Passive 通道屬性**：支援動態管理與映射多組自訂介面，明確指派 `Active (主動分流)` 或 `Passive (熱備援)` 成員，圖表自動以實線（Active）與虛線（Passive）進行視覺區隔。
* 📊 **智慧負載分流診斷與 HUD 狀態列**：動態計算多路 Active 通道間的即時與累計分流比（如 `1.00:1.02`）。狀態欄具備 NOC Telemetry Badge 智慧診斷功能，能自動感知 `Failover (備援切換)` 或 `Anomaly (異常分流)` 狀態。
* 🚀 **非同步背景更新檢查與 4 秒安全逾時**：重構啟動載入邏輯，更新檢查完全託管於背景執行緒，主 GUI 視窗 0.1 秒極速秒開。針對 SVD 離線測試網段，連線會在 4 秒內自動超時釋放，避免程式卡死留白。
* ⚡ **高精度 UDP 壓測發包器**：支援 IP 範圍（如 `192.168.1.10-30`）與 CIDR 網段。搭載 Token Bucket 演算法進行 Mbps 級精準流控。

---

## 📂 專案檔案結構 (Directory Structure)

在進行本地執行或打包前，請確保您的目錄結構配置如下：

```text
ZYXEL_Trunk_Monitor/
│
├── trunk_monitor.py         # 主程式碼
├── ZYXEL.ico                # EXE 實體檔案外觀縮圖 (必須是標準 ICO 格式)
├── trunk_monitor.db         # SQLite 本地歷史資料庫 (執行後自動生成)
└── assets/
    └── ZYXEL.png            # 軟體執行期 (視窗左上角/工作列) 顯示之高解析度圖示

📅 版本變更歷史 (Version History)
🔍 v1.0.5 (2026-11-19) - [SNMP Walk & Dual-Engine Edition]
修復：補齊 TimeTicks (Tag 0x43)、Counter32 等 ASN.1 型態解析，完美解決舊版在 USG FLEX 因預檢 sysUpTime 格式不合導致的「連線誤判」問題。

新增：導入標準 SNMP Walk (GetNext) 智慧巡覽引擎，自動遞迴走訪 ifName 與 ifDescr，秒速取得最完整的埠對應關係。

新增：介面選單採用「名稱 (Index X)」防碰撞命名法，徹底解決多埠同名（如全數回傳 GigabitEthernet）導致的字典覆蓋與掃描漏洞。

修復：修正 GUI 兩處 padding 初始化缺失導致的啟動 SyntaxError。

🔄 v1.0.4 (2026-11-18) - [NOC Seamless Update Edition]
新增：一鍵自體熱覆蓋更新重啟機制，使用者點選後全自動升級，無需手動複製替換。

新增：安全資料裝甲，更新下載期間全程隱藏本機實體路徑與暫存檔名，保護測試機隱私。

優化：細緻化 NOC 智慧狀態欄診斷，提高備援接管（Failover）與溢出異常（Anomaly）之判定精準度。

🚀 v1.0.3 (2026-11-18) - [NOC Threading Edition]
優化：重構線上更新確認機制，完全移入非同步背景執行緒（Background Threading），主 GUI 視窗載入時間縮短至 0.1 秒。

新增：數位 HUD 狀態列進度感知，啟動時即時點亮更新檢查進度。

優化：內建 4 秒安全網路逾時（Network Timeout），避免在離線（Off-line）SVD 封閉測試環境中因網路不通而導致程式卡死。

🛡️ v1.0.2 (2026-11-15) - [Trunk Role Edition]
新增：動態通道角色設定（Active 主動分流 / Passive 熱備援）。

新增：動態 Active 多路等價分流比（Multi-Active Ratio）實時計算。

新增：Matplotlib 圖表視覺與角色連動（Active 實線，Passive 虛線）。

新增：NOC 狀態列「智慧備援切換與異常診斷演算法」（支援 Failover 與 Anomaly 狀態自動警示）。

🎨 v1.0.1 (2026-11-10) - [Branding & UX Edition]
新增：自訂動態介面管理（支援 Treeview 介面隨時增刪與 SNMP walk 映射）。

新增：全域高科技 dark-theme（NOC 黑色科技風格）與 Zyxel 紅色視窗圖示。

新增：大量封包產生器與 Token Bucket Mbps 級速率限制器。