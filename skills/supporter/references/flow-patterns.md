# Flow Patterns Reference

Phase 2: フロー図作成フェーズの詳細ガイド。

## 起点パターン（3種類）

### 1. アクター起点

人間がシステムを操作してイベントが発生。

```
アクター → コマンド → 集約 → ドメインイベント
アクター → コマンド → 外部システム → ドメインイベント
```

**例:**
- 顧客 → 注文する → Order集約 → OrderPlaced
- 管理者 → 決済実行 → 決済サービス → PaymentProcessed

### 2. 外部システム起点

外部システムからの通知でイベントが発生。

```
外部システム → ドメインイベント
```

**例:**
- 決済サービス（Webhook） → PaymentReceived
- 配送業者API → ShipmentDelivered

### 3. 時間トリガー起点

スケジューラや時間経過でイベントが発生。

```
スケジューラ → コマンド → 集約 → ドメインイベント
```

**例:**
- 毎日0時 → ExpirePromotions → Promotion集約 → PromotionExpired
- 毎月1日 → RenewSubscriptions → Subscription集約 → SubscriptionRenewed

## 継続パターン（2種類）

### A. ポリシー経由（自動反応）

イベント発生後、自動的に次のアクションが実行。

```
ドメインイベント → ポリシー → コマンド → 集約/外部システム → ドメインイベント
```

**ポリシー命名:** `When{Event}Then{Action}`

**例:**
- OrderPlaced → WhenOrderPlacedReserveInventory → ReserveInventory → Inventory → InventoryReserved

### B. リードモデル経由（人間判断）

イベント発生後、人間が情報を確認して判断。

```
ドメインイベント → リードモデル → アクター → コマンド → 集約/外部システム → ドメインイベント
```

**例:**
- OrderPlaced → 注文一覧画面 → 倉庫スタッフ → PickItems → Shipment → ItemsPicked

## 排他的接続ルール

**コマンドからは一方のみ:**

```
✓ Good:
Command → Aggregate → Event
Command → ExternalSystem → Event

✗ Bad:
Command → Aggregate
       → ExternalSystem  （並列接続は不可）
```

## Questions to Ask

**トリガー確認:**
- 「このイベントは何がきっかけで発生しますか？」
- 「自動（ポリシー）ですか？人の判断（リードモデル経由）ですか？」

**パターン確認:**
- 「このコマンドは集約を更新しますか？外部システムを呼びますか？」
- 「人が見て判断する画面/レポートはありますか？」

## Phase Completion Criteria

- [ ] すべてのイベントにトリガーが明確
- [ ] フローパターンに沿っている
- [ ] 排他的接続ルールを守っている
- [ ] リードモデルが必要な箇所にある

**完了したら:**
「フローが整理できました。集約の特定（Phase 3）に進みましょうか？」
