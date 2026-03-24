[English](./README.md) | [简体中文](./README.zh-CN.md) | **日本語**

# DTC Eコマース向け AllyClaw OpenClaw スキル (Attribuly)

DTC Eコマースに特化した AI マーケティング・パートナー。Attribuly のファーストパーティデータを活用し、簡単にインストール可能で、自律的な分析のための完全なクラウド展開をサポートします。

### 主な機能:
- **真の ROI へのフォーカス** — Attribuly のファーストパーティ・アトリビューションの概念（真の ROAS、新規顧客 ROAS (ncROAS)、利益、利益率、LTV、MER）を活用し、プラットフォームの過剰なアトリビューションを削減します。
- **データコントロール** — ローカル環境またはクラウドに展開可能。メモリと戦略は安全な環境内に保持されます。
- **拡張可能なスキル** — 自動トリガーを内蔵。ファネル、予算消化ペース、クリエイティブ、データ乖離を自律的に分析します。特定のプラットフォームに縛られません。

### できること:
- **診断分析:** ファネルのボトルネックやランディングページの離脱要因を自律的に検出します。
- **パフォーマンス追跡:** 30秒で確認できる毎日の予算消化レポートや、詳細な週次エグゼクティブ・サマリーを生成します。
- **クリエイティブ最適化:** Google/Meta のクリエイティブを真の収益性に基づいて評価し、クリエイティブの疲労を特定します。
- **予算最適化:** 利益を最優先した予算の再配分やオーディエンス調整の推奨事項を取得します。

---

## ニュース＆更新履歴

**[2026-03-22] v1.1.0 をリリースしました！** 

### [v1.1.0] 追加機能
- **診断スキルスイート**: 
  - `funnel-analysis`: エンドツーエンドの顧客コンバージョンファネルを分析し、チャネルまたはランディングページごとの特定の離脱ボトルネックを特定する新しいスキル。
  - `landing-page-analysis`: ステージの進行状況、エンゲージメントの質、トラフィックソースの適合性を分析することで、ランディングページのコンバージョン損失を診断する新しいスキル。
  - `attribution-discrepancy`: 広告プラットフォームの指標（Meta/Google）、Attribuly の統合アトリビューション、およびバックエンドのストアデータ間のレポートの乖離を特定および診断する新しいスキル。
- **クリエイティブ分析スイート**: 
  - `google-creative-analysis`: Google Ads のクリエイティブパフォーマンスデータを抽出し、処理、分析する新しいスキル。品質スコア、PMax アセットデータ、および標準化された A/B テストプロトコルとの統合を含みます。

### [v1.1.0] 変更点
- **スキルレジストリの更新**: `SKILL_REGISTRY.md` を更新し、`funnel-analysis`、`landing-page-analysis`、`attribution-discrepancy`、および `google-creative-analysis` のステータスを `🔜 Planned` から `✅ Ready` に変更しました。
- **Google Ads Performance**: CTR の問題が検出された場合に、新しい `google-creative-analysis` スキルに直接マッピングされる、より詳細な根本原因のブレイクダウンロジックを追加しました。
- **Weekly Marketing Performance**: ファネルのヘルスチェックを統合し、CVR が低下したときに新しい `funnel-analysis` スキルを呼び出すトリガー条件を追加しました。
- **Meta Ads Performance**: 新しいアトリビューションパイプラインと互換性のある標準化されたパラメーターを含めるように、データ取得スキーマを更新しました。
- **Google Creative Analysis の統合**: 独立していたクリエイティブ分析フレームワーク（評価基準、DTC のベストプラクティス、ダッシュボードアーキテクチャ）を `google-creative-analysis` スキルに直接統合しました。

### [v1.1.0] 削除
- `attribution-discrepancy` の根本原因分析ロジックから、時代遅れになった「トラッキング不足 (Under-tracking)」のシナリオを削除しました。
- 冗長になった単独の `creative_analysis_framework.md` ファイルを削除しました。

**[初期リリース] v1.0.0**
- ユーザーの意図をエージェントのスキルにマッピングするための `SKILL_REGISTRY.md` を作成しました。
- コアとなるパフォーマンス分析スキル (`weekly_marketing_performance`, `daily_marketing_pulse`, `google_ads_performance`, `meta_ads_performance`) を実装しました。
- コアとなる最適化スキル (`budget_optimization`, `audience_optimization`, `bid_strategy_optimization`) を実装しました。

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

