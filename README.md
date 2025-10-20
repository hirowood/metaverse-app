# 🌍 Metaverse バーチャルスクール・オンライン展示空間

外出が困難な人（難病・不登校など）が、安全かつ自由にオンライン上で交流・学習・発表できるメタバース空間を構築するプロジェクトです。

## 🎯 プロジェクト概要

| 項目 | 詳細 |
|------|------|
| **システム名** | Metaverse Virtual School & Exhibition Space |
| **対象ユーザー** | 不登校生、難病者、在宅療養者、創作者、一般参加者 |
| **開発技術** | Next.js 15 + Express + WebSocket + WebRTC |
| **デプロイ先** | Vercel（フロント）+ Render（バック） |
| **同時接続数** | 50〜200ユーザー（MVP時） |

## 🚀 クイックスタート

### 必要な環境
- Docker & Docker Compose
- Node.js v20以上（ローカル開発時）
- Git

### セットアップ手順

1. **リポジトリのクローン**
```bash
git clone <repository-url>
cd metaverse-app
```

2. **環境変数の設定**
```bash
cp .env.example .env
```

3. **Docker起動**
```bash
docker-compose up --build
```

4. **ブラウザでアクセス**
- フロントエンド: http://localhost:3000
- バックエンド API: http://localhost:5000
- ヘルスチェック: http://localhost:5000/health

## 📂 プロジェクト構造

```
metaverse-app/
├── frontend/              # Next.js 15 + React + Tailwind CSS
│   ├── src/
│   │   ├── app/          # App Router
│   │   ├── components/   # UI コンポーネント
│   │   ├── hooks/        # カスタムフック
│   │   ├── context/      # Context API
│   │   ├── lib/          # クライアント通信ロジック
│   │   ├── types/        # TypeScript型定義
│   │   ├── styles/       # グローバルスタイル
│   │   └── utils/        # ユーティリティ関数
│   └── public/           # 静的ファイル
│
├── backend/               # Express + Socket.io
│   ├── src/
│   │   ├── config/       # 設定ファイル
│   │   ├── routes/       # REST API
│   │   ├── sockets/      # WebSocket ハンドラー
│   │   ├── services/     # ビジネスロジック
│   │   ├── models/       # DB モデル
│   │   ├── middlewares/  # ミドルウェア
│   │   └── utils/        # ユーティリティ
│   └── tests/            # テスト
│
├── docker/                # Docker設定
├── docs/                  # ドキュメント
├── scripts/               # ビルド・デプロイスクリプト
├── docker-compose.yml
├── .env.example
└── README.md
```

## ✨ 主な機能（MVP）

- ✅ アバター移動（矢印キー、リアルタイム同期）
- ✅ テキストチャット（吹き出し表示）
- ✅ 複数ルーム（学校、展示会場、ショップなど）
- ⏳ 音声通話（WebRTC）
- ⏳ ビデオ通話（オプション）
- ⏳ 展示機能（作品表示）

## 🔧 技術スタック

### フロントエンド
- **Framework**: Next.js 15（App Router）
- **UI**: Tailwind CSS
- **状態管理**: React Context + Hooks
- **通信**: WebSocket、REST API
- **描画**: HTML5 Canvas（2Dドット絵）

### バックエンド
- **Framework**: Express.js
- **リアルタイム**: Socket.io
- **キャッシュ**: Redis
- **データベース**: PostgreSQL
- **Node.js**: v20以上

### インフラ
- **コンテナ**: Docker & Docker Compose
- **フロントデプロイ**: Vercel
- **バックデプロイ**: Render

## 📚 ドキュメント

- [要件定義書](docs/requirements.md)
- [アーキテクチャ設計](docs/architecture.md)
- [API仕様書](docs/api-design.md)
- [デプロイガイド](docs/deployment.md)

## 🎬 開発フェーズ

| フェーズ | 内容 | 状況 |
|-----------|------|------|
| STEP 1 | プロジェクト初期化 | ✅ 完了 |
| STEP 2 | バックエンド基盤 | 🔄 進行中 |
| STEP 3 | フロントエンド基盤 | ⏳ 予定 |
| STEP 4 | MVP機能実装 | ⏳ 予定 |
| STEP 5 | デプロイ準備 | ⏳ 予定 |

## 🛠️ コマンド集

```bash
# Docker起動（開発）
docker-compose up --build

# Docker停止
docker-compose down

# ログ表示
docker-compose logs -f

# バックエンドのみ起動
docker-compose up backend

# フロントエンドのみ起動
docker-compose up frontend
```

## 📝 コミットルール

```
[feature] ユーザー移動機能の実装
[fix] チャット送信時のエラー修正
[docs] README更新
[chore] 依存パッケージ更新
```

## 🤝 開発方針

- **手動実装優先** - 基本概念の理解を最大化
- **段階的実装** - 各フェーズでテスト・検証
- **ドキュメント第一** - 設計→実装の順序を厳密に
- **本番運用を見据える** - スケーリング・運用性を意識

---

**最終更新**: 2025年10月16日  
**開発者**: Hiroki（学習用プロジェクト）
