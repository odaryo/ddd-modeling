# スキル名変更設計書

## 概要

スキルの呼び出し形式を `/ddd-1-event-storming` から `/ddd-modeling:1-event-storming` に変更する。

## 変更後の呼び出し形式

| スキル | 呼び出し |
|--------|----------|
| Phase 1 | `/ddd-modeling:1-event-storming` |
| Phase 2 | `/ddd-modeling:2-aggregate` |
| Phase 3 | `/ddd-modeling:3-context` |
| Phase 4 | `/ddd-modeling:4-model-diagram` |
| Feedback | `/ddd-modeling:feedback` |

## 変更内容

### 1. ディレクトリ構造の変更

```
plugins/
└── ddd-modeling-skills/     →  ddd-modeling/
    └── skills/
        ├── ddd-1-event-storming/  →  1-event-storming/
        ├── ddd-2-aggregate/       →  2-aggregate/
        ├── ddd-3-context/         →  3-context/
        ├── ddd-4-model-diagram/   →  4-model-diagram/
        └── diagram-feedback/      →  feedback/
```

内部スキルは変更なし:
- aggregate-designer/
- bounded-context-mapper/
- class-diagram/
- event-storming-diagram/
- event-storming-facilitator/
- sequence-diagram/

### 2. 設定ファイルの変更

#### .claude-plugin/marketplace.json

```json
{
  "plugins": [
    {
      "name": "ddd-modeling",
      "source": "./plugins/ddd-modeling",
      "description": "DDDモデリング支援スキル集 - ..."
    }
  ]
}
```

#### plugins/ddd-modeling/.claude-plugin/plugin.json

```json
{
  "name": "ddd-modeling",
  "description": "...",
  "version": "1.0.0"
}
```

### 3. スキルのフロントマター変更

| ファイル | 変更前 | 変更後 |
|----------|--------|--------|
| 1-event-storming/SKILL.md | `name: ddd-1-event-storming` | `name: 1-event-storming` |
| 2-aggregate/SKILL.md | `name: ddd-2-aggregate` | `name: 2-aggregate` |
| 3-context/SKILL.md | `name: ddd-3-context` | `name: 3-context` |
| 4-model-diagram/SKILL.md | `name: ddd-4-model-diagram` | `name: 4-model-diagram` |
| feedback/SKILL.md | `name: diagram-feedback` | `name: feedback` |

### 4. スキル内の相互参照更新

#### フェーズスキル内の次フェーズ案内

| ファイル | 現在 | 変更後 |
|----------|------|--------|
| 1-event-storming | `/ddd-2-aggregate` | `/ddd-modeling:2-aggregate` |
| 2-aggregate | `/ddd-3-context` | `/ddd-modeling:3-context` |
| 2-aggregate | `/ddd-4-model-diagram` | `/ddd-modeling:4-model-diagram` |
| 3-context | `/ddd-4-model-diagram` | `/ddd-modeling:4-model-diagram` |
| 4-model-diagram | `/diagram-feedback` | `/ddd-modeling:feedback` |
| event-storming-diagram | `/diagram-feedback` | `/ddd-modeling:feedback` |

#### 内部スキルのdescription

| スキル | 現在 | 変更後 |
|--------|------|--------|
| event-storming-facilitator | ddd-1-event-stormingから呼び出される | 1-event-stormingから呼び出される |
| event-storming-diagram | ddd-1-event-stormingから呼び出される | 1-event-stormingから呼び出される |
| aggregate-designer | ddd-2-aggregateから呼び出される | 2-aggregateから呼び出される |
| bounded-context-mapper | ddd-3-contextから呼び出される | 3-contextから呼び出される |
| class-diagram | ddd-4-model-diagramから呼び出される | 4-model-diagramから呼び出される |
| sequence-diagram | ddd-4-model-diagramから呼び出される | 4-model-diagramから呼び出される |

### 5. ドキュメント更新

#### CLAUDE.md

- アーキテクチャ図のディレクトリ名
- ワークフロー図のスキル呼び出し
- スキルファイル構造テーブル

#### README.md

- スキル名・呼び出し例の更新

## 実装手順

1. ディレクトリのリネーム（git mv使用）
2. 設定ファイルの更新（marketplace.json, plugin.json）
3. 各スキルのSKILL.mdフロントマター更新
4. スキル内相互参照の更新
5. CLAUDE.md, README.mdの更新
6. 動作確認
