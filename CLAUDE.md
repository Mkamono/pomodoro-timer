# ポモドーロタイマー - Claude向けプロジェクトガイド

## プロジェクト概要

このプロジェクトは、ポモドーロテクニックを実践するためのシンプルなWebタイマーアプリケーションです。25分の作業セッションと5分の休憩セッションを交互に繰り返すことで、生産性を高めることを目的としています。

- **公開URL**: https://mkamono.github.io/pomodoro-timer/
- **言語**: 日本語UI
- **デプロイ**: GitHub Pages
- **アーキテクチャ**: シングルファイルHTML + Vanilla JavaScript

## 技術スタック

### フロントエンド
- **HTML5**: セマンティックHTML
- **CSS**: Tailwind CSS 4 (CDN経由)
- **JavaScript**: Vanilla ES6 (フレームワークなし)

### 使用しているブラウザAPI
- **Web Audio API**: タイマー完了時の音声通知
- **localStorage**: テーマ設定の永続化
- **SVG**: 円形プログレスインジケーター

### 外部リソース
- Tailwind CSS CDN: `https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4`
- Favicon: Emoji CDN (`https://emojicdn.elk.sh/🍅`)

## プロジェクト構造

```
pomodoro-timer/
├── index.html          # アプリケーション全体（405行）
├── README.md          # プロジェクトメタデータ
└── CLAUDE.md          # このファイル
```

**重要**: このプロジェクトは意図的に単一ファイル構成を採用しています。ビルドプロセス、パッケージマネージャー、設定ファイルは不要です。

## コードアーキテクチャ

### 主要な状態変数 (index.html:160-167)

```javascript
let timerInterval = null;      // setIntervalの参照
let timeLeft = 25 * 60;        // 残り時間（秒）
let isRunning = false;         // タイマー実行状態
let currentMode = 'work';      // 'work' または 'break'
let cycleCount = 1;            // 現在のサイクル番号
let currentTask = '';          // ユーザーのタスク説明

const WORK_TIME = 25 * 60;     // 作業時間（1500秒 = 25分）
const BREAK_TIME = 5 * 60;     // 休憩時間（300秒 = 5分）
```

### 主要な関数

| 関数名 | 行数 | 目的 |
|--------|------|------|
| `initTheme()` | 169-180 | テーマの初期化（localStorage or システム設定） |
| `toggleTheme()` | 182-185 | ダーク/ライトモード切り替え |
| `playSound()` | 208-224 | Web Audio APIでビープ音を生成（800Hz） |
| `showTaskModal()` | 227-233 | タスク入力モーダルを表示 |
| `confirmTask()` | 241-256 | タスクを保存してタイマー開始 |
| `startWithWork()` | 260-267 | 作業モードで開始 |
| `startWithBreak()` | 270-277 | 休憩モードで開始 |
| `startTimer()` | 287-303 | タイマーのカウントダウン開始 |
| `pauseTimer()` | 306-311 | タイマー一時停止 |
| `resetTimer()` | 314-326 | 初期状態にリセット |
| `switchMode()` | 329-350 | 作業↔休憩の自動切り替え |
| `updateDisplay()` | 353-402 | UI全体の更新 |

### アプリケーションフロー

```
アプリ起動
    ↓
テーマ初期化（localStorage → システム設定）
    ↓
初期選択画面
    ├─ 「作業から開始」クリック → タスク入力モーダル表示 → タイマー開始
    └─ 「休憩から開始」クリック → 即座にタイマー開始
    ↓
タイマー実行中
    ├─ 1秒ごとに残り時間を減算・UI更新
    └─ 時間が0になったら自動的にモード切り替え
        ├─ 作業終了 → 音声通知 → 休憩開始（自動継続）
        └─ 休憩終了 → 音声通知 → サイクル+1 → タスク入力モーダル
```

## UI コンポーネント

### 1. 初期選択画面 (index.html:32-46)
- 「作業から開始」ボタン：赤色、25分作業セッション
- 「休憩から開始」ボタン：緑色、5分休憩セッション

### 2. タイマー画面 (index.html:49-162)
- **モード表示**: 現在のモード（作業/休憩）とアイコン
- **タスク表示**: 作業中のタスク名
- **タイマー表示**: MM:SS形式の大きな時計
- **プログレスサークル**: SVGによる円形進捗インジケーター
- **サイクルカウンター**: 現在のサイクル番号
- **次のセッション情報**: 次に来るモード
- **コントロールボタン**: 開始/一時停止/リセット

### 3. タスク入力モーダル (index.html:120-162)
- 作業セッション開始時に表示
- タスク名を入力（オプション）
- Enterキーで確定可能

### 4. テーマトグル (index.html:15-24)
- 右上の月/太陽アイコン
- ダーク/ライトモード切り替え
- 設定はlocalStorageに永続化

## スタイリングとデザイン

