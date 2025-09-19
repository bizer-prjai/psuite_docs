---
title: API認証
description: PSuite APIの認証方法について説明します
---

# API認証

PSuite APIを使用するための認証方法を説明します。

## 認証方式

PSuite APIは以下の認証方式をサポートしています：

- **APIキー認証** (推奨)
- **OAuth 2.0**
- **JWT トークン**

## APIキー認証

### APIキーの取得

1. PSuiteにログイン
2. 設定画面の「API設定」タブを開く
3. 「新しいAPIキーを生成」をクリック
4. キーの名前と権限を設定
5. 生成されたAPIキーを安全に保存

### APIキーの使用方法

HTTPヘッダーにAPIキーを含めてリクエストを送信：

\`\`\`bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     https://api.psuite.example.com/v1/projects
\`\`\`

### 権限レベル

APIキーには以下の権限レベルを設定できます：

- **読み取り専用**: データの取得のみ
- **読み書き**: データの取得・更新
- **管理者**: 全ての操作が可能

## OAuth 2.0認証

### 認証フロー

1. **認証URL**: ユーザーを認証ページにリダイレクト
2. **認証コード取得**: ユーザーが認証後、認証コードを受け取り
3. **アクセストークン取得**: 認証コードをアクセストークンに交換

### 実装例

\`\`\`javascript
// Step 1: 認証URLの生成
const authUrl = `https://auth.psuite.example.com/oauth/authorize?` +
  `client_id=${CLIENT_ID}&` +
  `redirect_uri=${REDIRECT_URI}&` +
  `response_type=code&` +
  `scope=read write`;

// Step 2: アクセストークンの取得
const response = await fetch('https://auth.psuite.example.com/oauth/token', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    grant_type: 'authorization_code',
    client_id: CLIENT_ID,
    client_secret: CLIENT_SECRET,
    code: authorizationCode,
    redirect_uri: REDIRECT_URI,
  }),
});

const { access_token } = await response.json();
\`\`\`

## エラーハンドリング

### 認証エラー

| ステータスコード | エラー | 説明 |
|---|---|---|
| 401 | Unauthorized | APIキーが無効または期限切れ |
| 403 | Forbidden | 権限が不足している |
| 429 | Too Many Requests | レート制限に達している |

### エラーレスポンス例

\`\`\`json
{
  "error": {
    "code": "INVALID_API_KEY",
    "message": "提供されたAPIキーが無効です",
    "details": "APIキーの有効期限が切れているか、無効化されています"
  }
}
\`\`\`

## セキュリティのベストプラクティス

- APIキーは環境変数で管理
- HTTPS通信の使用を必須とする
- 定期的なAPIキーのローテーション
- 最小権限の原則に従った権限設定

次のステップ：[プロジェクト API](./projects)の使用方法を確認してください。
