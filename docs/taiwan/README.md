# 臺灣教材版｜工作紀錄入口

> 本目錄是 jamesyyl/ChinaTextbookStudyFree 的臺灣教材適配工作區。沿用原專案的網站與題型，不另造學習平台。
>
> **單一事實原則**：程式碼 / 實際輸出 / 測試證據優先；文件不得把「研究過」、「打算做」寫成「已完成」。

## 從這裡開始

1. [CURRENT_STATE.md](CURRENT_STATE.md) — 當前唯一進度摘要：基準、已知事實、未驗證、阻塞、下一步。
2. [IMPLEMENTATION_TODO.md](IMPLEMENTATION_TODO.md) — 唯一權威 TODO：工作 ID、順序、依賴、狀態、驗收。
3. [CONTENT_CONTRACT.md](CONTENT_CONTRACT.md) — 產生 AI JSON 前需要遵守的原版資料格式與相容限制。
4. [DEV_WORKFLOW.md](DEV_WORKFLOW.md) — 現有建置方式、預定輕量測試方式和驗收規則。
5. [DECISIONS.md](DECISIONS.md) — 已確認的決策、尚待實測的技術選擇。
6. [CHANGELOG.md](CHANGELOG.md) — 每次修改實際做了什麼、測了什麼、沒測什麼。

## 本次工作界線

- 目標 A：AI 直接產生原 Repo 可接受的教材大綱 JSON 與題庫 JSON，無須強制走 PDF 拆解。
- 目標 B：Repo 能讀取、建置、顯示、判分並保存這些臺灣教材；能快速修改、再次試學。
- 第一個驗證素材：臺灣國小六年級數學 U1-1。既有素材只作內容參考，不承接另一套系統的架構或規格。
- 先沿用原版 Web 的操作流程及介面。教材圖片按需要才產生。原版大型音訊／PDF 資源並非第一階段必要條件。
- 不把「支援所有年級／所有出版社／手機 App／帳號同步／自動出題服務」列為本階段交付。
- 原版上游功能及說明保留；臺灣版本的差異集中記在本目錄。

## 每次變更必須更新的紀錄

- **開工前**：在 IMPLEMENTATION_TODO 指定 ID，寫清楚此次最小範圍和通過條件。
- **修改後**：在 CHANGELOG 補充檔案、結果、證據、commit/branch，區分「本機 PASS、瀏覽器 PASS、Docker PASS」。
- **改變決策時**：在 DECISIONS 留下原因、取捨與後果，不默默覆寫已採納決策。
- **結束前**：同步 CURRENT_STATE，下一個人先看此檔即可接續。
- 不在 README、issue、對話紀錄另開競爭版本的 TODO；討論的結論必須回填此處才算形成專案記錄。

## 狀態定義

- TODO：尚未動工。
- IN_PROGRESS：已開始，有分支或工作紀錄。
- BLOCKED：無法繼續，寫明阻塞及解除條件。
- PASS_LOCAL：完成本機／資料驗證，未必有瀏覽器結果。
- PASS_E2E：完成指定環境的使用者流程驗收並留證據。
- CLOSED：驗收證據完整且改動已按約定整合。單純文檔提交不代表產品功能 CLOSED。

最後更新：2026-10-16。
