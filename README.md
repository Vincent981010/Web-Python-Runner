# 🐍 Web Python Runner (網頁版 Python 執行器)

這是一個純前端的網頁應用程式，允許使用者直接在瀏覽器中上傳 `.py` 檔案並執行 Python 程式碼。本專案完全不需要任何後端伺服器，非常適合架設在 GitHub Pages 等靜態網頁代管平台上，作為教學、快速測試或展示 Python 腳本的工具。

## ✨ 主要功能 (Features)

*   **無伺服器架構**：100% 運行於使用者瀏覽器端，不消耗伺服器運算資源，且確保安全的沙盒環境。
*   **完美支援互動輸入**：
    *   支援一般 `input()` 單行輸入。
    *   支援 `sys.stdin.read()` 多行貼上輸入（解決瀏覽器無法使用 `Ctrl+D` 送出 EOF 的問題）。
*   **終端機視覺體驗**：將標準輸出 (stdout) 與錯誤輸出 (stderr) 導向至網頁的黑底對話框，模擬真實終端機。
*   **隨插即用**：只需一個 `index.html` 檔案即可在任何靜態伺服器上運作。

---

## 🛠️ 運作原理 (How it Works)

本專案的核心依賴 **[Pyodide](https://pyodide.org/)** 技術：
1.  **WebAssembly (Wasm)**：Pyodide 是一個將 CPython 編譯為 WebAssembly 的開源專案，這讓 Python 直譯器能夠直接在網頁的 JavaScript 引擎中高速運行。
2.  **I/O 攔截與重導向**：
    *   **輸出 (`print`)**：透過 Pyodide 的 `setStdout` 與 `setStderr` API，將輸出動態附加到 HTML 中。
    *   **輸入 (`input` & `sys.stdin`)**：JavaScript 單執行緒無法處理阻塞式輸入。因此我們在載入環境時，注入了一段 Python 腳本，覆寫了 `builtins.input` 與 `sys.stdin`。當使用者的 `.py` 檔要求輸入時，系統會自動呼叫瀏覽器的 `prompt()` 彈出視窗來接收文字。

---

## 🚀 部署教學 (Deployment - 搭配 GitHub Pages)

1.  **建立儲存庫**：在 GitHub 建立一個新的公開 Repository（例如命名為 `web-python-runner`）。
2.  **上傳檔案**：將本專案的 `index.html` 上傳到該 Repository 的根目錄。
3.  **開啟 GitHub Pages**：
    *   進入 Repository 的 **Settings**。
    *   在左側選單找到 **Pages**。
    *   在 **Build and deployment** 區塊中，Source 選擇 `Deploy from a branch`。
    *   Branch 選擇 `main` (或 master)，資料夾選擇 `/ (root)`，點擊 **Save**。
4.  **完成**：等待約 1~2 分鐘，前往 `https://<你的帳號>.github.io/<儲存庫名稱>/` 即可使用！

---

## 📖 使用教學 (Usage)

1.  開啟網頁後，請先等待上方狀態列顯示 **✅ Python 環境已準備就緒！**（首次載入需下載數 MB 的 Pyodide 核心檔）。
2.  點擊「**選擇檔案**」上傳你的 `.py` 檔案。
3.  點擊「**▶ 執行程式**」。
4.  若程式碼中有 `input()` 或是 `sys.stdin.read()`（要求貼上長篇文本並按下 Ctrl+D），網頁會彈出視窗。請將內容貼在彈出視窗內並按下確定，系統會自動模擬送出。
5.  執行結果與錯誤訊息會即時顯示在下方的「終端機輸出」區塊。

---

## ⚠️ 限制說明 (Limitations)

由於網頁瀏覽器安全沙盒 (Sandbox) 的先天限制，部分 Python 腳本可能無法執行：

*   **圖形化介面 (GUI)**：不支援 `tkinter`、`PyQt`、`pygame` 等需呼叫作業系統原生視窗的套件。
*   **本機檔案系統讀寫**：無法使用 `open('C:/text.txt')` 讀取電腦硬碟檔案。網頁只能存取虛擬記憶體內的檔案。
*   **無限迴圈**：請避免執行沒有結束條件的 `while True:`（且未包含非同步 `await` 的迴圈），否則會導致瀏覽器分頁卡死。
*   **CORS 網路請求限制**：使用 `requests` 抓取外部網站資料時，可能會被瀏覽器的跨來源資源共用 (CORS) 政策阻擋。

---
*Powered by [Pyodide](https://pyodide.org/)*