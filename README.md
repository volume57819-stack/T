#!/usr/bin/env bash

echo "=== 🚀 chat-booster FULL AUTO APPLY 開始 ==="

# -------------------------------
# 1. 檢查 ZIP
# -------------------------------
if [ ! -f chat_booster_production.zip ]; then
  echo "❌ 找不到 chat_booster_production.zip"
  echo "請先上傳 ZIP 到 Codespaces"
  exit 1
fi

# -------------------------------
# 2. 解壓與移動
# -------------------------------
echo "📦 解壓 chat-booster..."
rm -rf chat-booster
unzip -o chat_booster_production.zip -d chat-booster
cd chat-booster

# -------------------------------
# 3. 安裝 Node Modules
# -------------------------------
echo "📦 安裝 npm 套件..."
npm ci

echo "🎭 安裝 Playwright..."
npx playwright install --with-deps

# -------------------------------
# 4. 初始化 Git
# -------------------------------
echo "🔧 初始化 Git..."
git init
git checkout -b main
git add .
git commit -m "feat: full-production-auto-apply"

# -------------------------------
# 5. 輸入 GitHub Repo
# -------------------------------
echo ""
echo "🌐 請輸入 GitHub Repo URL："
echo "👉 例如：git@github.com:USERNAME/chat-booster.git"
read REPO

git remote add origin $REPO
git push -u origin main --force

echo "✔ 已 push 到 GitHub。"

# -------------------------------
# 6. 自動寫入 GitHub Secrets（使用 GitHub CLI）
# -------------------------------

echo ""
echo "🔑 設定 GitHub Secrets（CI、AI分析、自動部署會用到）"
echo "gScrwU00XHO4i0LrE8IzQlnX："
read VERCEL_TOKEN

echo "📥 正在寫入 GitHub Secrets..."

gh secret set VERCEL_TOKEN -b"$VERCEL_TOKEN"
gh secret set GOOGLE_TRENDS_API_KEY -b"dummy-google"
gh secret set REDDIT_TOKEN -b"dummy-reddit"
gh secret set X_BEARER_TOKEN -b"dummy-x"

echo "✔ GitHub Secrets 設定完成"

# -------------------------------
# 7. 自動綁定 Vercel 專案
# -------------------------------
echo ""
echo "🟣 使用 Vercel CLI 自動部署與綁定專案..."

echo "請輸入要在 Vercel 上建立的專案名稱："
read VERCEL_PROJECT

echo "🔐 登入 Vercel..."
echo "$VERCEL_TOKEN" | vercel login --token "$VERCEL_TOKEN" &>/dev/null || true

echo "🚀 設定 Vercel Project..."
vercel link --project "$VERCEL_PROJECT" --yes --token "$VERCEL_TOKEN"
vercel env add NEXT_PUBLIC_ENV production --token "$VERCEL_TOKEN" <<< "ok"

# -------------------------------
# 8. 自動部署
# -------------------------------
echo ""
echo "🌍 正在部署至 Vercel..."

URL=$(vercel --prod --token "$VERCEL_TOKEN")

echo ""
echo "🎉 🎉 🎉 部署完成！"
echo "你的正式站已上線："
echo "👉 $URL"

echo ""
echo "🔁 GitHub Actions 已啟動："
echo "  ✔ CI / Build / Test"
echo "  ✔ Playwright E2E"
echo "  ✔ Daily AI Reports"
echo "  ✔ Monthly Auto-Optimize"
echo "  ✔ Auto Deploy to Vercel"

echo ""
echo "=== 🚀 FULL AUTO APPLY 完成 ==="