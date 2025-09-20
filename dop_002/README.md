# これは何か

AWS Certified DevOps Engineer - Professional(DOP-C02)取得のための個人的勉強用メモです。

# Kibanaへのアクセス制御

- [KibanaのAmazon Cognito認証](https://aws.amazon.com/jp/blogs/news/get-started-with-amazon-elasticsearch-service-use-amazon-cognito-for-kibana-access-control/)
- プロキシサーバーのある、またはないIPベースのアクセスポリシー設定

# [Amazon GuardDuty](https://pages.awscloud.com/rs/112-TZM-766/images/20180509_AWS-BlackBelt_Amazon-GuardDuty.pdf)

AWS上で発生しているログを自動的に収集し、機械学習や脅威インテリジェンスを利用して怪しい動きを検出（脅威検出）するサービス。メンバーアカウントごとに有効化する必要がある。

# 異常のあるEC2インスタンスのAuto Scalingの終了を遅らせてトラブルシュートする

Auto Scalingフックを使用してインスタンスの状態を「Terminating:Wait」にして削除を一時停止する。その後、CloudWatchイベントを使用してログをバックアップする。

see. https://aws.amazon.com/jp/premiumsupport/knowledge-center/auto-scaling-delay-termination/

# Elastic Beanstalkのコマンドとコンテナコマンド

- コマンド
    - アプリとウェブサーバーがセットアップされ、アプリバージョンファイルが抽出される前に実行するコマンドを記述する
- コンテナコマンド
    - アプリとウェブサーバーがセットアップされ、アプリバージョンファイルが抽出された後、デプロイされる前に実行するコマンドを記述する

# Elastic Beanstalkの優先順位

設定は以下の順で設定される。参考情報は、[Elastic Beanstalkの優先順位のドキュメント](https://docs.aws.amazon.com/ja_jp/elasticbeanstalk/latest/dg/command-options.html#configuration-options-precedence)を参照。

1. 環境に直接適用される設定
    - 環境の作成時または更新時にコンソール、EB CLI、CSKなどのクライアントによってElastic Beanstalk APIで指定された設定
2. 保存済み設定
3. 設定ファイル（.ebextensions）
4. デフォルト値

# OpsWorks Berkshelf

Chef 11.10スタックにおいてBerkshelfを使用することで複数のリポジトリからクックブックを管理およびインストールすることができる。

参考情報は、[OpsWorksのBerkshelfの使用のドキュメント](https://docs.aws.amazon.com/ja_jp/opsworks/latest/userguide/workingcookbook-chef11-10.html#workingcookbook-chef11-10-berkshelf)を参照。

# Elastic Beanstalkで使えるデプロイポリシー

see. [AWS Elastic Beanstalkで使えるデプロイポリシーを理解する](https://dev.classmethod.jp/articles/elastic-beanstalk-deploy-policy/)

# Elastic Load Balancingのアクセスログはオプションであり、デフォルトはオフとなっている

see. [Access logs for your Classic Load Balancer](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/classic/access-log-collection.html)

# Elastic Beanstalkのローリング更新の設定

以下の3つの中から選択。

- Rolling based on Health
    - 現在のバッチのインスタンスが正常な状態になるまで待ってからインスタンスを実行状態にし、次のバッチを処理する
- Rolling based on Time
    - 新しいインスタンスを開始して実行状態にし、次のバッチを処理するまでの待機時間を指定する
- Immutable

# EC2 AutoScalingのライフサイクルフックを使用してスケールイン時にEC2のログを退避させる

1. AutoScalingグループにライフサイクルフックを設定
2. AutoScalingのターミネートイベントでEventBridgeにトリガー
3. EventBridgeトリガーでLambdaを起動
4. SSM Run Commandでコマンドを実行
5. 必要なログをS3に退避

詳細は[AutoScallingのライフサイクルフックを使用してスケールイン時にEC2インスタンス内のログを退避させる](https://dev.classmethod.jp/articles/autoscalling-terminating-log-upload/)を参照。

# EC2 describe-security-groups

セキュリティグループの詳細を取得するコマンド。各リージョンでのでキュリティグループを解析する。詳細は[AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html)を参照。

# CloudFormation

## CloudFormationにてオブジェクトを含むバケットを削除する方法

カスタムリソース利用して削除がトリガーされる前にバケットを空にする。

## CloudFormationのカスタムリソース

CloudFormationが対応していないリソースの作成などにおいて、CloudFormationのテンプレートにカスタムのプロビジョニングロジックを記述することで、削除・更新・作成をサポートする仕組み。`AWS::CloudFormation::CustomResource`または`Custom::MyCustomResourceTypeName`リソースタイプを使用する。

## CloudFormationにてOpsWorksを呼び出す

リソースタイプに`AWS::OpsWorks::Stack`を指定する。

## CloudFormation UpdatePolicy

[CloudFormation UpdatePolicyを使用してAuto Scaling Groupの更新を処理する](https://dev.classmethod.jp/articles/autoscalinggroup-cloudformation-updatepolicy/)の記述が詳しい。

AWS::AutoScaling::AutoScalingGroupリソースにおける更新ポリシーは以下のものがある。

- AutoScalingReplacingUpdate
    - Auto ScalingグループまたはAuto Scalingグループのインスタンスが更新時に置き換えられる。
    - 新しいAuto Scalingグループの作成が完了するまでCloudFormationが古いAuto Scalingグループを保持する。
- AutoScalingRollingUpdate
    - Auto Scalingグループのインスタンスをバッチで更新する。更新するサイズやPauseTimeを指定する必要がある。
    - 古いLaunch Configurationはバッチで更新した後で削除される
- AutoScalingScheduledAction
    - スケジュールされたアクションが関連づけられているAuto Scalingグループを更新する

## CloudFormationでLambdaのバージョンアップ

CloudFormationでLambdaをデプロイしたあと、バージョンアップをやる場合は、以下の点で設定する。

- バージョン
    - 既存のS3バケットでバージョニングを有効にする。その後、CloudFormationにてs3ObjectVersionプロパティにD3オブジェクトのバージョンIDを指定する
- コードの場所
    - バージョンアップ時には別のバケットの場所を指すようにする。
- オブジェクト名
    - CloudFormationにてS3Keyプロパティを更新して、別の場所とzipファイルの名前を示すようにする

## CloudFormationでDeletinPolicyでSnapshotをサポートするリソース

- AWS::EC2::Volume
- AWS::ElastiCache::CacheCluster
- AWS::ElastiCache::ReplicationGroup
- AWS::Neptune::DBCluster
- AWS::RDS::DBCluster
- AWS::RDS::DBInstance
- AWS::Redshift::Cluster

## CloudFormation init

AWS CloudFormation で EC2 インスタンスを起動するときに、その中でソフトウェアや設定を自動的に初期化・設定するための仕組み。CloudFormation テンプレートの AWS::CloudFormation::Init メタデータに、インスタンス起動時に適用する設定を記述する。EC2 インスタンスの UserData などで cfn-init ヘルパースクリプトを呼び出す。

## UPDATE_ROLLBACK_FAILEDからの復旧

- ContinueUpdateRollbackコマンドの実行
    - see. https://repost.aws/ja/knowledge-center/cloudformation-update-rollback-failed
    - 要アクセス許可の変更
- DeleteStackアクションを使用してスタックを削除
- スタックの期待値と一致するようにリソースを手動で修正する

## スタックポリシー

スタックの更新時に特定のリソースが誤って変更・削除されるのを防ぐための保護ルール。重要なデータベースやS3バケットなどが意図せず置き換えられたり削除されたりすると、業務停止やデータ消失のリスクがある。そこでスタックポリシーを設定することで、どのリソースを更新可能にするか／更新禁止にするかを制御する。

以下の例は、`MyDBInstance`を更新することを防止する。

```json
{
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "Update:*",
      "Principal": "*",
      "Resource": "LogicalResourceId/MyDBInstance"
    }
  ]
}
```

# Amazon SQS

## Amazon SQSで利用可能なCloudWatchメトリクス

| メトリクス名 | 説明 |
| ---------- | ---- |
| ApproximateNumberOfMessagesVisible | すぐに受信可能な（キューに残っている）メッセージ数。キューの滞留状況を把握するのに重要 |
| ApproximateNumberOfMessagesNotVisible | 処理中（まだ削除されていない）メッセージの数 |
| ApproximateNumberOfMessagesDelayed | DelaySeconds により遅延中のメッセージ数 |
| ApproximateNumberOfMessages | SQSキュー属性の一つ。Visible、Delayed、NotVisibleをまとめた情報をAPIで取得可能 |

# Organizations

## 委任管理者アカウント

管理アカウント (Management Account) が持つ管理権限の一部を、特定のサービスについて他のメンバーアカウントに委任できる仕組み。

管理アカウントに運用権限を集中させすぎるとセキュリティリスクや運用負荷が高まるため、特定のサービスの管理を別のアカウントに任せられる仕組みが「委任管理者」。AWS ConfigやAWS Security Hub、AWS Service Catalogなどで活用。

## SCP

SCPは組織単位（OU）に対して適用されるべきであるというベストプラクティスがある。

### EC2インスタンスの認証情報に関する各種条件キー

| 条件キー | 説明 |
| ------- | ---- |
| aws:EC2InstanceSourceVPC | EC2インスタンスが配置されているVPCの識別子 |
| aws:SourceVpc | リクエストが発生したVPCの識別子 |
| aws:EC2InstanceSourcePrivateIPv4 | EC2インスタンスに割り当てられたプライベートIPv4アドレス |
| aws:VpcSourceIp | VPC内からのリクエストのソースIPアドレス |

### 指定したリージョン以外は使わせないようにする方法

`aws:RequestedRegion`条件キーを使い、拒否タイプのSCCPを組織ルートにアタッチする。

# AWS Config

## AWS Config アグリゲーター

複数のアカウントやリージョンに分散している AWS Config の設定データやコンプライアンス情報を一元的に集約 する仕組み。

## 設定レコーダー

AWS Configが管理対象のリソースについて「どの種類のリソースを記録するか」を制御するコンポーネント。

## ec2-instance-profile-attached

EC2インスタンスにIAMプロファイルがアタッチされているかどうかを確認するルール。

## ec2-imdsv2-check

EC2インスタンスのメタデータのバージョンがIMDSv2で設定さているかどうかを確認するルール。

## cloudformation-stack-drift-detection-check

CloudFormationスタックの実際の設定が、予想される設定と異なるか、ドリフトしたかどうかを確認するルール。

## s3-bucket-server-side-encryption-enabled

S3のデフォルトの暗号化が有効になっていること、またはS3バケットポリシーが AES-256またはput-objectを使用するサーバー側の暗号化を使用していないAWS Key Management Serviceリクエストを明示的に拒否することを確認するルール。

## cmk-backing-key-rotation-enabled

KMSのカスタマーマネージドキーに対して、自動キーのローテーションが有効になっているかどうかをチェックするルール。

## iam-user-unused-credentials-check

IAMユーザーのアクセスキーやコンソールパスワードが一定期間使われていない場合に検出するルール。

# AWS Security Hub

## 信頼されたアクセス

OrganizationsとSecurity Hubを統合するための仕組み。有効化することでOrganizations経由でSecurity Hubを有効化しセキュリティ検出結果（Findings）を委任管理者アカウントに集約可能。

# AWS Control Tower

- マルチアカウント環境をベストプラクティスで構築・管理するマネージドサービス
- ガードレールを通じて、セキュリティや運用の統制を自動化。
- アカウントファクトリーで新規アカウントを安全に素早く作れる。

## Organizations vs Control Tower

| 項目     | AWS Organizations      | AWS Control Tower                                  |
| ------ | ---------------------- | -------------------------------------------------- |
| 主な役割   | マルチアカウント管理の基盤          | ベストプラクティスに基づいたマルチアカウント管理の自動化                       |
| 提供機能   | OU, アカウント作成, SCP, 請求統合 | Landing Zone, Guardrails, Account Factory, ダッシュボード |
| 自動化レベル | 低い（手動で設計・設定する必要あり）     | 高い（標準基盤を自動構築）                                      |
| ガバナンス  | SCPのみ                  | SCP + Configルール + Hook で強化                         |
| 可視化    | 無し（自分で作る必要あり）          | ダッシュボードで一元可視化                                      |
| 利用シーン  | 柔軟な独自設計が必要な場合          | 標準的なマルチアカウント運用を早く導入したい場合                           |

## プロアクティブ vs 検出用

| 項目    | プロアクティブ                     | 検出用                   |
| ----- | --------------------------- | --------------------- |
| タイミング | 作成・変更の前にブロック                | 作成・変更の後に検知            |
| 仕組み   | SCP, CloudFormation Hook など | AWS Config ルール        |
| 対応方法  | 違反リソースは作成できない               | 違反リソースは作成できるが後から検出    |
| 利用シーン | 絶対に許可したくない操作を防ぐ             | 継続的な監査やベストプラクティスの遵守確認 |


# AWS CloudTrail

AWS アカウント内で発生する API コールや管理アクションを記録・監査するサービス。

| 種類              | 対象                  | デフォルト記録 | 代表例                                | コスト                    |
|-------------------|-----------------------|----------------|---------------------------------------|---------------------------|
| **管理イベント**  | 制御プレーン操作      | ✅（有効）     | EC2 作成/削除, IAM 操作                | 無料                      |
| **データイベント**| データプレーン操作    | ❌（無効）     | S3 オブジェクト操作, Lambda 呼び出し   | 有料（イベント数課金）    |
| **インサイトイベント** | 異常検知           | ❌（無効）     | API 呼び出し頻度の急増                 | 有料（有効化時）          |

# CodeDeploy

## AWS CodeDeploy ライフサイクルイベント詳細まとめ

### EC2 / オンプレ (In-Placeデプロイ)

| イベント | 説明 |
|----------|------|
| **ApplicationStop** | 既存アプリケーションを停止する処理。不要ならスキップ可能。 |
| **DownloadBundle** | 新しいアプリケーションリビジョンをS3やGitHubから取得。 |
| **BeforeInstall** | 旧ファイル削除、依存関係確認、バックアップなどの準備処理。 |
| **Install** | アプリケーションファイルを所定の場所にコピー。 |
| **AfterInstall** | 設定変更や依存関係インストールなどの後処理。 |
| **ApplicationStart** | 新しいアプリケーションを起動。 |
| **ValidateService** | アプリケーションが正常に稼働しているかを確認。失敗するとロールバック。 |

---

### EC2 / オンプレ (Blue/Greenデプロイ)

| イベント | 説明 |
|----------|------|
| **BeforeInstall** | 新環境での準備処理。 |
| **AfterInstall** | 新環境に対して設定や後処理を実施。 |
| **ApplicationStart** | 新環境のアプリケーションを起動。 |
| **ValidateService** | 正常動作確認。 |
| **トラフィック切替関連** | BlueからGreenへ段階的にトラフィックを切替。失敗時は元に戻せる。 |

---

### AWS Lambda

| イベント | 説明 |
|----------|------|
| **BeforeAllowTraffic** | 新バージョンにトラフィックを流す前に実行。ウォームアップ処理などに利用可能。 |
| **AfterAllowTraffic** | 新バージョンにトラフィックを切り替えた後に実行。検証やクリーンアップに利用可能。 |

---

### Amazon ECS

| イベント | 説明 |
|----------|------|
| **BeforeInstall** | 新しいタスク定義を適用する前に実行。 |
| **AfterInstall** | 新しいタスク定義を適用した直後に実行。 |
| **AfterAllowTestTraffic** | テスト用ターゲットグループに新バージョンへトラフィックが流れた後に実行。 |
| **BeforeAllowTraffic** | 本番トラフィックを新バージョンに切替える前に実行。 |
| **AfterAllowTraffic** | 本番トラフィックを新バージョンに切替えた後に実行。 |

# AWS Step Functions

## StandardワークフローとExpressワークフロー

| 特徴             | Standardワークフロー                     | Expressワークフロー                  |
|------------------|------------------------------------------|--------------------------------------|
| 最大実行時間     | 1年                                      | 5分                                   |
| スループット     | 低〜中                                   | 高（毎秒数千件以上）                  |
| 実行履歴         | 詳細保持                                 | デフォルトは保持しない（ログ出力可能）|
| 課金方式         | ステート遷移回数ベース                  | 実行回数＋処理時間ベース              |
| 信頼性           | 少なくとも1回実行                        | 少なくとも1回実行                     |
| 代表ユースケース | 承認フロー、長時間バッチ処理             | IoTイベント処理、リアルタイムAPI       |


# CloudWatch

## CloudWatch Contributor Insights

ログデータやメトリクスストリームをリアルタイムに分析して「誰が一番寄与しているか（contributor）」を把握できるサービス。異常検知・パフォーマンス解析・トラブルシューティングに役立つ。

## 基本モニタリングと詳細モニタリング

| 項目 | 基本モニタリング | 詳細モニタリング |
|------|------------------|------------------|
| メトリクス収集間隔 | 5分 | 1分 |
| 料金 | 無料 | 有料 (メトリクス課金) |
| デフォルト設定 | 有効（多くのサービスで） | 無効（オプションで有効化） |
| 適用リソース | EC2, EBS, RDS など | 主に EC2（他一部サービスも対応） |
| ユースケース | コスト重視、粗い監視で十分 | スパイク検知、オートスケーリング精度向上、厳密な監視 |

# AWS Resource Access Manager

AWSアカウントや組織内の複数のアカウント間でAWSリソースを安全に共有できるサービス。

## 📋 AWS RAM 共有可能リソース一覧（代表例）

| カテゴリ | 共有可能リソース | 説明 |
|----------|------------------|------|
| **ネットワーク** | **VPC サブネット** | 他アカウントにサブネットを共有し、同じVPC上でリソースを稼働できる |
| | **Transit Gateway** | 複数VPC・オンプレ環境をつなぐTransit Gatewayを共有 |
| | **Route 53 Resolver ルール** | DNS解決ルールを他アカウントに共有 |
| | **IPAMプール** | VPC向けのIPアドレス管理プールを共有 |
| | **AWS Network Firewall ポリシー** | Firewallポリシーを複数アカウントで利用 |
| | **VPC Lattice** | サービスネットワークをアカウント間で共有 |
| **セキュリティ/アクセス管理** | **License Manager ライセンス構成** | ソフトウェアライセンスの使用制御を共有 |
| | **Resource Access Manager 自体のリソース共有設定** | RAMによるリソース共有設定を委譲可能 |
| **その他** | **Aurora クラスター (一部機能)** | DBクロスアカウント接続用のリソース共有が可能 |
| | **Outposts リソース** | AWS Outposts のサブネットなどを共有可能 |
| | **Cloud WAN** | グローバルネットワーク構成を共有 |
| | **容量予約 (Capacity Reservation)** | EC2のオンデマンド容量予約を共有 |

# S3

## SubmitMultiRegionAccessPointRoutes

Amazon S3 Multi-Region Access Points（MRAP）用の APIのひとつ。MRAP では、クライアントからのリクエストをどのリージョンの S3 バケットにルーティングするかを制御できる。この API を使って、ルート構成（ルーティングテーブル）を AWS に反映させることができる。

## 整合性チェック

1. ETag（MD5ハッシュ）
2. Content-MD5ヘッダー
3. Checksum(`x-amz-checksum-*`ヘッダー）

# GuardDuty / Inspector

## 役割まとめ

| サービス        | 主な目的       | 主な対象                           | 検出方法                                      | 利用シーン                                |
|-----------------|----------------|------------------------------------|-----------------------------------------------|-------------------------------------------|
| **GuardDuty**   | 脅威検知       | AWSアカウント全体<br>(VPC Flow Logs, CloudTrail, DNS Logs) | 機械学習＋脅威インテリジェンスによるログ解析 | 不正アクセス・マルウェア感染の兆候検知     |
| **Inspector**   | 脆弱性管理     | EC2 / ECR / Lambda                 | SSM Agent やスキャンによる脆弱性診断          | 未修正CVEや設定不備を発見し修正に活用      |
| **Security Hub**| 統合管理・可視化| GuardDuty / Inspector / 他サービス | 各サービスの検出結果を集約・標準化            | セキュリティ状態の全体把握、監査・準拠対応 |

# Lambda

## 予約済み同時実行 vs プロビジョンドコンカレンシー

| 項目 | 予約済み同時実行 (Reserved Concurrency) | プロビジョンドコンカレンシー (Provisioned Concurrency) |
|------|----------------------------------------|------------------------------------------------|
| 主な目的 | 関数ごとの同時実行枠を**確保・制御**する | コールドスタートを防ぎ、**常に温めた環境**を維持する |
| 効果 | - 他関数にリソースを奪われない<br>- 同時実行数の上限制御が可能 | - 初回リクエストでも即応答<br>- レイテンシを安定化 |
| 動作の仕組み | アカウント全体の同時実行クォータから**専有枠**を割り当てる | Lambda 実行環境を事前に起動し、リクエストに即対応できる状態で維持 |
| コールドスタート | 解消できない | 解消できる |
| スロットル | 設定値がその関数の**最大同時実行数**になる | プロビジョンド数を超えた分は通常の実行環境に回る（コールドスタートあり） |
| 課金 | 実行されたリクエスト分のみ（通常課金） | プロビジョニングした環境の維持に**追加料金**が発生 |
| ユースケース | - 重要関数の枠を保証したい<br>- 他関数にリソースを奪われたくない<br>- 意図的にスロットルしたい | - API のレイテンシを一定にしたい<br>- 常時低遅延が求められるサービス（例：Web API、チャット、ゲームなど） |

# EventBridge

## 入力トランスフォーマー

イベントルールでキャッチしたイベントデータを、そのままターゲットに送るのではなく、必要な形に変換してからターゲットに渡すための機能。

# EC2

## EC2 配分戦略まとめ（メリット・デメリット付き）

| 対象 | 戦略名 | 特徴 | 主な用途 | メリット | デメリット |
|------|--------|------|----------|-----------|-------------|
| **オンデマンド** | lowest-price | 最も安いインスタンスタイプから順に起動する | コスト最適化を重視 | コストを抑えやすい | 特定のインスタンスタイプを選びにくい |
| **オンデマンド** | prioritized | 管理者が設定した優先順位に従って起動する | 特定のインスタンスタイプを第一候補にしたい場合 | 利用者の意図通りに起動できる | 優先度の低いインスタンスタイプがほぼ使われない可能性 |
| **スポット** | lowest-price | 最も安いスポットプール（インスタンスタイプ＋AZ）から起動する | コスト最小化を重視 | 最安値で利用可能 | 特定プールに集中しやすく、中断リスクが高い |
| **スポット** | capacity-optimized | キャパシティに余裕があるスポットプールを優先 | 安定性を重視（中断リスク低減） | 中断されにくい | コストが最安ではない場合がある |
| **スポット** | capacity-optimized-prioritized | capacity-optimized を基本にしつつ、管理者が優先順位を設定可能 | 安定性を確保しつつ特定インスタンスタイプを優先 | 安定性＋制御性の両立 | 優先度が低いインスタンスタイプは使われにくい |
| **スポット** | price-capacity-optimized | 価格とキャパシティを両方考慮してバランスよく選択 | コストと安定性のバランスを取りたい場合 | コストと安定性を両立 | 戦略が複雑で動作を予測しにくい |
