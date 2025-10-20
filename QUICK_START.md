#!/bin/bash
# 🧹 Docker 環境リセットスクリプト (macOS/Linux)
# EACCES: permission denied エラーを解決します

set -e

echo "🧹 Docker environment をリセット中..."
echo ""

# 色付け出力
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# 1. コンテナを停止
echo -e "${YELLOW}📦 コンテナを停止中...${NC}"
docker-compose down
echo -e "${GREEN}✅ コンテナ停止完了${NC}"
echo ""

# 2. ボリュームをクリア
echo -e "${YELLOW}🗑️  ボリュームをクリア中...${NC}"
docker-compose down -v
echo -e "${GREEN}✅ ボリュームクリア完了${NC}"
echo ""

# 3. イメージを再ビルド
echo -e "${YELLOW}🔨 イメージを再ビルド中...${NC}"
docker-compose build --no-cache
echo -e "${GREEN}✅ イメージビルド完了${NC}"
echo ""

# 4. システム全体のクリーンアップ
echo -e "${YELLOW}🧹 Docker システムをクリーンアップ中...${NC}"
docker system prune -f
echo -e "${GREEN}✅ システムクリーンアップ完了${NC}"
echo ""

echo -e "${GREEN}═══════════════════════════════════════════════════════${NC}"
echo -e "${GREEN}✨ クリーンアップが完了しました！${NC}"
echo -e "${GREEN}═══════════════════════════════════════════════════════${NC}"
echo ""
echo -e "${YELLOW}🚀 次のコマンドで起動してください：${NC}"
echo -e "${YELLOW}   docker-compose up${NC}"
echo ""
echo -e "${YELLOW}📝 ログを確認する場合：${NC}"
echo -e "${YELLOW}   docker-compose logs -f frontend${NC}"
echo ""
