Pomodoro Timer

https://mkamono.github.io/pomodoro-timer/

## 概要

シンプルで使いやすいポモドーロタイマーです。HTMLファイル一つで完結するシンプルな構成で、PWA（Progressive Web App）として動作し、オフラインでも使用可能です。モバイルデバイスでの使用も考慮されており、レスポンシブデザインを採用しています。

なお、PWA対応のためmanifest.json, service-worker.jsはhtmlとは別ファイルとして配置しています。

## 特徴

- 🍅 **シンプルな構成**: HTMLファイル1つで完結
- 📱 **PWA対応**: インストール可能でオフライン使用可能
- 🌙 **ダークモード**: ライト/ダークモードの切り替え対応
- 📝 **タスク管理**: チェックリスト形式でタスクを管理
- 🔔 **サウンド通知**: タイマー切り替え時に音でお知らせ
- 🔒 **画面保持**: Wake Lock APIで画面を常時起動

## 技術仕様

### フロントエンド
- **Tailwind CSS**: CDN経由で読み込み、スタイルのカスタマイズが容易
```html
<script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
```

### PWA設定
```html
<!-- PWA対応 -->
<link rel="manifest" href="manifest.json" />
<meta name="theme-color" content="#ef4444" />

<!-- アイコン -->
<link rel="icon" type="image/png" href="https://emojicdn.elk.sh/🍅" />
<link rel="apple-touch-icon" href="https://emojicdn.elk.sh/🍅?size=180" />

<!-- iOS PWA対応 -->
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-status-bar-style" content="default" />
<meta name="apple-mobile-web-app-title" content="ポモドーロ" />
```

manifest.json
```json
{
  "name": "ポモドーロタイマー",
  "short_name": "ポモドーロ",
  "description": "シンプルで使いやすいポモドーロタイマー",
  "start_url": "/",
  "display": "standalone",
  "theme_color": "#ef4444",
  "background_color": "#ffffff",
  "orientation": "portrait",
  "scope": "/",
  "lang": "ja",
  "icons": [
    {
      "src": "https://emojicdn.elk.sh/🍅?size=72",
      "sizes": "72x72",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "https://emojicdn.elk.sh/🍅?size=96",
      "sizes": "96x96",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "https://emojicdn.elk.sh/🍅?size=128",
      "sizes": "128x128",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "https://emojicdn.elk.sh/🍅?size=144",
      "sizes": "144x144",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "https://emojicdn.elk.sh/🍅?size=152",
      "sizes": "152x152",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "https://emojicdn.elk.sh/🍅?size=192",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "https://emojicdn.elk.sh/🍅?size=384",
      "sizes": "384x384",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "https://emojicdn.elk.sh/🍅?size=512",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any"
    }
  ],
  "categories": ["productivity", "utilities"],
  "screenshots": []
}
```

### サービスワーカー
画面を常時起動するため、Wake Lock APIを使用しています。

## 使用方法

### 画面構成
アプリケーションは3つの画面で構成されています：

1. **トップ画面**: 作業・休憩開始ボタン
2. **作業画面**: 25分タイマー + タスク管理(タスクの追加・編集・完了)
3. **休憩画面**: 5分タイマー + タスク管理(次の作業タスクの追加・編集)

### 基本操作

#### トップ画面
- **作業を開始**: 作業画面に移動（タイマーは一時停止状態）
- **休憩を開始**: 休憩画面に移動（タイマーは自動開始）

#### 作業・休憩画面
- **タイマー表示**: 文字と円グラフで残り時間を表示
- **タイマー操作**: 円グラフをクリックで一時停止・再開。タイマーの状態（一時停止/動作中）を表示
- **タスク管理**:
  - 「この作業でやること」: 現在の作業タスク
  - 「次の作業でやること」: 次回の作業タスク
  - チェックリスト形式で追加・編集・完了管理
- **リセット**: 下部のリセットボタンでトップ画面に戻る

### 設定機能
上部の設定ボタンから以下を変更可能：
- 作業時間（デフォルト: 25分）
- 休憩時間（デフォルト: 5分）
- ダークモード切り替え

### タイマーの流れ

1. **作業時間**: 指定時間（25分）経過後、自動的に休憩画面に切り替わり、タイマーが開始
2. **休憩時間**: 指定時間（5分）経過後、作業画面に移行（タイマーは一時停止状態、｢この作業でやること｣が削除される）
3. **次の作業準備**: 次にやることを入力・整理してから手動でタイマーを開始(この画面に遷移した時点で｢次の作業でやること｣が｢この作業でやること｣に移動)
4. **繰り返し**: この動作を何度でも繰り返し可能

### サウンド通知
以下のタイミングで音が鳴ります：
- 作業、休憩タイマー開始時
- 休憩タイマー終了時
