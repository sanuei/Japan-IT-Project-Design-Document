# API仕様書 - 動画脚本自動生成ツール

## 1. API概要

### 1.1 ベースURL
```
Production: https://api.videoscript-generator.com/v1
Development: http://localhost:3000/api/v1
```

### 1.2 認証方式
- JWT (JSON Web Token) による Bearer Token認証
- APIキーによる認証（サードパーティ統合用）

### 1.3 レスポンス形式
すべてのAPIレスポンスは以下の形式に従います：

```json
{
  "success": boolean,
  "data": object | array | null,
  "error": {
    "code": string,
    "message": string,
    "details": object
  } | null,
  "meta": {
    "timestamp": string,
    "requestId": string,
    "version": string
  }
}
```

### 1.4 エラーコード
| HTTPステータス | エラーコード | 説明 |
|---|---|---|
| 400 | VALIDATION_ERROR | リクエストパラメータの検証エラー |
| 401 | UNAUTHORIZED | 認証が必要 |
| 403 | FORBIDDEN | アクセス権限がない |
| 404 | NOT_FOUND | リソースが見つからない |
| 429 | RATE_LIMIT_EXCEEDED | レート制限を超過 |
| 500 | INTERNAL_ERROR | サーバー内部エラー |
| 502 | LLM_ERROR | LLM APIエラー |
| 503 | SERVICE_UNAVAILABLE | サービス利用不可 |

## 2. 認証API

### 2.1 ユーザー登録
```http
POST /auth/register
```

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123",
  "name": "山田太郎",
  "language": "ja"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "name": "山田太郎",
      "settings": {
        "language": "ja",
        "theme": "light"
      },
      "subscription": {
        "plan": "free",
        "usageCount": 0,
        "usageLimit": 10
      }
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "refresh_token_here"
  }
}
```

### 2.2 ログイン
```http
POST /auth/login
```

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "name": "山田太郎"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "refresh_token_here"
  }
}
```

### 2.3 トークン更新
```http
POST /auth/refresh
```

**Request Body:**
```json
{
  "refreshToken": "refresh_token_here"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "token": "new_jwt_token_here",
    "refreshToken": "new_refresh_token_here"
  }
}
```

### 2.4 ログアウト
```http
POST /auth/logout
```

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": null
}
```

## 3. マインドマップAPI

### 3.1 マインドマップ一覧取得
```http
GET /mindmaps
```

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
| パラメータ | 型 | 必須 | 説明 |
|---|---|---|---|
| page | number | No | ページ番号 (デフォルト: 1) |
| limit | number | No | 1ページあたりの件数 (デフォルト: 20) |
| search | string | No | 検索キーワード |
| sortBy | string | No | ソート項目 (createdAt, updatedAt, title) |
| sortOrder | string | No | ソート順 (asc, desc) |

**Response:**
```json
{
  "success": true,
  "data": {
    "mindmaps": [
      {
        "id": "mindmap_123",
        "title": "YouTubeチャンネル戦略",
        "description": "チャンネル成長のための戦略",
        "nodeCount": 15,
        "createdAt": "2024-01-15T10:30:00Z",
        "updatedAt": "2024-01-16T14:20:00Z",
        "tags": ["YouTube", "マーケティング"]
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 45,
      "totalPages": 3
    }
  }
}
```

### 3.2 マインドマップ作成
```http
POST /mindmaps
```

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "title": "新しいマインドマップ",
  "description": "動画企画用のマインドマップ",
  "nodes": [
    {
      "id": "node_1",
      "text": "メインテーマ",
      "children": ["node_2", "node_3"],
      "level": 0,
      "position": { "x": 0, "y": 0 }
    },
    {
      "id": "node_2",
      "text": "サブテーマ1",
      "children": [],
      "level": 1,
      "position": { "x": 200, "y": -100 }
    },
    {
      "id": "node_3",
      "text": "サブテーマ2",
      "children": [],
      "level": 1,
      "position": { "x": 200, "y": 100 }
    }
  ],
  "tags": ["動画企画", "YouTube"]
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "mindmap_124",
    "title": "新しいマインドマップ",
    "description": "動画企画用のマインドマップ",
    "nodes": [...],
    "metadata": {
      "totalNodes": 3,
      "maxDepth": 1,
      "tags": ["動画企画", "YouTube"]
    },
    "createdAt": "2024-01-17T09:15:00Z",
    "updatedAt": "2024-01-17T09:15:00Z"
  }
}
```

### 3.3 マインドマップ取得
```http
GET /mindmaps/{id}
```

**Headers:**
```
Authorization: Bearer <token>
```

**Path Parameters:**
| パラメータ | 型 | 必須 | 説明 |
|---|---|---|---|
| id | string | Yes | マインドマップID |

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "mindmap_123",
    "title": "YouTubeチャンネル戦略",
    "description": "チャンネル成長のための戦略",
    "nodes": [
      {
        "id": "node_1",
        "text": "チャンネル戦略",
        "children": ["node_2", "node_3"],
        "level": 0,
        "position": { "x": 0, "y": 0 },
        "style": {
          "color": "#333333",
          "backgroundColor": "#f0f0f0"
        }
      }
    ],
    "metadata": {
      "totalNodes": 15,
      "maxDepth": 3,
      "tags": ["YouTube", "マーケティング"]
    },
    "createdAt": "2024-01-15T10:30:00Z",
    "updatedAt": "2024-01-16T14:20:00Z"
  }
}
```

### 3.4 マインドマップ更新
```http
PUT /mindmaps/{id}
```

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "title": "更新されたタイトル",
  "description": "更新された説明",
  "nodes": [...],
  "tags": ["更新", "タグ"]
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "mindmap_123",
    "title": "更新されたタイトル",
    "updatedAt": "2024-01-17T11:45:00Z"
  }
}
```

