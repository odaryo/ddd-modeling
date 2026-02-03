# スキル名変更 実装計画

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** スキルの呼び出し形式を `/ddd-modeling:1-event-storming` に統一する

**Architecture:** プラグイン名を `ddd-modeling` に短縮し、各スキル名から `ddd-` プレフィックスを削除。ディレクトリ名、設定ファイル、相互参照、ドキュメントを一括更新。

**Tech Stack:** Git (mv), Markdown, JSON

---

## Task 1: プラグインディレクトリのリネーム

**Files:**
- Rename: `plugins/ddd-modeling-skills/` → `plugins/ddd-modeling/`

**Step 1: ディレクトリをリネーム**

```bash
git mv plugins/ddd-modeling-skills plugins/ddd-modeling
```

**Step 2: 確認**

Run: `ls plugins/`
Expected: `ddd-modeling` が表示される

**Step 3: Commit**

```bash
git add -A
git commit -m "refactor: プラグインディレクトリ名を ddd-modeling に変更"
```

---

## Task 2: スキルディレクトリのリネーム

**Files:**
- Rename: `plugins/ddd-modeling/skills/ddd-1-event-storming/` → `plugins/ddd-modeling/skills/1-event-storming/`
- Rename: `plugins/ddd-modeling/skills/ddd-2-aggregate/` → `plugins/ddd-modeling/skills/2-aggregate/`
- Rename: `plugins/ddd-modeling/skills/ddd-3-context/` → `plugins/ddd-modeling/skills/3-context/`
- Rename: `plugins/ddd-modeling/skills/ddd-4-model-diagram/` → `plugins/ddd-modeling/skills/4-model-diagram/`
- Rename: `plugins/ddd-modeling/skills/diagram-feedback/` → `plugins/ddd-modeling/skills/feedback/`

**Step 1: 各ディレクトリをリネーム**

```bash
git mv plugins/ddd-modeling/skills/ddd-1-event-storming plugins/ddd-modeling/skills/1-event-storming
git mv plugins/ddd-modeling/skills/ddd-2-aggregate plugins/ddd-modeling/skills/2-aggregate
git mv plugins/ddd-modeling/skills/ddd-3-context plugins/ddd-modeling/skills/3-context
git mv plugins/ddd-modeling/skills/ddd-4-model-diagram plugins/ddd-modeling/skills/4-model-diagram
git mv plugins/ddd-modeling/skills/diagram-feedback plugins/ddd-modeling/skills/feedback
```

**Step 2: 確認**

Run: `ls plugins/ddd-modeling/skills/`
Expected: `1-event-storming`, `2-aggregate`, `3-context`, `4-model-diagram`, `feedback` が表示される

**Step 3: Commit**

```bash
git add -A
git commit -m "refactor: スキルディレクトリ名を短縮形に変更"
```

---

## Task 3: 設定ファイルの更新

**Files:**
- Modify: `.claude-plugin/marketplace.json`
- Modify: `plugins/ddd-modeling/.claude-plugin/plugin.json`

**Step 1: marketplace.json を更新**

`.claude-plugin/marketplace.json` の plugins 配列内を編集:

```json
{
  "name": "ddd-modeling",
  "source": "./plugins/ddd-modeling",
  "description": "DDDモデリング支援スキル集 - イベントストーミング、集約設計、境界コンテキストマッピング、UML図生成"
}
```

**Step 2: plugin.json を更新**

`plugins/ddd-modeling/.claude-plugin/plugin.json` を編集:

```json
{
  "name": "ddd-modeling",
  "description": "DDDモデリング支援スキル集 - イベントストーミング、集約設計、境界コンテキストマッピング、UML図生成をチーム設計会議で対話形式で実行",
  "version": "1.0.0",
  "author": {
    "name": "odaryo"
  }
}
```

**Step 3: 確認**

Run: `cat .claude-plugin/marketplace.json && cat plugins/ddd-modeling/.claude-plugin/plugin.json`
Expected: 両ファイルで `"name": "ddd-modeling"` が設定されている

**Step 4: Commit**

```bash
git add .claude-plugin/marketplace.json plugins/ddd-modeling/.claude-plugin/plugin.json
git commit -m "refactor: 設定ファイルのプラグイン名を ddd-modeling に更新"
```

---

## Task 4: フェーズスキルのフロントマター更新

