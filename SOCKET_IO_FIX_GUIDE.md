# 🔧 Socket.io 接続エラー修正ガイド

## 📋 実施した修正内容

### 1. **socket-io-client.ts を完全書き換え** ✅

**問題点:**
- HTTP long-polling の手動実装が不完全
- Socket.io ハンドシェイクプロトコルに従わない
- `sid=null` のまま GET リクエストを送信

**解決方法:**
- `socket.io-client` NPM パッケージを直接使用
- 標準的な Socket.io クライアント実装に統一

**修正内容:**
```typescript
// Before: 手動実装版 (失敗)
connectWithPolling() { /* 複雑で不完全 */ }

// After: socket.io-client 使用 (成功)
import { io, Socket } from 'socket.io-client';
this.socket = io(this.url, { /* 標準オプション */ });
```

### 2. **frontend/.env.local に Socket.io URL を明示化** ✅

```env
# 追加した環境変数
NEXT_PUBLIC_SOCKET_IO_URL=http://localhost:5000
```

### 3. **room-content.tsx イベント名を修正**

**必要な変更:**
```typescript
// Before (失敗):
socketClient.on('open', () => { /* ... */ });
socketClient.on('error', () => { /* ... */ });
socketClient.on('close', () => { /* ... */ });

// After (成功):
socketClient.on('connect', () => { /* ... */ });
socketClient.on('connect_error', () => { /* ... */ });
socketClient.on('disconnect', () => { /* ... */ });
```

---

## 🚀 実行手順

### Step 1: フロントエンド再ビルド

```bash
cd frontend
npm run build
```

### Step 2: バックエンドが起動していることを確認

```bash
# ターミナルで確認
npm run dev  # backend ディレクトリ内

# ログに以下が表示されれば OK:
# ✅ HTTP Server running on port 5000
```

### Step 3: フロントエンド起動

```bash
cd frontend
npm run dev  # port 3000
```

### Step 4: ブラウザで検証

1. `http://localhost:3000` にアクセス
2. ルーム（学校/ギャラリー/公園）を選択してプレイヤー名を入力
3. コンソールで以下のログを確認:
   ```
   📡 Connecting to Socket.io server...
   ✅ Socket.io connected (ID: xxxxx)
   📤 Sent join-room event to backend
   ```

---

## 🔍 トラブルシューティング

### もし「Still getting 400 Bad Request」が表示される場合:

**原因:** バックエンド側の設定

1. `backend/.env.local` を確認:
   ```env
   PORT=5000
   NODE_ENV=development
   FRONTEND_URL=http://localhost:3000
   ```

2. CORS 設定を確認 (`backend/src/config/cors.ts`):
   ```typescript
   const allowedOrigins = [
     'http://localhost:3000',   // ← フロントエンドのポートがあるか?
     'http://localhost:5000',   // ← バックエンドも許可されているか?
   ];
   ```

3. バックエンドを再起動:
   ```bash
   npm run dev
   ```

### もし「接続中...」が進まない場合:

**原因:** バックエンドが起動していない

```bash
# バックエンド確認
curl -i http://localhost:5000/socket.io/?EIO=4&transport=polling

# 200 OK が返ればOK
# Connection refused が返ればバックエンドを起動
```

---

## 📊 修正前後の比較

| 項目 | Before | After |
|------|--------|-------|
| 実装方法 | HTTP polling 手動実装 | socket.io-client NPM |
| ハンドシェイク | ❌ 不正形式 | ✅ 標準プロトコル |
| SID 管理 | ❌ sid=null のまま | ✅ 自動管理 |
| イベント名 | ❌ 'open', 'close' | ✅ 'connect', 'disconnect' |
| WebSocket 対応 | ❌ polling のみ | ✅ WebSocket + polling |
| 自動再接続 | ⚠️ 手動実装 | ✅ 内蔵 |

---

## 🎯 次のステップ

修正後に以下を確認してください:

1. ✅ Socket.io 接続成功
2. ✅ マルチプレイヤー同期（複数ブラウザで確認）
3. ✅ チャット機能
4. ✅ Canvas での avatar 表示

何か問題があれば、ブラウザのコンソール (`F12 > Console`) のエラーメッセージを共有してください。
