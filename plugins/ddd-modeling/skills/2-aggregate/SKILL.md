---
name: 2-aggregate
description: DDDモデリング Phase 2。集約（Aggregate）の境界を対話形式で設計します。不変条件、整合性境界、ライフサイクル、集約ルートを特定。「集約設計」「Aggregate設計」「DDDフェーズ2」時に使用。
---

# DDD Modeling Phase 2: Aggregate Design

集約（Aggregate）の境界を対話形式で設計するフェーズ2スキル。

## Session Selection

**開始時に必ず実行:**

1. `docs/modeling/` ディレクトリを検索して既存セッションを一覧表示
2. 既存セッションがある場合、ユーザーに選択肢を提示:

```
既存のモデリングセッションが見つかりました:
1. 2024-01-15-order-management (Phase 1完了)
2. 2024-01-10-user-auth (Phase 2まで完了)
3. 新規セッションを開始

どれを選びますか？
```

3. 既存セッション選択時:
   - `01-event-storming.md` の有無を確認（Phase 1の成果物）
   - `02-aggregates.md` が存在する場合:
     - 「続きから編集しますか？それとも最初からやり直しますか？」
     - 続きの場合: ファイルを読み込み、内容を確認して未完了箇所を特定
     - やり直しの場合: 既存ファイルをバックアップ後、新規作成
   - 存在しない場合: 新規作成として Phase 2 を開始

4. 新規セッション選択時:
   - トピック名を確認して `docs/modeling/{YYYY-MM-DD}-{topic}/` を作成
   - Phase 1 をスキップして開始することをユーザーに確認

## Prerequisites

Phase 1の出力を読み込み:
- `docs/modeling/{session}/01-event-storming.md`

または、ユーザーが提供するドメイン概念から開始可能。

## Workflow

```
1. Concept Review      → イベントからエンティティ候補を抽出
2. Invariant Discovery → 常に真でなければならないルールを特定
3. Boundary Analysis   → 同時に変更されるべきものを特定
4. Lifecycle Mapping   → 作成から削除までのライフサイクル
5. Root Identification → 各集約の単一エントリポイントを特定
6. Export              → 集約定義を保存
```

## Output File

- `docs/modeling/{session}/02-aggregates.md`

---

## Session Flow

### Phase 1: Concept Review

If event storming exists:
- Extract nouns from events/commands as candidate entities
- Present list: 「イベントストーミングから以下のエンティティ候補を抽出しました」

Ask:
- 「他に重要な**ドメイン概念**はありますか？」
- 「これらの中で**独立して存在できない**ものはどれですか？」

### Phase 2: Invariant Discovery

For each candidate entity, ask:
- 「{Entity}について、**常に真でなければならないルール**は何ですか？」
- 「{Entity}が**不正な状態**になるのはどんなとき？」
- 「複数の{Entity}間で**同時に守るべきルール**はありますか？」

Capture invariants:
```markdown
## Invariants

| Entity | Invariant | Description |
|--------|-----------|-------------|
| Order | OrderTotal = sum(LineItems) | 合計金額は明細の合計と一致 |
| Order | LineItems.count >= 1 | 注文には最低1つの明細が必要 |
| LineItem | Quantity > 0 | 数量は正の数 |
```

### Phase 3: Boundary Analysis

Ask:
- 「{Entity A}と{Entity B}は**同じトランザクション**で更新される必要がありますか？」
- 「{Entity}を変更するとき、**他に影響を受ける**ものは？」
- 「**別々に**変更しても問題ないものはどれですか？」

Key questions for boundaries:
- 「これらを**同時に**ロックする必要がありますか？」
- 「片方だけ**成功**して問題ないですか？」

### Phase 4: Lifecycle Mapping

For each aggregate candidate:
- 「いつ**作成**されますか？」
- 「いつ**削除**されますか？」
- 「**親**が削除されたら、**子**はどうなりますか？」

Lifecycle patterns:
- **Dependent**: Child exists only within parent's lifetime
- **Independent**: Can outlive parent (reference only)

### Phase 5: Root Identification

