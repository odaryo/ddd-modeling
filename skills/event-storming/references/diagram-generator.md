# Event Storming Diagram Generator Reference

イベントストーミング結果からMermaidフローチャート図を生成するための詳細ガイド。

## Generation Rules

1. **Read input file** and extract all elements
2. **Determine flow direction**: LR (left-to-right) for linear flows, TB (top-to-bottom) for complex branching
3. **Group related elements** using subgraphs if multiple bounded contexts
4. **Connect elements** following: Actor → Command → Event → (Policy) → Command...
5. **Apply styles** consistently using classDef
6. **Add legend** for clarity

## Node Naming Convention

| Element Type | ID Pattern | Display |
|--------------|------------|---------|
| Event | `evt_{PascalCase}` | `[EventName]` |
| Command | `cmd_{PascalCase}` | `[CommandName]` |
| Actor | `act_{name}` | `[ActorName]` |
| Policy | `pol_{name}` | `{PolicyName}` |
| External | `ext_{name}` | `[(SystemName)]` |

## Diagram Patterns

### Basic Flow
```mermaid
flowchart LR
    Actor[Actor]:::actor --> Command[Command]:::command
    Command --> Event[Event]:::event
```

### Policy Reaction
```mermaid
flowchart LR
    Event1[Event]:::event --> Policy{Policy}:::policy
    Policy --> Command2[Command]:::command
    Command2 --> Event2[Event]:::event
```

### External System Integration
```mermaid
flowchart LR
    Command[Command]:::command --> External[(External)]:::external
    External --> Event[Event]:::event
```

### Parallel Flows
```mermaid
flowchart LR
    Event[Event]:::event --> Policy1{Policy1}:::policy
    Event --> Policy2{Policy2}:::policy
```

## Subgraph Usage

For multiple contexts or phases:
```mermaid
flowchart LR
    subgraph Ordering
        cmd_PlaceOrder[PlaceOrder]:::command
        evt_OrderPlaced[OrderPlaced]:::event
    end

    subgraph Payment
        cmd_ProcessPayment[ProcessPayment]:::command
        evt_PaymentReceived[PaymentReceived]:::event
    end

    evt_OrderPlaced --> cmd_ProcessPayment
```

## Template

```markdown
# Event Storming Diagram: {Topic}

Generated from: `01-event-storming.md`

## Flow Diagram

\`\`\`mermaid
flowchart LR
    %% Styles
    classDef event fill:#FF6B35,color:#fff,stroke:#333
    classDef command fill:#4A90D9,color:#fff,stroke:#333
    classDef actor fill:#FFD93D,color:#333,stroke:#333
    classDef policy fill:#9B59B6,color:#fff,stroke:#333
    classDef external fill:#E91E8C,color:#fff,stroke:#333

    %% Actors
    act_Customer[Customer]:::actor

    %% Flow
    act_Customer --> cmd_PlaceOrder[PlaceOrder]:::command
    cmd_PlaceOrder --> evt_OrderPlaced[OrderPlaced]:::event
    evt_OrderPlaced --> pol_SendConfirmation{SendConfirmation}:::policy
    pol_SendConfirmation --> ext_EmailService[(EmailService)]:::external
\`\`\`

## Legend

| Symbol | Meaning |
|--------|---------|
| [Rectangle] | Command |
| [Rectangle]:::event | Domain Event |
| [Actor] | Actor/User |
| {Diamond} | Policy |
| [(Cylinder)] | External System |

## Notes
{Any diagram-specific notes}
```

## Validation Checklist

Before saving, verify:
- [ ] All events from input are represented
- [ ] All commands are connected to events
- [ ] Actors are connected to their commands
- [ ] Policies show trigger and result
- [ ] External systems are clearly marked
- [ ] Colors match the convention
