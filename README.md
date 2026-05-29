# booking

這是一個可直接部署到 GitHub Pages 的單頁醫療預約 App 原型。

## GitHub Pages

1. 將 `index.html`、`images/`、`dev-server.js`、`.nojekyll` 一起 commit 並 push。
2. 到 GitHub repo 的 `Settings > Pages`，選擇要發布的 branch，例如 `main`，資料夾選 `/root`。
3. 使用 GitHub Pages 提供的網址開啟，不要用 GitHub 的檔案預覽頁看效果。

注意：本機瀏覽器建立的預約、診所、上傳照片等資料存在 `localStorage`，不會跟著 Git 上傳。若要讓第一次開啟也有畫面，請把預設資料寫在 `index.html` 裡。
