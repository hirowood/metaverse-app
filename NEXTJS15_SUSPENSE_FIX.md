# ✅ Next.js 15 Suspense エラー解決ガイド

**エラー**: `useSearchParams() should be wrapped in a suspense boundary`

---

## 🔴 **根本的な問題**

### **Next.js 15 の設計**

```typescript
// ❌ これはエラーになる（SSR中に useSearchParams() が実行されない）
'use client';

export default function RoomPage() {
  const searchParams = useSearchParams();  // ← SSR中は URL パラメータが存在しない
  const roomName = searchParams.get('room');
  // ...
}
```

### **原因**

```
【Next.js 15 App Router の流れ】

1. サーバーサイドで コンポーネントをレンダリング（SSR）
   ├─ useSearchParams() を実行しようとする
   └─ ❌ エラー：URLパラメータが存在しない

2. クライアントサイドで ハイドレーション
   ├─ useSearchParams() が正常に動作
   └─ ✅ OK

【解決方法】
SSR中の実行をスキップするため、Suspense で囲む
```

---

## ✅ **実装した修正**

### **構造**

```
/app/room/
├─ page.tsx          ← Suspense でラップするだけ
└─ room-content.tsx  ← 実際のコンポーネント（useSearchParams 使用）
```

### **修正内容**

#### **1. page.tsx（Suspense でラップ）**

```typescript
'use client';

import { Suspense } from 'react';
import RoomContent from './room-content';

export default function RoomPage() {
  return (
    <Suspense fallback={<LoadingFallback />}>
      <RoomContent />
    </Suspense>
  );
}

function LoadingFallback() {
  return (
    <div className="min-h-screen bg-gray-900 flex flex-col items-center justify-center p-4">
      <div className="text-center">
        <h1 className="text-3xl font-bold text-white mb-4">🌍 メタバース</h1>
        <p className="text-gray-400">ルームを読み込み中...</p>
        <div className="mt-6 flex justify-center">
          <div className="animate-spin rounded-full h-12 w-12 border-4 border-white border-t-blue-500"></div>
        </div>
      </div>
    </div>
  );
}
```

#### **2. room-content.tsx（元のコンポーネント）**

```typescript
'use client';

import { useSearchParams } from 'next/navigation';

export default function RoomContent() {
  // ✅ Suspense 内で実行されるため、useSearchParams() が安全に動作
  const searchParams = useSearchParams();
  const roomName = searchParams.get('room') || 'school';
  
  // ... 元のコンポーネント実装
}
```

---

## 🌳 **解決メカニズム（ツリー構造）**

```
【SSR フェーズ】
Server でコンポーネントをレンダリング
    ├─ page.tsx の Suspense を評価
    ├─ fallback (LoadingFallback) をレンダリング
    └─ RoomContent はスキップ（Suspense 内だから）

【クライアント フェーズ】
ハイドレーション開始
    ├─ useSearchParams() が使用可能な状態
    ├─ RoomContent をレンダリング
    └─ LoadingFallback は置き換わる

【ユーザー体験】
1. サーバーから HTML を受け取る（ローディング画面）
2. ブラウザで useSearchParams() を実行
3. RoomContent がレンダリングされる
```

---

## 📊 **修正前後の比較**

| 側面 | Before | After |
|------|--------|-------|
| **構造** | 単一ファイル | 2ファイル分割 |
| **Suspense** | なし | あり |
| **SSR互換性** | ❌ エラー | ✅ 安全 |
| **UX** | N/A | ローディング画面で改善 |

---

## 🚀 **次のステップ**

### **Step 1: ビルド実行**

```bash
cd C:\Users\hiroki\Desktop\AI_project\metaverse-app\frontend
npm run build
```

**期待結果**:
```
✓ Compiled successfully
✓ Created optimized production build
```

### **Step 2: 開発サーバー起動**

```bash
npm run dev
```

### **Step 3: ブラウザでアクセス**

```
http://localhost:3000/room?room=school&name=太郎
```

**確認項目**:
- ✅ ローディング画面が一瞬表示される
- ✅ ルームが正常に読み込まれる
- ✅ Canvas にアバターが表示される
- ✅ WebSocket が接続される

---

## 💡 **Next.js 15 での重要なポイント**

### **ルール1: useSearchParams は Client Component でのみ使用可能**

```typescript
// ❌ Server Component（デフォルト）では使用不可
export default function Page() {
  const params = useSearchParams();  // ← エラー
}

// ✅ 'use client' 宣言して、Suspense で囲む
'use client';

export default function Page() {
  return (
    <Suspense fallback={...}>
      <Content />
    </Suspense>
  );
}
```

### **ルール2: Client Component内でもSSRが試みられる**

```
Client Component = 'use client' で宣言したコンポーネント
↓
でも Next.js は SSR (Static Generation) を試みる
↓
その際に useSearchParams() が使用不可
↓
Suspense で囲むことで SSR時のスキップを指示
```

### **ルール3: useSearchParams + Next.js 15 の基本パターン**

```typescript
// page.tsx
'use client';
import { Suspense } from 'react';

export default function Page() {
  return (
    <Suspense fallback={<Loading />}>
      <PageContent />
    </Suspense>
  );
}

// page-content.tsx
'use client';
import { useSearchParams } from 'next/navigation';

export default function PageContent() {
  const params = useSearchParams();
  // ...
}
```

---

**修正完了！ビルド・起動してみてください。** 🚀

