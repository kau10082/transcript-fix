# transcript-fix

> **Clean up messy medical voice-to-text transcripts — fix the misheard drug names and numbers, without ever making anything up.**
> **把亂七八糟的醫學語音轉文字稿整理乾淨 —— 修好被聽錯的藥名和數字，而且絕不亂編。**

This is a [Claude](https://claude.com) skill for tidying up the transcripts you get when you run a medical lecture or meeting through voice-to-text (ASR). The hard part isn't typos — it's that whole words get heard wrong: drug names, trial names, lab values, numbers. This skill fixes those carefully, and is built to **never guess and pretend it's sure**.

這是一個 [Claude](https://claude.com) skill，專門整理「醫學演講／會議錄音跑語音轉文字（ASR）」之後得到的逐字稿。難的不是錯字，而是**整個詞被聽錯** —— 藥名、試驗名、檢驗值、數字。這個 skill 會小心地把它們修好，而且設計上**絕不亂猜還裝得很確定**。

---

## Why you'd want it / 為什麼會需要它

The tricky thing about these transcripts is that the mistakes *look fine*. "Benralizumab" comes out as "法西瑪", a trial name turns into gibberish, a steroid dose reads as "three grams". If you just "tidy it up" without thinking, you end up with smooth, confident text that's quietly wrong — which is worse than an obviously broken one. This skill is built to not do that.

這種稿子最麻煩的地方是：錯得**看起來很正常**。「Benralizumab」變成「法西瑪」、試驗名整段變亂碼、類固醇劑量被聽成「三克」。如果你不多想、直接「整理乾淨」，最後會得到一份通順又自信、但悄悄錯掉的稿 —— 這比一看就壞掉的稿更危險。這個 skill 就是為了不犯這種錯而做的。

**The one idea behind everything / 背後就一個觀念：**
> Don't keep polishing a broken copy — go get a better starting point first.
> 別一直在壞掉的副本上打磨，先去拿一個更好的起點。

---

## Who it's for / 適合誰

- Doctors, researchers, and teachers who want to turn a recorded talk into notes they can actually use.
- Anyone dealing with mixed Chinese-English medical transcripts where the drug names and data keep coming out wrong.

- 想把錄音演講變成「真的能用的筆記」的醫師、研究者、老師。
- 任何要處理「中英夾雜、藥名和數據老是被聽錯」的醫學逐字稿的人。

---

## How it works / 它怎麼運作

It walks through six steps / 它會走六個步驟：

| Step / 步驟 | English | 中文 |
|---|---|---|
| 1 | **Ask you for the good stuff first.** Before editing, it asks whether you have any *other* versions of the transcript (2–4 is ideal) and the speaker's slides. The more it has to work with, the less it has to guess. | **先跟你要好料。** 動手前，它會先問你有沒有**別份**的逐字稿（2–4 份最理想）和講者的投影片。手上的材料越多，要靠猜的就越少。 |
| 2 | **Pull in your word list from Notion** — your saved "this gets heard as that" corrections for this kind of talk. *(Optional — needs the Notion connector.)* | **從 Notion 拉進你的詞庫** —— 你存過的「這個字常被聽成那個」的更正，只取這類場次的。*（可選 —— 需要 Notion 連接器。）* |
| 3 | **Line up the versions and compare.** Where they all say the same thing, it's almost certainly right. Where they disagree, that's the real problem spot — and the majority wins. | **把幾份稿擺在一起比對。** 大家都一樣的地方，幾乎一定對；不一樣的地方就是真正的問題點 —— 以多數的為準。 |
| 4 | **Rewrite it into clean, correct text.** Anything it's only guessing at gets clearly marked, never quietly slipped in. | **重寫成乾淨、正確的中文。** 凡是它只是在猜的，都會清楚標出來，絕不偷偷塞進去。 |
| 5 | **Double-check named trials and figures against the literature.** *(Optional — needs PubMed or a similar tool.)* | **拿具名試驗和數字去對文獻查證。** *（可選 —— 需要 PubMed 之類的工具。）* |
| → | **You get two files:** the cleaned-up transcript (`.docx`), and a checklist of everything still worth double-checking (`.docx`). | **你會拿到兩份檔案：** 整理好的逐字稿（`.docx`），和一份「還值得再確認的事項」清單（`.docx`）。 |

### About that word list (the "glossary") / 關於那個詞庫

The word list in Step 2 is just a **Notion database you make yourself**, and you tell the skill where it is in `config.local.json`. Each row is one correct term, plus the wrong ways it usually gets heard:

Step 2 的詞庫，就是**一個你自己建立的 Notion 資料庫**，你在 `config.local.json` 裡告訴 skill 它在哪。每一列＝一個正確的詞，加上它平常被聽錯的各種樣子：

| Column / 欄位 | What goes in it / 放什麼 | Example / 範例 |
|---|---|---|
| `正確術語` Correct term | The right word. / 正確的那個詞 | `benralizumab` |
| `誤判片段` Misheard as | The wrong versions, separated by `/`. / 被聽錯的樣子，用 `/` 分開 | `法西瑪 / 班瑞莉` |
| `類別` Category | What kind of thing it is: `drug` / `bio` / `trial` / `pathogen`… | `drug` |
| `主題` Topic | A label that groups terms by talk type, so it only loads what's relevant. / 一個把詞按場次類型分組的標籤，讓它只載相關的 | `airway` |
| `備註` Note · `確認日期` Date | Optional — any context, and when you confirmed it. / 選填 —— 補充說明、確認日期 | — |

**What this looks like in practice / 實際上用起來是這樣**

You don't poke at the database while you're working — you just give Claude the transcript and it handles the lookup. Plainly put:

整理稿子的時候你不用去動那個資料庫 —— 你只要把逐字稿給 Claude，它會自己去查。白話講就是：

- **It figures out the topic on its own.** Claude reads the transcript, works out roughly what the talk is about (asthma? infection control?), and loads just that topic's words — not the whole list. You don't lift a finger.
  **主題它會自己判斷。** Claude 看過稿子，大致抓出這場在講什麼（氣喘？感染管制？），就只載那個主題的詞、不是整份載。你完全不用做。

- **You can also just tell it the topic.** If you'd rather say it outright, or it guesses wrong, just tell it *"use the `icu` topic for this one"* and it switches. You're always allowed to decide.
  **你也可以直接跟它講主題。** 想講明白、或它猜錯時，跟它說「**這份用 `icu` 主題**」就好，它馬上換。要不要指定，隨你。

- **Adding a new topic is literally just naming one.** Nothing to set up, no new file. The first time you've got a talk in a new area, just say so — or type a new label like `cardio` into the `主題` column yourself. That's it; the topic now exists and starts building up its own words.
  **新增主題，真的就是「取個名字」而已。** 沒有什麼要設定、不用建新檔。第一次遇到新領域的場次，講一聲就行 —— 或自己在 `主題` 欄打個新標籤（如 `cardio`）。就這樣，這個主題就出現了，開始累積自己的詞。

- **After each job, it saves what it learned.** Any new "heard-as → correct" pairs go back into the list automatically, under the right topic. (If the correct word's already there, it just adds the new wrong-version to that row — no duplicates.) That's why the next talk on the same topic is always easier.
  **每整理完一份，它會把學到的存回去。** 新的「被聽成→正解」會自動回存到詞庫、歸到對的主題。（如果正解早就有了，就只是把新的誤判補進那一列 —— 不會重複。）所以同主題的下一場，總是越整理越輕鬆。

---

## The word list keeps getting smarter / 詞庫會越用越聰明

This is the part that makes transcript-fix different: **the word list is alive — it grows on its own, and it's completely yours.**

這是 transcript-fix 跟別人不一樣的地方：**詞庫是活的 —— 它會自己長大，而且完全是你的。**

- **It's yours to edit.** The list lives in *your own* Notion. Add, change, or rearrange anything you like; the skill reads it fresh every time, so your edits take effect on the very next run.
  **你想怎麼改都行。** 詞庫就放在**你自己的** Notion，你愛怎麼加、怎麼改、怎麼重排都行。skill 每次都是現查現用，你一改，下一次就生效。

- **It remembers so you don't repeat yourself.** Every time it nails down a confusing mis-hearing, it saves that fix. You never have to teach it the same word twice.
  **它會記住，你就不用一直重講。** 每次它搞定一個容易混淆的誤判，就把這個更正存起來。同一個詞你不必教第二次。

- **It pays off more the more you use it.** Common drug names, trial names, and markers pile up over time, and *every* conversation connected to your Notion sees the latest version — nothing to download or re-send.
  **越用越划算。** 常見的藥名、試驗名、標記會隨時間累積，而且**每一個**連到你 Notion 的對話都看得到最新版 —— 不用下載、不用重傳。

- **It stays tidy on purpose.** Words are grouped by topic, so it only loads what each talk needs; and it only keeps things that show up across different talks. One-off names and patient details never get saved — that keeps quality up and stops the list from bloating.
  **它刻意保持精簡。** 詞按主題分組，每場只載需要的；而且只留「會跨場重複出現」的東西。一次性的人名、病人細節一律不存 —— 既保品質，也讓詞庫不會越長越肥。

> The more you use it, the smarter it gets.
> 用得越多，它就越聰明。

---

## The lines it won't cross / 它絕不會越過的線

1. **It won't fill in anything it can't actually confirm** — names, places, drug or trial names, numbers, doses, years, lab values. If the transcript can't pin it down, it marks it `〔？〕` rather than guessing from something that just *sounds* similar.
2. **It won't make up a citation.** If it can't find the source, it says so.
3. **It won't pass off a guess as a fact.** Guesses are always flagged as guesses.
4. **Output is plain text** (no LaTeX), ready to paste anywhere.

1. **它不會補任何「無法真正確認」的東西** —— 人名、地點、藥名或試驗名、數字、劑量、年份、檢驗值。稿子定不下來的，它就標 `〔？〕`，而不是憑「聽起來很像」硬填。
2. **它不會編造文獻引用。** 找不到出處，就直說找不到。
3. **它不會把猜測當成事實。** 猜的就一定標成「猜的」。
4. **輸出是純文字**（不含 LaTeX），可以直接複製貼到任何地方。

---

## What you need / 你需要什麼

- **A version of Claude that supports skills** (Claude Code / desktop app / claude.ai).
- **The Notion connector (MCP)** — *optional.* Only needed if you want to use the live word list. Without it, the skill still works fine; you just won't have the shared word bank.
- **A literature tool like PubMed** — *optional.* Only needed for the citation-checking step. Without it, named trials simply stay on the "to-confirm" list instead of being guessed.

- **一個支援 skill 的 Claude**（Claude Code／桌面版／claude.ai）。
- **Notion 連接器（MCP）** —— *可選。* 只有你想用即時詞庫時才需要。沒有它 skill 照樣能用，只是少了共享詞庫。
- **PubMed 之類的文獻工具** —— *可選。* 只有查證引用那一步才需要。沒有的話，具名試驗就乖乖留在「待確認」清單，不會被亂猜。

---

## Getting set up / 怎麼安裝

> **Quick note on "do I need an API key?" — No.** The skill reaches Notion through Claude's **connector** (a one-time *authorize* button), not through a key you paste into a file. The only things you put in `config.local.json` are the IDs that say *which* database is your word list. None of those are secrets.
> **先回答「要不要 API key？」—— 不用。** 這個 skill 是透過 Claude 的**連接器**連到 Notion（按一次「**授權**」就好），不是在檔案裡貼什麼金鑰。你會寫進 `config.local.json` 的，只有「指出哪個資料庫是詞庫」的 ID，那些都不是機密。

**Step 1 — Put the skill in place. / 放好 skill。**
Drop this folder where your skills live (e.g. `/skills/user/transcript-fix/`).
把這個資料夾放到你的 skills 位置（例如 `/skills/user/transcript-fix/`）。

**Step 2 — Connect Notion to Claude.** *(only needed if you want the word list)*
**把 Notion 連上 Claude。**（只有想用詞庫才需要）

The word list lives in Notion, so Claude needs permission to read it. You set this up **once**, through Claude's connector (a.k.a. MCP): in the Claude app, open your **Connectors / integrations** settings, find **Notion**, and click **Connect / Authorize**. That opens a normal Notion login where you choose which pages Claude is allowed to see. No key, no token to copy — the connector handles sign-in for you.

詞庫放在 Notion，所以得讓 Claude 有權限讀它。這只要設定**一次**，透過 Claude 的連接器（也叫 MCP）：在 Claude app 裡打開**連接器／整合（Connectors）**設定，找到 **Notion**，按 **連接／授權**。它會開一個正常的 Notion 登入畫面，讓你勾選哪些頁面可以給 Claude 看。沒有金鑰、沒有 token 要複製 —— 登入由連接器幫你處理。

> *Advanced only:* if you run your **own** Notion MCP server instead of the built-in connector, that route uses a Notion "internal integration token" you'd configure on the server side — but with the normal connector there's nothing like that to manage.
> *僅進階：* 如果你是自己架**自己的** Notion MCP server（而不是用內建連接器），那條路才會用到 Notion 的「internal integration token」，且設定在 server 端 —— 用一般連接器就沒有這種東西要管。

**Step 3 — Tell the skill which database is your word list. / 告訴 skill 哪個資料庫是詞庫。**

First copy the example config / 先複製範本設定檔：
```bash
cp config.example.json config.local.json
```

Then open `config.local.json` and fill in two values / 然後打開 `config.local.json`，填兩個值：

- **`glossaryPage`** — open your glossary database in Notion, click **Share → Copy link** (or the `•••` menu → **Copy link**), and paste that URL here.
  在 Notion 打開你的詞庫資料庫，按 **Share → Copy link**（或 `•••` 選單 → **複製連結**），把那個網址貼進來。

- **`glossaryDataSource`** — this is the database's internal ID, written as `collection://…`. Easiest way to get it: with the Notion connector switched on, just ask Claude *"fetch this Notion database and tell me its data-source (collection://) id"* and paste back what it returns.
  這是資料庫的內部 ID，長這樣 `collection://…`。最簡單的拿法：開著 Notion 連接器，直接跟 Claude 說「**幫我 fetch 這個 Notion 資料庫，告訴我它的 data source（collection://）id**」，把它回給你的貼上即可。

```json
{
  "glossaryDataSource": "collection://<YOUR_GLOSSARY_DATA_SOURCE_ID>",
  "glossaryPage": "https://app.notion.com/p/<YOUR_GLOSSARY_PAGE_ID>"
}
```

> These IDs just point at a database — they're not passwords. Even so, `config.local.json` is git-ignored, so your workspace details never get uploaded.
> 這些 ID 只是指向一個資料庫 —— 不是密碼。不過 `config.local.json` 還是被 git 忽略，所以你的 workspace 細節永遠不會上傳。

**Step 4 — Use it. / 開始用。**
Give Claude a medical transcript and ask it to "proofread / clean up / reconstruct" it.
要用的時候，把一份醫學逐字稿給 Claude，說「校稿／清稿／語意重建」就會啟動。

---

## Your privacy / 你的隱私

There's **no** personal data in this repo. Your Notion IDs only ever live in the git-ignored `config.local.json`, and the names, institutions, and patient details from any transcript you process stay in your own Notion and local files — they never end up in this skill.

這個 repo 裡**沒有**任何個資。你的 Notion ID 只存在被 git 忽略的 `config.local.json`；你處理的逐字稿裡的人名、機構、病人細節，都留在你自己的 Notion 和本機檔案 —— 永遠不會跑進這個 skill。

---

*Built with Claude. / 由 Claude 協作打造。*
