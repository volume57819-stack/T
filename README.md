# 1. 移除地端 Git（因為現在 repo 是空的）
rm -rf .git

# 2. 初始化 Git
git init

# 3. 用 bundle 匯入真正完整的專案內容
git clone reply_ai_repo.bundle temp_folder
cp -R temp_folder/. .
rm -rf temp_folder

# 4. 建立初始 commit
git add .
git commit -m "Initial import from bundle"

# 5. 設定 main 分支
git branch -M main

# 6. 設定 GitHub 遠端（自動抓你的 GitHub 帳號名稱）
git remote add origin https://github.com/$(gh api user --jq .login)/chat-booster.git

# 7. 推到 GitHub
git push -u origin main

# 8. 安裝所有依賴
npm ci

# 9. 安裝 Playwright（含 browser）
npx playwright install --with-deps

echo "-------------------------------------------------------"
echo "🎉 專案已完整匯入並推送到 GitHub: chat-booster"
echo "🟢 下一步：到 Vercel → Import → Chat-Booster → Deploy"
echo "📌 之後所有：commit / test / e2e / monthly AI 會全部自動化"
echo "-------------------------------------------------------"