### 3.5 マインドマップ削除
```http
DELETE /mindmaps/{id}
```

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": null
}
```

## 4. 脚本生成API

### 4.1 脚本生成
```http
POST /scripts/generate
```

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "mindmapId": "mindmap_123",
  "options": {
    "tone": "professional",
    "style": "informative",
    "duration": 10,
    "language": "ja",
    "targetAudience": "ビジネスパーソン",
    "keywords": ["YouTube", "マーケティング", "戦略"]
  }
}
```

**Request Parameters:**
| パラメータ | 型 | 必須 | 説明 |
|---|---|---|---|
| mindmapId | string | Yes | マインドマップID |
| options.tone | enum | Yes | トーン (professional, casual, educational) |
| options.style | enum | Yes | スタイル (informative, entertaining, persuasive) |
| options.duration | number | Yes | 動画時間（分） |
| options.language | string | Yes | 言語コード (ja, en, zh) |
| options.targetAudience | string | No | ターゲット層 |
| options.keywords | array | No | キーワード配列 |

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "generation_456",
    "titles": [
      "YouTube成功の秘訣：チャンネル戦略完全ガイド",
      "登録者数10万人突破！効果的なYouTube運営術",
      "初心者でもできる！YouTubeチャンネル成長戦略",
      "YouTube収益化への道のり：実践的アプローチ",
      "成功するYouTuberが実践する5つの戦略",
      "YouTubeアルゴリズムを攻略する方法",
      "チャンネル登録者を増やす確実な方法",
      "YouTube動画の再生回数を伸ばすコツ",
      "プロが教えるYouTube運営の基本",
      "YouTubeで稼ぐための実践的戦略"
    ],
    "summary": "YouTubeチャンネルの成長戦略について、初心者から上級者まで実践できる具体的な手法を解説します。登録者数増加、再生回数向上、収益化のポイントを分かりやすく説明し、成功への道筋を示します。",
    "script": "こんにちは、皆さん。今日はYouTubeチャンネルの成長戦略について...",
    "thumbnailGuide": "サムネイルは視覚的インパクトを重視し、以下の要素を含めることを推奨します：\n1. 大きな文字で魅力的なタイトル\n2. 明るい色合いと高いコントラスト\n3. 人物の表情豊かな写真\n4. YouTube関連のアイコンや図表",
    "metadata": {
      "wordCount": 2450,
      "estimatedDuration": 9.8,
      "readabilityScore": 78,
      "keywordDensity": {
        "YouTube": 0.08,
        "チャンネル": 0.06,
        "戦略": 0.05
      }
    },
    "llmProvider": "openai",
    "processingTime": 15.6,
    "createdAt": "2024-01-17T12:30:00Z"
  }
}
```

### 4.2 脚本生成履歴
```http
GET /scripts/history
```

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
| パラメータ | 型 | 必須 | 説明 |
|---|---|---|---|
| page | number | No | ページ番号 |
| limit | number | No | 1ページあたりの件数 |
| mindmapId | string | No | マインドマップIDでフィルタ |

**Response:**
```json
{
  "success": true,
  "data": {
    "scripts": [
      {
        "id": "generation_456",
        "mindmapId": "mindmap_123",
        "mindmapTitle": "YouTubeチャンネル戦略",
        "selectedTitle": "YouTube成功の秘訣：チャンネル戦略完全ガイド",
        "options": {
          "tone": "professional",
          "style": "informative",
          "duration": 10
        },
        "metadata": {
          "wordCount": 2450,
          "estimatedDuration": 9.8
        },
        "createdAt": "2024-01-17T12:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 3,
      "totalPages": 1
    }
  }
}
```

### 4.3 脚本詳細取得
```http
GET /scripts/{id}
```

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "generation_456",
    "mindmapId": "mindmap_123",
    "titles": [...],
    "summary": "...",
    "script": "完全な脚本内容...",
    "thumbnailGuide": "...",
    "metadata": {...},
    "createdAt": "2024-01-17T12:30:00Z"
  }
}
```

## 5. インスピレーションAPI

### 5.1 推薦取得
```http
GET /inspiration/recommendations
```

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
| パラメータ | 型 | 必須 | 説明 |
|---|---|---|---|
| mindmapId | string | No | 基準となるマインドマップID |
| category | string | No | カテゴリフィルタ |
| limit | number | No | 取得件数 (デフォルト: 20) |
| includesTrending | boolean | No | トレンド情報を含める |

**Response:**
```json
{
  "success": true,
  "data": {
    "recommendations": [
      {
        "id": "insp_789",
        "title": "TikTokマーケティング戦略",
        "description": "短尺動画を活用したマーケティング手法",
        "category": "ソーシャルメディア",
        "tags": ["TikTok", "マーケティング", "SNS"],
        "relevanceScore": 0.85,
        "trending": true,
        "trendingRank": 3,
        "relatedTopics": [
          "インフルエンサーマーケティング",
          "バイラル動画",
          "ソーシャルコマース"
        ]
      }
    ],
    "trending": {
      "topics": [
        {
          "keyword": "AI活用",
          "score": 0.92,
          "growthRate": 0.15
        }
      ],
      "updatedAt": "2024-01-17T08:00:00Z"
    }
  }
}
```

### 5.2 推薦フィードバック
```http
POST /inspiration/feedback
```

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "recommendationId": "insp_789",
  "feedback": "helpful",
  "rating": 5,
  "comment": "とても参考になりました"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "message": "フィードバックを受け付けました"
  }
}
```

## 6. ユーザー設定API

### 6.1 設定取得
```http
GET /users/settings
```

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "userId": "user_123",
    "settings": {
      "language": "ja",
      "defaultTone": "professional",
      "preferredStyle": "informative",
      "theme": "light",
      "notifications": {
        "email": true,
        "browser": true,
        "generationComplete": true,
        "weeklyReport": false
      },
      "teleprompter": {
        "fontSize": 24,
        "scrollSpeed": 50,
        "backgroundColor": "#000000",
        "textColor": "#ffffff",
        "autoScroll": true
      }
    }
  }
}
```

