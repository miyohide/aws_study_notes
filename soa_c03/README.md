# これは何か

AWS Certified CloudOps Engineer - Associate(SOA-C03)取得のための個人的勉強用メモです。

試験ガイドは[AWS Certified CloudOps Engineer - Associate (SOA-C03)](https://docs.aws.amazon.com/ja_jp/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03.html)を参照。

# 試験の概要

CloudOps エンジニアを対象とし、AWS でのワークロードのデプロイ、管理、運用の能力を検証する試験。

- 合格スコア: 720（100〜1,000 の換算スコア）
- 採点対象の設問: 50 問（採点対象外が別途 15 問）
- 設問形式: 択一選択問題と複数選択問題

## コンテンツ分野と重み

| 分野 | 内容 | 重み |
| ---- | ---- | ---- |
| 分野 1 | モニタリング、ログ記録、分析、修復、パフォーマンスの最適化 | 22% |
| 分野 2 | 信頼性と事業の継続性 | 22% |
| 分野 3 | デプロイ、プロビジョニング、オートメーション | 22% |
| 分野 4 | セキュリティとコンプライアンス | 16% |
| 分野 5 | ネットワークとコンテンツ配信 | 18% |

試験ガイドによる重み設定であり、各分野に出題が偏ることを把握しておく。分野 1〜3 の運用系が全体の 2/3 を占める。

# 分野 1: モニタリング、ログ記録、分析、修復、パフォーマンスの最適化

システムの状態を可視化し、異常を検知して自動修復するためのサービス群。

## Amazon CloudWatch

AWS リソースやアプリケーションのメトリクス、ログ、イベントを収集・可視化・監視するサービス。運用監視の中核。

- メトリクス: リソースのパフォーマンス指標を収集。カスタムメトリクスも送信可能
- アラーム: メトリクスがしきい値を超えたら通知やアクション（Auto Scaling、EC2 停止など）を実行
- ログ（CloudWatch Logs）: ログの収集・保存・検索。Logs Insights でクエリ分析が可能
- ダッシュボード: 複数メトリクスを一画面で可視化
- エージェント（CloudWatch Agent）: EC2 やオンプレのメモリ・ディスク使用率などの OS メトリクスとログを収集

### 基本モニタリングと詳細モニタリング

| 項目 | 基本モニタリング | 詳細モニタリング |
| ---- | ---------------- | ---------------- |
| メトリクス収集間隔 | 5 分 | 1 分 |
| 料金 | 無料 | 有料 |
| デフォルト | 有効 | 無効（オプションで有効化） |
| ユースケース | 粗い監視で十分な場合 | スパイク検知、オートスケーリング精度向上 |

## Amazon EventBridge

AWS サービスやアプリケーションのイベントをルールでフィルタリングし、ターゲット（Lambda、SNS、Step Functions など）に配信するイベントバス。イベント駆動の自動修復に利用する。

## AWS CloudTrail

AWS アカウント内の API コールや管理アクションを記録・監査するサービス。「誰が・いつ・何をしたか」を追跡する。

| 種類 | 対象 | デフォルト記録 | 代表例 |
| ---- | ---- | -------------- | ------ |
| 管理イベント | 制御プレーン操作 | 有効 | EC2 作成/削除、IAM 操作 |
| データイベント | データプレーン操作 | 無効 | S3 オブジェクト操作、Lambda 呼び出し |
| インサイトイベント | 異常検知 | 無効 | API 呼び出し頻度の急増 |

## AWS Systems Manager

EC2 やオンプレを含むリソースの運用管理を一元化するサービス。試験頻出。

- Run Command: 複数インスタンスへのコマンド一括実行
- Patch Manager: OS パッチ適用の自動化
- Session Manager: SSH/RDP 不要のセキュアなシェルアクセス
- Parameter Store: 設定値やシークレットの一元管理
- State Manager: 設定を望ましい状態に維持
- Automation: 運用タスクのワークフロー自動化
- OpsCenter: 運用上の問題（OpsItem）を一元管理

## Amazon Managed Grafana / CloudWatch への分析

CloudWatch メトリクスやログを可視化・分析し、パフォーマンスのボトルネックを特定する。Logs Insights や Contributor Insights で寄与度の高い要因を分析する。

# 分野 2: 信頼性と事業の継続性

高可用性、スケーラビリティ、バックアップ、ディザスタリカバリを実現するサービス群。

## Elastic Load Balancing (ELB)

トラフィックを複数のターゲットに分散し、可用性を高めるロードバランサー。

| 種類 | レイヤー | 主な用途 |
| ---- | ------- | -------- |
| Application Load Balancer (ALB) | L7 (HTTP/HTTPS) | パスベース・ホストベースのルーティング |
| Network Load Balancer (NLB) | L4 (TCP/UDP) | 超低遅延・高スループット |
| Gateway Load Balancer (GWLB) | L3 | ファイアウォールなどの仮想アプライアンス |

アクセスログはデフォルトで無効のため、必要に応じて有効化する。

## Amazon EC2 Auto Scaling

需要に応じて EC2 インスタンス数を自動で増減させる。希望/最小/最大キャパシティとスケーリングポリシーを設定する。

- ライフサイクルフック: スケールイン/アウト時に処理（ログ退避など）を挿入できる
- ヘルスチェック: 異常インスタンスを自動で置き換える

## Amazon Route 53

スケーラブルな DNS サービス。ルーティングポリシー（加重、レイテンシー、フェイルオーバー、位置情報など）とヘルスチェックで可用性を高める。DR 時のトラフィック切り替えに使用する。

## AWS Backup

複数の AWS サービス（EBS、RDS、DynamoDB、EFS など）のバックアップを一元管理するサービス。バックアップポリシーとスケジュールを集中管理する。

## Amazon S3

高耐久性(99.999999999%)のオブジェクトストレージ。バックアップ、静的コンテンツ、ログ保管などに利用。

- ストレージクラス: Standard、Standard-IA、One Zone-IA、Glacier 系などコストと取り出し速度のトレードオフで選択
- ライフサイクルポリシー: 自動的に別クラスへ移行・削除
- バージョニング / レプリケーション（CRR/SRR）: 誤削除対策とリージョン間冗長化

## Amazon RDS / Amazon Aurora

マネージドなリレーショナルデータベース。

- マルチ AZ 配置: 同期レプリカへ自動フェイルオーバーし高可用性を実現
- リードレプリカ: 読み取り負荷の分散
- 自動バックアップ / スナップショット: ポイントインタイムリカバリ
- Aurora グローバルデータベース: リージョン間 DR と低遅延リード

## AWS Elastic Disaster Recovery / バックアップ戦略

RTO/RPO に応じて DR 戦略（バックアップ&リストア、パイロットライト、ウォームスタンバイ、マルチサイト）を選択する考え方が問われる。

# 分野 3: デプロイ、プロビジョニング、オートメーション

Infrastructure as Code(IaC)と自動デプロイのサービス群。

## AWS CloudFormation

テンプレート(JSON/YAML)でインフラをコード化しプロビジョニングする IaC サービス。試験頻出。

- スタック: テンプレートから作成されるリソースの集合
- StackSets: 複数アカウント・複数リージョンへ一括展開
- ドリフト検出: 実リソースとテンプレートの差分を検出
- 変更セット: 更新前に影響範囲をプレビュー
- DeletionPolicy / UpdatePolicy: 削除・更新時の挙動を制御
- cfn-init / ヘルパースクリプト: EC2 起動時の初期化

## AWS Elastic Beanstalk

アプリをアップロードするだけで、EC2・ELB・Auto Scaling などの環境を自動構築する PaaS。デプロイポリシー（All at once、Rolling、Immutable、Blue/Green）を選択する。

## AWS Systems Manager（オートメーション）

Automation ドキュメント(Runbook)で定型運用を自動化する。パッチ適用やインスタンス復旧などに利用。

## EC2 Image Builder

OS イメージ(AMI)やコンテナイメージのビルド・テスト・配布を自動化するサービス。ディストリビューション設定で配布先リージョンやアカウントを管理する。

## AWS OpsWorks

Chef / Puppet を使った構成管理サービス。（新規採用は非推奨傾向だが試験範囲として押さえる）

## AWS Lambda

サーバー管理不要でコードを実行するサーバーレスコンピュート。イベント駆動の自動化処理に多用する。

| 項目 | 予約済み同時実行 | プロビジョンドコンカレンシー |
| ---- | ---------------- | ---------------------------- |
| 目的 | 同時実行枠の確保・制御 | コールドスタートの解消 |
| コールドスタート | 解消できない | 解消できる |
| 課金 | 通常課金 | 環境維持に追加料金 |

# 分野 4: セキュリティとコンプライアンス

アクセス制御、暗号化、監査、脅威検知のサービス群。

## AWS IAM

ユーザー・グループ・ロール・ポリシーで AWS リソースへのアクセスを制御する。最小権限の原則が基本。

- IAM ロール: 一時認証情報を付与。EC2 インスタンスプロファイルやサービス間連携に使用
- IAM ポリシー: JSON で許可/拒否を定義

## AWS IAM Identity Center

複数アカウントとアプリへのシングルサインオン(SSO)を提供。Organizations と連携し、権限セットでアクセスを集中管理する。

## AWS Organizations

複数の AWS アカウントを一元管理する。

- 組織単位(OU): アカウントをグループ化
- SCP(サービスコントロールポリシー): アカウント/OU に対する権限のガードレール（上限）
- 委任管理者: 特定サービスの管理を他アカウントに委任

## AWS KMS

暗号化キーを管理するサービス。S3、EBS、RDS などの暗号化(保管時の暗号化)に使用。カスタマーマネージドキーの自動ローテーションが設定可能。

## AWS Config

リソースの構成変更を記録し、ルール(マネージド/カスタム)でコンプライアンス評価する。アグリゲーターで複数アカウント・リージョンのデータを集約する。

## AWS Secrets Manager / Parameter Store

認証情報や API キーなどのシークレットを安全に保管・ローテーションする。

## Amazon GuardDuty

ログ(VPC Flow Logs、CloudTrail、DNS Logs)を機械学習と脅威インテリジェンスで解析し、脅威を検知するサービス。

## Amazon Inspector

EC2 / ECR / Lambda の脆弱性(CVE)や設定不備を自動スキャンするサービス。

## AWS Security Hub

GuardDuty や Inspector などの検出結果を集約・標準化し、セキュリティ状態を一元管理する。

| サービス | 主な目的 | 対象 |
| -------- | -------- | ---- |
| GuardDuty | 脅威検知 | アカウント全体のログ |
| Inspector | 脆弱性管理 | EC2 / ECR / Lambda |
| Security Hub | 統合管理・可視化 | 各サービスの検出結果 |

## AWS WAF / AWS Shield

- WAF: Web アプリへの SQL インジェクションや XSS などをフィルタリング
- Shield: DDoS 攻撃からの保護(Standard は自動、Advanced は追加保護)

# 分野 5: ネットワークとコンテンツ配信

VPC、DNS、接続、コンテンツ配信のサービス群。

## Amazon VPC

論理的に分離された仮想ネットワーク。サブネット、ルートテーブル、インターネットゲートウェイ、NAT ゲートウェイなどを構成する。

- セキュリティグループ: インスタンス単位のステートフルなファイアウォール
- ネットワーク ACL: サブネット単位のステートレスなフィルタリング
- VPC Flow Logs: ネットワークトラフィックの記録。接続のトラブルシュートに使用
- VPC エンドポイント: AWS サービスへプライベート接続(Gateway/Interface 型)

## Amazon Route 53

DNS サービス。ドメイン登録、名前解決、各種ルーティングポリシー、ヘルスチェックを提供。Resolver でハイブリッド環境の DNS 解決も行う。

## Amazon CloudFront

エッジロケーションからコンテンツを配信する CDN。キャッシュによる低遅延配信と、オリジン保護(OAC)を提供する。

## AWS Direct Connect / Site-to-Site VPN

オンプレミスと AWS を接続するハイブリッド接続。

- Direct Connect: 専用線による安定・低遅延接続
- Site-to-Site VPN: インターネット経由の IPsec 暗号化トンネル

## AWS Transit Gateway

複数の VPC とオンプレミスネットワークをハブ&スポーク型で相互接続する。マルチ VPC 環境の接続を簡素化する。

## AWS Global Accelerator

AWS のグローバルネットワークを使い、エンドユーザーからアプリへの経路を最適化して可用性と性能を高める。

# 対象の AWS サービス（カテゴリ別の整理）

| カテゴリ | 代表的なサービス |
| -------- | ---------------- |
| コンピュート | EC2、Lambda、ECS、Auto Scaling、Elastic Beanstalk |
| ストレージ | S3、EBS、EFS、FSx、Storage Gateway、AWS Backup |
| データベース | RDS、Aurora、DynamoDB、ElastiCache |
| ネットワーク | VPC、Route 53、CloudFront、ELB、Direct Connect、Transit Gateway、Global Accelerator |
| 管理・運用 | CloudWatch、CloudTrail、Systems Manager、Config、CloudFormation、Organizations、Control Tower、Trusted Advisor、Health Dashboard |
| セキュリティ | IAM、IAM Identity Center、KMS、Secrets Manager、GuardDuty、Inspector、Security Hub、WAF、Shield |

# 学習のポイント

- 運用系の分野 1〜3 で全体の 2/3 を占めるため、CloudWatch / Systems Manager / CloudFormation / Auto Scaling は重点的に学習する
- AWS マネジメントコンソールだけでなく AWS CLI での操作も問われる
- 高可用性・DR の設計パターン(RTO/RPO)と各サービスの可用性オプションを整理する
- トラブルシューティング(ネットワーク疎通、権限不足、デプロイ失敗など)の原因切り分けを問う設問が多い
