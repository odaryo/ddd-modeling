# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Claude Code / Codex CLI両対応のDDDモデリングスキルマーケットプレイスリポジトリ。DDDモデリングを段階的に進めるためのスキルを管理・配布する。

### Codex CLI対応

このリポジトリのSKILL.mdはClaude CodeとCodex CLIの両方で動作する：
- **共通**: `name`, `description` フロントマター（両環境で必須）
- **Claude固有**: `plugin.json`, `marketplace.json`, `REFERENCE.md`
- **Codex固有**: `$skill-installer` でパス指定インストール

Codex用のインストール手順は `README.md` を参照。

## アーキテクチャ

```
ddd-modeling/
├── .claude-plugin/
│   ├── marketplace.json        # マーケットプレイス定義
│   └── plugin.json             # プラグイン定義
├── commands/                   # ユーザー向けコマンド
│   ├── 1-event-storming.md     # /ddd-modeling:1-event-storming
│   ├── 2-aggregate.md          # /ddd-modeling:2-aggregate
│   ├── 3-context.md            # /ddd-modeling:3-context
│   ├── 4-model-diagram.md      # /ddd-modeling:4-model-diagram
│   └── feedback.md             # /ddd-modeling:feedback
├── skills/                     # 内部スキル（コマンドから呼び出し）
│   ├── event-storming/         # Phase 1: イベントストーミング
│   ├── aggregate/              # Phase 2: 集約設計
│   ├── context/                # Phase 3: 境界コンテキスト
│   ├── model-diagram/          # Phase 4: モデル図生成
│   ├── feedback/               # 補助: 図のフィードバック
│   └── [その他内部スキル...]
├── tests/
│   └── scenarios.md
├── CLAUDE.md
└── README.md
```

## DDDモデリングワークフロー

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

## コマンドとスキル構造

### コマンド（ユーザー向け）

`/ddd-modeling:command-name` 形式で呼び出し。

| コマンド | 説明 | 呼び出すスキル |
|----------|------|----------------|
| 1-event-storming | Phase 1: イベントストーミング | event-storming |
| 2-aggregate | Phase 2: 集約設計 | aggregate |
| 3-context | Phase 3: 境界コンテキスト | context |
| 4-model-diagram | Phase 4: モデル図生成 | model-diagram |
| feedback | 図へのフィードバック | feedback |

### 内部スキル（コマンドから呼び出し）

| スキル | 説明 |
|--------|------|
| event-storming | イベントストーミングファシリテーション＋図生成 |
| aggregate | 集約設計支援 |
| context | 境界コンテキストマッピング |
| model-diagram | シーケンス図・クラス図生成 |
| feedback | 図へのDDDフィードバック |
| event-storming-facilitator | (内部) イベントストーミングファシリテーション |
| event-storming-diagram | (内部) イベントストーミング図生成 |
| aggregate-designer | (内部) 集約設計支援 |
| bounded-context-mapper | (内部) 境界コンテキストマッピング |
| sequence-diagram | (内部) シーケンス図生成 |
| class-diagram | (内部) クラス図生成 |

## コマンドファイル作成ガイドライン

### フロントマター（commands/*.md）
```yaml
---
description: 説明文            # 何ができるか
---

Invoke the ddd-modeling:{skill-name} skill and follow it exactly as presented to you
```

## SKILL.md 作成ガイドライン

### フロントマター（skills/*/SKILL.md）
```yaml
---
name: skill-name              # 小文字、数字、ハイフンのみ（両環境共通）
description: 説明文            # 何ができるか + いつ使うか（両環境共通）
user-invocable: false         # 内部スキルとして設定
---
```

### 参照ファイル
- `REFERENCE.md`: 詳細なリファレンス情報（オプション）
- SKILL.md内で `[REFERENCE.md](REFERENCE.md)` として参照

## 出力ディレクトリ規約

DDDモデリング成果物の保存先:
```
docs/modeling/{YYYY-MM-DD}-{topic}/
├── 01-event-storming.md
├── 01-event-storming-diagram.md
├── 02-aggregates.md
├── 03-bounded-contexts.md
├── 04-sequence-{usecase}.md
└── 05-class-diagram.md
```

## Mermaid図の色規約

| 要素 | 色 | Mermaid Style |
|------|-----|---------------|
| Domain Event | Orange | `fill:#FF6B35,color:#fff` |
| Command | Blue | `fill:#4A90D9,color:#fff` |
| Actor | Yellow | `fill:#FFD93D,color:#333` |
| Policy | Purple | `fill:#9B59B6,color:#fff` |
| External System | Pink | `fill:#E91E8C,color:#fff` |
| Aggregate | Green | `fill:#27AE60,color:#fff` |

## プラグインの追加方法

1. `plugins/<plugin-name>/` ディレクトリを作成
2. `.claude-plugin/plugin.json` を作成
3. `skills/<skill-name>/SKILL.md` を作成
4. `.claude-plugin/marketplace.json` に追加
5. Codex対応の場合: `README.md` にインストール手順を追加
