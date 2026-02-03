# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Claude Code / Codex CLI両対応のDDDモデリング支援プラグイン。イベントストーミングの相談相手・アドバイザーとして機能し、スクリーンショットと質問からフェーズを自動推測してアドバイスを提供する。

### Codex CLI対応

このリポジトリのSKILL.mdはClaude CodeとCodex CLIの両方で動作する：
- **共通**: `name`, `description` フロントマター（両環境で必須）
- **Claude固有**: `plugin.json`, `marketplace.json`
- **Codex固有**: `$skill-installer` でパス指定インストール

Codex用のインストール手順は `README.md` を参照。

## アーキテクチャ

```
ddd-modeling-supporter/
├── .claude-plugin/
│   ├── marketplace.json        # マーケットプレイス定義
│   └── plugin.json             # プラグイン定義
├── commands/                   # ユーザー向けコマンド
│   └── supporter.md            # /ddd-modeling-supporter:supporter
├── skills/                     # スキル
│   └── supporter/              # メインスキル
│       ├── SKILL.md
│       └── references/         # 詳細リファレンス
│           ├── event-analysis.md
│           ├── flow-patterns.md
│           ├── aggregate-advice.md
│           ├── context-mapping.md
│           ├── usecase-validation.md
│           └── color-convention.md
├── tests/
│   └── scenarios.md
├── CLAUDE.md
└── README.md
```

## 5フェーズワークフロー

```
Phase 1: イベント洗い出し
    → オレンジのふせん中心、イベント名が並んでいる
    ↓
Phase 2: フロー図作成
    → 矢印で接続、コマンド/ポリシーが含まれる
    ↓
Phase 3: 集約特定
    → 緑のふせん、グループ化されている
    ↓
Phase 4: 境界コンテキスト決定
    → 大きな枠で囲まれている、複数のグループ
    ↓
Phase 5: ユースケース検証
    → フローチャート、永続化の記述
    → 問題あり: Phase 3へ戻る
    → 問題なし: 完了
```

## コマンドとスキル構造

### コマンド（ユーザー向け）

`/ddd-modeling-supporter:supporter` で呼び出し。

| コマンド | 説明 |
|----------|------|
| supporter | DDDイベントストーミングの相談相手・アドバイザー |

### リファレンス構造

メインスキルは `references/` ディレクトリに詳細なガイドを持つ：

| リファレンス | 説明 |
|--------------|------|
| event-analysis.md | Phase 1: イベント分析 |
| flow-patterns.md | Phase 2: フローパターン |
| aggregate-advice.md | Phase 3: 集約アドバイス |
| context-mapping.md | Phase 4: コンテキストマップ |
| usecase-validation.md | Phase 5: ユースケース検証 |
| color-convention.md | Mermaid図の色規約 |

## フローパターン

### 起点パターン（3種類）

1. **アクター起点**: アクター → コマンド → 集約/外部システム → ドメインイベント
2. **外部システム起点**: 外部システム → ドメインイベント
3. **時間トリガー起点**: スケジューラ → コマンド → 集約 → ドメインイベント

### 継続パターン（2種類）

A. **ポリシー経由（自動）**: ドメインイベント → ポリシー → コマンド → ...
B. **リードモデル経由（人間判断）**: ドメインイベント → リードモデル → アクター → コマンド → ...

## Mermaid図の色規約

| 要素 | 色 | Mermaid Style |
|------|-----|---------------|
| Domain Event | オレンジ | `fill:#FF6B35,color:#fff` |
| Command | 青 | `fill:#4A90D9,color:#fff` |
| Actor | 黄 | `fill:#FFD93D,color:#333` |
| Policy | 紫 | `fill:#9B59B6,color:#fff` |
| External System | ピンク | `fill:#E91E8C,color:#fff` |
| Aggregate | 緑 | `fill:#27AE60,color:#fff` |
| Read Model | ミントグリーン | `fill:#98D8AA,color:#333` |
| Hot Spot | 赤 | `fill:#E74C3C,color:#fff` |

詳細は `skills/supporter/references/color-convention.md` を参照。

## コマンドファイル作成ガイドライン

### フロントマター（commands/*.md）
```yaml
---
description: 説明文            # 何ができるか
---

Invoke the ddd-modeling-supporter:{skill-name} skill and follow it exactly as presented to you
```

## SKILL.md 作成ガイドライン

### フロントマター（skills/*/SKILL.md）
```yaml
---
name: skill-name              # 小文字、数字、ハイフンのみ（両環境共通）
description: 説明文            # 何ができるか + いつ使うか（両環境共通）
user-invocable: false         # 内部スキルとして設定
---
```
