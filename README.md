# DDD Modeling Skills

DDDモデリングを段階的に進めるためのClaude Code / Codex CLIプラグインです。

## 概要

ドメインモデリングにおいて、ドメインエキスパートとの対話だけでは見落としがちな観点や、暗黙的な知識を明示化することが難しい場面があります。

このスキルセットは、DDDの各手法（イベントストーミング、集約設計、境界コンテキストマッピング等）をAIとの対話形式で進めることで、**深堀りのための補助ツール**として機能することを目的としています。

- 「他に重要なドメイン概念はありますか？」
- 「常に真でなければならないルールは何ですか？」
- 「同じ言葉で異なる意味を持つものはありますか？」

といった問いかけを通じて、見落としていた観点や曖昧だった境界を明確化する手助けをします。

## ワークフロー

```
Phase 1: /ddd-modeling:1-event-storming
    → 01-event-storming.md, 01-event-storming-diagram.md
    ↓
Phase 2: /ddd-modeling:2-aggregate
    → 02-aggregates.md
    ↓
Phase 3: /ddd-modeling:3-context
    → 03-bounded-contexts.md
    ↓
Phase 4: /ddd-modeling:4-model-diagram
    → 04-sequence-*.md, 05-class-diagram.md
```

## 利用可能なコマンド

### コマンド（ユーザー向け）

`/ddd-modeling:command-name` 形式で呼び出します。

| コマンド | 説明 | Codex対応 |
|----------|------|-----------|
| 1-event-storming | DDDフェーズ1: イベントストーミング | Yes |
| 2-aggregate | DDDフェーズ2: 集約設計 | Yes |
| 3-context | DDDフェーズ3: 境界コンテキスト | Yes |
| 4-model-diagram | DDDフェーズ4: モデル図生成 | Yes |
| feedback | UML/Mermaid図へのフィードバック | Yes |

### スキル構造

各コマンドは対応するフェーズスキルを呼び出します。フェーズスキルは `references/` ディレクトリに詳細なガイドを持ちます：

| スキル | 説明 | リファレンス |
|--------|------|--------------|
| event-storming | イベントストーミングファシリテーション＋図生成 | facilitator.md, diagram-generator.md, color-convention.md |
| aggregate | 集約設計支援 | designer.md |
| context | 境界コンテキストマッピング | mapper.md |
| model-diagram | シーケンス図・クラス図生成 | sequence-diagram.md, class-diagram.md |
| feedback | 図へのDDDフィードバック | - |

## 導入方法

### Claude Code

#### 方法1: Claude Code上で登録

1. `/plugin`コマンドを実行し、`Marketplace`タブから新規追加
2. URLに `odaryo/ddd-modeling`（owner/repo形式）を指定
3. プラグイン名 `ddd-modeling` を選択してインストール

#### 方法2: プロジェクト内に設定

各プロジェクトの `.claude/settings.json` に以下を追加：

```json
{
  "extraKnownMarketplaces": {
    "ddd-modeling": {
      "source": {
        "source": "github",
        "repo": "<owner>/ddd-modeling"
      }
    }
  }
}
```

その後、Claude Codeで以下を実行：

```bash
/plugin install ddd-modeling@ddd-modeling
```

### Codex CLI

Codexの `$skill-installer` スキルを使用してGitHubリポジトリからインストールします。

**DDDモデリング一式:**
```
$skill-installer install --repo <owner>/ddd-modeling \
  --path skills/event-storming \
  --path skills/aggregate \
  --path skills/context \
  --path skills/model-diagram \
  --path skills/feedback
```

**使い方:**
```
# スキル一覧を表示
/skills

# 明示的に呼び出す
$event-storming

# または暗黙的に呼び出し（トリガーワードで自動起動）
「イベントストーミングを始めたい」→ event-storming が自動起動
```

## ディレクトリ構造

```
ddd-modeling/
├── .claude-plugin/
│   ├── marketplace.json        # マーケットプレイス定義
│   └── plugin.json             # プラグイン定義
├── commands/                   # ユーザー向けコマンド
│   ├── 1-event-storming.md
│   ├── 2-aggregate.md
│   ├── 3-context.md
│   ├── 4-model-diagram.md
│   └── feedback.md
├── skills/                     # フェーズスキル
│   ├── event-storming/
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── facilitator.md
│   │       ├── diagram-generator.md
│   │       └── color-convention.md
│   ├── aggregate/
│   │   ├── SKILL.md
│   │   └── references/
│   │       └── designer.md
│   ├── context/
│   │   ├── SKILL.md
│   │   └── references/
│   │       └── mapper.md
│   ├── model-diagram/
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── sequence-diagram.md
│   │       └── class-diagram.md
│   └── feedback/
│       └── SKILL.md
├── tests/
│   └── scenarios.md
├── CLAUDE.md                   # Claude Code用ガイド
└── README.md
```

## 出力例

DDDモデリング成果物は以下の形式で保存されます：

```
docs/modeling/{YYYY-MM-DD}-{topic}/
├── 01-event-storming.md         # ドメインイベント、コマンド、アクター、ポリシー
├── 01-event-storming-diagram.md # Mermaidフローチャート
├── 02-aggregates.md             # 集約定義、不変条件、ライフサイクル
├── 03-bounded-contexts.md       # コンテキスト定義、コンテキストマップ
├── 04-sequence-{usecase}.md     # シーケンス図
└── 05-class-diagram.md          # クラス図（DDDステレオタイプ付き）
```

## ライセンス

MIT License
