---
name: feedback
description: "[補助スキル] Mermaid図にDDD観点でフィードバックを提供。生成された図のレビュー・改善に使用。"
user-invocable: false
---

# Diagram Feedback

Provide DDD-focused feedback on Mermaid diagrams or diagram images.

## Input

Accepts:
1. **File path**: `docs/modeling/.../diagram.md`
2. **Image path**: Screenshot or exported diagram image
3. **Inline Mermaid**: Pasted Mermaid code

## Feedback Categories

| Category | Focus |
|----------|-------|
| DDD Principles | Aggregate boundaries, stereotypes, relationships |
| Clarity | Readability, layout, grouping |
| Naming | Consistency, domain language, conventions |
| Completeness | Missing elements, relationships |
| Improvements | Specific actionable suggestions |

## Workflow

```
1. Load Diagram → Read file or view image
2. Analyze Structure → Parse elements and relationships
3. Check DDD Principles → Evaluate against best practices
4. Assess Clarity → Readability and visual organization
5. Review Naming → Check domain language alignment
6. Generate Feedback → Provide structured feedback
```

## Output Format

```markdown
# Diagram Feedback

Reviewed: {file path or "inline diagram"}
Date: {YYYY-MM-DD}

## Summary

**Overall Assessment:** {Good / Needs Work / Major Issues}

| Category | Score | Notes |
|----------|-------|-------|
| DDD Principles | {1-5} | {brief note} |
| Clarity | {1-5} | {brief note} |
| Naming | {1-5} | {brief note} |
| Completeness | {1-5} | {brief note} |

## Strengths

1. {What's done well}
2. {Another positive point}

## Issues Found

### Issue 1: {Title}
**Category:** {DDD/Clarity/Naming/Completeness}
**Severity:** {High/Medium/Low}
**Location:** {Where in diagram}

**Problem:**
{Description of the issue}

**Suggestion:**
{How to fix it}

**Example:**
\`\`\`mermaid
{Corrected code snippet}
\`\`\`

## Recommended Changes

### High Priority
1. {Most important fix}

### Nice to Have
1. {Optional improvement}
```

## DDD Review Checklist

### Aggregate Boundaries
- [ ] Each aggregate has exactly one root
- [ ] Entities are inside their aggregate
- [ ] Value objects are properly marked
- [ ] Cross-aggregate references use IDs (dashed lines)

### Stereotypes
- [ ] `<<AggregateRoot>>` on root entities
- [ ] `<<Entity>>` on non-root entities with identity
- [ ] `<<ValueObject>>` on immutable value types
- [ ] Stereotypes used consistently

### Relationships
- [ ] Composition (`*--`) for owned entities
- [ ] Aggregation (`o--`) for optional/shared
- [ ] Dependency (`..>`) for cross-aggregate
- [ ] Cardinality specified where important

### Naming
- [ ] PascalCase for classes
- [ ] Singular names (Order, not Orders)
- [ ] Domain language used (not technical jargon)
- [ ] No abbreviations unless standard

## Common Issues

### DDD Issues

| Issue | Example | Fix |
|-------|---------|-----|
| Missing aggregate boundary | Entities floating alone | Use namespace or note |
| Wrong stereotype | Entity marked as ValueObject | Check identity requirement |
| Direct object reference | Solid line between aggregates | Change to dashed, use ID |
| Missing root | Aggregate without <<AggregateRoot>> | Mark the entry point |

### Clarity Issues

| Issue | Example | Fix |
|-------|---------|-----|
| Spaghetti layout | Lines crossing everywhere | Reorganize, use subgraphs |
| Information overload | 50+ classes in one diagram | Split by aggregate/context |

### Naming Issues

| Issue | Example | Fix |
|-------|---------|-----|
| Technical names | `UserDTO`, `OrderEntity` | `User`, `Order` |
| Plural names | `Orders` | `Order` |

---

## Error Handling

### ファイルが見つからない場合
- 「指定されたファイルが見つかりません: {path}」
- インラインコードの入力を促す、または正しいパスの確認を依頼

### ファイルが読み込めない場合
- 「ファイルを読み込めませんでした」
- 別の形式（インラインまたは画像）での入力を提案

### Mermaid構文エラー
- 「Mermaid構文にエラーがあります」
- エラー箇所を特定し、修正案を提示

---

## Follow-up Suggestions

After feedback:
- 「修正版を生成しましょうか？」
- 「特定の問題について詳しく説明しましょうか？」
- 「関連するフェーズスキルを実行しますか？」
