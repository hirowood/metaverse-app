# ✅ **今すぐ実行すべきアクション - 簡潔ガイド**

> 📍 **現在地**: Docker ビルドエラー発生状態  
> 🎯 **目標**: backend/frontend を Docker で起動・確認  
> ⏱️ **所要時間**: 30 分程度

---

## 🚀 **ステップ 1: npm パッケージ同期（5 分）**

### **PowerShell で実行:**

```powershell
# backend ディレクトリに移動
cd C:\Users\hiroki\Desktop\AI_project\metaverse-app\backend

# 古いロックファイルを削除
Remove-Item package-lock.json -Force -ErrorAction SilentlyContinue

# 新しいロックファイル生成
npm install

# 同期確認
npm ci --dry-run

# 出力が「added XX packages」なら成功
```

### **次: frontend**

```powershell
# frontend に移動
cd ../frontend

# 古いロックファイル削除
Remove-Item package-lock.json -Force -ErrorAction SilentlyContinue

# 新規インストール
npm install

# 同期確認
npm ci --dry-run
```

---

## 🔨 **ステップ 2: ローカルビルド確認（5 分）**

```powershell
# backend ビルド
cd C:\Users\hiroki\Desktop\AI_project\metaverse-app\backend
npm run build

# ✅ 出力: Successfully compiled XX files

# frontend ビルド
cd ../frontend
npm run build

# ✅ 出力: Compiled client and server successfully
```

---

## 🐳 **ステップ 3: Docker イメージ再ビルド（10 分）**

```powershell
cd C:\Users\hiroki\Desktop\AI_project\metaverse-app

# 古いイメージをクリア
docker-compose down -v

# キャッシュなしで再ビルド
docker-compose build --no-cache

# 出力例（成功時）:
# => => naming to docker.io/library/metaverse_backend:latest
# => => naming to docker.io/library/metaverse_frontend:latest
# ✅ Successfully built
```

---

## ▶️ **ステップ 4: コンテナ起動（3 分）**

```powershell
# すべてのサービス起動
docker-compose up -d

# ステータス確認
docker-compose ps

# 出力例:
# NAME                STATUS
# metaverse_db        Up (healthy)
# metaverse_redis     Up (healthy)
# metaverse_backend   Up (healthy)
# metaverse_frontend  Up (healthy)
```

---

## ✅ **ステップ 5: 動作確認（5 分）**

### **Backend ヘルスチェック**

```powershell
curl http://localhost:5000/health

# 期待される応答:
# {
#   "status": "ok",
#   "timestamp": "2025-10-17T...",
#   "uptime": 12.34
# }
```

### **Frontend アクセス**

```
ブラウザで開く:
http://localhost:3000

✅ ホーム画面が表示されればOK
```

### **ログ確認（エラーがないか）**

```powershell
# backend ログ
docker-compose logs backend

# frontend ログ
docker-compose logs frontend

# エラーがなければ成功
```

---

## 🎯 **もしエラーが出たら**

### **エラー1: npm ci で同期エラー**

```powershell
# 完全リセット
cd backend
Remove-Item node_modules -Recurse -Force
Remove-Item package-lock.json -Force
npm install
```

### **エラー2: Docker ビルド失敗**

```powershell
# キャッシュクリア
docker system prune -a --volumes

# 再ビルド
docker-compose build --no-cache
```

### **エラー3: ポート競合**

```powershell
# 既存プロセス終了
Get-Process | Where-Object {$_.Id -eq (Get-NetTCPConnection -LocalPort 3000).OwningProcess} | Stop-Process
Get-Process | Where-Object {$_.Id -eq (Get-NetTCPConnection -LocalPort 5000).OwningProcess} | Stop-Process
```

### **エラー4: Docker が起動しない**

```powershell
# Docker Desktop を再起動
# または WSL を再起動
wsl --shutdown
```

---

## 📊 **進捗チェック**

| # | タスク | 状態 | コマンド |
|----|--------|------|---------|
| 1 | npm install (backend) | ⏳ | `npm install` |
| 2 | npm install (frontend) | ⏳ | `npm install` |
| 3 | npm run build (backend) | ⏳ | `npm run build` |
| 4 | npm run build (frontend) | ⏳ | `npm run build` |
| 5 | docker-compose build | ⏳ | `docker-compose build --no-cache` |
| 6 | docker-compose up | ⏳ | `docker-compose up -d` |
| 7 | curl localhost:5000/health | ⏳ | `curl http://localhost:5000/health` |
| 8 | Browser localhost:3000 | ⏳ | Open http://localhost:3000 |

---

## 🎉 **成功時の次のステップ**

✅ すべてが成功したら:

1. **Git に commit**
   ```bash
   git add .
   git commit -m "[fix] npm dependencies sync and Dockerfile improvement"
   ```

2. **詳細ドキュメントを読む**
   - `docs/IMPLEMENTATION_ROADMAP.md` - 全体ロードマップ
   - `docs/DEPENDENCY_SYNC_GUIDE.md` - パッケージ同期詳細

3. **次のマイルストーン: Service レイヤー実装**
   - `backend/src/services/userService.ts`
   - `backend/src/services/roomService.ts`
   - `backend/src/services/messageService.ts`

---

**質問がある場合は、ドキュメント参照のうえ、具体的なエラーメッセージを共有してください。**
