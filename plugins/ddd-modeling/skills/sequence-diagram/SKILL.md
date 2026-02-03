---
name: sequence-diagram
description: "[内部スキル] Mermaidシーケンス図の生成。ddd-4-model-diagramから呼び出される。"
user-invocable: false
---

# Sequence Diagram Generator

Generate Mermaid sequence diagrams from use cases or event storming results.

## Input

Option 1: Read from event storming
- `docs/modeling/{session}/01-event-storming.md`
- User selects specific scenario

Option 2: User describes scenario directly

## Workflow

```
1. Scenario Selection → Choose or describe scenario
2. Actor/System Identification → List participants
3. Message Flow → Define interactions
4. Generate Diagram → Create Mermaid sequence diagram
5. Export → Save to file
```

## Session Flow

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

Participant types:
```mermaid
sequenceDiagram
    actor User
    participant Frontend
    participant API
    participant DB
    participant External
```

### Step 3: Define Message Flow

For each step:
- 「誰が誰に何を送りますか？」
- 「同期ですか？非同期ですか？」
- 「レスポンスはありますか？」
- 「条件分岐はありますか？」

## Output Format

Save to: `docs/modeling/{session}/04-sequence-{usecase}.md`

```markdown
# Sequence Diagram: {Use Case Name}

Generated: {YYYY-MM-DD}
Scenario: {Brief description}

## Diagram

\`\`\`mermaid
sequenceDiagram
    autonumber

    actor Customer
    participant Web as Web Frontend
    participant API as Order API
    participant Payment as Payment Service
    participant DB as Database

    Customer->>Web: Add items to cart
    Web->>API: POST /cart/items
    API->>DB: Save cart
    DB-->>API: OK
    API-->>Web: Cart updated
    Web-->>Customer: Show updated cart

    Customer->>Web: Checkout
    Web->>API: POST /orders
    API->>DB: Create order (pending)
    API->>Payment: Process payment
    Payment-->>API: Payment result
    alt Payment successful
        API->>DB: Update order (confirmed)
        API-->>Web: Order confirmed
        Web-->>Customer: Show confirmation
    else Payment failed
        API->>DB: Update order (failed)
        API-->>Web: Payment failed
        Web-->>Customer: Show error
    end
\`\`\`

## Participants

| Participant | Type | Description |
|-------------|------|-------------|
| Customer | Actor | End user placing order |
| Web Frontend | System | React SPA |
| Order API | System | Backend API service |
| Payment Service | External | Third-party payment processor |
| Database | System | PostgreSQL |

## Notes
- Payment is synchronous for simplicity
- Consider async payment flow for production

## Related
- Event storming: `01-event-storming.md`
- Aggregates: `02-aggregates.md`
```

## Mermaid Sequence Syntax

### Basic Messages

```mermaid
sequenceDiagram
    A->>B: Synchronous request
    B-->>A: Response
    A-)B: Async message (fire and forget)
    A--xB: Async message lost
```

### Activation (Lifeline)

```mermaid
sequenceDiagram
    A->>+B: Request
    B->>+C: Sub-request
    C-->>-B: Response
    B-->>-A: Response
```

### Conditionals

```mermaid
sequenceDiagram
    alt Condition A
        A->>B: Message A
    else Condition B
        A->>C: Message B
    end
```

### Loops

```mermaid
sequenceDiagram
    loop Every item
        A->>B: Process item
    end
```

### Optional

```mermaid
sequenceDiagram
    opt If condition
        A->>B: Optional message
    end
```

### Parallel

```mermaid
sequenceDiagram
    par Action 1
        A->>B: Message 1
    and Action 2
        A->>C: Message 2
    end
```

### Notes

```mermaid
sequenceDiagram
    A->>B: Request
    Note over A,B: This is a note
    Note right of B: Processing
```

### Grouping (Box)

```mermaid
sequenceDiagram
    box Sales Context
        participant Order
        participant Customer
    end
    box Shipping Context
        participant Shipment
    end
```

## Common Patterns

### Request-Response
```mermaid
sequenceDiagram
    Client->>+Server: Request
    Server->>Server: Process
    Server-->>-Client: Response
```

### Event-Driven
```mermaid
sequenceDiagram
    A->>EventBus: Publish Event
    EventBus-)B: Event
    EventBus-)C: Event
```

### Saga Pattern
```mermaid
sequenceDiagram
    Orchestrator->>A: Step 1
    A-->>Orchestrator: Done
    Orchestrator->>B: Step 2
    B-->>Orchestrator: Failed
    Orchestrator->>A: Compensate Step 1
```

## Validation

Before saving:
- [ ] All participants identified
- [ ] Message direction clear (→ vs ←)
- [ ] Sync vs async distinguished
- [ ] Error paths shown (alt/opt)
- [ ] Autonumber for readability
- [ ] Notes explain non-obvious steps
