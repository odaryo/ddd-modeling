---
name: context
description: DDDモデリング Phase 3。境界付けられたコンテキスト（Bounded Context）を対話形式で発見し、コンテキストマップをMermaidで生成します。「境界コンテキスト」「コンテキストマップ」「DDDフェーズ3」時に使用。
user-invocable: false
---

# DDD Modeling Phase 3: Bounded Context Discovery

境界付けられたコンテキスト（Bounded Context）を発見し、コンテキストマップを生成するフェーズ3スキル。

## Session Selection

**開始時に必ず実行:**

1. `docs/modeling/` ディレクトリを検索して既存セッションを一覧表示
2. 既存セッションがある場合、ユーザーに選択肢を提示:

```
既存のモデリングセッションが見つかりました:
1. 2024-01-15-order-management (Phase 2まで完了)
2. 2024-01-10-user-auth (Phase 3まで完了)
3. 新規セッションを開始

どれを選びますか？
```

3. 既存セッション選択時:
   - `01-event-storming.md`, `02-aggregates.md` の有無を確認
   - `03-bounded-contexts.md` が存在する場合:
     - 「続きから編集しますか？それとも最初からやり直しますか？」
     - 続きの場合: ファイルを読み込み、内容を確認して未完了箇所を特定
     - やり直しの場合: 既存ファイルをバックアップ後、新規作成
   - 存在しない場合: 新規作成として Phase 3 を開始

4. 新規セッション選択時:
   - トピック名を確認して `docs/modeling/{YYYY-MM-DD}-{topic}/` を作成
   - Phase 1-2 をスキップして開始することをユーザーに確認

## Prerequisites

Phase 1, 2の出力を読み込み:
- `docs/modeling/{session}/01-event-storming.md`
- `docs/modeling/{session}/02-aggregates.md`

または、ユーザーが提供する情報から開始可能。

## Workflow

```
1. Team/Org Review     → チーム構造の理解
2. Language Analysis   → ユビキタス言語の境界を特定
3. Context Discovery   → 境界コンテキストを定義
4. Relationship Mapping → コンテキスト間の関係を定義
5. Integration Points  → 統合メカニズムを特定
6. Export              → コンテキストマップを生成
```

## Output File

- `docs/modeling/{session}/03-bounded-contexts.md`

---

## Session Flow

### Phase 1: Team/Organization Review

Ask:
- 「このドメインに関わる**チーム**はいくつありますか？」
- 「それぞれのチームの**責任範囲**は？」
- 「チーム間の**コミュニケーション頻度**は？」

Conway's Law insight: Team boundaries often align with context boundaries.

### Phase 2: Language Analysis

Ask:
- 「同じ言葉で**異なる意味**を持つものはありますか？」
- 「**Customer**はどの文脈でどういう意味ですか？」
- 「この用語が**最も重要**なのはどのチームですか？」

Capture language variations:
```markdown
## Language Analysis

| Term | Context A Meaning | Context B Meaning |
|------|-------------------|-------------------|
| Customer | 購入者、アカウント情報 | 配送先、連絡先 |
| Product | 販売単位、価格 | 在庫単位、ロケーション |
| Order | 購入トランザクション | 出荷指示 |
```

### Phase 3: Context Discovery

Based on language/team analysis, propose contexts:
- 「以下の境界コンテキストが考えられます」

For each proposed context:
- 「このコンテキストの**主な責任**は何ですか？」
- 「**含まれる集約**はどれですか？」
- 「**除外すべきもの**はありますか？」

### Phase 4: Relationship Mapping

For each context pair:
- 「{Context A}と{Context B}は**データをやり取り**しますか？」
- 「どちらが**上流**（提供側）ですか？」
- 「**依存関係の強さ**はどの程度ですか？」

Present relationship patterns and ask user to select appropriate one.

### Phase 5: Integration Points

Ask:
- 「コンテキスト間の**通信方法**は？（API, イベント, 共有DB）」
- 「**データ変換**は必要ですか？」
- 「**障害時**の影響範囲は？」

---

## Context Relationship Patterns

| Pattern | Description | Use When |
|---------|-------------|----------|
| **Shared Kernel** | 共有コード/モデル | 密接に協力するチーム |
| **Customer-Supplier** | 上流が下流の要求を考慮 | 協力的な上下関係 |
| **Conformist** | 下流が上流に従う | 上流に影響力なし |
| **Anti-Corruption Layer** | 変換レイヤーで保護 | レガシー/外部システム |
| **Open Host Service** | 公開API/プロトコル | 多数の下流がいる |
| **Published Language** | 標準化された交換形式 | 業界標準がある |
| **Separate Ways** | 統合しない | 統合コストが価値を超える |
| **Partnership** | 相互依存、共同進化 | 成功が相互依存 |

---

## Output Format: 03-bounded-contexts.md

