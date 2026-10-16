# CURRENT_STATE｜臺灣教材版

更新：2026-10-16
Repo：jamesyyl/ChinaTextbookStudyFree
調查基準：main @ a4cfe46cf50c87f0c29ebbe0abd780c7e818bf98
紀錄狀態：**研究完成部分原始碼；產品實作與端到端驗收均尚未開始。**

## 現在的唯一目標

讓 AI 直接產生符合原版規格的臺灣教材 JSON，再以原版網站載入並讓孩子完成一節課。
不重新設計網站，不為第一輪投入整冊 PDF 分析、大型音訊或全學科資料。

## 已確認（程式碼閱讀，不等於運行測試）

- 原版資料建置：apps/web/scripts/build-data.ts 從 output/{math,chinese,english,science}/outlines 與 quizzes 讀來源；輸出 apps/web/public/data/index.json、books/{bookId}/outline.json、lessons/{lessonId}.json。
- 每次資料建置會先移除再重建 apps/web/public/data；因此來源必須留在 output 目錄，而非僅編輯產出檔。
- 題目 knowledge_point、知識摘要 point、outline 知識點 name 以文字相等關聯；不一致可能造成無課、額外課或沒有觀念的課。
- 原版書本解析只識別簡體「一年级上册」等形式；數學同年級同學期容易產生相同 bookId，尚不支援台灣多出版社隔離。
- 一般 Question 目前沒有 image 欄位；課文 pageImages 與故事 image 另有獨立 JPG 路徑機制。一般題目圖片非必填。
- 原版 Web 為 Next.js 靜態匯出（apps/web/next.config.mjs）；Docker 以 Nginx 提供預先建置的網站。
- Dockerfile 會下載約 2 GB 資源作為可快取建置層；npm run build:data 又啟動音訊轉換。完整模式不適合頻繁測試一課。
- Service Worker 對 /data/ 採 stale-while-revalidate；學習進度存在本地瀏覽器並按 lessonId / question.id 關聯。
- 數字判分 packages/core/src/grade.ts 使用 parseFloat 寬鬆比較，分數字串例如 3/4 有錯判風險；繁體是非題答案「對／錯」需適配。
- 原品質腳本 apps/web/scripts/check-data-quality.mjs 未涵蓋數學，且不是完整的嚴格阻擋門檻。

## 尚未驗證／不可宣稱成功

- 未以臺灣 U1-1 生成任何正式可用的來源 JSON 或圖片。
- 未驗證「最小 JSON」是否成功輸出索引、學習地圖及課程。
- 未建置或啟動本 Fork 的 Docker；未實測無大型資源的輕量建置。
- 未用瀏覽器操作臺灣課程的作答、判分、錯題、續做、進度。
- 尚未修正判分問題、出版社 ID 或題目圖片功能。
- 尚未定義多出版社上線後的進度資料遷移規則。

## 目前阻塞

- 無硬性外部阻塞；需先完成規格／最小樣本與資料驗證。
- 原版輕量 Docker 模式不存在；此為後續實作項目，不是已備妥的功能。

## 下一步（一件事）

實作 [TW-001 ～ TW-003](IMPLEMENTATION_TODO.md)：固定 AI 輸出契約、建立樣本與前置檢查；以一組 U1-1 來源 JSON 通過結構及數學正確性檢查。

接著才做無 2 GB 依賴的建置與完整課程驗收。

## 狀態記錄規則

優先使用實際輸出與測試證據，不以測試計畫、程式碼推測、工具交辦作為 PASS。
本頁只記最新狀態，完成／失敗經過見 CHANGELOG，待辦細節見 IMPLEMENTATION_TODO。
