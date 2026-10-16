# CONTENT_CONTRACT｜AI JSON 生成與資料對應（研究版）

狀態：**研究規格；未跑過臺灣教材端到端驗證**。
原始依據：packages/core/src/types.ts、scripts/quiz/prompts.py、apps/web/scripts/build-data.ts、apps/web/src/components/question/QuestionRenderer.tsx、packages/core/src/grade.ts。
本文件只描述現有 Repo 的實際欄位和需要驗證的相容性；此處沒有宣告新的資料架構。

## 原版真正讀取的來源

根目錄 output/math/outlines/{stem}.json：一冊教材的大綱。
根目錄 output/math/quizzes/{stem}_unit{unit_number}.json：對應單元的題庫。
其他現有科目為 chinese、english、science，沿用同樣 outlines / quizzes 結構。

build-data.ts 會把來源轉成：

- apps/web/public/data/index.json
- apps/web/public/data/books/{bookId}/outline.json
- apps/web/public/data/books/{bookId}/lessons/{lessonId}.json

注意：每次執行 build-data.ts 會刪除並重建 public/data；產出資料不是可長期人工維護的來源。

## 大綱 outline.json 的形狀

~~~json
{
  "textbook": "六年級上學期（名稱示意；原版檔名解析仍需適配）",
  "units": [{
    "unit_number": 1,
    "title": "質數與合數",
    "knowledge_points": [{
      "name": "認識質數與合數",
      "description": "透過正因數判斷分類",
      "difficulty": 1,
      "question_types": ["choice", "true_false"]
    }]
  }]
}
~~~

以上只示範資料形狀，**不是目前原版可以直接辨識的完整教材檔名**。現在的 parseStem() 仍依簡體「六年级上册」類檔名辨識。

## 單元 quiz JSON 的形狀

~~~json
{
  "textbook": "六年級上學期",
  "unit": "質數與合數",
  "unit_number": 1,
  "unit_test": {
    "title": "單元練習",
    "total_score": 5,
    "time_minutes": 10,
    "questions": [{
      "id": 1,
      "type": "choice",
      "score": 5,
      "difficulty": 1,
      "knowledge_point": "認識質數與合數",
      "question": "下列哪一個數是合數？",
      "options": ["1", "9", "11", "13"],
      "answer": "9",
      "explanation": "9 的正因數是 1、3、9，因此是合數。"
    }]
  },
  "exam": {
    "title": "單元挑戰",
    "total_score": 0,
    "time_minutes": 10,
    "questions": []
  },
  "knowledge_summary": [{
    "point": "認識質數與合數",
    "core_concept": "大於 1 且只有 1 和本身兩個正因數的是質數。",
    "key_formula": "1 不是質數也不是合數。",
    "common_mistakes": ["以為所有奇數都是質數。"],
    "tips": "先檢查正因數。"
  }]
}
~~~

以上是 **AI 生成格式示例，不是已提交的教材**。原版的完整 AI pipeline 還另設固定分數／題數品質規則，但靜態網站轉換器並非要求每個來源單元都恰好 23 題。正式採用的最低題量與評分規則要由 TW-001／TW-003 固定且實測。

## 配對與編號是最容易出錯的地方

三個字串需要相同：
1. outline.units[].knowledge_points[].name
2. quiz.unit_test.questions[].knowledge_point
3. quiz.knowledge_summary[].point

原版以文字名稱做映射，並以題目 knowledge_point 分組來產生 lesson。
- 大綱存在但該點沒有題目：可能不產生 lesson。
- 題目知識點與大綱不符：可能產生額外 lesson。
- 題目有課但知識摘要不匹配：lesson.knowledge 可能是 null。
- 對應 quiz 檔案不存在：單元可能直接被跳過。
- 課程 ID 由 bookId、unit_number、知識點排序推導；若排序改變，同一 ID 可能代表不同課，污染已保存的進度。

TODO TW-003 必須將上述情況轉為可讀且阻擋建置的錯誤；TW-005 處理臺灣多出版社的 bookId 碰撞。

## 現有題型與輸入界線

| type | 原版操作與限制 |
| --- | --- |
| choice | 點選選項；建議 4 個，answer 使用選項完整文字而非 A/B/C/D。 |
| true_false | 原判分/介面採簡體「对／错」；繁體「對／錯」須 TW-007 改好。 |
| fill_blank、calculation | 螢幕數字鍵盤可輸入 0–9、小數點、負號、斜線；判分需避免分數錯判。 |
| fill_blank_text | 字元候選鍵盤，現有限制最多 10 字元；不適合長篇回答。 |
| word_order | 依逗號分隔詞語重排，答案與 options 必須一致。 |
| matching | 原版介面固定四對：8 個選項、答案如 A-1,B-2,C-3,D-4。 |
| word_problem | 歷史題型，實際與數字填空共用輸入，不是逐步解題介面。 |

圖形拖拉、畫線、手寫列式、多步驟開放回答，並非原版通用 Question 的現成功能。數學公式文字由 MathText 的 $...$ 與 $$...$$ 配合 KaTeX 處理。

## 圖片

- 原版 Question 無 image 欄位，不會因 JSON 多一個 image 就自動顯示。
- 已有課文 pageImages 和故事 image，透過靜態 JPG 路徑顯示；一般題圖為可選的 TW-014。
- 沒有「每課都必須有圖」或通用圖片尺寸硬性規格。新圖片推薦獨立存 PNG/JPG，以相對網站根路徑引用，不內嵌 base64。
- 數線、幾何、統計圖等須核對數據，不能僅依 AI 插圖外觀當作正確。
- 教材原頁圖片可能涉及著作權；原版程式碼 MIT 不等於原教材可以任意轉載。

## AI 生成前置檢查（TW-003 的驗收目標）

- JSON 可解析且來源檔名、單元編號能被建置器對上。
- 單元、知識點、題目 ID 唯一，所有名稱完全配對。
- 題目 type 合法，選項與答案配合題型；解說不可空白。
- 數學答案已核對：尤其分數、小數、負數、式子；不得依賴模糊 parseFloat。
- 本來應該有的課程不能被靜默略過；圖片有引用就檢查檔案存在。
- 輸出 index、outline、lesson 必須與預期數量一致。
