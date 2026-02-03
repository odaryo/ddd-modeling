# Codex CLI スキル一覧

このリポジトリのスキルは [Codex CLI](https://github.com/openai/codex) でも利用可能です。

## インストール方法

Codexの `$skill-installer` スキルを使用してGitHubリポジトリからインストールします。

### 基本構文

```
$skill-installer install --repo <owner>/<repo> --path <skill-path>
```

## 利用可能なスキル

### ddd-modeling-skills（DDDモデリングスキル集）

DDDモデリングを段階的に進めるためのフェーズスキル群です。

| スキル名 | パス | 説明 |
|----------|------|------|
| ddd-1-event-storming | `plugins/ddd-modeling-skills/skills/ddd-1-event-storming` | DDDフェーズ1: イベントストーミング |
| ddd-2-aggregate | `plugins/ddd-modeling-skills/skills/ddd-2-aggregate` | DDDフェーズ2: 集約設計 |
| ddd-3-context | `plugins/ddd-modeling-skills/skills/ddd-3-context` | DDDフェーズ3: 境界コンテキスト |
| ddd-4-model-diagram | `plugins/ddd-modeling-skills/skills/ddd-4-model-diagram` | DDDフェーズ4: モデル図生成 |
| diagram-feedback | `plugins/ddd-modeling-skills/skills/diagram-feedback` | UML/Mermaid図へのフィードバック |

**インストールコマンド（DDDフェーズスキル一式）:**
```
$skill-installer install --repo <owner>/ddd-modeling \
  --path plugins/ddd-modeling-skills/skills/ddd-1-event-storming \
  --path plugins/ddd-modeling-skills/skills/ddd-2-aggregate \
  --path plugins/ddd-modeling-skills/skills/ddd-3-context \
  --path plugins/ddd-modeling-skills/skills/ddd-4-model-diagram \
  --path plugins/ddd-modeling-skills/skills/diagram-feedback
```

## 除外されるスキル

以下のスキルはCodex CLIからのインストール対象外です：

### 内部スキル（ddd-modeling-skills）
フェーズスキルから内部的に呼び出される補助スキルです。単体での使用は想定されていません：

- event-storming-facilitator
- event-storming-diagram
- aggregate-designer
- bounded-context-mapper
- sequence-diagram
- class-diagram

## 使い方

インストール後、Codex内で以下のように使用できます：

```
# スキル一覧を表示
/skills

# スキルを呼び出す
$ddd-1-event-storming

# または暗黙的に呼び出し（descriptionに記載のトリガーワードで自動起動）
「イベントストーミングを始めたい」→ ddd-1-event-storming が自動起動
```

## ワークフロー

DDDモデリングは以下のフェーズ順で進めることを推奨します：

```
Phase 1: $ddd-1-event-storming
    ↓ 対話でドメインイベントを洗い出し
    → 01-event-storming.md
    → 01-event-storming-diagram.md (Mermaid図)

Phase 2: $ddd-2-aggregate
    ↓ 不変条件から集約境界を設計
    → 02-aggregates.md

Phase 3: $ddd-3-context
    ↓ チーム/言語境界からコンテキストを発見
    → 03-bounded-contexts.md (コンテキストマップ含む)

Phase 4: $ddd-4-model-diagram
    ↓ シーケンス図・クラス図を生成
    → 04-sequence-*.md
    → 05-class-diagram.md
```

## 互換性について

このリポジトリのSKILL.mdはClaude CodeとCodex CLIの両方で動作するよう設計されています：

- **フロントマター**: 両環境で必須の `name`, `description` を使用
- **REFERENCE.md**: Claude Code固有ですが、SKILL.md内で明示参照すればCodexでも読み込み可能
- **追加メタデータ**: `metadata` フィールドはCodexで無視されるため、Claude固有情報を安全に共存可能

## トラブルシューティング

### スキルが見つからない場合

1. `$skill-installer list` でインストール済みスキルを確認
2. パスが正しいか確認（`plugins/<plugin>/skills/<skill>`）
3. リポジトリ名が正しいか確認

### スキルが動作しない場合

1. SKILL.mdの `description` にトリガーワードが含まれているか確認
2. 明示的に `$skill-name` で呼び出してテスト