### 6.2 設定更新
```http
PUT /users/settings
```

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "settings": {
    "language": "ja",
    "defaultTone": "casual",
    "theme": "dark",
    "teleprompter": {
      "fontSize": 28,
      "scrollSpeed": 60
    }
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "message": "設定を更新しました"
  }
}
```

### 6.3 使用量確認
```http
GET /users/usage
```

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "subscription": {
      "plan": "premium",
      "expiresAt": "2024-12-31T23:59:59Z",
      "status": "active"
    },
    "usage": {
      "current": 45,
      "limit": 100,
      "resetDate": "2024-02-01T00:00:00Z"
    },
    "dailyUsage": [
      {
        "date": "2024-01-17",
        "count": 3
      }
    ]
  }
}
```

## 7. プロンプターAPI

### 7.1 プロンプター設定取得
```http
GET /teleprompter/settings
```

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "settings": {
      "fontSize": 24,
      "scrollSpeed": 50,
      "backgroundColor": "#000000",
      "textColor": "#ffffff",
      "isReversed": false,
      "pauseOnClick": true,
      "autoStart": false,
      "wordHighlight": true
    }
  }
}
```

### 7.2 プロンプター設定更新
```http
PUT /teleprompter/settings
```

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "settings": {
    "fontSize": 28,
    "scrollSpeed": 60,
    "backgroundColor": "#1a1a1a",
    "textColor": "#ffffff"
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "message": "プロンプター設定を更新しました"
  }
}
```