**Files:**
- Modify: `plugins/ddd-modeling/skills/1-event-storming/SKILL.md` (line 2)
- Modify: `plugins/ddd-modeling/skills/2-aggregate/SKILL.md` (line 2)
- Modify: `plugins/ddd-modeling/skills/3-context/SKILL.md` (line 2)
- Modify: `plugins/ddd-modeling/skills/4-model-diagram/SKILL.md` (line 2)
- Modify: `plugins/ddd-modeling/skills/feedback/SKILL.md` (line 2)

**Step 1: 各SKILL.mdのnameを更新**

| ファイル | 変更前 | 変更後 |
|----------|--------|--------|
| 1-event-storming/SKILL.md | `name: ddd-1-event-storming` | `name: 1-event-storming` |
| 2-aggregate/SKILL.md | `name: ddd-2-aggregate` | `name: 2-aggregate` |
| 3-context/SKILL.md | `name: ddd-3-context` | `name: 3-context` |
| 4-model-diagram/SKILL.md | `name: ddd-4-model-diagram` | `name: 4-model-diagram` |
| feedback/SKILL.md | `name: diagram-feedback` | `name: feedback` |

**Step 2: 確認**

Run: `grep "^name:" plugins/ddd-modeling/skills/*/SKILL.md`
Expected: 各スキルで短縮形の名前が設定されている

**Step 3: Commit**

```bash
git add plugins/ddd-modeling/skills/*/SKILL.md
git commit -m "refactor: フェーズスキルのフロントマター名を短縮形に更新"
```

---

## Task 5: 内部スキルのdescription更新

**Files:**
- Modify: `plugins/ddd-modeling/skills/event-storming-facilitator/SKILL.md` (line 3)
- Modify: `plugins/ddd-modeling/skills/event-storming-diagram/SKILL.md` (line 3)
- Modify: `plugins/ddd-modeling/skills/aggregate-designer/SKILL.md` (line 3)
- Modify: `plugins/ddd-modeling/skills/bounded-context-mapper/SKILL.md` (line 3)
- Modify: `plugins/ddd-modeling/skills/class-diagram/SKILL.md` (line 3)
- Modify: `plugins/ddd-modeling/skills/sequence-diagram/SKILL.md` (line 3)

**Step 1: 各内部スキルのdescriptionを更新**

| スキル | 変更内容 |
|--------|----------|
| event-storming-facilitator | `ddd-1-event-storming` → `1-event-storming` |
| event-storming-diagram | `ddd-1-event-storming` → `1-event-storming` |
| aggregate-designer | `ddd-2-aggregate` → `2-aggregate` |
| bounded-context-mapper | `ddd-3-context` → `3-context` |
| class-diagram | `ddd-4-model-diagram` → `4-model-diagram` |
| sequence-diagram | `ddd-4-model-diagram` → `4-model-diagram` |

**Step 2: 確認**

Run: `grep "description:" plugins/ddd-modeling/skills/{event-storming-facilitator,event-storming-diagram,aggregate-designer,bounded-context-mapper,class-diagram,sequence-diagram}/SKILL.md`
Expected: 各descriptionで短縮形の参照が使われている

**Step 3: Commit**

```bash
git add plugins/ddd-modeling/skills/{event-storming-facilitator,event-storming-diagram,aggregate-designer,bounded-context-mapper,class-diagram,sequence-diagram}/SKILL.md
git commit -m "refactor: 内部スキルのdescriptionで参照名を短縮形に更新"
```

---

## Task 6: スキル内の相互参照更新

**Files:**
- Modify: `plugins/ddd-modeling/skills/1-event-storming/SKILL.md`
- Modify: `plugins/ddd-modeling/skills/2-aggregate/SKILL.md`
- Modify: `plugins/ddd-modeling/skills/3-context/SKILL.md`
- Modify: `plugins/ddd-modeling/skills/4-model-diagram/SKILL.md`
- Modify: `plugins/ddd-modeling/skills/event-storming-diagram/SKILL.md`

**Step 1: 各ファイル内の次フェーズ参照を更新**

| 置換対象 | 変更後 |
|----------|--------|
| `/ddd-2-aggregate` | `/ddd-modeling:2-aggregate` |
| `/ddd-3-context` | `/ddd-modeling:3-context` |
| `/ddd-4-model-diagram` | `/ddd-modeling:4-model-diagram` |
| `/diagram-feedback` | `/ddd-modeling:feedback` |

**Step 2: 確認**

