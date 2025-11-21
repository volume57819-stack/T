# 初始化 repo（因為你剛剛只有放 bundle）
rm -rf .git
git init

# 將 bundle 的內容套用到真實 repo
git clone reply_ai_repo.bundle temp_folder
cp -R temp_folder/. .
rm -rf temp_folder

# 建立初始 commit
git add .
git commit -m "Initial import from bundle"

# 設定 GitHub 遠端並 push
git branch -M main
git remote add origin https://github.com/$(gh api user --jq .login)/chat-booster.git
git push -u origin main

# 安裝套件（雲端環境會跑，不會用到你的 iPhone）
npm ci

# 安裝 Playwright browser（雲端環境可跑）
npx playwright install --with-deps

echo "--------------------------------------------"
echo "🎉 完成！你的專案已推到 chat-booster GitHub。"
echo "🟢 你現在只需：前往 Vercel → Import Project。"
echo "📌 GitHub Actions 會自動：Lint → Test → Build → Deploy → 每30天分析。"
echo "--------------------------------------------"