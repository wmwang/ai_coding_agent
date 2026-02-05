# AI 虛擬團隊系統 - 使用者體驗指南 (User Experience Guide)

> **"您不再是一個人在寫 Code，您是一個 24/7 開發團隊的指揮官。"**

本文件旨在描繪 **雙 Agent 架構 (Dual-Agent System)** 完成後的實際操作體驗。請透過以下情境，評估這套系統是否符合您對「強大虛擬團隊」的想像。

---

## 1. 核心體驗概觀 (The Core Experience)

在這個系統中，您擁有兩種身份的切換：

1.  **Commander (指揮官)**: 使用 **Cline**。處理高難度決策、架構設計、指派任務。
2.  **Observer (觀察者)**: 看著後端 **Virtual Team (Custom Agent)** 在您睡覺時自動完成那些繁瑣的遷移、測試與重構工作。

---

## 2. 情境一：指揮官的日常 (The Commander Mode)
**場景**: 週一早上，您收到一個需求，要將 VB6 的舊模組 `modInvoices.bas` 遷移到 Java，並整合到新的 Spring Boot 專案。

### Step 1: 戰略分析 (Analysis)
您打開 VS Code，喚醒 Cline。注意，您不再需要貼上整段 Code 問它怎麼改，您直接下達戰略指令。

> **User (You)**: "Cline，幫我分析 `modInvoices.bas` 這個模組。我想知道它的相依性多複雜？如果遷移到 Java 有什麼風險？"

> [!NOTE]
> **幕後揭密：Cline 怎麼知道要呼叫 MCP？**
> 這不是魔法，而是 **LLM 的工具呼叫 (Function Calling)** 能力。
> 1. 當 Gateway 連接時，它會告訴 Client：「我有一個工具叫 `architect_agent.analyze_dependency`，功能是 **『分析 VB6 模組的相依性與遷移風險』**」。
> 2. 當您問「分析這個模組...風險？」時，Claude (Cline 的大腦) 會自動將您的意圖與工具描述進行匹配，進而決定呼叫此工具。
> 3. 我們更可以透過 `.clinerules` 設定檔強制規定：「遇到遷移問題，優先諮詢架構師 Agent」。

**Cline (Commander Interface)**:
```text
[System] 連接 MCP Gateway... 成功。
[System] 識別可用工具: VB6 Parser, Graph Database, Git Ops.

正在呼叫 `architect_agent.analyze_dependency("modInvoices.bas")`...

分析報告如下：
1. 資料庫依賴: 讀取了 5 張 Table，其中 `TBL_INV_LOG` 沒有主鍵。
2. 外部呼叫: 呼叫了 `Global.dll` (這部分無法直接自動轉，需要人工介入)。
3. 複雜度評分: High (Cyclomatic Complexity > 50)。

建議: 先將 `Global.dll` 封裝成 Java Interface，再進行邏輯遷移。
```

### Step 2: 指派任務 (Dispatch)
您看過報告，決定執行。

> **User (You)**: "好，這個策略可行。啟動 **'Alpha Squad'** (後端團隊)，幫我把 `modInvoices.bas` 的邏輯轉換成 Java Service。先別管 DLL，用 Mock 取代。任務代號 #MIG-101。"

**Cline (Commander Interface)**:
```text
收到。正在建立任務 #MIG-101...
[Dispatcher] 呼叫 `team_manager.create_mission(target="modInvoices", goal="convert_to_java_mock_dll")`...

任務已啟動！
- 指派 PM: Agent-PM-01 (負責拆解 Story)
- 指派 Dev: Agent-Dev-Java (負責寫 Code)
- 指派 QA: Agent-QA-Auto (負責產生 Unit Test)

您現在可以繼續處理別的工作，或去喝杯咖啡。我也會隨時監控進度。
```

---

## 3. 情境二：夜間的自動化工廠 (The Night Shift)
**場景**: 您下班了。但您的虛擬團隊才剛開始工作。這是 **Custom Agent (LangGraph/CrewAI)** 在背景運作的時刻。

### 20:00 - PM Agent 規劃
*   PM Agent 讀取任務 #MIG-101，將其拆解為 3 個子任務：
    1.  建立 `InvoiceService.java` 介面。
    2.  實作 `calculateTax()` 邏輯。
    3.  建立 `GlobalDllMock.java`。

### 02:00 - Dev Agent 執行
*   Dev Agent 透過 MCP `git.checkout` 拉出新分支 `feature/mig-101`。
*   它呼叫 MCP `vb_parser` 讀取 VB6 邏輯，一行行翻譯成 Java。
*   **遇到編譯錯誤！** Dev Agent 自己發現 `ArrayList` 沒 import。
*   **自動修正**: Dev Agent 呼叫 MCP `java_linter`，發現錯誤，自動修正程式碼。

### 04:00 - QA Agent 驗證
*   Dev Agent 提交 Code。
*   QA Agent 醒來，掃描變更，自動生成 `InvoiceServiceTest.java`。
*   執行 JUnit 測試... **通過 (Green)**。

### 06:00 - Architect Agent 審查
*   Architect Agent (扮演 Tech Lead) 進行 Code Review。
*   發現 Dev Agent 雖然邏輯對了，但變數命名不符合公司規範 (`inv_log` 應該是 `invoiceLog`)。
*   **自動退回**: Architect Agent 在 PR 留言，把任務退回給 Dev Agent。
*   Dev Agent 修正命名，再次提交。

---

## 4. 情境三：晨間驗收 (The Morning Review)
**場景**: 週二早上，您回到電腦前。

> **User (You)**: "Cline，回報 #MIG-101 的進度。"

**Cline**:
```text
報告指揮官：
任務 #MIG-101 狀態: 待合併 (Ready to Merge)。

執行摘要:
1. 已建立 3 個 Java 檔案。
2. 測試覆蓋率: 85%。
3. 經歷過 1 次 Code Review (由 Architect Agent 自動執行)，已修正命名風格問題。

PR連結: [PR-204](http://github/repo/pull/204)
您要現在進行最後人工審核嗎？
```

您點開 PR，發現程式碼整齊、測試通過，只要按一個 "Approve" 按鈕。

---

## 為什麼這個體驗吸引人？

1.  **不是「問答」，是「指揮」**: 您不需要陪著 AI 一行一行寫。您是下達「戰略目標」，AI 團隊負責「戰術執行」。
2.  **感覺真的有團隊**: 您會看到 Log 裡有 PM 在拆票、Dev 在修 Bug、QA 在寫測試。雖然都是 AI，但那種**分工合作**的感覺非常真實。
3.  **MCP 的魔力**: 
    *   以前：您要手動把 VB6 Code 貼給 ChatGPT。
    *   現在：Agent 自己透過 MCP 去硬碟裡翻找、讀取、分析。**它比您更了解整個專案的結構**。

---

### 您覺得這樣的協作模式夠吸引人嗎？
如果這個 **"Command -> Autonomous Work -> Review"** 的流程符合您的期待，我們就可以開始構建那個核心的 MCP Gateway 與虛擬團隊邏輯了。