```markdown
# Bounded Contexts: {Topic}

Date: {YYYY-MM-DD}

## Context Overview

| Context | Responsibility | Team | Key Aggregates |
|---------|---------------|------|----------------|
| Sales | 販売・注文管理 | Sales Team | Order, Customer |
| Shipping | 配送管理 | Logistics | Shipment, Carrier |
| Inventory | 在庫管理 | Warehouse | Stock, Location |

## Context Definitions

### Sales Context

**Responsibility:** 商品の販売と注文の管理

**Ubiquitous Language:**
- Customer: 購入者（アカウント、購入履歴を持つ）
- Order: 購入トランザクション
- Product: 販売可能な商品単位

**Aggregates:**
- Order
- Customer

**Team:** Sales Team

---

### Shipping Context
{Same structure}

## Context Map

\`\`\`mermaid
flowchart TB
    subgraph Sales["Sales Context"]
        S_Order[Order]
        S_Customer[Customer]
    end

    subgraph Shipping["Shipping Context"]
        SH_Shipment[Shipment]
    end

    subgraph Inventory["Inventory Context"]
        I_Stock[Stock]
    end

    Sales -->|"Customer-Supplier"| Shipping
    Sales -->|"ACL"| Inventory
    Shipping -->|"Conformist"| Inventory
\`\`\`

## Relationship Details

| Upstream | Downstream | Pattern | Integration |
|----------|------------|---------|-------------|
| Sales | Shipping | Customer-Supplier | REST API |
| Inventory | Sales | ACL | Event-driven |
| Inventory | Shipping | Conformist | Shared DB (readonly) |

## Integration Points

### Sales → Shipping
- **Trigger:** OrderPlaced event
- **Data:** Order details, shipping address
- **Protocol:** REST API
- **ACL:** ShippingOrderAdapter

## Anti-Corruption Layers

### Sales-Inventory ACL
**Purpose:** Protect Sales model from Inventory complexity
**Transformations:**
- Inventory.StockItem → Sales.ProductAvailability
- Inventory.Location → (not exposed)

## Open Questions
{Any unresolved items}

## Next Steps
- [ ] Review with all teams
- [ ] Design integration contracts
- [ ] Proceed to Phase 4: `/ddd-modeling:4-model-diagram`
```

---

## Mermaid Context Map Styles

### Basic Context Map
```mermaid
flowchart TB
    classDef context fill:#E8F4FD,stroke:#1976D2,stroke-width:2px
    classDef external fill:#FFF3E0,stroke:#F57C00,stroke-width:2px

    subgraph Internal["Internal Contexts"]
        Sales[Sales]:::context
        Shipping[Shipping]:::context
    end

    subgraph External["External Systems"]
        Payment[(Payment Gateway)]:::external
    end

    Sales -->|"OHS"| Shipping
    Sales -->|"ACL"| Payment
```

### Detailed Context Map
```mermaid
flowchart TB
    subgraph Sales["Sales Context"]
        S_Order[Order]
        S_Customer[Customer]
    end

    subgraph Shipping["Shipping Context"]
        SH_Shipment[Shipment]
    end

    subgraph Inventory["Inventory Context"]
        I_Stock[Stock]
    end

    Sales -->|"Customer-Supplier"| Shipping
    Sales -->|"ACL"| Inventory
    Shipping -->|"Conformist"| Inventory
```

---

## Context Discovery Heuristics

### Signs of Separate Contexts

| Signal | Example |
|--------|---------|
| Different teams own the code | Sales vs Warehouse |
| Same word, different meaning | "Product" in Sales vs Inventory |
| Different lifecycle/cadence | Real-time orders vs batch inventory |
| Different data requirements | Full customer profile vs shipping address only |

### Signs of Same Context

| Signal | Example |
|--------|---------|
| Same team, tight collaboration | |
| Shared ubiquitous language | |
| Strong consistency requirements | |
| Frequent co-changes | |

---

## Pattern Selection Guide

```
Q: Do teams collaborate closely?
├─ Yes → Shared Kernel or Partnership
└─ No → Continue

Q: Does upstream consider downstream needs?
├─ Yes → Customer-Supplier
└─ No → Conformist or ACL

Q: Is upstream a legacy/external system?
├─ Yes → Anti-Corruption Layer
└─ No → Continue

Q: Does upstream serve many consumers?
├─ Yes → Open Host Service + Published Language
└─ No → Direct integration
```

---

## Validation Checklist

Before completing:
- [ ] All aggregates assigned to a context
- [ ] Ubiquitous language defined per context
- [ ] Relationship patterns selected with rationale
- [ ] Integration points identified
- [ ] ACLs defined where needed
- [ ] Context map diagram generated

---

## Next Steps

After completing Phase 3:
```
→ Phase 4: /ddd-modeling:4-model-diagram (クラス図・シーケンス図生成)
```
