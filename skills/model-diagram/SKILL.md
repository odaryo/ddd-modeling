---
name: model-diagram
description: DDDモデリング Phase 4。集約定義からクラス図とシーケンス図をMermaidで生成します。「クラス図」「シーケンス図」「ドメインモデル図」「DDDフェーズ4」時に使用。
user-invocable: false
---

# DDD Modeling Phase 4: Model Diagrams

集約定義からクラス図とシーケンス図を生成するフェーズ4スキル。

## Session Selection

**開始時に必ず実行:**

1. `docs/modeling/` ディレクトリを検索して既存セッションを一覧表示
2. 既存セッションがある場合、ユーザーに選択肢を提示:

```
既存のモデリングセッションが見つかりました:
1. 2024-01-15-order-management (Phase 2まで完了)
2. 2024-01-10-user-auth (Phase 4まで完了)
3. 新規セッションを開始

どれを選びますか？
```

3. 既存セッション選択時:
   - `02-aggregates.md` の有無を確認（必須の前提ファイル）
   - `04-sequence-*.md`, `05-class-diagram.md` が存在する場合:
     - 「続きから編集しますか？それとも最初からやり直しますか？」
     - 続きの場合: ファイルを読み込み、追加のシナリオや修正を行う
     - やり直しの場合: 既存ファイルをバックアップ後、新規作成
   - 存在しない場合: 新規作成として Phase 4 を開始

4. 新規セッション選択時:
   - トピック名を確認して `docs/modeling/{YYYY-MM-DD}-{topic}/` を作成
   - Phase 1-3 をスキップして開始することをユーザーに確認
   - ただし `02-aggregates.md` がないと図生成は難しいことを警告

## Prerequisites

Phase 1-3の出力を読み込み:
- `docs/modeling/{session}/01-event-storming.md`
- `docs/modeling/{session}/02-aggregates.md`
- `docs/modeling/{session}/03-bounded-contexts.md` (optional)

## Workflow

```
1. Select Scenarios    → シーケンス図を作成するシナリオを選択
2. Generate Sequences  → 各シナリオのシーケンス図を生成
3. Generate Class Diagram → 集約定義からクラス図を生成
4. Export              → ファイルを保存
```

## Output Files

- `docs/modeling/{session}/04-sequence-{usecase}.md` (per scenario)
- `docs/modeling/{session}/05-class-diagram.md`

---

## Part 1: Sequence Diagram Generation

### Step 1: Scenario Selection

If event storming exists:
- 「以下のシナリオが見つかりました。どれを図にしますか？」
- List available scenarios from events

If not:
- 「どのユースケース/シナリオを図にしますか？」

### Step 2: Identify Participants

Ask:
- 「このシナリオに関わる**アクター**は誰ですか？」
- 「関与する**システム/サービス**は何ですか？」

### Step 3: Define Message Flow

For each step:
- 「誰が誰に何を送りますか？」
- 「同期ですか？非同期ですか？」
- 「条件分岐はありますか？」

---

## Part 2: Class Diagram Generation

### DDD Stereotypes

| Stereotype | Meaning |
|------------|---------|
| `<<AggregateRoot>>` | Entry point to aggregate |
| `<<Entity>>` | Has identity, lifecycle |
| `<<ValueObject>>` | Immutable, no identity |
| `<<DomainService>>` | Stateless domain logic |
| `<<Repository>>` | Aggregate persistence |

### Generation Process

1. Load aggregate definitions
2. Classify elements: AggregateRoot, Entity, Value Object
3. Define relationships: Composition, association, inheritance
4. Generate Mermaid class diagram

---

## Validation Checklist

### Sequence Diagrams
- [ ] All participants identified
- [ ] Message direction clear (→ vs ←)
- [ ] Sync vs async distinguished
- [ ] Error paths shown (alt/opt)
- [ ] Autonumber for readability

### Class Diagrams
- [ ] All aggregates from input represented
- [ ] Stereotypes correctly applied
- [ ] Composition vs association clear
- [ ] Aggregate boundaries visible
- [ ] Cross-aggregate references by ID only (dashed line)

---

## Error Handling

### 前提ファイルが存在しない場合
- 「集約定義（02-aggregates.md）が見つかりません」
- ユーザーに直接定義を入力させるか、先に `/ddd-modeling:2-aggregate` の実行を案内

### 既存セッションの破損
- ファイルが読み込めない場合、バックアップを提案
- 新規作成オプションを提示

---

## References

詳細なガイド:
- シーケンス図構文とパターン: [references/sequence-diagram.md](references/sequence-diagram.md)
- クラス図構文とパターン: [references/class-diagram.md](references/class-diagram.md)

---

## Summary

After Phase 4 completion:

```
docs/modeling/{session}/
├── 01-event-storming.md
├── 01-event-storming-diagram.md
├── 02-aggregates.md
├── 03-bounded-contexts.md
├── 04-sequence-{usecase1}.md
├── 04-sequence-{usecase2}.md
└── 05-class-diagram.md
```

## Diagram Review

For feedback on generated diagrams:
- Use `/ddd-modeling:feedback` with the file path
- Get DDD-focused review and improvement suggestions
