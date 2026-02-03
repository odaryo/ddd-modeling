# Bounded Context Mapper Reference

## Context Relationship Patterns Detail

### Shared Kernel

**Description:** Two contexts share a subset of the domain model.

**When to use:**
- Teams are tightly integrated
- Shared concepts are core to both contexts
- Teams can coordinate changes

**Risks:**
- Tight coupling
- Coordination overhead
- Merge conflicts

**Example:**
```
Sales Context ←→ Marketing Context
       ↓
  Shared: Customer, Product catalog
```

### Customer-Supplier

**Description:** Upstream (supplier) serves downstream (customer). Downstream needs are considered in upstream planning.

**When to use:**
- Clear dependency direction
- Upstream willing to accommodate downstream
- Negotiated interface

**Roles:**
- Upstream: Provides API/events
- Downstream: Consumes, provides feedback

**Example:**
```
Order Context (Supplier) → Shipping Context (Customer)
  - Order provides shipping requirements
  - Shipping requests influence Order's interface
```

### Conformist

**Description:** Downstream conforms to upstream model without influence.

**When to use:**
- Upstream won't/can't change
- Cost of ACL > cost of conforming
- Upstream model is acceptable

**Risks:**
- Tight coupling to upstream model
- Changes in upstream cascade down

**Example:**
```
External Payment API → Our Payment Context
  - We conform to their model
  - No ACL needed if model is acceptable
```

### Anti-Corruption Layer (ACL)

**Description:** Translation layer protects downstream from upstream model.

**When to use:**
- Upstream model doesn't fit downstream needs
- Legacy system integration
- External system with different domain language

**Implementation:**
```
Upstream ─────────────────────────────────────→ Downstream
         │                                   │
         └── ACL (Adapter, Translator, Facade) ──┘
```

**Example:**
```python
# ACL translates legacy model to our model
class LegacyCustomerAdapter:
    def to_domain(self, legacy_customer):
        return Customer(
            id=CustomerID(legacy_customer.CUST_NO),
            name=CustomerName(
                legacy_customer.FNAME,
                legacy_customer.LNAME
            )
        )
```

### Open Host Service (OHS)

**Description:** Upstream provides a well-defined, stable API/protocol for multiple downstream consumers.

**When to use:**
- Multiple downstream consumers
- Need stable, versioned interface
- Public or semi-public API

**Characteristics:**
- Versioned endpoints
- Documentation
- Backward compatibility

**Example:**
```
Product Catalog Context (OHS)
    ↓ REST API v1, v2
    ├── Sales Context
    ├── Marketing Context
    └── Partner Integration
```

### Published Language

**Description:** A shared, standardized language for exchange.

**When to use:**
- Industry standards exist
- Multiple parties need common understanding
- Cross-organization integration

**Examples:**
- JSON Schema
- Protocol Buffers
- Industry standards (HL7, EDIFACT)

### Separate Ways

**Description:** No integration between contexts.

**When to use:**
- Integration cost > benefit
- Contexts truly independent
- Duplication is acceptable

**Risks:**
- Data inconsistency
- Duplicated functionality

### Partnership

**Description:** Two contexts evolve together with mutual dependency.

**When to use:**
- Success depends on both contexts
- Teams can coordinate closely
- Shared goals and timeline

**Characteristics:**
- Joint planning
- Coordinated releases
- Shared responsibility

## Integration Patterns

### Synchronous Integration

| Pattern | Use Case | Trade-off |
|---------|----------|-----------|
| REST API | Simple query/command | Tight coupling, immediate response |
| gRPC | High performance | More complex, requires schema |
| GraphQL | Flexible queries | Complexity on server |

### Asynchronous Integration

| Pattern | Use Case | Trade-off |
|---------|----------|-----------|
| Domain Events | Loose coupling | Eventual consistency |
| Message Queue | Reliability | Infrastructure complexity |
| Event Sourcing | Full history | Storage, complexity |

### Data Sharing

| Pattern | Use Case | Trade-off |
|---------|----------|-----------|
| Shared Database | Simple, immediate | Tight coupling (avoid!) |
| Read Replica | Read-heavy downstream | Eventual consistency |
| Data Lake | Analytics | Not for operational use |

## Anti-Patterns

### Big Ball of Mud
**Symptom:** No clear boundaries, everything coupled

**Solution:** Identify contexts, establish boundaries gradually

### Context per Entity
**Symptom:** Too many small contexts (one per aggregate)

**Solution:** Group by business capability, not technical concerns

### Distributed Monolith
**Symptom:** Microservices that must deploy together

**Solution:** Review boundaries, consider merging or proper decoupling

### Shared Database
**Symptom:** Multiple contexts writing to same tables

**Solution:** Each context owns its data, share via APIs/events

## Context Discovery Heuristics

### Team-Based
Look at existing team structure:
- What does each team own?
- Where are communication bottlenecks?

### Language-Based
Find ubiquitous language boundaries:
- Same term, different meaning?
- Different terms, same concept?

### Process-Based
Follow business processes:
- Where are handoffs?
- What triggers process steps?

### Data-Based
Analyze data ownership:
- Who creates this data?
- Who is the source of truth?

## Context Map Validation

Before finalizing:

- [ ] All aggregates assigned to exactly one context
- [ ] Each context has clear ownership (team)
- [ ] Relationship patterns are explicit
- [ ] Integration mechanisms defined
- [ ] ACLs identified where needed
- [ ] No shared database patterns (unless intentional)
- [ ] Ubiquitous language documented per context

## Team Topologies Alignment

| Team Topology | Context Pattern |
|---------------|-----------------|
| Stream-aligned | Owns one or more contexts |
| Platform | Provides Open Host Services |
| Enabling | Helps establish boundaries |
| Complicated-subsystem | Owns complex context with ACL |
