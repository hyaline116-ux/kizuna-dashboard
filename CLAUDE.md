# CLAUDE.md

## 省錢模式（Trigger: `$$$`）

當使用者輸入訊息為 `$$$` 時，套用以下規則直到使用者取消：

- 角色：務實的資料分析 & 軟體工程專家，追求最少 token 傳遞最大價值。
- 回覆只給結論，不寒暄、不加開場白/填充語。
- 程式碼一律用 diff / partial code block；修改 <50 行不重寫整個檔案。
- 不解釋程式碼邏輯，除非使用者明確問「為什麼」。
- 策略類回覆：每主題最多 3 個 bullet，每 bullet ≤15 字。

## Context Handoff（Trigger: `%%%`）

當使用者輸入訊息為 `%%%` 時，立即產出「Context Handoff Prompt」，格式如下：

```
## Context Handoff Prompt

**Task Summary** (≤3 lines)
...

**Modified Files / Current Status**
- path/to/file — 狀態

**Next Step**
...
```

輸出後提示使用者：複製上方內容 → 執行 `/clear` → 貼到新 session 開頭。

## 財務儀表板同步（Trigger: `>>>`）

當使用者輸入訊息為 `>>>` 時，把 Google 試算表「絆Kizuna 運營財務/資金管理」同步到 `index.html` 並上線：

1. `git status` 確認沒有別的 session 在改。
2. 用 Google Drive 連接器抓試算表（匯出成 xlsx），再 `python3 fetch.py drive <結果檔>`。fileId、匯出格式與細節見 `.kz_sync/README.md`。連接器不可用時，請使用者下載後改用 `python3 fetch.py file`。
3. `cd .kz_sync && ./sync.sh` 看差異與稽核。
4. 有需要使用者判斷的地方（檢查沒過、未記錄支出不為 0、營收漏加、分類或應付款有疑問）→ 先停下來說明並詢問，不上線。
5. 沒問題 → `./sync.sh apply` → 瀏覽器跑全部篩選範圍測試 → commit、push → curl 確認線上是新數字 → 更新 memory。
6. 回報：改了什麼（之前 → 現在）、還有哪些事等使用者處理。

只在使用者下指令時同步，不做定時或全自動同步。不修改試算表內容；要改時告訴使用者哪一格、改成什麼。
