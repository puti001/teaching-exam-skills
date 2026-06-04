# 國小數學出題專案複盤報告 (Project Review)

本文件收錄將「國中數學出題技能集」改版為「國小數學出題技能集」的專案複盤，包含核心成果、關鍵技術名詞、提示詞範本以及 Windows 本地踩坑的重大技術突破。

---

## 📋 專案摘要 (Project Summary)

* **專案起因**：Fork 自國中數學老師「三師爸」放在 GitHub 上的 [國中數學教學出題技能集](https://github.com/mathruffian-dot/teaching-exam-skills)。
* **改版目標**：全面改造成適用於**國小數學（一至六年級）**的段考出題、審題、幾何繪圖、生活情境非選題技能集。
* **主要成果**：
  1. **技能目錄重命名**：全面將 `jh-math-` (Junior High) 前綴移轉為 `es-math-` (Elementary School)。
  2. **補齊 108 國小課綱對照表**：建立 [es-math-curriculum.md](file:///c:/antigravity/EP02/skills/es-math-exam/references/es-math-curriculum.md)，收錄一至六年級完整課綱代碼（N/S/R/D），解決原專案缺少課綱參考的問題。
  3. **重新修訂認知難度**：將 Bloom 認知層次修訂為國小生認知水平，重寫了幾何圖形參數目錄，精簡了國中相似形、三角形三心等進階內容。
  4. **自主開發 Word 產生器**：實作了 [generate_exam_docx.py](file:///c:/antigravity/EP02/skills/es-math-exam/scripts/generate_exam_docx.py)，實現一鍵產出題目卷、答案卷、雙向細目表，並支援**原生 OMML 數學公式編譯**。
  5. **實測出題成功**：成功為「五年級下學期期末考」生成包含選擇、填充、計算共 23 題的完整試卷，幾何配圖完美嵌入，且所有數學公式原生 OMML 渲染。

---

## 📖 關鍵名詞解釋 (Glossary)

### 1. Git / GitHub 術語
* **Repository (Repo / 儲存庫)**：專案在 GitHub 線上的儲存庫，用來保存所有檔案、程式碼及每次修改的歷史紀錄。
* **Fork (分叉)**：把別人的 Repo 複製一份到自己的 GitHub 帳號下，成為自己擁有完整修改權限的新 Repo。
* **Commit (提交)**：在自己電腦（本地）將階段性的修改成果打包封存，並寫下修改說明的存檔動作。
* **Push (推送)**：把本地打包好的 Commit 上傳並同步至 GitHub 線上 Repo 的動作。
* **GitHub Pages**：GitHub 的免費靜態網頁託管服務。

### 2. 排版術語
* **OMML (Office Math Markup Language)**：Microsoft Word 的原生數學公式格式。我們在 Word 裡「插入公式」時，底層就是這個格式。它能呈現完美的上下疊加分數、Cambria Math 的斜體方程式字元，外觀極度專業。

---

## 💬 實用提示詞範本 (Useful Prompts)

### 💡 提示詞 A：在線讀取（免下載直接出題）
> 「請你讀取這份國小出題技能說明書：`https://github.com/你的帳號/teaching-exam-skills/blob/master/skills/es-math-exam/SKILL.md`。讀完後請扮演裡面的國小數學段考出題專家，幫我出一份【康軒版五年級下學期第二次段考】的題目。」

### 💡 提示詞 B：本地端執行（自動排版一鍵產出 Word 文件）
> 「請讀取本地的 `skills\es-math-exam\SKILL.md` 技能。我想出一份【翰林版五下期末考】的試卷，請幫我設計題目，並自動執行 `skills\es-math-exam\scripts\generate_exam_docx.py` 腳本，將排版好的題目卷、答案卷和雙向細目表產出到本地的 `output` 資料夾。」

---

## 🛠️ 踩坑啟發與技術突破 (Key Troubleshooting)

### 1. Windows 的 CP950 Unicode 列印崩潰 ➔ **重整 stdout 編碼**
* **踩坑**：Windows CMD/PowerShell 預設採用 CP950 中文編碼。Python 腳本在執行 `print` 列印出表情符號（如 ✅, ⚠️, ❌）時，會引發無法解碼的 `UnicodeEncodeError` 導致程式完全崩潰。
* **啟發**：在腳本最開頭加入檢測，若為 Windows 則重整輸出流編碼：
  ```python
  if sys.platform.startswith('win'):
      sys.stdout.reconfigure(encoding='utf-8')
  ```

### 2. Windows 無法安裝 Cairo C 語言庫 ➔ **Edge Headless 降級截圖方案**
* **踩坑**：`python-docx` 無法插入 SVG 向量圖。但 `cairosvg` 依賴系統的 Cairo C 語言底層函式庫，在 Windows 上極難安裝。
* **啟發（神級替代方案）**：我們利用 Windows 系統 100% 自帶的 Microsoft Edge 瀏覽器的 **Headless（無頭）模式**，直接透過命令行對 SVG 進行高解析度截圖：
  ```bash
  msedge.exe --headless --disable-gpu --window-size=276,226 --screenshot="out.png" "file:///in.svg"
  ```
  這完全避開了複雜的環境依賴，實現了 100% 的跨平台相容性。

### 3. Word 分數公式不專業 ➔ **python-docx 寫入 OMML XML**
* **踩坑**：一般的 Word 產生器只能把分數產出為 `1/2`，文字看起來很不專業。原作者在影片中也提到他漏掉了 OMML 數學公式的處理。
* **啟發**：我們直接操作 OpenXML，利用 `parse_xml` 函數，在寫入 Word 段落時，直接將 `{frac(a,b)}` 解譯為 Word 原生的 OMML 分數節點。這讓產出的 Word 試卷具備了極度專業的公式排版效果。

---

## 👥 Google 生態系協同合作建議

1. **Google 雲端硬碟 (Google Drive) 共用資料夾**：
   - 建立一個資料夾（如 `數學科段考參考資料`），將權限設為 **「知道連結的任何人皆可檢視」**。
   - 裡面可放 Word 大綱、PDF 教材等。貼上網址給 AI，AI 即可直接讀取內部檔案。
2. **本地 `inputs` 目錄**：
   - 直接在電腦專案下開一個 `inputs/` 資料夾，將簡歷或課程大綱存成 `.txt` 或 `.docx` 放進去，讓 AI Agent 在本地端直接讀取，速度最快且 100% 穩定安全。

---

## 💡 AI 段考命題與品質保證最佳實踐 (Best Practices)

### 1. 避坑的「黃金出題三步驟」
各教科書版本（康軒、翰林、南一）每年、各校的段考範圍常有微調。**請不要直接讓 AI 憑空估算或上網搜尋**，這很容易產生單元範圍錯亂的「範圍幻覺」。
* **第一步：提供課本目錄** ➔ 出題前，直接拍照上傳該學期實體課本的「目錄頁」圖片給 AI，或手動貼上目錄文字。這能確保出題單元 100% 正確。
* **第二步：提供難度參考範本** ➔ 將「學校平時練習卷或習作的 2~3 題」拍照或打字提供給 AI（Few-Shot 學習），AI 出出來的題目難易度就會與學校教學完全契合。
* **第三步：一鍵生成** ➔ 交由 AI 進行雙向細目表規劃並一鍵產生 Word 試卷。

### 2. 「多代理人自動審查 (Multi-Agent Review)」雙層防線
為了 100% 確保圖形完整性與答案正確度，在完成題目設計後，應採用**雙 AI 協同審核**機制：
* **第一層（出題 Agent）**：負責解讀範圍、設計題目與初步繪圖規格生成。
* **第二層（獨立審查 Subagent）**：出題後自動啟動一個獨立對話的子代理，專門扮演「審核委員」。它沒有出題時的認知包袱，能客觀地以 Python 驗算每一題的答案，並核對幾何 PNG 檔的虛實線規格、有沒有少配圖。
* 審查 Subagent 回饋錯誤清單，出題 Agent 自動修正，直至審查通過後才生成最終的 Word 試卷。
* **第三層（人類老師）**：打開 Word 考卷，花 1 分鐘快速瀏覽圖形和排版，確認沒問題直接送印。