For each aggregate:
- 「外部から**直接アクセス**されるのはどれですか？」
- 「**ID**で参照されるのはどれですか？」

Aggregate root criteria:
- Single entry point to the aggregate
- Owns all contained entities
- Controls access to invariants

---

## Output Format: 02-aggregates.md

```markdown
# Aggregate Design: {Topic}

Date: {YYYY-MM-DD}

## Aggregates Overview

| Aggregate | Root | Contains | Key Invariant |
|-----------|------|----------|---------------|
| Order | Order | LineItem, ShippingInfo | Total matches line items |
| Product | Product | - | SKU is unique |

## Detailed Definitions

### Aggregate: Order

**Root Entity:** Order

**Contained Entities:**
- LineItem (1..*)
- ShippingInfo (0..1)

**Value Objects:**
- Money (amount, currency)
- Address

**Invariants:**
1. Order must have at least one LineItem
2. Order total = sum of LineItem totals
3. ShippingInfo required before shipping

**Lifecycle:**
- Created: When customer places order
- Deleted: Soft delete only (audit requirements)

**External References:**
- Customer (by ID only)
- Product (by ID only)

---

### Aggregate: Product
{Same structure}

## Design Decisions

| Decision | Rationale |
|----------|-----------|
| Order contains LineItem | Must maintain total invariant in same transaction |
| Product is separate aggregate | Can be modified independently of orders |

## Open Questions
{Any unresolved items}

## Next Steps
- [ ] Review with domain experts
- [ ] Proceed to Phase 3: `/ddd-modeling:3-context`
- [ ] Or skip to Phase 4: `/ddd-modeling:4-model-diagram`
```

---

## Design Guidelines

### Aggregate Size

**Prefer small aggregates:**
- Easier to maintain consistency
- Better concurrency (less lock contention)
- Simpler to understand

**Signs of too-large aggregate:**
- Multiple unrelated invariants
- High contention in concurrent access
- Changes in one part rarely affect others

### Entity vs Value Object

| Criteria | Entity | Value Object |
|----------|--------|--------------|
| Identity | Has unique ID | No identity |
| Equality | By ID | By attributes |
| Lifecycle | Independent | Belongs to entity |
| Mutability | Can change | Immutable |

**Examples:**
- Entity: Order, Customer, Product
- Value Object: Money, Address, DateRange

### Cross-Aggregate References

**Rule:** Reference by ID only, not object reference

```
# Good
Order.customerId: CustomerID

# Bad
Order.customer: Customer
```

**Why:**
- Maintains aggregate independence
- Allows eventual consistency
- Clearer transaction boundaries

---

## Common Patterns

### Order with LineItems
```markdown
Aggregate: Order
├── Root: Order
├── Entity: LineItem (1..*)
├── VO: Money, Address
└── Ref: Customer (by ID)
```

### User with Profile
```markdown
Aggregate: User
├── Root: User
├── VO: Email, Password, Profile
└── Ref: Organization (by ID)
```

### Product Catalog
```markdown
Aggregate: Product
├── Root: Product
├── Entity: Variant (0..*)
├── VO: Price, SKU
└── Ref: Category (by ID)
```

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| God Aggregate | Contains everything | Split by invariant boundaries |
| Anemic Aggregate | No business logic | Move logic into aggregate |
| Direct Object Reference | Tight coupling | Use ID references |
| Missing Root | No clear entry point | Identify single access point |
| Shared Mutable State | Consistency issues | Each aggregate owns its state |

---

## Validation Checklist

Before completing:
- [ ] Each aggregate has exactly one root
- [ ] All invariants can be enforced by root
- [ ] Lifecycle of contained entities is clear
- [ ] Cross-aggregate references use IDs only
- [ ] Aggregate size is reasonable (not too large)
- [ ] Design decisions documented with rationale

---

## Next Steps

After completing Phase 2:
```
→ Phase 3: /ddd-modeling:3-context (境界コンテキスト発見)
→ Phase 4: /ddd-modeling:4-model-diagram (クラス図・シーケンス図生成)
```
