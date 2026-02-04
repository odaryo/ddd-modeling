# Event Analysis Reference

Phase 1: イベント洗い出しフェーズの詳細ガイド。

## Good Event Naming

**過去形で、ビジネス視点:**

| Good | Bad | 理由 |
|------|-----|------|
| OrderPlaced | PlaceOrder | 過去形でない（コマンド） |
| PaymentReceived | Payment | 動詞がない |
| ShipmentDispatched | SendShipment | 過去形でない |
| UserRegistered | SaveUserToDatabase | 技術的すぎる |

## Common Missing Events

### 失敗ケース

成功イベントがあれば、失敗イベントも検討:

| 成功 | 失敗 |
|------|------|
| PaymentReceived | PaymentFailed |
| OrderPlaced | OrderRejected |
| UserRegistered | RegistrationFailed |

### 状態変化

中間状態も重要:

```
OrderPlaced → OrderConfirmed → OrderShipped → OrderDelivered
                    ↓
              OrderCancelled
```

### 時間関連

期限や定期処理:

- `PromotionExpired` - キャンペーン終了
- `SubscriptionRenewed` - サブスク更新
- `PaymentOverdue` - 支払い期限超過

## Questions to Ask

**網羅性チェック:**
- 「最初から最後までの流れで、漏れているイベントは？」
- 「失敗した場合はどうなりますか？」
- 「キャンセルや変更はできますか？」
- 「定期的に起きることはありますか？」

**重複チェック:**
- 「〇〇と△△は同じ意味ですか？」
- 「この2つは統合できそうですか？」

## Phase Completion Criteria

- [ ] すべてのイベントが過去形
- [ ] ビジネス視点（技術的でない）
- [ ] 失敗ケースが考慮されている
- [ ] 重複がない
- [ ] 時系列で並べられる

**完了したら:**
「イベントが出揃いました。フロー図作成（Phase 2）に進みましょうか？」
