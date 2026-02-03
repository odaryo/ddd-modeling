---
name: event-storming-facilitator
description: "[内部スキル] イベントストーミングのファシリテーション。1-event-stormingから呼び出される。"
user-invocable: false
---

# Event Storming Facilitator

Facilitate event storming sessions through structured dialogue to extract domain events, commands, actors, policies, and external systems.

## Workflow

```
1. Context Setup    → Confirm business scenario/domain
2. Event Discovery  → Extract domain events (past tense)
3. Command Mapping  → Identify triggering commands
4. Actor Identification → Who issues commands?
5. Policy & External → Reactive policies, external systems
6. Review & Export  → Summarize and save results
```

## Session Flow

### Phase 1: Context Setup

Ask the user:
- 「どのビジネスシナリオ/ユースケースを分析しますか？」
- 「関係するステークホルダーは誰ですか？」

Create output directory based on response:
```
docs/modeling/{YYYY-MM-DD}-{topic}/
```

### Phase 2: Event Discovery

Guide with these questions:
- 「このシナリオで**起きる出来事**は何ですか？（過去形で）」
- 「ユーザー視点で、何が**完了した**と言えますか？」
- 「システムが記録すべき**重要な状態変化**は？」

Capture events in format:
```markdown
## Domain Events

| Event | Description | Trigger |
|-------|-------------|---------|
| OrderPlaced | 注文が確定された | Customer |
| PaymentReceived | 支払いが完了した | PaymentGateway |
```

### Phase 3: Command Mapping

For each event, ask:
- 「この{Event}を引き起こす**アクション**は何ですか？」
- 「そのアクションの**入力データ**は？」

Capture commands:
```markdown
## Commands

| Command | Triggers Event | Input Data |
|---------|---------------|------------|
| PlaceOrder | OrderPlaced | items, shippingAddress, paymentMethod |
```

### Phase 4: Actor Identification

Ask:
- 「誰が/何がこのコマンドを発行しますか？」
- 「人間？システム？時間トリガー？」

Actor types:
- **Human**: Customer, Admin, Staff
- **System**: Scheduler, ExternalAPI
- **Time**: Cron, Deadline

### Phase 5: Policy & External Systems

Ask:
- 「{Event}が起きたら、**自動的に**何が起きるべきですか？」
- 「外部システム連携はありますか？」

Capture:
```markdown
## Policies (When-Then)

| When | Then | Notes |
|------|------|-------|
| OrderPlaced | SendConfirmationEmail | Notification service |
| PaymentFailed | NotifyCustomer + CancelOrder | After 3 retries |

## External Systems

| System | Integration Point | Direction |
|--------|------------------|-----------|
| PaymentGateway | ProcessPayment | Outbound |
| InventoryService | CheckStock | Inbound |
```

### Phase 6: Review & Export

Present summary and confirm with user before saving.

## Output Format

Save to: `docs/modeling/{YYYY-MM-DD}-{topic}/01-event-storming.md`

```markdown
# Event Storming: {Topic}

Date: {YYYY-MM-DD}
Participants: {list}

## Overview
{Brief description of the analyzed scenario}

## Domain Events
{Table from Phase 2}

## Commands
{Table from Phase 3}

## Actors
{List with descriptions}

## Policies
{Table from Phase 5}

## External Systems
{Table from Phase 5}

## Open Questions
{Any unresolved items for follow-up}

## Next Steps
- [ ] Review with domain experts
- [ ] Create event storming diagram: `/event-storming-diagram`
- [ ] Design aggregates: `/aggregate-designer`
```

## Facilitation Tips

**Keep momentum:**
- Start with the "happy path" scenario
- Defer edge cases to later passes
- Use "What happens next?" to drive flow

**Resolve conflicts:**
- Different terms for same concept → Pick one, document aliases
- Disagreement on event boundaries → Mark as "needs clarification"

**Signs of good events:**
- Past tense verb + noun (OrderPlaced, not PlaceOrder)
- Business-meaningful (not technical implementation)
- Observable state change

## Reference

For detailed facilitation questions and anti-patterns, see [REFERENCE.md](REFERENCE.md).
