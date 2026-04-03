[English](./README.md) | [简体中文](./README.zh-CN.md) | **日本語**

# 🛍️ Attribuly OpenClaw スキル：Shopify & WooCommerce 向け AI マーケティング分析

DTC Eコマース（Shopify、WooCommerce など）に特化した **AI マーケティング・パートナー**。Attribuly のファーストパーティデータを活用し、自律的なマーケティング分析、真の ROAS トラッキング、利益優先の最適化を提供します。

> **完全自動化されたマーケティング診断ワークフロー：**
> 
> 以下の見本は、AI スキルが自律的に連動し、パフォーマンス低下の原因を診断する様子を示しています。たとえば、週次レポートで Google 広告の ROAS 低下が検出された場合、AI は自動的に詳細な診断をトリガーし、CTR の問題をチェックして、必要に応じてクリエイティブレベルまで掘り下げて分析します。
> 
> <div align="center">
>   <img src="./assets/workflow.gif" width="600" alt="自動化された診断ワークフロー" />
> </div>

### なぜ Shopify と WooCommerce 向けなのか？
従来の広告プラットフォーム（Meta、Google など）は、売上を誤って帰属させることがよくあります。Shopify や WooCommerce の販売者にとって、これらの AI スキルは店舗のバックエンドの実際の注文データを直接活用し、**真の利益率、顧客獲得単価（CAC）、顧客生涯価値（LTV）** を明らかにし、実際の収益に基づいたマーケティングの意思決定を可能にします。

### 主な機能:
- **真の ROI & ROAS へのフォーカス** — Attribuly のファーストパーティ・アトリビューションの概念（真の ROAS、新規顧客 ROAS (ncROAS)、利益、利益率、LTV、MER）を活用し、Meta/Google 広告プラットフォームの過剰なアトリビューションを削減します。
- **データコントロール** — ローカル環境またはクラウドに展開可能。メモリと戦略は安全な環境内に保持されます。
- **拡張可能なスキル** — 自動トリガーを内蔵。ファネル、予算消化ペース、クリエイティブ、データ乖離を自律的に分析します。特定のプラットフォームに縛られません。

### できること:
- **診断分析:** ファネルのボトルネックやランディングページの離脱要因を自律的に検出します。
- **パフォーマンス追跡:** 30秒で確認できる毎日の予算消化レポートや、詳細な週次エグゼクティブ・サマリーを生成します。
- **クリエイティブ最適化:** Google/Meta のクリエイティブを真の収益性に基づいて評価し、クリエイティブの疲労を特定します。
- **予算最適化:** 利益を最優先した予算の再配分やオーディエンス調整の推奨事項を取得します。

---

## 目次

