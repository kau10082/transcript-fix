# transcript-fix

> **Proofread & semantically reconstruct error-dense medical ASR transcripts — without ever fabricating.**
> **校稿並語意重建「辨識錯誤密集」的醫學語音逐字稿 —— 絕不捏造。**

A [Claude](https://claude.com) skill for cleaning up speech-to-text (ASR) transcripts of medical lectures and meetings. It fixes the *semantic* damage — misheard drug names, trial names, biomarkers, and numbers — using a conservative, evidence-first workflow.

一個用於整理醫學演講／會議「語音轉文字（ASR）」逐字稿的 [Claude](https://claude.com) skill。它修的不是錯字，而是**語意層次的破損** —— 被聽錯的藥名、試驗名、生物標記、數據 —— 採用「保守、先求證」的工作流程。

---

## Why / 為什麼需要它

ASR transcripts of medical talks fail in a specific way: the words are *plausible* but wrong. "Benralizumab" becomes "法西瑪", a trial name dissolves into nonsense, a steroid dose reads as "three grams". Naïvely "cleaning" such a transcript just launders the errors into confident-looking prose. This skill refuses to do that.

醫學演講的 ASR 稿有個特定的失敗模式：字看起來**像真的**，但其實是錯的。「Benralizumab」變成「法西瑪」、試驗名稱整段崩解、類固醇劑量被聽成「三克」。對這種稿子直接「清乾淨」，只是把錯誤洗成看起來很有自信的文章。本 skill 拒絕這樣做。

**Core principle / 核心心法：**
> Don't hard-process a broken copy — go back upstream for better inputs first.
> 不要在壞掉的副本上硬加工，先回上游拿更好的輸入。

---

## Who it's for / 適合誰

- Clinicians, researchers, and medical educators turning recorded talks into usable notes.
- Anyone handling Chinese/English-mixed medical transcripts where drug names and data get mis-recognized.

- 把錄音演講整理成可用筆記的臨床醫師、研究者、醫學教育者。
- 任何要處理「中英夾雜、藥名／數據常被聽錯」的醫學逐字稿的人。

---

## How it works / 運作方式

A six-step workflow / 六步流程：

| Step | English | 中文 |
|---|---|---|
| 1 | **Ask for high-leverage inputs** — request extra independent transcripts (2–4 cross-checked is best) and the speaker's slides before editing. | **索取高槓桿輸入** —— 動手前先要更多份獨立逐字稿（2–4 份交叉最佳）與講者投影片。 |
| 2 | **Load the glossary** from a Notion database (misheard → correct term, by topic). *Optional, needs Notion connector.* | **載入術語詞庫**（Notion 資料庫，誤判→正解，依主題）。*可選，需 Notion 連接器。* |
| 3 | **Multi-transcript diff** — line up all transcripts; agreement = high confidence, divergence pinpoints real doubts, majority vote converges them. | **多稿 diff** —— 並排所有稿；一致＝高信心，分歧＝鎖定疑點，多數決收斂。 |
| 4 | **Semantic reconstruction** into fluent, term-correct text — speculation is always marked, never silently filled. | **語意重建**為通順、術語正確的文字 —— 推測一律標記，絕不默默補值。 |
| 5 | **Literature verification** of named trials/data points. *Optional, needs PubMed/literature tool.* | **文獻查證**具名試驗／數據點。*可選，需 PubMed／文獻工具。* |
| → | **Two outputs:** a reconstructed transcript (`.docx`) + a "to-confirm" list (`.docx`). | **兩份產出：** 重建版逐字稿（`.docx`）＋待確認清單（`.docx`）。 |

---

## A glossary that grows itself / 會自己成長的詞庫

This is what sets transcript-fix apart: **the term bank is a living, self-growing asset, fully under your control.**

這是 transcript-fix 最關鍵的特點：**詞庫是一份活的、會自己成長的資產，且完全由你掌控。**

- **Yours to configure / 自由設定** — the glossary lives in *your own* Notion database. Add, edit, or reorganize terms freely; the skill reads it live, so changes take effect on the very next run.
  詞庫存在**你自己的** Notion 資料庫，可自由新增／修改／重整。skill 是即時讀取，改完下一次就生效。

- **Self-recording / 自動記錄** — every time the skill resolves a confusing mis-hearing (misheard fragment → correct term), it writes that pairing **back into the glossary automatically**. Nothing has to be re-learned twice.
  每次 skill 解開一個容易混淆的誤判（誤判片段→正解），都會把這組對應**自動寫回詞庫**。同一個坑不必踩第二次。

- **Compounding over time / 會複利** — so every transcript makes the next one easier. Recurring drug names, trial names, and biomarkers accumulate, and **any future session reads the latest version live** — no re-downloading, no re-packaging.
  於是每整理一份稿，下一份就更輕鬆。會重複出現的藥名、試驗名、生物標記不斷累積，**任何之後的對話都即時讀到最新版** —— 免下載、免重打包。

- **Disciplined growth / 受控成長** — terms are topic-scoped (airway, ipc, biogend…) so only what this talk needs gets loaded; and only *cross-session* terms are kept. One-off personal names and case details are never stored — a quality rule and a growth valve at once.
  詞庫依主題分類（airway、ipc、biogend…），只載當場需要的；且**只收會跨場重複的詞**。單場的人名、病例細節一律不入庫 —— 既是品質規則，也是控制成長的閥門。

> The more you use it, the smarter it gets — and the term bank is shared across every conversation that connects to your Notion.
> 用得越多它越聰明 —— 而且這份詞庫被所有連到你 Notion 的對話共享。

---

## Hard rules / 硬規則（紅線）

1. **Never fill in unconfirmable specifics** — names, institutions, drug/trial names, numbers, doses, years, lab values. If a transcript can't confirm it, mark `〔？〕`; never guess from what merely *sounds* similar.
2. **Never fabricate a citation.** If it can't be found, say so.
3. **Never present a guess as a fact.** Speculation is always flagged.
4. **LaTeX-free output**, ready to paste anywhere.

1. **絕不替無法確認的具體資訊補值** —— 人名、機構、藥名／試驗名、數字、劑量、年份、檢驗值。稿件無法確認就標 `〔？〕`，絕不憑音近硬填。
2. **絕不捏造文獻引用。** 查不到就說查不到。
3. **絕不把推測寫成確定。** 推測一律標記。
4. **輸出 LaTeX-free**，可直接複製貼上。

---

## Requirements / 需求

- **Claude with skill support** (Claude Code / Claude desktop / claude.ai with skills).
- **Notion connector (MCP)** — *optional*; needed only to use the live glossary database. Without it, the skill still works, just without the shared term bank.
- **A literature tool (PubMed/academic search)** — *optional*; needed only for citation verification (Step 5). Without it, named trials are left in the "to-confirm" list rather than guessed.

- **支援 skill 的 Claude**（Claude Code／桌面版／claude.ai）。
- **Notion 連接器（MCP）** —— *可選*；只在使用即時詞庫資料庫時需要。沒有也能用，只是少了共享詞庫。
- **文獻工具（PubMed／學術搜尋）** —— *可選*；只在 Step 5 查證引用時需要。沒有的話，具名試驗會留在「待確認」清單，而不是亂猜。

---

## Setup / 安裝設定

1. Place this folder at your skills path (e.g. `/skills/user/transcript-fix/`).
   把這個資料夾放到你的 skills 路徑（如 `/skills/user/transcript-fix/`）。

2. **Configure your own glossary (optional).** Copy the example config and fill in your Notion IDs:
   **設定你自己的詞庫（可選）。** 複製範本設定檔，填入你的 Notion ID：

   ```bash
   cp config.example.json config.local.json
   ```

   Then edit `config.local.json` / 然後編輯 `config.local.json`：
   ```json
   {
     "glossaryDataSource": "collection://<YOUR_GLOSSARY_DATA_SOURCE_ID>",
     "glossaryPage": "https://app.notion.com/p/<YOUR_GLOSSARY_PAGE_ID>"
   }
   ```

   > `config.local.json` is git-ignored and never uploaded — it holds your private workspace IDs.
   > `config.local.json` 已被 git 忽略、不會上傳 —— 它存的是你私人 workspace 的 ID。

3. Trigger it by giving Claude a medical transcript and asking to "proofread / clean up / reconstruct" it.
   給 Claude 一份醫學逐字稿，說「校稿／清稿／語意重建」即可觸發。

---

## Privacy / 隱私

This repo contains **no** personal data. Your Notion workspace IDs live only in the git-ignored `config.local.json`; the speaker names, institutions, and case details from any transcript you process stay in your own Notion / local files, never in this skill.

本 repo **不含**任何個資。你的 Notion workspace ID 只存在被 git 忽略的 `config.local.json`；你處理的逐字稿裡的講者姓名、機構、病例細節都留在你自己的 Notion／本機檔案，不會進到這個 skill。

---

*Built with Claude. / 由 Claude 協作打造。*
