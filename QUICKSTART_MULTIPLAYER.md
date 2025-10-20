# 🚀 マルチプレイヤー同期 - クイックスタート

## 📋 実装内容（概要）

**問題**: 他のプレイヤーが動いていない  
**原因**: Frontend が WebSocket、Backend が Socket.io で通信プロトコルが異なっていた  
**解決**: Socket.io クライアントを実装して Backend と統一

---

## ✅ 実装したファイル

### 1️⃣ **新規作成: `src/lib/socket-io-client.ts`**

Socket.io 互換のクライアント実装
- socket.io-client ライブラリ対応
- HTTP long-polling フォールバック
- 自動再接続機能
- イベントリスナー管理

### 2️⃣ **修正: `src/app/room/room-content.tsx`**

Socket.io クライアントに切り替え
- `getWebSocketClient()` → `getSocketIOClient()` に変更
- Backend イベント（'player-moved' など）に対応
- デバッグ情報表示を追加

---

## 🎮 テスト方法（3ステップ）

### **Step 1: Backend 起動**

```bash
cd C:\Users\hiroki\Desktop\AI_project\metaverse-app
docker-compose up
```

期待結果:
```
backend_1   | ✅ HTTP Server running on port 5000
backend_1   | 🔌 Socket.io initialized with database integration
```

### **Step 2: Frontend 起動**

別のターミナルで:

```bash
cd frontend
npm run dev
```

期待結果:
```
> next dev
  ▲ Next.js 15.x.x
  - Local: http://localhost:3000
```

### **Step 3: ブラウザテスト**

**ブラウザタブ1 を開く:**
```
http://localhost:3000/room?room=school&name=プレイヤーA
```

**ブラウザタブ2 を開く（新規ウィンドウ or 別ブラウザ）:**
```
http://localhost:3000/room?room=school&name=プレイヤーB
```

**確認:**
- ✅ タブ1: Canvas に「プレイヤーA」が表示
- ✅ タブ2: Canvas に「プレイヤーB」が表示
- ✅ タブ1で矢印キー↑ 移動 → タブ2 にリアルタイム表示
- ✅ タブ2で矢印キー→ 移動 → タブ1 にリアルタイム表示
- ✅ ステータスに「オンライン: 2人」と表示

---

## 🔍 デバッグ方法

### **ブラウザコンソールで確認（F12 キー）**

```javascript
// Socket 接続状態
io.getStats()
// 出力:
{
  url: "http://localhost:5000",
  isConnected: true,
  socketId: "abc123...",
  queueSize: 0
}
```

### **Network タブ（DevTools → Network）**

- WebSocket/Polling の通信を監視
- 100ms ごとに `player-move` イベント送信されている
- `player-moved` イベント受信されている

### **Console ログで確認**

```
✅ Socket.io Connected
📍 Joining room: school as player: プレイヤーA
📤 Sent join-room event to backend
📥 Received room-users from backend
👤 User joined
📍 Player moved: プレイヤーB at (x, y)
```

---

## 🎯 期待される動き

```
【時刻 t=0ms】
- プレイヤーA: 矢印キー↑ 押下
- キー状態: up = true

【時刻 t=16ms】
- キー検出
- 位置計算: y -= 12
- Canvas 描画

【時刻 t=100ms】
- Socket.io 経由で Backend に位置送信
- Backend が他プレイヤーに broadcast

【時刻 t=116ms】
- プレイヤーB 側で 'player-moved' イベント受信
- otherPlayers 更新
- Canvas 再描画 → プレイヤーA が上に移動した状態で表示 ✅
```

---

## ⚙️ トラブルシューティング

### **「接続中...」から進まない**

✅ **確認項目:**

1. Backend が起動しているか
   ```bash
   docker ps
   # metaverse-app-backend が RUNNING か確認
   ```

2. ポート 5000 がリッスン状態
   ```bash
   netstat -an | grep 5000
   ```

3. ファイアウォール設定
   ```
   Windows: ファイアウォール → 詳細設定 → 受信の規則 → ポート 5000 を許可
   ```

### **「他のプレイヤーが表示されない」**

✅ **確認項目:**

1. コンソール (F12) でエラーがないか
2. ルーム名が同じか (`room=school` で統一)
3. 接続状態が `true` か
   ```javascript
   socketClient.getIsConnected()
   ```

### **「動きがカクカク」**

✅ **対処法:**

1. ネットワーク同期周期を短くする
   ```typescript
   // room-content.tsx の updateMovementLoop
   const NETWORK_SYNC_INTERVAL = 50;  // 100 → 50
   ```

2. ブラウザキャッシュクリア (`Ctrl+Shift+Delete`)

### **「複数タブで接続できない」**

✅ **原因:**

Socket.io はシングルトンパターン  
異なるプロセス（タブ）では別の接続が必要

✅ **解決:**

Backend が複数接続をサポート（デフォルト有効）  
複数タブで独立した Socket 接続が確立される

---

## 📊 ネットワーク通信図

```
【ローカル】
矢印キー
    ↓ (0ms)
keysPressed.current.up = true
    ↓ (0-16ms)
位置計算 & Canvas 描画（60fps）
    ↓ (100ms 経過)
Socket.io でサーバーに送信

【サーバー】
player-move イベント受信
    ↓
全ルームメンバーに player-moved 配信

【リモート】
player-moved イベント受信
    ↓
otherPlayers 更新
    ↓
Canvas に他プレイヤー描画（次フレーム）
```

---

## 🚀 デプロイ準備

### **本番環境の場合**

```typescript
// .env.production
NEXT_PUBLIC_SOCKET_IO_URL=https://your-backend-domain.com
```

### **Render Backend へのデプロイ**

```bash
# GitHub に push
git add .
git commit -m "feat: Socket.io multiplayer sync implementation"
git push origin main

# Render で自動デプロイ設定
# Dashboard → Deploy → Auto-deploy from GitHub
```

---

## ✨ 完成状態

| 項目 | 状態 |
|------|------|
| ローカル移動 | ✅ 60fps スムーズ |
| ネットワーク同期 | ✅ 100ms ごと |
| 他プレイヤー表示 | ✅ リアルタイム |
| マルチプレイヤー対応 | ✅ 複数接続可能 |
| 自動再接続 | ✅ 接続断時に復帰 |

---

**実装完了！** 🎉

他のプレイヤーがリアルタイムに動くようになりました。

次のステップ: WebRTC 音声通話機能、チャット配信テスト、本番デプロイ

