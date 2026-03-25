# gstack 日本語開発ガイド

> 最終更新: 2026-03-25

このガイドでは、gstackのコードベース全体を把握し、カスタマイズを行うための情報をまとめています。

---

## 目次

1. [プロジェクト概要](#プロジェクト概要)
2. [ファイル・ディレクトリ一覧](#ファイルディレクトリ一覧)
3. [カスタマイズ重要ポイント](#カスタマイズ重要ポイント)
4. [開発ワークフロー](#開発ワークフロー)
5. [設計思想](#設計思想)

---

## プロジェクト概要

gstackは、Claude Codeに以下の機能を追加するオープンソースのAIエンジニアリングワークフローシステムです：

- **永続的なヘッドレスブラウザ**（Playwright）
- **構造化されたワークフロースキル**（ship、review、QAなど）
- **LLM評価テストインフラ**

コア哲学は「**Boil the Lake**（AIが安価にした今こそ完全実装を選ぶ）」と「**Search Before Building**（設計前にエコシステムを調査する）」です。

---

## ファイル・ディレクトリ一覧

### ルートファイル

| ファイル | 概要 |
|----------|------|
| `CLAUDE.md` | **開発の中心的なガイド**。コマンド一覧、プロジェクト構造、スキルテンプレートのワークフロー、コミット規約、CHANGELOG規約など |
| `ETHOS.md` | 設計思想ドキュメント。「Golden Age」「Boil the Lake」「Search Before Building」の3原則を定義。全スキルのプリアンブルに自動注入される |
| `README.md` | インストール手順と30秒クイックスタート |
| `CONTRIBUTING.md` | コントリビューター向けセットアップ手順、devモード、シンリンクワークフロー |
| `ARCHITECTURE.md` | デーモンモデル採用理由、Bun採用理由、ポート選択方式、バージョン自動再起動の設計判断 |
| `CHANGELOG.md` | リリース履歴（ブランチスコープのエントリ） |
| `TODOS.md` | アクティブな機能トラッキング |
| `SKILL.md.tmpl` | ルートgstackスキルのテンプレート（**直接編集する**） |
| `SKILL.md` | テンプレートから自動生成（**直接編集禁止**） |
| `package.json` | Bun設定とビルドスクリプト定義 |
| `setup` | 初回セットアップスクリプト（バイナリビルド＋スキルシンリンク） |

### スクリプト (`/scripts/`)

| ファイル | 概要 |
|----------|------|
| `gen-skill-docs.ts` | **テンプレートエンジン**（約1317行）。`.tmpl`ファイルを読み込み、`{{PLACEHOLDER}}`を解決してSKILL.mdを生成する |
| `skill-check.ts` | 全スキルのヘルスダッシュボードを表示 |
| `dev-skill.ts` | ウォッチモード：変更を検知して自動再生成＋バリデーション |
| `discover-skills.ts` | スキルテンプレートの自動探索 |
| `eval-list.ts` | eval実行結果の一覧表示 |
| `eval-compare.ts` | 2つのeval実行結果の比較 |
| `eval-summary.ts` | 全eval実行の集計統計 |
| `eval-watch.ts` | eval結果のリアルタイム監視 |
| `eval-select.ts` | diffベースで実行するテストのプレビュー |

### ブラウザCLI (`/browse/`)

| パス | 概要 |
|------|------|
| `browse/src/commands.ts` | **コマンドレジストリ**（信頼できる唯一のソース）。82個のコマンドを定義 |
| `browse/src/snapshot.ts` | スナップショットフラグのメタデータ配列 |
| `browse/src/cli.ts` | CLIエントリーポイント |
| `browse/src/server.ts` | Bun HTTPサーバー（デーモン実装） |
| `browse/src/find-browse.ts` | ポート検出ロジック |
| `browse/dist/browse` | コンパイル済みバイナリ（約58MB、単一実行ファイル） |
| `browse/dist/.version` | git SHA（自動再起動用） |
| `browse/test/` | ブラウザ統合テスト |

### テスト (`/test/`)

| ファイル | 概要 |
|----------|------|
| `skill-validation.test.ts` | **Tier 1（無料）**：スキルの静的バリデーション（構造、フロントマター、プレースホルダー） |
| `gen-skill-docs.test.ts` | **Tier 1（無料）**：テンプレート生成品質のチェック |
| `skill-e2e-*.test.ts` | **Tier 2（有料）**：`claude -p`経由のE2Eテスト。カテゴリ別に分割 |
| `skill-llm-eval.test.ts` | **Tier 3（有料）**：LLMをジャッジとして使う品質評価 |
| `codex-e2e.test.ts` | Codex CLIとの統合テスト |
| `helpers/skill-parser.ts` | SKILL.md構造パーサー |
| `helpers/session-runner.ts` | `claude -p`経由でスキルを実行するヘルパー |
| `helpers/llm-judge.ts` | 出力の品質採点 |
| `helpers/eval-store.ts` | 結果の永続化 |
| `helpers/touchfiles.ts` | **diffベーステスト選択**のためのファイル依存関係定義 |

### ワークフロースキル（各 `/スキル名/`）

| ディレクトリ | 概要 |
|-------------|------|
| `ship/` | 完全なシッピングワークフロー（マージ→テスト→バージョンアップ→CHANGELOG→PR） |
| `review/` | ランディング前のPRレビュー |
| `autoplan/` | 自動レビューパイプライン（CEO→デザイン→エンジニアリング） |
| `qa/` / `qa-only/` | QAワークフロー（qa: 修正あり、qa-only: レポートのみ） |
| `design-review/` | デザイン監査＋修正ループ |
| `plan-design-review/` | デザイン計画（レポートのみ） |
| `plan-ceo-review/` | 製品戦略レビュー |
| `plan-eng-review/` | アーキテクチャ計画レビュー |
| `investigate/` | 体系的なルートコーズ分析デバッグ |
| `benchmark/` | パフォーマンス回帰検出 |
| `canary/` | デプロイ後の監視ループ |
| `land-and-deploy/` | マージ→デプロイ→カナリア検証 |
| `cso/` | OWASP Top 10 + STRIDE セキュリティ監査 |
| `codex/` | OpenAI Codex CLI経由のマルチAIセカンドオピニオン |
| `retro/` | レトロスペクティブ（クロスプロジェクト対応） |
| `office-hours/` | YC Office Hours形式のスタートアップ診断 |
| `document-release/` | シップ後のドキュメント更新 |
| `design-consultation/` | ゼロからのデザインシステム構築 |
| `setup-deploy/` | 一回限りのデプロイ設定 |
| `careful/` / `guard/` / `freeze/` / `unfreeze/` | 本番安全ガード系スキル |

### その他の重要ディレクトリ

| ディレクトリ | 概要 |
|-------------|------|
| `bin/` | CLIユーティリティ（gstack-repo-mode、gstack-slug、gstack-config等） |
| `lib/` | 共有ライブラリ |
| `.github/workflows/` | CI（evals.yml、skill-docs.yml、actionlint.yml） |
| `.github/docker/` | Dockerfile.ci（Playwright/Chromium入りビルド環境） |
| `supabase/` | DBマイグレーション、関数、設定 |
| `agents/` | OpenAI Codexインテグレーション設定 |

---

## カスタマイズ重要ポイント

### 1. 新しいスキルの追加

スキルはClaude Codeが読み込む自然言語のプロンプトテンプレートです。

**手順：**

```bash
# 1. スキルディレクトリを作成
mkdir my-skill

# 2. テンプレートファイルを作成（後述の構造を参照）
# my-skill/SKILL.md.tmpl を編集

# 3. SKILL.mdを生成
bun run gen:skill-docs

# 4. 両方をコミット
git add my-skill/SKILL.md.tmpl my-skill/SKILL.md
```

**SKILL.md.tmplの基本構造：**

```yaml
---
name: my-skill
preamble-tier: 2          # 1-4: 注入するプリアンブルの量を制御
version: 1.0.0
description: |
  このスキルは〇〇を行います。
allowed-tools:
  - Bash
  - Read
  - Edit
  - Grep
  - Glob
  - AskUserQuestion
---

{{PREAMBLE}}

# スキルのタイトル

## Step 1: ...

## Step 2: ...
```

**重要なテンプレートプレースホルダー：**

| プレースホルダー | 用途 |
|-----------------|------|
| `{{PREAMBLE}}` | セッション/設定コンテキスト（preamble-tierで制御） |
| `{{BASE_BRANCH_DETECT}}` | PRターゲットスキル用の動的ベースブランチ検出 |
| `{{BROWSE_SETUP}}` | ブラウザ初期化（ブラウザ使用スキル向け） |
| `{{SLUG_EVAL}}` | プロジェクトスラッグ検出 |
| `{{REVIEW_DASHBOARD}}` | レビューステータスダッシュボード |

**テンプレート作成のルール（CLAUDE.mdより）：**

- **ロジックと状態は自然言語で表現する**。シェル変数でコードブロック間の状態を受け渡さない
- **ブランチ名をハードコードしない**。`gh repo view`等で動的に検出する
- **bashブロックは自己完結させる**。前のステップのコンテキストが必要なら散文で再記述する
- **条件分岐は英語の番号付きステップで表現する**：「1. If X, do Y. 2. Otherwise, do Z.」

### 2. ブラウザコマンドの追加

gstackのブラウザCLIは82個のコマンドを持ちます。新しいコマンドを追加するには：

**`/browse/src/commands.ts` を編集：**

```typescript
// コマンドを適切なセットに追加
export const READ_COMMANDS = new Set([
  // ... 既存のコマンド
  'my-command',  // 追加
]);

// コマンドの説明を追加
export const COMMAND_DESCRIPTIONS: Record<string, CommandInfo> = {
  'my-command': {
    category: 'Reading',           // Navigation / Reading / Interaction / Meta
    description: 'コマンドの説明',
    usage: 'my-command [options]', // オプション
  },
};
```

**`/browse/src/server.ts` で実装後、ビルド：**

```bash
bun run build  # バイナリコンパイル + ドキュメント再生成
```

### 3. テンプレートプレースホルダーの追加

**`/scripts/resolvers/index.ts` を編集：**

```typescript
export const RESOLVERS: Record<string, Resolver> = {
  // 既存のリゾルバー...

  MY_PLACEHOLDER: (ctx: TemplateContext) => {
    // 注入する文字列を返す
    return `## マイセクション\n\nここに内容を書く`;
  }
};
```

`.tmpl`ファイルで使用：

```markdown
{{MY_PLACEHOLDER}}
```

### 4. diffベーステスト選択の設定

新しいテストを追加する際は、そのテストが依存するファイルを `test/helpers/touchfiles.ts` に宣言します：

```typescript
export const TOUCHFILES: Record<string, string[]> = {
  'my-skill-e2e': [
    'my-skill/SKILL.md.tmpl',
    'my-skill/SKILL.md',
    // このテストが依存する他のファイル
  ],
};
```

これにより、関連ファイルが変更された場合のみそのテストが実行されます。

### 5. プロジェクト設定のカスタマイズ（CLAUDE.md）

gstackスキルは**プラットフォーム非依存**です。プロジェクト固有の設定はCLAUDE.mdから読み込まれます。

**CLAUDE.mdに追加できる設定例：**

```markdown
## Commands

\`\`\`bash
bun test              # テストコマンド
bun run deploy        # デプロイコマンド
bun run build         # ビルドコマンド
\`\`\`
```

スキルは設定が存在しない場合に `AskUserQuestion` でユーザーに問い合わせ、その答えをCLAUDE.mdに永続化します。

### 6. CHANGELOG・バージョン管理

CHANGELOG.mdはユーザー向けのリリースノートです。以下のルールに従ってください：

- **バージョンはブランチスコープ**：各フィーチャーブランチが独自のバージョンバンプとCHANGELOGエントリを持つ
- CHANGELOGエントリは `/ship` 時点（Step 5）に書く（開発中ではない）
- **ユーザー向けの言葉で書く**：「〇〇できるようになりました」という形式で
- 実装詳細ではなく、ユーザーが何をできるかにフォーカスする

**良い例：**
```markdown
## v0.12.0.0

- 日本語のエラーメッセージに対応しました
- `browse diff` コマンドで2つのURLを比較できるようになりました
```

**悪い例：**
```markdown
## v0.12.0.0

- i18n レイヤーをリファクタリング
- SNAPSHOT_FLAGSにdiffコマンドを追加
```

---

## 開発ワークフロー

### セットアップ

```bash
bun install          # 依存関係インストール
./setup              # 初回のみ: バイナリビルド + スキルシンリンク
```

### 日常の開発サイクル

```bash
bun run dev:skill    # ウォッチモード: .tmpl変更を検知して自動再生成
bun test             # 毎コミット前に実行（無料、2秒未満）
bun run test:evals   # シップ前に実行（有料、diffベース、最大$4）
```

### スキル開発の注意点

`.claude/skills/gstack` がシンリンクになっている場合、テンプレート変更は**即時反映**されます。

```bash
# シンリンクかどうかを確認
ls -la .claude/skills/gstack

# 大きなリファクタリング時はシンリンクを解除（他セッションへの影響防止）
rm .claude/skills/gstack
```

### コミット規約

**「bisect commits」** - 各コミットは単一の論理的な変更にする：

```bash
# 良い例
git commit -m "Add SKILL.md.tmpl for my-skill"
git commit -m "Generate SKILL.md from my-skill template"

# 悪い例
git commit -m "Add my-skill with all changes"
```

---

## 設計思想

### Boil the Lake（湖を沸かす）

AIアシストにより完全実装のコストが限りなくゼロに近づいた今、「完全な実装」を選ぶべきです。

| 判断 | 説明 |
|------|------|
| **Lake（湖）** | ✅ 沸かせる = 100%テストカバレッジ、全エッジケース、完全なエラーパス |
| **Ocean（海）** | ❌ 沸かせない = システム全体の書き直し、マルチクォーターのプラットフォーム移行 |

### Search Before Building（作る前に調べる）

設計前に以下の3層で知識を収集します：

1. **Layer 1（実績あり）**: 実戦テスト済みのアプローチ
2. **Layer 2（新しくて人気）**: ブログ記事、最近のトレンド
3. **Layer 3（第一原理）**: オリジナルの観察と推論 ← 最重要

### プラットフォーム非依存設計

スキルはフレームワーク固有のコマンド、ファイルパターン、ディレクトリ構造を**決してハードコードしない**：

1. **CLAUDE.mdを読む** → プロジェクト固有の設定を取得
2. **存在しない場合はAskUserQuestion** → ユーザーに問い合わせ
3. **答えをCLAUDE.mdに永続化** → 次回から問い合わせ不要

---

## クイックリファレンス

```bash
# ビルド
bun run build                    # ドキュメント再生成 + バイナリコンパイル
bun run gen:skill-docs           # SKILL.mdのみ再生成

# テスト
bun test                         # 無料テスト（必ず実行）
bun run test:evals               # 有料テスト（シップ前に実行）
bun run eval:select              # どのテストが実行されるかプレビュー
bun run skill:check              # スキルヘルスダッシュボード

# 開発
bun run dev:skill                # ウォッチモード
bun run dev <command>            # CLI開発モード実行

# eval管理
bun run eval:list                # eval実行一覧
bun run eval:compare             # 2つの実行を比較
bun run eval:summary             # 集計統計

# デプロイ後の更新
cd ~/.claude/skills/gstack
git fetch origin && git reset --hard origin/main
bun run build
```
