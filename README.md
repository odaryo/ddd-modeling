# DDD Modeling Supporter

DDDイベントストーミングの相談相手・アドバイザーとして機能するClaude Code / Codex CLIプラグインです。

## 概要

イベントストーミングのスクリーンショット（ふせんの図）とテキスト（要件/質問）を渡すと、現在のフェーズを自動推測してアドバイスを提供します。

ドメインモデリングにおいて、ドメインエキスパートとの対話だけでは見落としがちな観点や、暗黙的な知識を明示化することが難しい場面があります。このスキルは、DDDの各フェーズでの**深堀りのための補助ツール**として機能することを目的としています。

## 使い方

```
/ddd-modeling-supporter:supporter
```

1. イベントストーミングのスクリーンショットを添付
2. 質問や相談内容をテキストで入力
3. フェーズが自動推測され、確認される
4. OKならアドバイスが提供される

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

## 各フェーズでのアドバイス例

### Phase 1: イベント洗い出し
- 「〇〇が成功した場合のイベントはありますが、失敗した場合は？」
- 「〇〇の前に、△△というイベントが必要では？」
- 「〇〇と△△は同じ意味ですか？統合できそうです」

### Phase 2: フロー図作成
- 「このイベントのトリガーは何ですか？」
- 「ここはポリシー（自動）ですか？人の判断（リードモデル経由）ですか？」

### Phase 3: 集約特定
- 「この2つは同じ集約に含めるべきでは？（同時に更新が必要）」
- 「この集約は大きすぎます。分割を検討しては？」

### Phase 4: 境界コンテキスト決定
- 「"顧客"という言葉が2つのコンテキストで異なる意味で使われていませんか？」
- 「コンテキスト間の関係はCustomer-Supplierが適切では？」

### Phase 5: ユースケース検証
- 「このステップで2つの集約を同時更新していますが、問題ないですか？」
- 「→ Phase 3に戻って集約を再設計することをお勧めします」

## 導入方法

### Claude Code

#### 方法1: Claude Code上で登録

1. `/plugin`コマンドを実行し、`Marketplace`タブから新規追加
2. URLに `odaryo/ddd-modeling-supporter`（owner/repo形式）を指定
3. プラグイン名 `ddd-modeling-supporter` を選択してインストール

#### 方法2: プロジェクト内に設定

各プロジェクトの `.claude/settings.json` に以下を追加：

```json
{
  "extraKnownMarketplaces": {
    "ddd-modeling-supporter": {
      "source": {
        "source": "github",
        "repo": "<owner>/ddd-modeling-supporter"
      }
    }
  }
}
```

その後、Claude Codeで以下を実行：

```bash
/plugin install ddd-modeling-supporter@ddd-modeling-supporter
```

### Codex CLI

Codexの `$skill-installer` スキルを使用してGitHubリポジトリからインストールします。

```
$skill-installer install --repo <owner>/ddd-modeling-supporter \
  --path skills/supporter
```

**使い方:**
```
# スキル一覧を表示
/skills

# 明示的に呼び出す
$supporter

# または暗黙的に呼び出し（トリガーワードで自動起動）
「イベントストーミングのアドバイスが欲しい」→ supporter が自動起動
```

## ディレクトリ構造

```
ddd-modeling-supporter/
├── .claude-plugin/
│   ├── marketplace.json        # マーケットプレイス定義
│   └── plugin.json             # プラグイン定義
├── commands/                   # ユーザー向けコマンド
│   └── supporter.md
├── skills/                     # スキル
│   └── supporter/
│       ├── SKILL.md
│       └── references/
│           ├── event-analysis.md
│           ├── flow-patterns.md
│           ├── aggregate-advice.md
│           ├── context-mapping.md
│           ├── usecase-validation.md
│           └── color-convention.md
├── tests/
│   └── scenarios.md
├── CLAUDE.md                   # Claude Code用ガイド
└── README.md
```

## ライセンス

MIT License
