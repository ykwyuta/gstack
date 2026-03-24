# gstack — 日本語ガイド

> 「おそらく12月からコードを一行も書いていないと思う。これは非常に大きな変化だ。」— [Andrej Karpathy](https://fortune.com/2026/03/21/andrej-karpathy-openai-cofounder-ai-agents-coding-state-of-psychosis-openclaw/)、No Priors ポッドキャスト、2026年3月

私は [Garry Tan](https://x.com/garrytan)、[Y Combinator](https://www.ycombinator.com/) の社長 & CEO です。過去20年間プロダクトを作り続けてきましたが、今が一番コードを書いています。直近60日間で **60万行以上の本番コード**（35%はテスト）、**1日1万〜2万行**、YC のフルタイム業務と並行してパートタイムで実現しました。

**gstack はその答えです。** Claude Code をバーチャルなエンジニアリングチームに変える、私のオープンソース ソフトウェアファクトリーです。CEO、エンジニアリングマネージャー、デザイナー、レビュアー、QAリード、セキュリティオフィサー、リリースエンジニア — 20人のスペシャリストと8つのパワーツールを、すべてスラッシュコマンドで、すべてMarkdown、完全無料、MITライセンスで提供します。

---

## 対象ユーザー

- **創業者・CEO** — 特にコードを書き続けたい技術系の方
- **Claude Code 初心者** — 白紙のプロンプトより、構造化されたロールで始めたい方
- **テックリード・スタッフエンジニア** — すべての PR に厳格なレビュー・QA・リリース自動化を適用したい方

---

## クイックスタート（5分で体感できます）

1. gstack をインストール（30秒 — 下記参照）
2. `/office-hours` を実行 — 作るものを説明する
3. `/plan-ceo-review` を任意のアイデアに対して実行
4. `/review` をコードのある任意のブランチに対して実行
5. `/qa` をステージング URL に対して実行
6. ここで止まって確かめてください。gstack が自分に合っているかわかります。

---

## インストール（30秒）

**必要なもの:** [Claude Code](https://docs.anthropic.com/en/docs/claude-code)、[Git](https://git-scm.com/)、[Bun](https://bun.sh/) v1.0+、[Node.js](https://nodejs.org/)（Windowsのみ）

### Step 1: マシンにインストール

Claude Code を開き、以下を貼り付けてください。Claude が残りを処理します。

> Install gstack: run **`git clone https://github.com/garrytan/gstack.git ~/.claude/skills/gstack && cd ~/.claude/skills/gstack && ./setup`** then add a "gstack" section to CLAUDE.md...

### Step 2: チームメンバー向けにリポジトリに追加（任意）

```bash
cp -Rf ~/.claude/skills/gstack .claude/skills/gstack
rm -rf .claude/skills/gstack/.git
cd .claude/skills/gstack && ./setup
```

実ファイルがリポジトリにコミットされる（サブモジュール不要）ので、`git clone` するだけで動きます。`.claude/` の中にすべて収まり、PATH を汚染したりバックグラウンドで何かが起動したりしません。

### Codex・Gemini CLI・Cursor の場合

```bash
git clone https://github.com/garrytan/gstack.git .agents/skills/gstack
cd .agents/skills/gstack && ./setup --host codex
```

---

## スプリントの流れ — Think → Plan → Build → Review → Test → Ship → Reflect

gstack のスキルは実際のソフトウェアスプリントの順序で実行されます。

### 計画フェーズ

| コマンド | 役割 | 説明 |
|----------|------|------|
| `/office-hours` | YC オフィスアワー | コードを書く前にプロダクトを再フレーミング。作るものが定まる |
| `/plan-ceo-review` | CEO レビュー | スコープを再考。拡張・絞り込み・保留・削減モードで判断 |
| `/plan-eng-review` | エンジニアリングマネージャー | アーキテクチャ・データフロー・図・テストを固める |
| `/plan-design-review` | デザイナー（報告のみ） | デザイン品質を0〜10でスコアリング、改善提案 |
| `/design-consultation` | デザインシステム構築 | ゼロからデザインシステムを設計 |
| `/autoplan` | 自動計画パイプライン | CEO → デザイン → エンジニアリングレビューを一コマンドで実行 |

### ビルドフェーズ

実装はあなたのエディタ/IDEで行います。gstack は検証済みのアーキテクチャを提供します。

### レビュー & 監査フェーズ

| コマンド | 役割 | 説明 |
|----------|------|------|
| `/review` | スタッフエンジニア | 本番バグを発見し、明らかなものは自動修正 |
| `/design-review` | デザイナー（修正あり） | ビジュアルを監査し、アトミックコミットで修正 |
| `/investigate` | デバッガー | 鉄則：調査なしに修正なし。根本原因を体系的に特定 |
| `/cso` | セキュリティオフィサー | OWASP Top 10 + STRIDE 脅威モデルを実行 |

### テストフェーズ

| コマンド | 役割 | 説明 |
|----------|------|------|
| `/qa` | QAリード | 実際のブラウザでアプリをテストし、バグ修正 + リグレッションテスト自動生成 |
| `/qa-only` | QAリード（報告のみ） | 同じ手法だが修正なし — バグを報告のみ |
| `/benchmark` | パフォーマンスエンジニア | Core Web Vitals のベースラインを計測し、前後を比較 |

### デプロイフェーズ

| コマンド | 役割 | 説明 |
|----------|------|------|
| `/ship` | リリースエンジニア | main を同期、テスト実行、カバレッジ監査、PR を開く |
| `/land-and-deploy` | デプロイオートメーション | PR マージ → CI 待機 → デプロイ → 本番確認 |
| `/canary` | SRE | デプロイ後のコンソールエラー・パフォーマンス低下・障害を監視 |
| `/document-release` | テクニカルライター | リリースした内容にすべてのドキュメントを更新 |

### リフレクションフェーズ

| コマンド | 役割 | 説明 |
|----------|------|------|
| `/retro` | チームレトロ | 担当者別内訳・shipping ストリーク・テスト健全性のレトロスペクティブ |

### パワーツール

| コマンド | 説明 |
|----------|------|
| `/browse` | 実際の Chromium ブラウザ（~100-200ms/コマンド）。QA・テスト・認証に使用 |
| `/setup-browser-cookies` | 実際のブラウザから Cookie をインポートし、認証済みテストを実現 |
| `/codex` | OpenAI Codex による独立したコードレビュー（セカンドオピニオン） |
| `/careful` | 安全ガードレール（`rm -rf`・`DROP TABLE`・force-push の前に警告） |
| `/freeze` | 編集ロック（特定ディレクトリのみ編集を許可） |
| `/guard` | フルセーフティ（`/careful` + `/freeze`） |
| `/setup-deploy` | デプロイ設定のワンタイムセットアップ |
| `/gstack-upgrade` | gstack 自身のセルフアップデーター |

---

## /browse — ブラウザスキル詳細

ブラウザは gstack の中核技術です。**永続的デーモン** として動作し、高速な応答を実現します。

- **初回起動:** 約3秒（Chromium 起動・ロック・ヘルスチェック）
- **2回目以降:** 約100-200ms（localhost HTTP サーバーへのリクエスト）
- **セッション間でステートを保持:** Cookie・タブ・ログインセッション
- **自動シャットダウン:** アイドル30分後

### 典型的な QA ワークフロー

```bash
$B goto https://app.example.com/login
$B snapshot -i                          # インタラクティブ要素を表示
$B fill @e3 "test@example.com"
$B fill @e4 "password"
$B click @e5
$B snapshot -D                          # 差分：何が変わったか？
$B is visible ".dashboard"              # アサーション
$B screenshot /tmp/after-login.png      # 証拠をキャプチャ
```

100以上のコマンド: `goto`, `click`, `fill`, `snapshot`, `screenshot`, `console`, `network`, `js`, `responsive`, `upload`, `cookie-import-browser` など。

### なぜ mcp__claude-in-chrome__* ではなく /browse を使うのか

| 比較項目 | /browse | mcp__claude-in-chrome |
|---------|---------|----------------------|
| 速度 | ~100-200ms（ウォーム時） | 遅い・不安定 |
| 永続性 | セッション間で Cookie・タブ保持 | なし |
| 信頼性 | 高い | 低い |

---

## コアフィロソフィー

### 湖を沸かせ（Boil the Lake）

AI がコーディングにおける完全性の限界コストをほぼゼロにした今、常に完全なことをせよ。90% の近道を出荷するな。テストカバレッジ、すべてのエッジケース、エラーパス、機能の完全性はすべて「湖」（沸騰可能）であり、「海」（多四半期にわたる移行）ではない。

**圧縮率の目安:**

| タスク種別 | 人間チーム | CC+gstack | 圧縮率 |
|-----------|-----------|-----------|--------|
| ボイラープレート | 2日 | 15分 | ~100x |
| テスト作成 | 1日 | 15分 | ~50x |
| 機能実装 | 1週間 | 30分 | ~30x |
| バグ修正+リグレッションテスト | 4時間 | 15分 | ~20x |
| アーキテクチャ設計 | 2日 | 4時間 | ~5x |
| 調査・探索 | 1日 | 3時間 | ~3x |

### 作る前に探せ（Search Before Building）

同時並行・不慣れなパターン・インフラ・ランタイムが内蔵機能を持つ可能性があるものを扱う前に、必ず検索せよ。

知識の3層:
1. **Layer 1（実績あり）** — 自明に見えるものでも疑え
2. **Layer 2（新潮流）** — 流行に乗る前に精査せよ
3. **Layer 3（ファーストプリンシプル）** — 最も価値が高い

---

## プロジェクト構成

```
gstack/
├── README.md                 # ユーザー向け概要
├── README.ja.md              # 日本語ガイド（このファイル）
├── ETHOS.md                  # ビルダー哲学
├── ARCHITECTURE.md           # 設計判断（Bun・ブラウザデーモン・HTTP）
├── CLAUDE.md                 # 開発コマンド
├── CONTRIBUTING.md           # コントリビューター向けセットアップ
├── CHANGELOG.md              # リリースノート（100バージョン以上）
├── SKILL.md / SKILL.md.tmpl  # 全スキルのドキュメント（生成 + テンプレート）
│
├── browse/                   # Headless Chromium CLI（コア技術）
│   ├── src/
│   │   ├── commands.ts       # コマンドレジストリ（100以上のコマンド）
│   │   ├── server.ts         # Bun HTTP サーバー
│   │   └── snapshot.ts       # アクセシビリティツリーパーサー
│   └── dist/                 # コンパイル済みバイナリ
│
├── {skill-dir}/              # 28スキル（各ディレクトリに SKILL.md）
├── scripts/                  # ビルドツール
├── test/                     # スキルバリデーション + E2E エバリュエーション
└── bin/                      # CLI ユーティリティ
```

**重要:** スキルは**コードではありません**。Bashコードブロックを含むMarkdownファイルです。各スキルは Claude が読んで従うプロンプトテンプレートです。

---

## 開発コマンド

```bash
bun install              # 依存関係をインストール
bun test                 # 無料テストを実行（<2秒、コミット前に必ず実行）
bun run test:evals       # 有料 eval を実行（LLMジャッジ + E2E、差分ベース、最大~$4/回）
bun run test:evals:all   # すべての eval を強制実行
bun run dev <cmd>        # CLI を開発モードで実行（例: bun run dev goto https://example.com）
bun run build            # ドキュメント生成 + バイナリコンパイル
bun run gen:skill-docs   # SKILL.md をテンプレートから再生成
bun run skill:check      # 全スキルのヘルスダッシュボード
bun run eval:list        # eval 実行履歴を一覧表示
bun run eval:compare     # 2つの eval 実行を比較
```

`bun test` はコミット前、`bun run test:evals` は PR 作成前に必ず実行してください。

---

## プライバシー & テレメトリー

**デフォルトはオフです。** 初回起動時に共有するかどうか尋ねられます。

共有される内容:
- スキル名・実行時間・成功/失敗・バージョン・OS
- **共有されない:** コード・ファイルパス・リポジトリ名・ブランチ名・プロンプト内容

変更はいつでも: `gstack-config set telemetry off`

---

## ライセンス

**MIT。永遠に無料です。** プレミアムプランなし、ウェイトリストなし、テレメトリー強制なし。

フォークしてください。改善してください。あなたのものにしてください。

---

*gstack は毎日使われ、活発に開発されています。フィードバックや質問は [GitHub Issues](https://github.com/garrytan/gstack/issues) へ。*