## 8. システムAPI

### 8.1 ヘルスチェック
```http
GET /health
```

**Response:**
```json
{
  "success": true,
  "data": {
    "status": "healthy",
    "timestamp": "2024-01-17T15:30:00Z",
    "version": "1.0.0",
    "uptime": 86400,
    "services": {
      "database": "healthy",
      "redis": "healthy",
      "llm": "healthy"
    }
  }
}
```

### 8.2 システム情報
```http
GET /info
```

**Response:**
```json
{
  "success": true,
  "data": {
    "version": "1.0.0",
    "features": [
      "script-generation",
      "teleprompter",
      "inspiration",
      "mindmap-editor"
    ],
    "limits": {
      "maxMindmapNodes": 100,
      "maxScriptLength": 10000,
      "maxDuration": 30
    }
  }
}
```

## 9. WebSocket API

### 9.1 接続
```javascript
const socket = io('wss://api.videoscript-generator.com', {
  auth: {
    token: 'your-jwt-token'
  }
});
```

### 9.2 脚本生成進捗
```javascript
// 脚本生成開始
socket.emit('script:generate:start', {
  generationId: 'generation_456'
});

// 進捗受信
socket.on('script:generate:progress', (data) => {
  console.log('進捗:', data.progress); // 0-100
  console.log('ステータス:', data.status); // 'analyzing', 'generating', 'formatting'
});

// 完了通知
socket.on('script:generate:complete', (data) => {
  console.log('生成完了:', data.result);
});

// エラー通知
socket.on('script:generate:error', (error) => {
  console.error('生成エラー:', error);
});
```

### 9.3 リアルタイム協業（将来実装予定）
```javascript
// マインドマップの変更通知
socket.emit('mindmap:update', {
  mindmapId: 'mindmap_123',
  changes: [
    {
      type: 'node:add',
      nodeId: 'node_new',
      data: { text: '新しいノード' }
    }
  ]
});

// 他のユーザーの変更を受信
socket.on('mindmap:changed', (data) => {
  console.log('マインドマップが更新されました:', data.changes);
});
```

## 10. レート制限

### 10.1 制限一覧
| エンドポイント | 制限 | 期間 |
|---|---|---|
| /auth/login | 5回 | 15分 |
| /scripts/generate | 10回 | 1時間 |
| /mindmaps | 100回 | 1時間 |
| /inspiration/recommendations | 50回 | 1時間 |
| その他 | 1000回 | 1時間 |

### 10.2 制限超過時のレスポンス
```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "レート制限を超過しました",
    "details": {
      "limit": 10,
      "remaining": 0,
      "resetTime": "2024-01-17T16:30:00Z"
    }
  }
}
```

## 11. 国際化対応

### 11.1 言語コード
| コード | 言語 |
|---|---|
| ja | 日本語 |
| en | 英語 |
| zh | 中国語（簡体字） |
| ko | 韓国語 |

### 11.2 言語指定
```http
# Headerで指定
Accept-Language: ja

# Query parameterで指定
GET /mindmaps?lang=ja
```

## 12. SDKとサンプルコード

### 12.1 JavaScript SDK
```javascript
import { VideoScriptAPI } from '@videoscript/sdk';

const api = new VideoScriptAPI({
  apiKey: 'your-api-key',
  baseURL: 'https://api.videoscript-generator.com/v1'
});

// 脚本生成
const result = await api.scripts.generate({
  mindmapId: 'mindmap_123',
  options: {
    tone: 'professional',
    style: 'informative',
    duration: 10,
    language: 'ja'
  }
});

console.log(result.data.script);
```

### 12.2 Python SDK
```python
from videoscript import VideoScriptAPI

api = VideoScriptAPI(
    api_key='your-api-key',
    base_url='https://api.videoscript-generator.com/v1'
)

# 脚本生成
result = api.scripts.generate(
    mindmap_id='mindmap_123',
    options={
        'tone': 'professional',
        'style': 'informative',
        'duration': 10,
        'language': 'ja'
    }
)

print(result.data.script)
```

この詳細なAPI仕様書により、フロントエンド開発者とバックエンド開発者が一貫したAPIを実装できます。 