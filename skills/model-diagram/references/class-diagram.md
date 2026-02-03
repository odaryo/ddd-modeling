# Class Diagram Reference

Mermaidクラス図/ドメインモデル図生成の詳細ガイド。

## DDD Stereotypes

| Stereotype | Meaning | Notation |
|------------|---------|----------|
| `<<AggregateRoot>>` | Entry point to aggregate | Bold border |
| `<<Entity>>` | Has identity, lifecycle | Normal |
| `<<ValueObject>>` | Immutable, no identity | Dashed border |
| `<<DomainService>>` | Stateless domain logic | Italicized |
| `<<Repository>>` | Aggregate persistence | Interface-style |

## Mermaid Class Syntax

### Basic Class

```mermaid
classDiagram
    class ClassName {
        +publicField Type
        -privateField Type
        #protectedField Type
        +publicMethod() ReturnType
        -privateMethod(param Type) ReturnType
    }
```

### Stereotypes

```mermaid
classDiagram
    class Order {
        <<AggregateRoot>>
    }
    class Money {
        <<ValueObject>>
    }
    class OrderRepository {
        <<interface>>
    }
```

### Relationships

```mermaid
classDiagram
    %% Composition (strong ownership)
    Order *-- LineItem : contains

    %% Aggregation (weak ownership)
    Order o-- Address : has

    %% Association
    Order --> Customer : references

    %% Dependency (uses)
    Order ..> Product : uses

    %% Inheritance
    PaymentMethod <|-- CreditCard
    PaymentMethod <|-- BankTransfer

    %% Realization (interface)
    Repository <|.. OrderRepository
```

### Cardinality

```mermaid
classDiagram
    Order "1" *-- "1..*" LineItem : contains
    Customer "1" --> "0..*" Order : places
```

### Namespaces (Aggregate Boundaries)

```mermaid
classDiagram
    namespace OrderAggregate {
        class Order
        class LineItem
    }
    namespace CustomerAggregate {
        class Customer
    }
```

### Notes

```mermaid
classDiagram
    class Order
    note for Order "Invariant: total = sum(lineItems)"
```

## Styling

```mermaid
classDiagram
    class Order {
        <<AggregateRoot>>
    }
    class Money {
        <<ValueObject>>
    }

    style Order fill:#E8F5E9,stroke:#2E7D32
    style Money fill:#FFF3E0,stroke:#F57C00
```

## Common Patterns

### Aggregate Pattern
```mermaid
classDiagram
    class AggregateRoot {
        <<AggregateRoot>>
        -ID id
        -List~Entity~ entities
        +addEntity()
        +removeEntity()
    }
    class Entity {
        <<Entity>>
        -ID id
    }
    AggregateRoot *-- Entity
```

### Value Object Equality
```mermaid
classDiagram
    class Money {
        <<ValueObject>>
        -amount Decimal
        -currency Currency
        +equals(Money) bool
        +hashCode() int
    }
```

### Enum as Value Object
```mermaid
classDiagram
    class OrderStatus {
        <<enumeration>>
        DRAFT
        SUBMITTED
        PAID
        CANCELLED
    }
```

### Repository Interface
```mermaid
classDiagram
    class OrderRepository {
        <<interface>>
        +findById(OrderID) Order
        +save(Order)
        +delete(OrderID)
    }
```

## Output Template

```markdown
# Class Diagram: {Topic}

Generated: {YYYY-MM-DD}
Source: `02-aggregates.md`

## Domain Model

\`\`\`mermaid
classDiagram
    class Order {
        <<AggregateRoot>>
        -OrderID id
        -CustomerID customerId
        -OrderStatus status
        -Money total
        -List~LineItem~ lineItems
        +addItem(Product, Quantity)
        +removeItem(LineItemID)
        +submit()
        +cancel()
    }

    class LineItem {
        <<Entity>>
        -LineItemID id
        -ProductID productId
        -Quantity quantity
        -Money unitPrice
        +changeQuantity(Quantity)
        +calculateTotal() Money
    }

    class Money {
        <<ValueObject>>
        -Decimal amount
        -Currency currency
        +add(Money) Money
        +multiply(int) Money
    }

    class OrderStatus {
        <<ValueObject>>
        DRAFT
        SUBMITTED
        PAID
        SHIPPED
        CANCELLED
    }

    class Address {
        <<ValueObject>>
        -String street
        -String city
        -String postalCode
        -Country country
    }

    Order *-- LineItem : contains
    Order *-- Money : total
    Order *-- OrderStatus : status
    Order o-- Address : shippingAddress
\`\`\`

## Aggregate Boundaries

\`\`\`mermaid
classDiagram
    namespace OrderAggregate {
        class Order {
            <<AggregateRoot>>
        }
        class LineItem {
            <<Entity>>
        }
    }

    namespace ProductAggregate {
        class Product {
            <<AggregateRoot>>
        }
    }

    Order ..> Product : references by ID
\`\`\`

## Element Catalog

### Aggregate Roots
| Name | Key Methods | Invariants |
|------|-------------|------------|
| Order | addItem, submit, cancel | Has at least 1 item, total matches |

### Entities
| Name | Parent Aggregate | Identity |
|------|-----------------|----------|
| LineItem | Order | LineItemID |

### Value Objects
| Name | Used By | Attributes |
|------|---------|------------|
| Money | Order, LineItem | amount, currency |
| OrderStatus | Order | enum values |

## Notes
{Any diagram-specific notes}
```

## Validation Checklist

Before saving:
- [ ] All aggregates from input represented
- [ ] Stereotypes correctly applied
- [ ] Composition vs association clear
- [ ] Aggregate boundaries visible
- [ ] Cross-aggregate references by ID only (dashed line)
- [ ] Key methods/attributes shown
- [ ] Invariants noted
