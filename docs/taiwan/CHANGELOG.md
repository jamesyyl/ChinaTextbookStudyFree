# CHANGELOG｜臺灣教材版工作紀錄

此檔是**已發生的實際變更**，不是預定工作清單。任務和狀態以 IMPLEMENTATION_TODO 為準，當前總覽見 CURRENT_STATE。

## 2026-10-16｜文件與決策紀錄基線

- 範圍：**documentation only**；未修改任何應用程式、題目、建置或 Docker 行為。
- 調查基準：jamesyyl/ChinaTextbookStudyFree，main @ a4cfe46cf50c87f0c29ebbe0abd780c7e818bf98。
- 文件變更：新增 docs/taiwan/ 工作入口、CURRENT_STATE、IMPLEMENTATION_TODO、CONTENT_CONTRACT、DECISIONS、DEV_WORKFLOW、CHANGELOG；在根 README 加臺灣版工作入口。
- 來源：GitHub 遠端原始碼、Dockerfile、compose.yaml、Next.js 靜態設定、資料型別、資料建置與品質檢查、題目判分、Service Worker。
- 程式測試：**未執行**；瀏覽器端到端驗收：**未執行**；Docker 建置／執行：**未執行**。
- 本次驗證：對照遠端基準及文件連結，新增文件僅記錄已確認事項／待辦。實際分支與 commit 以 Git 歷史為準。
- 下一步：TW-001～TW-004（JSON 格式／生成／檢查／U1-1 首份樣本）。

## 之後每次新增記錄的格式

### YYYY-MM-DD｜TW-xxx｜簡短任務名稱

- 目的與最小範圍：
- 變更的檔案：
- Branch / commit / PR：
- 驗證命令與環境：
- 實際結果（PASS_LOCAL / PASS_E2E / FAIL / BLOCKED）：
- 證據位置與尚未驗證之處：
- 下一步及是否需更新 DECISIONS、CURRENT_STATE：