トリガーと使用マッピングの詳細については、[SKILL\_REGISTRY.md](SKILL_REGISTRY.md) を参照してください。

---

## インストールガイド

### ステップ 0: Attribuly API キーの取得

スキルをインストールする前に、Attribuly API キーが必要です。これらのスキルは、自律的に機能するために Attribuly 独自の指標（`new_order_roas` や真の利益など）に大きく依存しています。

- **有料機能:** API キーは有料プランのユーザーのみが利用できます。キーを生成するには、ワークスペースをアップグレードする必要があります。
- **無料トライアル:** 新規ユーザーの場合は、[14日間の無料トライアル](https://attribuly.com/pricing/) を開始してプラットフォームをテストできます。
- **設定方法:** 取得したキーは、OpenClaw 環境に安全に設定する必要があります。**チャットで API キーを直接送信しないでください。**
  1. OpenClaw の **エージェント設定 (Agent Settings)** を開きます。
  2. **環境変数 (Environment Variables)** または **シークレット (Secrets)** セクションに移動します。
  3. `ATTRIBULY_API_KEY` という名前の新しい変数を追加します。
  4. API キーをその変数の値として貼り付けて保存します。エージェントはデータを呼び出すときに、この変数を自動的かつ安全に読み取ります。


---

これらの Attribuly スキルを独自の OpenClaw 環境にインストールするには、主に 2 つの方法があります。ワークフローに最適な方法を選択してください。

### 方法 1: チャット経由でのインストール (クイックスタート)

以下のプロンプトを OpenClaw のインターフェースにコピーすると、エージェントが自動的にインストールを行います。

> Install these skills from https\://github.com/Alexchulee/Attribuly-DTC-skills-openclaw\.git

### 方法 2: Git サブモジュール (推奨: 更新が簡単)

このリポジトリからの最新の改善に合わせてスキルを常に最新の状態に保ちたい場合は、Git サブモジュールとして追加するのが最善のアプローチです。

1. ターミナルで OpenClaw インスタンスのルートディレクトリに移動します。
2. このリポジトリをサブモジュールとして追加します。
   ```bash
   git submodule add https://github.com/Alexchulee/Attribuly.git vendor/attribuly
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

### ステップ 3: エージェントの役割（Rule & Soul）の初期化

エージェントが DTC グロースパートナーの専門家として機能するように、コアアイデンティティを構成する必要があります。OpenClaw は、ワークスペースのブートストラップファイルをシステムプロンプトに自動的に注入します。

**自動化メソッド (推奨):**
役割プロンプトを直接エージェントのワークスペースに `SOUL.md` としてコピーします (ファイルが既に存在する場合は追記します):
```bash
cp vendor/attribuly/role_prompt.md ./openclaw-config/SOUL.md
```
*(特定のマルチエージェントセットアップを使用している場合は、`~/.openclaw/agents/<エージェント名>/agent.md` にコピーしてください)*

**手動メソッド (チャットインターフェース):**
1. このリポジトリにある [`role_prompt.md`](role_prompt.md) ファイルを開きます。
2. ファイルの内容全体をコピーします。
3. OpenClaw のチャット/ダイアログボックスに貼り付けて、エージェントのルール、魂、ペルソナを初期化します。

---

## マネージドクラウドホスティング（展開）

OpenClaw をローカルで実行せず、常時稼働する完全なマネージド環境で Attribuly スキルと LLM を実行したい場合は、**ModelScope Cloud Hosting** または **AWS Bedrock / SageMaker** の使用をお勧めします。

> **重要**: 完全なマネージドクラウド環境へのアクセスは、現在段階的に展開されています。優先アクセスをリクエストするには、[AllyClaw ウェイティングリスト登録フォーム](https://attribuly.sg.larksuite.com/share/base/form/shrlgSK0KaktsDwbTJqPkjDczCd) に記入してください。

---

## インストール後の設定

スキルバンドルが `openclaw-config/skills/` ディレクトリ（ローカルまたはクラウド）に正常に配置されたら、[SKILL\_REGISTRY.md](SKILL_REGISTRY.md) を確認して、各スキルを効果的に使用するための特定のトリガーと必要なコンテキストの詳細を参照してください。
