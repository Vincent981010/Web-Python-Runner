# 🐍 Web Python Runner (網頁版 Python 執行器)

這是一個純前端的網頁應用程式，允許使用者直接在瀏覽器中上傳 `.py` 檔案並執行 Python 程式碼。本專案完全不需要任何後端伺服器，非常適合架設在 GitHub Pages 等靜態網頁代管平台上，作為教學、快速測試或展示 Python 腳本的工具。

## ✨ 主要功能 (Features)

*   **無伺服器架構**：100% 運行於瀏覽器端，節省伺服器成本並確保極致的安全沙盒環境。
*   **支援互動輸入**：完美解決一般網頁版 Python 無法使用 `input()` 的痛點。
*   **終端機視覺體驗**：將標準輸出 (stdout) 與錯誤輸出 (stderr) 導向至網頁的黑底對話框，模擬真實終端機。
*   **隨插即用**：只需一個 `index.html` 檔案即可運作。

---

## 🛠️ 運作原理 (How it Works)

本專案的核心依賴 **[Pyodide](https://pyodide.org/)** 技術：
1.  **WebAssembly (Wasm)**：Pyodide 是一個將 CPython 編譯為 WebAssembly 的開源專案，這讓 Python 直譯器能夠直接在網頁的 JavaScript 引擎中高速運行。
2.  **I/O 攔截與重導向**：
    *   **輸出 (`print`)**：透過 Pyodide 的 `setStdout` 與 `setStderr` API，將 Python 的字串輸出攔截，並動態附加到 HTML 的 `<pre>` 元素中。
    *   **輸入 (`input`)**：JavaScript 單執行緒無法處理 Python 阻塞式的 `input()` 函數。因此，本專案在載入環境時，預先注入了一段 Python 程式碼，將內建的 `input` 函數覆寫，改為呼叫瀏覽器原生的 `prompt()` 彈出視窗來接收使用者輸入。

---

## 🚀 部署教學 (Deployment)

這份專案專為 **GitHub Pages** 設計，部署過程只需 3 分鐘：

1.  **建立儲存庫**：在你的 GitHub 建立一個新的公開 Repository（例如命名為 `web-python-runner`）。
2.  **上傳檔案**：將包含上述程式碼的 `index.html` 上傳到該 Repository 的根目錄。
3.  **開啟 GitHub Pages**：
    *   進入該 Repository 的 **Settings** (設定)。
    *   在左側選單找到 **Pages**。
    *   在 **Build and deployment** 區塊中，Source 選擇 `Deploy from a branch`。
    *   Branch 選擇 `main` (或 `master`)，資料夾選擇 `/ (root)`，點擊 **Save**。
4.  **完成**：等待約 1~2 分鐘後，GitHub 會提供一組網址（如 `https://你的帳號.github.io/web-python-runner/`），點開即可使用！

---

## 📖 使用教學 (Usage)

1.  開啟網頁後，請先等待上方狀態列顯示 **✅ Python 環境已準備就緒！**（首次載入需下載數 MB 的 Pyodide 核心檔，請稍候）。
2.  點擊「**選擇檔案**」上傳你本機端的 `.py` 檔案。
3.  點擊「**▶ 執行程式**」。
4.  若程式碼中有 `input()`，網頁會彈出輸入視窗，請填寫後按下確定。
5.  執行結果與錯誤訊息會即時顯示在下方的「終端機輸出」區塊。

---

## ⚠️ 限制說明 (Limitations)

由於網頁瀏覽器安全沙盒 (Sandbox) 的先天限制，部分 Python 腳本可能無法順利執行，請注意以下幾點：

*   **圖形化介面 (GUI)**：不支援 `tkinter`、`PyQt`、`pygame` 等需要呼叫作業系統視窗的套件。
*   **本機檔案讀寫**：無法使用 `open('C:/text.txt')` 讀取你電腦硬碟中的檔案（網頁沒有權限）。若需處理資料，建議將資料直接寫死在腳本中，或透過網路 API 抓取。
*   **無限迴圈**：請避免執行沒有結束條件的 `while True:` 且未包含非同步等待的迴圈，這會導致網頁分頁卡死。
*   **CORS 網路請求限制**：使用 `requests` 等套件抓取外部網站資料時，可能會被瀏覽器的跨來源資源共用 (CORS) 政策阻擋。

---
*Powered by [Pyodide](https://pyodide.org/)*