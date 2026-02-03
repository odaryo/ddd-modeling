# Event Storming Facilitator Reference

## Facilitation Question Bank

### Event Discovery Questions

**Initial exploration:**
- 「このビジネスプロセスの**ゴール**は何ですか？」
- 「成功した場合、最後に何が**起きています**か？」
- 「そこに至るまでに**何が起きる**必要がありますか？」

**Deepening:**
- 「{Event}の**前に**何が起きていますか？」
- 「{Event}の**後に**何が起きますか？」
- 「{Event}が**失敗**した場合は？」
- 「{Event}を**取り消す**ことはできますか？」

**Edge cases:**
- 「例外的なケースはありますか？」
- 「このルールに**例外**はありますか？」
- 「**タイムアウト**や**期限切れ**はありますか？」

### Command Discovery Questions

- 「このイベントを**誰が/何が**引き起こしますか？」
- 「引き起こすために**何の情報**が必要ですか？」
- 「この操作に**前提条件**はありますか？」
- 「**許可されない**のはどんな場合ですか？」

### Policy Discovery Questions

- 「{Event}が起きたら**自動的に**何かしますか？」
- 「{Event}を**監視**している人/システムはいますか？」
- 「この情報を**通知**すべき相手はいますか？」
- 「**次のステップ**をトリガーする条件は？」

## Event Naming Conventions

### Good Examples

| Event Name | Why Good |
|------------|----------|
| `OrderPlaced` | Past tense, clear business meaning |
| `PaymentAuthorized` | Specific state (not just "processed") |
| `ShipmentDispatched` | Clear action completed |
| `InventoryReserved` | Business-relevant state change |

### Bad Examples

| Event Name | Problem | Better |
|------------|---------|--------|
| `PlaceOrder` | Imperative (command, not event) | `OrderPlaced` |
| `OrderEvent` | Too vague | `OrderPlaced`, `OrderCancelled` |
| `SaveOrder` | Technical, not business | `OrderPlaced` |
| `OrderDTO` | Data structure, not event | `OrderPlaced` |
| `ProcessPayment` | Imperative | `PaymentProcessed` |

## Anti-Patterns

### Session Anti-Patterns

| Anti-Pattern | Symptom | Solution |
|--------------|---------|----------|
| **Tech-first** | Discussing databases, APIs early | Redirect to business outcomes |
| **Solution-jumping** | "We should use Kafka for this" | Focus on what, not how |
| **Scope creep** | Every edge case explored immediately | Park items, return later |
| **Single voice** | One person dominates | Rotate facilitator questions |
| **Analysis paralysis** | Endless debate on naming | Timebox, decide and move on |

### Modeling Anti-Patterns

| Anti-Pattern | Example | Problem |
|--------------|---------|---------|
| **CRUD events** | `OrderCreated`, `OrderUpdated` | Too generic, loses business meaning |
| **UI events** | `ButtonClicked`, `FormSubmitted` | Not domain events |
| **Composite events** | `OrderPlacedAndPaid` | Split into atomic events |
| **Missing actor** | Event with no clear trigger | Every event needs a cause |

## Sticky Note Colors (Reference)

Traditional event storming uses colored sticky notes:

| Color | Element | Mermaid Style |
|-------|---------|---------------|
| Orange | Domain Event | `fill:#FF6B35` |
| Blue | Command | `fill:#4A90D9` |
| Yellow | Actor/User | `fill:#FFD93D` |
| Lilac/Purple | Policy | `fill:#9B59B6` |
| Pink | External System | `fill:#E91E8C` |
| Green | Aggregate | `fill:#27AE60` |
| Red | Hot Spot / Question | `fill:#E74C3C` |

## Conflict Resolution

### Term Conflicts

When different stakeholders use different terms:

1. Document both terms
2. Ask: "What's the **subtle difference** between these?"
3. If same concept → Pick canonical term, list aliases
4. If different concepts → Keep both, clarify distinction

### Boundary Conflicts

When unclear if one event or two:

1. Ask: "Can these happen **independently**?"
2. Ask: "Do they have **different triggers**?"
3. Ask: "Could one **fail** while the other **succeeds**?"

If yes to any → Separate events

### Sequence Conflicts

When order is unclear:

1. Draw both versions
2. Ask: "What would **break** if order was reversed?"
3. Mark as "needs domain expert clarification" if unresolved

## Session Checklist

Before starting:
- [ ] Scenario/domain clearly defined
- [ ] Key stakeholders present (or their input available)
- [ ] Timebox agreed (recommend 45-90 min per scenario)

During session:
- [ ] Happy path captured first
- [ ] Events in past tense
- [ ] Commands linked to events
- [ ] Actors identified
- [ ] Policies captured
- [ ] External systems noted
- [ ] Hot spots marked for follow-up

After session:
- [ ] Results documented in `01-event-storming.md`
- [ ] Open questions listed
- [ ] Next steps identified