Run: `grep -r "/ddd-modeling:" plugins/ddd-modeling/skills/`
Expected: 新しい形式の参照が表示される

Run: `grep -rE "/ddd-[1-4]|/diagram-feedback" plugins/ddd-modeling/skills/`
Expected: 一致なし（古い参照が残っていない）

**Step 3: Commit**

```bash
git add plugins/ddd-modeling/skills/
git commit -m "refactor: スキル内の相互参照を新形式に更新"
```

---

## Task 7: CLAUDE.md 更新

**Files:**
- Modify: `CLAUDE.md`

**Step 1: アーキテクチャ図を更新**

```
├── plugins/
│   └── ddd-modeling/           # DDDモデリングスキル集
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── skills/
│       │   ├── 1-event-storming/       # Phase 1: イベントストーミング
│       │   ├── 2-aggregate/            # Phase 2: 集約設計
│       │   ├── 3-context/              # Phase 3: 境界コンテキスト
│       │   ├── 4-model-diagram/        # Phase 4: モデル図生成
│       │   ├── feedback/               # 補助: 図のフィードバック
│       │   └── [内部スキル...]
```

**Step 2: ワークフロー図を更新**

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

**Step 3: スキルファイル構造テーブルを更新**

| スキル | 説明 | Codex対応 |
|--------|------|-----------|
| 1-event-storming | Phase 1: イベントストーミング | Yes |
| 2-aggregate | Phase 2: 集約設計 | Yes |
| 3-context | Phase 3: 境界コンテキスト | Yes |
| 4-model-diagram | Phase 4: モデル図生成 | Yes |
| feedback | 図へのフィードバック | Yes |

**Step 4: 確認**

Run: `grep -E "ddd-modeling:|1-event-storming|2-aggregate|3-context|4-model-diagram|feedback" CLAUDE.md`
Expected: 新しい名前が正しく設定されている

**Step 5: Commit**

```bash
git add CLAUDE.md
git commit -m "docs: CLAUDE.mdを新スキル名に更新"
```

---

## Task 8: README.md 更新

**Files:**
- Modify: `README.md`

**Step 1: ワークフロー図を更新**

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

**Step 2: スキルテーブルを更新**

| スキル名 | 説明 | Codex対応 |
|----------|------|-----------|
| 1-event-storming | DDDフェーズ1: イベントストーミング | Yes |
| 2-aggregate | DDDフェーズ2: 集約設計 | Yes |
| 3-context | DDDフェーズ3: 境界コンテキスト | Yes |
| 4-model-diagram | DDDフェーズ4: モデル図生成 | Yes |
| feedback | UML/Mermaid図へのフィードバック | Yes |

**Step 3: 導入方法セクションを更新**

- プラグイン名 `ddd-modeling-skills` → `ddd-modeling`
- Codexインストールパス更新

**Step 4: ディレクトリ構造図を更新**

```
├── plugins/
│   └── ddd-modeling/           # DDDモデリングスキル集
│       ├── skills/
│       │   ├── 1-event-storming/
│       │   ├── 2-aggregate/
│       │   ├── 3-context/
│       │   ├── 4-model-diagram/
│       │   ├── feedback/
```

**Step 5: 使い方セクションの例を更新**

```
# 明示的に呼び出す
$1-event-storming
```

**Step 6: 確認**

Run: `grep -E "ddd-modeling|1-event-storming" README.md`
Expected: 新しい名前が正しく設定されている

**Step 7: Commit**

```bash
git add README.md
git commit -m "docs: README.mdを新スキル名に更新"
```

---

## Task 9: 動作確認

**Step 1: ファイル構造確認**

Run: `find plugins/ddd-modeling -type f -name "*.md" -o -name "*.json" | head -20`
Expected: 新しいディレクトリ構造でファイルが存在

**Step 2: 古い名前の残存確認**

Run: `grep -rE "ddd-1-event-storming|ddd-2-aggregate|ddd-3-context|ddd-4-model-diagram|diagram-feedback|ddd-modeling-skills" --include="*.md" --include="*.json" .`
Expected: docs/plans/ 以外で一致なし（設計書は許容）

**Step 3: 新しい名前の設定確認**

Run: `grep -r "name.*ddd-modeling" .claude-plugin/ plugins/ddd-modeling/.claude-plugin/`
Expected: 両設定ファイルで `ddd-modeling` が設定されている

**Step 4: 最終確認コミット**

```bash
git status
```

Expected: クリーンな状態（すべてコミット済み）
