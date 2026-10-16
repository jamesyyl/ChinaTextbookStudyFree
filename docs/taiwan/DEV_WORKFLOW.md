# DEV_WORKFLOW｜建置與驗收

狀態：**原版命令已由程式碼確認；輕量測試命令尚未實作、未執行**。
請不要把下方「預定流程」當作今天可直接執行的腳本。

## 原版現況

- compose.yaml 預設 build Repo 根目錄的 Dockerfile，對外由 Nginx :8080 提供靜態檔。
- Dockerfile 將下載 v1.2.0-assets 放在較早的快取層；之後才 COPY 程式與 output。快取存在時只改 JSON 通常不必重新下載 2 GB，但完整站點與音訊流程仍可能耗時。
- apps/web/package.json：npm run build:data = tsx scripts/build-data.ts 加上 python3 scripts/tts/web_audio.py；npm run dev 與 npm run build 都會呼叫 build:data。
- apps/web/next.config.mjs = output: export，靜態檔輸出在 apps/web/out；既有課程頁多在匯出時讀入課程 JSON。
- 原版可執行的完整建置命令：docker compose up -d --build。此命令**不適合**無大型資源的第一課快速測試；不要現在執行它來驗證輕量模式。

## 擬新增的輕量模式（TW-008～TW-010，尚未存在）

1. 只準備 output/math/outlines、output/math/quizzes 的臺灣試點來源 JSON。
2. 先跑嚴格 validator；若缺題／配對錯／判分格式不合法，立即失敗。
3. 單獨執行 build-data.ts 生成 data，不啟動 web_audio.py，也不要求原版資源下載。
4. 產生本次測試課程的 Web 靜態頁，或使用能可靠刷新 JSON 的開發模式；驗證內容更新後真正顯示新版。
5. 語音相關 UI 不得指向缺少的原版音訊，也不得讓沒有 TTS 的課被語音鎖定；保留閱讀與答題可完成的替代流程。
6. 測試時關閉或隔離 Service Worker 快取，使用獨立測試來源／瀏覽器資料，避免舊進度污染。
7. 一課 PASS 後，才加最小 Docker/Nginx 封裝與版本回滾需求。

**重要：** 以上是驗收目標，不代表 Repo 已具備單科增量建置或快速同步。是否能省下整站重建，須由 TW-009 實測。

## 第一堂課驗收矩陣（TW-011）

| 範圍 | 必須實際確認 |
| --- | --- |
| 資料 | output 來源有效，index/outline/lesson 數量與內容一致 |
| 選書 | 臺灣教材可從首頁／年級／科目進入，不會與其他出版社撞 ID |
| 課程 | 正確顯示觀念、題幹、選項與解析，無空白課程 |
| 判分 | 正答、錯答、繁體是非、分數/小數/負數都符合期待 |
| 中斷 | 退出、重新進入，課程進度合理，不使用舊版本題目 |
| 複習 | 錯題可以進入原版複習機制 |
| 發布 | Docker 無 2 GB 下載，網址能連線；頁面與 JSON 版本一致 |
| 圖片 | 只在 TW-014 需要時加入；無圖片課程須照常可用 |

## 驗收紀錄最低欄位

日期、branch、commit、任務 ID、測試環境、命令、實際數據、PASS/FAIL/BLOCKED、截圖或輸出位置、未涵蓋項目。
如果沒有真正啟動瀏覽器，記為「原始碼檢查」，不能寫成 E2E PASS。

## 已知的第一輪陷阱

- build-data.ts 清空 public/data；手改產物會遺失。
- Service Worker 對 /data/ 先回快取；不能只按重新整理就認為看到最新資料。
- lessonId 由順序生成；重排課程後舊完成進度可能顯示在新課。
- web_audio.py 會處理原版音訊引用，不能拿原版 npm run build 原封不動當輕量模式。
- Docker compose 預設只綁 127.0.0.1，區網共享需要明確配置與防火牆。