### カラースキーム
- **作業時間**: 赤色 (#ef4444)
- **休憩時間**: 緑色 (#16a34a)
- **背景（ライトモード）**: 青〜藍色のグラデーション
- **背景（ダークモード）**: 濃いグレーのグラデーション

### レスポンシブデザイン
- モバイルファースト設計
- Tailwindのブレークポイントを使用（md:）
- 最大幅: 28rem（max-w-md）

## 開発ガイドライン

### コード編集時の注意点

1. **単一ファイル構成を維持する**
   - すべてのHTML、CSS、JavaScriptは `index.html` に含まれます
   - 外部ファイルへの分割は避けてください

2. **依存関係を追加しない**
   - npmパッケージは使用しません
   - 新しいCDNの追加は慎重に検討してください

3. **日本語UIを維持する**
   - すべてのユーザー向けテキストは日本語で記述
   - コメントも日本語が推奨されます

4. **アクセシビリティ**
   - aria-label属性を適切に使用
   - キーボード操作をサポート（Enterキーなど）

5. **テーマ対応**
   - 新しいUI要素には `dark:` プレフィックスのクラスを追加
   - カラーコントラストに注意

### Git開発ワークフロー

- **メインブランチ**: `main`（保護されています）
- **開発ブランチ**: `claude/` プレフィックスで始まる
- **プルリクエスト**: すべての変更はPR経由でマージ
- **コミットメッセージ**: 日本語で簡潔に

### テストとデプロイ

**テスト:**
- 手動テストのみ（自動テストなし）
- ブラウザでindex.htmlを開いて動作確認
- Chrome、Firefox、Safari、モバイルブラウザで確認

**デプロイ:**
- GitHub Pagesで自動デプロイ
- `main` ブランチへのマージで自動的にデプロイされます

## 機能の詳細

### ✅ 実装済み機能

1. **基本タイマー機能**
   - 25分作業 / 5分休憩
   - 一時停止・再開
   - リセット

2. **タスク管理**
   - 作業セッションごとのタスク入力
   - タスク名の表示

3. **視覚的フィードバック**
   - SVG円形プログレスバー
   - モード別の色分け
   - サイクル番号表示

4. **音声通知**
   - モード切り替え時にビープ音

5. **テーマ**
   - ダーク/ライトモード
   - システム設定の自動検出
   - 設定の永続化

6. **UX改善**
   - レスポンシブデザイン
   - スムーズなトランジション
   - Enterキーでの操作

### 過去の主要な改善（Git履歴より）

- PR #12: タスク入力モーダルのスキップボタン削除
- PR #11: テキストコントラスト改善
- PR #10: カラーパレットの柔らかい調整
- PR #9: ダークモードサポート追加
- PR #8: トマトファビコン追加
- PR #7: タスク入力モーダル実装
- PR #6: プログレスサークル追加

## 技術的詳細

### SVG円形プログレスの実装 (index.html:353-402)

```javascript
// 円周の計算: 2πr (r=120)
const circumference = 2 * Math.PI * 120;
const progressCircle = document.getElementById('progressCircle');

// 進捗に応じてstroke-dashoffsetを更新
const totalTime = currentMode === 'work' ? WORK_TIME : BREAK_TIME;
const progress = (totalTime - timeLeft) / totalTime;
const offset = circumference - progress * circumference;
progressCircle.style.strokeDashoffset = offset;
```

### Web Audio APIによる音声生成 (index.html:208-224)

```javascript
function playSound() {
  const audioContext = new (window.AudioContext || window.webkitAudioContext)();
  const oscillator = audioContext.createOscillator();
  const gainNode = audioContext.createGain();

  oscillator.frequency.value = 800; // 800Hzのサイン波
  oscillator.connect(gainNode);
  gainNode.connect(audioContext.destination);

  gainNode.gain.setValueAtTime(0.3, audioContext.currentTime);
  gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.5);

  oscillator.start(audioContext.currentTime);
  oscillator.stop(audioContext.currentTime + 0.5);
}
```

### テーマ永続化 (index.html:169-185)

```javascript
function initTheme() {
  const savedTheme = localStorage.getItem('theme');
  const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;

  if (savedTheme === 'dark' || (!savedTheme && prefersDark)) {
    document.documentElement.classList.add('dark');
  }
}

function toggleTheme() {
  document.documentElement.classList.toggle('dark');
  const theme = document.documentElement.classList.contains('dark') ? 'dark' : 'light';
  localStorage.setItem('theme', theme);
}
```

## よくある作業パターン

### 新しい機能を追加する場合

1. 新しいブランチを作成: `claude/feature-name-{session-id}`
2. `index.html` を編集
3. ブラウザでテストして動作確認
4. コミット & プッシュ
5. PRを作成

### UI要素を変更する場合

1. Tailwindのユーティリティクラスを使用
2. ダークモード対応を忘れずに（`dark:` クラス）
3. モバイルとデスクトップの両方で確認

### 新しい状態を追加する場合

1. グローバル変数として定義 (index.html:160-167付近)
2. `resetTimer()` 関数でリセット処理を追加
3. `updateDisplay()` 関数でUI更新処理を追加

## パフォーマンス最適化

- **単一setInterval**: 1秒ごとに1回のみ実行
- **DOM操作の最小化**: 必要な要素のみ更新
- **CSS transitions**: GPU加速を利用
- **軽量**: 15KB程度の単一HTMLファイル

## ブラウザ互換性

- ✅ Chrome/Edge (最新版)
- ✅ Firefox (最新版)
- ✅ Safari (webkitAudioContext対応)
- ✅ モバイルブラウザ (iOS Safari、Chrome Mobile)

**最小要件:**
- ES6サポート
- CSS Grid/Flexbox
- Web Audio API
- localStorage API

## 今後の改善案（検討中）

- 作業時間/休憩時間のカスタマイズ機能
- 統計情報の表示（完了したセッション数など）
- タスク履歴の保存
- デスクトップ通知のサポート
- ショートカットキー対応

## 関連リンク

- **公開サイト**: https://mkamono.github.io/pomodoro-timer/
- **リポジトリ**: https://github.com/Mkamono/pomodoro-timer
- **ポモドーロテクニック**: https://ja.wikipedia.org/wiki/ポモドーロ・テクニック

## サポート

質問や問題がある場合は、GitHubのIssuesで報告してください。