- [利用可能なスキル](#利用可能なスキル)
- [インストールガイド](#インストールガイド)
- [マネージドクラウドホスティング（展開）](#マネージドクラウドホスティング展開)
- [インストール後の設定](#インストール後の設定)

---

## 利用可能なスキル

### ✅ 利用可能 (Ready)

- `weekly-marketing-performance` — チャネル横断の週次エグゼクティブ・サマリー
- `daily-marketing-pulse` — 日次異常検出＋予算消化レポート（30秒でスキャン可能）
- `google-ads-performance` — Google 広告 / PMax パフォーマンス診断
- `meta-ads-performance` — Meta 広告パフォーマンス診断（iOS14 データのギャップ対応）
- `budget-optimization` — 利益優先の予算再配分の推奨
- `audience-optimization` — カニバリゼーション分析＋新規獲得/リターゲティングのオーディエンス調整
- `bid-strategy-optimization` — ファーストパーティデータに基づく tCPA/tROAS 目標設定
- `funnel-analysis` — カスタマージャーニー全体の離脱診断
- `landing-page-analysis` — ランディングページにおけるトラフィックと UX の摩擦を特定
- `attribution-discrepancy` — 広告ネットワークとバックエンドシステム間のレポート乖離の定量化と診断
- `google-creative-analysis` — Google 広告の品質スコア、PMax アセット、標準化された評価基準の統合

### 🔜 計画中 (Coming Soon)

- `tiktok-ads-performance`
- `meta-creative-analysis`
- `creative-fatigue-detector`
- `product-performance`
- `customer-journey-analysis`
- `ltv-analysis`

トリガーと使用マッピングの詳細については、下部の **Technical Reference（技術リファレンス）** を参照してください。

---

## インストールガイド

### 🚀 Shopify & WooCommerce ユーザー向けのノーコード設定
コードが書けなくても問題ありません！以下の手順で、コードを書かずにこれらの AI スキルを実行できます：
1. Shopify または WooCommerce ストアを [Attribuly](https://attribuly.com) に接続します。
2. Attribuly ダッシュボードから API キーを取得します。
3. OpenClaw エージェント設定の `ATTRIBULY_API_KEY` の下にキーを貼り付けます。
4. AI に質問します： *"過去7日間の Shopify ストアのファネル離脱率を分析して。"*

### ステップ 0: Attribuly API キーの取得

スキルをインストールする前に、Attribuly API キーが必要です。これらのスキルは、自律的に機能するために Attribuly 独自の指標（`new_order_roas` や真の利益など）に大きく依存しています。

- **有料機能:** API キーは有料プランのユーザーのみが利用できます。キーを生成するには、ワークスペースをアップグレードする必要があります。
- **無料トライアル:** 新規ユーザーの場合は、[14日間の無料トライアル](https://attribuly.com/pricing/) を開始してプラットフォームをテストできます。
- **設定方法:** 最高レベルのセキュリティを確保するため、API キーは必ず OpenClaw の **Agent Settings UI（環境変数/Secrets）** を通じて設定してください。チャット履歴でのキーの漏洩を防ぐため、チャットボックスに直接 API キーを貼り付けないでください。

---

これらの Attribuly スキルを独自の OpenClaw 環境にインストールするには、主に 2 つの方法があります。ワークフローに最適な方法を選択してください。

### 方法 1: チャット経由でのインストール (クイックスタート)

以下のプロンプトを OpenClaw のインターフェースにコピーすると、エージェントが自動的にインストールを行います。

> Install these skills from https\://github.com/Attribuly-US/ecommerce-dtc-skills.git

### 方法 2: Git サブモジュール (推奨: 更新が簡単)

このリポジトリからの最新の改善に合わせてスキルを常に最新の状態に保ちたい場合は、Git サブモジュールとして追加するのが最善のアプローチです。

1. ターミナルで OpenClaw インスタンスのルートディレクトリに移動します。
2. このリポジトリをサブモジュールとして追加します。
   ```bash
   git submodule add https://github.com/Attribuly-US/ecommerce-dtc-skills.git vendor/attribuly
   ```
3. `skills` ディレクトリがまだ存在しない場合は作成します。
   ```bash
   mkdir -p ./openclaw-config/skills
   ```
4. アクティブな構成ディレクトリにスキルフォルダを同期します。
   ```bash
   rsync -av --exclude=".*" --exclude="LICENSE" vendor/attribuly/ ./openclaw-config/skills/attribuly-dtc-analyst/
   ```

**今後のアップデートを取得する方法:**
常に最新のスキルロジックを使用できるように、アップデートを簡単に取得して再同期できます。

```bash
git submodule update --remote --merge
rsync -av --exclude=".*" --exclude="LICENSE" vendor/attribuly/ ./openclaw-config/skills/attribuly-dtc-analyst/
```

---

## マネージドクラウドホスティング（展開）

OpenClaw をローカルで実行せず、常時稼働する完全なマネージド環境で Attribuly スキルと LLM を実行したい場合は、**ModelScope Cloud Hosting** または **AWS Bedrock / SageMaker** の使用をお勧めします。

> **重要**: 完全なマネージドクラウド環境へのアクセスは、現在段階的に展開されています。優先アクセスをリクエストするには、[AllyClaw ウェイティングリスト登録フォーム](https://attribuly.sg.larksuite.com/share/base/form/shrlgSK0KaktsDwbTJqPkjDczCd) に記入してください。

---

## インストール後の設定

スキルバンドルが `openclaw-config/skills/` ディレクトリ（ローカルまたはクラウド）に正常に配置されたら、各スキルを効果的に使用するための特定のトリガーと必要なコンテキストの詳細について、以下の **Technical Reference（技術リファレンス）** を確認してください。

---

## Technical Reference（技術リファレンス）

### スキルトリガーマトリックス (Skill Trigger Matrix)

#### 自動トリガー (Automatic Triggers)

| 条件 (Condition) | トリガーされるスキル (Triggered Skill) | 優先度 (Priority) |
| :--- | :--- | :--- |
| 毎週月曜日 09:00 AM | `weekly-marketing-performance` | 高 (High) |
| 毎日 09:00 AM | `daily-marketing-pulse` | 中 (Medium) |
| ROAS が 20% 超低下 | `weekly-marketing-performance` + チャネル別ドリルダウン | クリティカル (Critical) |
| CPA が 20% 超上昇 | チャネル固有のパフォーマンススキル | 高 (High) |
| CTR が 15% 超低下 | `creative-fatigue-detector` | 中 (Medium) |
| CVR が 15% 超低下 | `funnel-analysis` | 高 (High) |
| 予算消化が 30% 超過 | `budget-optimization` | クリティカル (Critical) |

### スキルチェーンロジック (Skill Chaining Logic)

1つのスキルが問題を検出すると、関連するスキルをトリガーできます：

```text
weekly-marketing-performance
├── IF Google Ads issue detected → google-ads-performance
│   └── IF CTR issue → google-creative-analysis
├── IF Meta Ads issue detected → meta-ads-performance
│   └── IF frequency high → meta-creative-analysis
├── IF CVR issue detected → funnel-analysis
│   └── IF landing page issue → landing-page-analysis
└── IF budget inefficiency → budget-optimization
```

### グローバル API パラメータ (Global API Parameters)

これらのデフォルト値は、特定のスキルで上書きされない限り、すべてのスキルに適用されます：

| パラメータ | デフォルト値 | 備考 |
| :--- | :--- | :--- |
| `model` | `linear` | 線形アトリビューション (Linear attribution) |
| `goal` | `purchase` | 購入コンバージョン（Settings APIから動的目標コードを使用） |
| `version` | `v2-4-2` | API バージョン |
| `page_size` | `100` | 1ページあたりの最大レコード数 |

**Base URL:** `https://data.api.attribuly.com`
**Authentication:** `ApiKey` ヘッダー（`ATTRIBULY_API_KEY` 環境変数から読み取ります。**チャットでユーザーにキーを絶対に要求しないでください。**）

### 意思決定フレームワーク: プラットフォームデータ vs Attribuly データの比較

| シナリオ | プラットフォーム ROAS | Attribuly ROAS | 診断 | アクション |
| :--- | :--- | :--- | :--- | :--- |
| **隠れた原石 (Hidden Gem)** | 低 (<1.5) | 高 (>2.5) | プラットフォームによって過小評価されているトップオブファネル（TOFU）の推進力 | **一時停止しないでください。**「TOFU Driver」としてタグ付けし、スケーリングを検討します。 |
| **虚ろな勝利 (Hollow Victory)** | 高 (>3.0) | 低 (<1.5) | プラットフォームの過剰なアトリビューション（おそらくブランド指名またはリターゲティング） | **予算の上限を設定します。** インクリメンタリティ（純増効果）を調査します。 |
| **真の勝者 (True Winner)** | 高 (>2.5) | 高 (>2.5) | 真正な高パフォーマンス | **スケールします。** 3〜5日ごとに予算を20%増やします。 |
| **真の敗者 (True Loser)** | 低 (<1.0) | 低 (<1.0) | 非効率的な支出 | **一時停止または削減します。** クリエイティブまたはオーディエンスを刷新します。 |

### 主要指標用語集 (Key Metrics Glossary)

| 指標 | 計算式 | 説明 |
| :--- | :--- | :--- |
| **ROAS** | `conversion_value / spend` | Attribuly が追跡する広告費用対効果 |
| **ncROAS** | `ncPurchase / spend` | 新規顧客 ROAS (New Customer ROAS) |
| **MER** | `total_revenue / total_spend` | マーケティング効率比率 (Marketing Efficiency Ratio) |
| **CPA** | `spend / conversions` | 顧客獲得単価 (Cost Per Acquisition) |
| **CPC** | `spend / clicks` | クリック単価 (Cost Per Click) |
| **CPM** | `(spend / impressions) * 1000` | 1000回インプレッションあたりのコスト |
| **CTR** | `(clicks / impressions) * 100%` | クリック率 (Click-Through Rate) |
| **CVR** | `(conversions / clicks) * 100%` | コンバージョン率 (Conversion Rate) |
| **LTV** | `total_sales / unique_customers` | 顧客生涯価値 (Lifetime Value) |
| **Net Profit** | `sales - shipping - spend - COGS - taxes - fees` | 真の純利益 (True Profit) |
| **Net Margin** | `net_profit / sales * 100%` | 利益率 (Profit Margin) |
