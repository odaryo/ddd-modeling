# Aggregate Designer Reference

## Aggregate Design Rules

### Core Principles

1. **Consistency Boundary**: Aggregate defines a transactional consistency boundary
2. **Single Root**: One entity serves as the aggregate root
3. **Reference by ID**: Other aggregates reference by ID only, not direct object reference
4. **Small Aggregates**: Prefer smaller aggregates when possible

### Size Guidelines

| Sign | Meaning | Action |
|------|---------|--------|
| Many invariants across entities | Might need same aggregate | Group together |
| Large transaction scope | Aggregate too big | Consider splitting |
| Frequent concurrent conflicts | Aggregate too big | Split along conflict lines |
| Orphan entities after root delete | Lifecycle dependency | Include in aggregate |

## Invariant Categories

### Single-Entity Invariants
Rules within one entity:
- `Order.status` valid state transitions
- `LineItem.quantity > 0`
- `Email` format validation

### Cross-Entity Invariants
Rules spanning multiple entities:
- Order total = sum(LineItems)
- Max 10 items per order
- At least one payment method

### Temporal Invariants
Rules about time:
- Order can only be cancelled within 24h
- Subscription must have future end date

## Boundary Heuristics

### Same Aggregate When:

| Question | If Yes |
|----------|--------|
| Must change atomically? | Same aggregate |
| Created/deleted together? | Same aggregate |
| Makes no sense alone? | Same aggregate |
| Shares invariants? | Same aggregate |

### Separate Aggregates When:

| Question | If Yes |
|----------|--------|
| Can exist independently? | Separate |
| Changed by different users/processes? | Separate |
| Different rate of change? | Separate |
| Would cause lock contention? | Separate |

## Anti-Patterns

### God Aggregate
**Symptom**: One huge aggregate containing everything

**Problems**:
- High contention
- Large transactions
- Difficult to scale

**Solution**: Split by invariant boundaries

### Anemic Aggregate
**Symptom**: Aggregate with only getters/setters, logic elsewhere

**Problems**:
- Invariants not enforced
- Business logic scattered

**Solution**: Move behavior into aggregate

### Reference by Object
**Symptom**: Aggregate holds direct reference to another aggregate

**Problems**:
- Unclear boundaries
- Transaction scope creep
- Tight coupling

**Solution**: Reference by ID only

### Premature Splitting
**Symptom**: Entities that must change together are in different aggregates

**Problems**:
- Eventual consistency issues
- Complex compensating transactions

**Solution**: Merge if invariants require it

## Transaction Boundaries

### Aggregate = Transaction Boundary

```
❌ Bad: Transaction spans multiple aggregates
   BEGIN TRANSACTION
     Update Order
     Update Inventory
     Update Customer
   COMMIT

✅ Good: One aggregate per transaction
   BEGIN TRANSACTION
     Update Order (including LineItems)
   COMMIT

   -- Eventual consistency for others via events
   OrderPlaced → UpdateInventory
   OrderPlaced → UpdateCustomerStats
```

### Eventual Consistency Patterns

When aggregates must coordinate:

| Pattern | Use When |
|---------|----------|
| Domain Events | Async, can tolerate delay |
| Saga/Process Manager | Multi-step, compensatable |
| Choreography | Simple, few steps |
| Orchestration | Complex, many steps |

## Entity vs Value Object

### Entity
- Has identity (ID)
- Lifecycle matters
- Can be referenced externally
- Example: `Order`, `Customer`

### Value Object
- Identity by attributes
- Immutable
- Replaceable
- Example: `Money`, `Address`, `DateRange`

### Decision Guide

| Question | Entity | Value Object |
|----------|--------|--------------|
| Does identity matter? | Yes | No |
| Can it be replaced with equivalent? | No | Yes |
| Is it mutable? | Often | Never |
| Needs its own lifecycle? | Yes | No |

## Lifecycle Patterns

### Create

```markdown
| Pattern | Example |
|---------|---------|
| Factory Method | Order.create(customer, items) |
| Constructor | new Order(id, customer, items) |
| Builder | OrderBuilder.withCustomer().withItems().build() |
```

### Delete

```markdown
| Pattern | When to Use |
|---------|-------------|
| Hard Delete | No audit requirements |
| Soft Delete | Need history, can be restored |
| Archive | Legal requirements, rarely accessed |
```

### State Transitions

```markdown
Order States: Draft → Placed → Paid → Shipped → Delivered
                 ↓         ↓
              Abandoned  Cancelled
```

## Common Aggregate Patterns

### Order Pattern
```
Order (Root)
├── LineItem (Entity, 1..*)
├── ShippingInfo (Value Object)
├── BillingInfo (Value Object)
└── OrderStatus (Value Object)
```

### Account Pattern
```
Account (Root)
├── Transaction (Entity, 0..*)
└── Balance (Value Object)

Invariant: Balance = sum(Transactions)
```

### Reservation Pattern
```
Reservation (Root)
├── ReservedItem (Entity, 1..*)
├── TimeSlot (Value Object)
└── ReservationStatus (Value Object)

Invariant: Items available in TimeSlot
```

## Validation Checklist

Before finalizing aggregate design:

- [ ] Each aggregate has exactly one root
- [ ] All invariants can be enforced within single transaction
- [ ] External references are by ID only
- [ ] Lifecycle dependencies are contained
- [ ] Aggregate size is reasonable (< 10 entities typically)
- [ ] No circular aggregate references
- [ ] Clear creation and deletion rules
