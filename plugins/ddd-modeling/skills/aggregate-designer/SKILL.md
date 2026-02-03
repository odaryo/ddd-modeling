---
name: aggregate-designer
description: "[内部スキル] 集約（Aggregate）の境界設計。2-aggregateから呼び出される。"
user-invocable: false
---

# Aggregate Designer

Design aggregate boundaries through structured dialogue, identifying invariants, consistency boundaries, lifecycles, and aggregate roots.

## Input

Optionally reads: `docs/modeling/{session}/01-event-storming.md`

Can also start fresh with user-provided domain concepts.

## Workflow

```
1. Concept Review    → List candidate entities from events
2. Invariant Discovery → What rules must ALWAYS be true?
3. Boundary Analysis   → What changes together?
4. Lifecycle Mapping   → Creation to deletion
5. Root Identification → Single entry point per aggregate
6. Export             → Save aggregate definitions
```

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

## Output Format

Save to: `docs/modeling/{session}/02-aggregates.md`

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
- [ ] Map bounded contexts: `/bounded-context-mapper`
- [ ] Generate class diagram: `/class-diagram`
```

## Reference

For detailed design rules and anti-patterns, see [REFERENCE.md](REFERENCE.md).
