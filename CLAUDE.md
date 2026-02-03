# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Claude Code / Codex CLI両対応のDDDモデリングスキルマーケットプレイスリポジトリ。DDDモデリングを段階的に進めるためのスキルを管理・配布する。

### Codex CLI対応

このリポジトリのSKILL.mdはClaude CodeとCodex CLIの両方で動作する：
- **共通**: `name`, `description` フロントマター（両環境で必須）
- **Claude固有**: `plugin.json`, `marketplace.json`, `REFERENCE.md`
- **Codex固有**: `$skill-installer` でパス指定インストール

Codex用のインストール手順は [CODEX_SKILLS.md](CODEX_SKILLS.md) を参照。

## アーキテクチャ

```
ddd-modeling/
├── .claude-plugin/
│   └── marketplace.json        # マーケットプレイス定義
├── plugins/
│   └── ddd-modeling-skills/    # DDDモデリングスキル集
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── skills/
│       │   ├── ddd-1-event-storming/   # Phase 1: イベントストーミング
│       │   ├── ddd-2-aggregate/        # Phase 2: 集約設計
│       │   ├── ddd-3-context/          # Phase 3: 境界コンテキスト
│       │   ├── ddd-4-model-diagram/    # Phase 4: モデル図生成
│       │   ├── diagram-feedback/       # 補助: 図のフィードバック
│       │   └── [内部スキル...]
│       └── tests/
│           └── scenarios.md
├── CLAUDE.md
├── CODEX_SKILLS.md
└── README.md
```

## DDDモデリングワークフロー

```
Phase 1: /ddd-1-event-storming
    → 01-event-storming.md, 01-event-storming-diagram.md
    ↓
Phase 2: /ddd-2-aggregate
    → 02-aggregates.md
    ↓
Phase 3: /ddd-3-context
    → 03-bounded-contexts.md
    ↓
Phase 4: /ddd-4-model-diagram
    → 04-sequence-*.md, 05-class-diagram.md
```

## スキルファイル構造

### フェーズスキル（ユーザー向け）

| スキル | 説明 | Codex対応 |
|--------|------|-----------|
| ddd-1-event-storming | Phase 1: イベントストーミング | Yes |
| ddd-2-aggregate | Phase 2: 集約設計 | Yes |
| ddd-3-context | Phase 3: 境界コンテキスト | Yes |
| ddd-4-model-diagram | Phase 4: モデル図生成 | Yes |
| diagram-feedback | 図へのフィードバック | Yes |

### 内部スキル（フェーズスキルから呼び出し）

| スキル | 説明 |
|--------|------|
| event-storming-facilitator | イベントストーミングファシリテーション |
| event-storming-diagram | イベントストーミング図生成 |
| aggregate-designer | 集約設計支援 |
| bounded-context-mapper | 境界コンテキストマッピング |
| sequence-diagram | シーケンス図生成 |
| class-diagram | クラス図生成 |

## SKILL.md 作成ガイドライン

### フロントマター
```yaml
---
name: skill-name              # 小文字、数字、ハイフンのみ（両環境共通）
description: 説明文            # 何ができるか + いつ使うか（両環境共通）
user-invocable: false         # 内部スキルの場合のみ追加
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
5. Codex対応の場合: `CODEX_SKILLS.md` にインストール手順を追加
