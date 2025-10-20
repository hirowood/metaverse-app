# 🎯 **今すぐ実行チェックリスト**

> ⏱️ **所要時間**: 30分  
> 📍 **現在位置**: Docker ビルドエラー  
> 🚀 **目標**: backend/frontend 起動・動作確認

---

## ✅ **Step 1: npm install（5分）**

### 📝 **実行コマンド**

```powershell
# PowerShell を起動
# → ここに貼り付けてコピー＆ペーストで実行

cd C:\Users\hiroki\Desktop\AI_project\metaverse-app\backend
Remove-Item package-lock.json -Force -ErrorAction SilentlyContinue
npm install

# 出力例: added 123 packages
```

**チェック**:
- [ ] `npm install` が完了した
- [ ] エラーメッセージがない
- [ ] "added XX packages" と表示された

---

```powershell
# Frontend も同様に

cd ../frontend
Remove-Item package-lock.json -Force -ErrorAction SilentlyContinue
npm install

# 出力例: added 456 packages
```

**チェック**:
- [ ] `npm install` が完了した
- [ ] エラーメッセージがない

---

## ✅ **Step 2: ローカルビルド確認（5分）**

```powershell
# Backend ビルド

cd C:\Users\hiroki\Desktop\AI_project\metaverse-app\backend
npm run build

# 出力例: Successfully compiled XX files
```

**チェック**:
- [ ] ビルドに成功した
- [ ] エラーメッセージがない
- [ ] `dist/` ディレクトリが作成された

---

```powershell
# Frontend ビルド

cd ../frontend
npm run build

# 出力例: Compiled client and server successfully
```

**チェック**:
- [ ] ビルドに成功した
- [ ] エラーメッセージがない
- [ ] `.next/` ディレクトリが作成された

---

## ✅ **Step 3: Docker キャッシュクリア（3分）**

```powershell
cd C:\Users\hiroki\Desktop\AI_project\metaverse-app

# 既存コンテナ停止・削除
docker-compose down -v

# 出力例: Removing metaverse_backend ...
#        Removing metaverse_frontend ...
#        Removing network metaverse_network
```

**チェック**:
- [ ] `docker-compose down -v` が完了した
- [ ] エラーメッセージがない

---

## ✅ **Step 4: Docker イメージ再ビルド（10分）**

```powershell
# キャッシュなしで再ビルド
docker-compose build --no-cache

# 出力は長いので、最後を確認

# 期待される最終出力:
# => => naming to docker.io/library/metaverse_backend:latest
# => => naming to docker.io/library/metaverse_frontend:latest
# ✅ Successfully built
```

**チェック**:
- [ ] Backend イメージビルド成功
- [ ] Frontend イメージビルド成功
- [ ] エラーメッセージがない

---

## ✅ **Step 5: コンテナ起動（3分）**

```powershell
# すべてのサービス起動
docker-compose up -d

# ステータス確認
docker-compose ps

# 期待される出力:
# NAME                STATUS
# metaverse_db        Up (healthy)
# metaverse_redis     Up (healthy)
# metaverse_backend   Up (healthy)
# metaverse_frontend  Up (healthy)
```

**チェック**:
- [ ] 4 つのコンテナが "Up" 状態
- [ ] db が "(healthy)" 状態
- [ ] redis が "(healthy)" 状態

---

## ✅ **Step 6: 動作確認（3分）**

### **6-1: Backend ヘルスチェック**

```powershell
curl http://localhost:5000/health

# 期待される応答:
# {
#   "status": "ok",
#   "timestamp": "2025-10-17T14:30:45.123Z",
#   "uptime": 12.34
# }
```

**チェック**:
- [ ] HTTP 200 を取得
- [ ] `status: ok` が返される

---

### **6-2: Frontend アクセス**

```
ブラウザで以下にアクセス:
http://localhost:3000
```

**期待される画面**:
- [ ] ホーム画面が表示される
- [ ] ニックネーム入力フィールドがある
- [ ] ルーム選択ボタンがある

---

### **6-3: ログ確認**

```powershell
# Backend ログ確認
docker-compose logs backend | Select-String -Pattern "error|Error|ERROR" -Context 2

# エラーがなければOK
# 表示例: "✨ Backend server is ready to accept connections"
```

**チェック**:
- [ ] エラーメッセージがない
- [ ] "Backend server is ready" ログがある

---

```powershell
# Frontend ログ確認
docker-compose logs frontend | Select-String -Pattern "error|Error|ERROR" -Context 2

# エラーがなければOK
```

**チェック**:
- [ ] エラーメッセージがない

---

## 🎉 **すべてチェックされたら成功！**

```
✅ npm install 完了
✅ ローカルビルド成功
✅ Docker イメージビルド成功
✅ コンテナ起動成功
✅ Backend ヘルスチェック成功
✅ Frontend ホーム画面表示
✅ ログにエラーなし

→ 準備完了！
```

---

## 🔧 **もしエラーが出たら**

### **エラー 1: npm ERR! code E**

```powershell
# 完全リセット
cd backend
Remove-Item node_modules -Recurse -Force
Remove-Item package-lock.json -Force
npm install
```

### **エラー 2: Docker build error**

```powershell
# Docker リセット
docker system prune -a --volumes
docker-compose build --no-cache
```

### **エラー 3: ポート競合**

```powershell
# ポートを確認
Get-NetTCPConnection -LocalPort 3000
Get-NetTCPConnection -LocalPort 5000

# プロセス ID を取得して終了
Stop-Process -Id <PID> -Force
```

### **エラー 4: それでも解決しない場合**

```
1. Docker Desktop を再起動
   → 設定 > 再起動

2. WSL を再起動（WSL2使用時）
   $ wsl --shutdown

3. 完全リセット
   $ docker-compose down -v
   $ docker system prune -a --volumes
   $ npm cache clean --force
```

---

## 📞 **詳細な解説が必要な場合**

以下のドキュメントを参照:

- **すべての詳細**: `docs/COMPREHENSIVE_ANALYSIS.md`
- **パッケージ同期**: `docs/DEPENDENCY_SYNC_GUIDE.md`
- **実装ロードマップ**: `docs/IMPLEMENTATION_ROADMAP.md`

---

## 🚀 **次のステップ（成功後）**

1. **Git に commit**
   ```bash
   git add .
   git commit -m "[fix] npm dependencies sync and Dockerfile improvement"
   ```

2. **ニックネームを入力して入室テスト**
   - http://localhost:3000
   - ニックネーム入力
   - ルーム選択（例: "学校"）
   - キーボード矢印キーで移動

3. **次のマイルストーン**
   - Backend Service レイヤー実装
   - 複数ユーザー同時接続テスト

---

**🎯 このチェックリストをすべて完了させて、Docker 環境を起動させましょう！**
