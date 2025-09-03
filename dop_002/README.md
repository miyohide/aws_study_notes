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

# CloudFormationにてオブジェクトを含むバケットを削除する方法

カスタムリソース利用して削除がトリガーされる前にバケットを空にする。

# CloudFormationのカスタムリソース

CloudFormationが対応していないリソースの作成などにおいて、CloudFormationのテンプレートにカスタムのプロビジョニングロジックを記述することで、削除・更新・作成をサポートする仕組み。`AWS::CloudFormation::CustomResource`または`Custom::MyCustomResourceTypeName`リソースタイプを使用する。

# CloudFormationにてOpsWorksを呼び出す

リソースタイプに`AWS::OpsWorks::Stack`を指定する。

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

# CloudFormation UpdatePolicy

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

# CloudFormationでLambdaのバージョンアップ

CloudFormationでLambdaをデプロイしたあと、バージョンアップをやる場合は、以下の点で設定する。

- バージョン
    - 既存のS3バケットでバージョニングを有効にする。その後、CloudFormationにてs3ObjectVersionプロパティにD3オブジェクトのバージョンIDを指定する
- コードの場所
    - バージョンアップ時には別のバケットの場所を指すようにする。
- オブジェクト名
    - CloudFormationにてS3Keyプロパティを更新して、別の場所とzipファイルの名前を示すようにする

# CloudFormationでDeletinPolicyでSnapshotをサポートするリソース

- AWS::EC2::Volume
- AWS::ElastiCache::CacheCluster
- AWS::ElastiCache::ReplicationGroup
- AWS::Neptune::DBCluster
- AWS::RDS::DBCluster
- AWS::RDS::DBInstance
- AWS::Redshift::Cluster

# Amazon SQS

## Amazon SQSで利用可能なCloudWatchメトリクス

| メトリクス名 | 説明 |
| ---------- | ---- |
| ApproximateNumberOfMessagesVisible |　すぐに受信可能な（キューに残っている）メッセージ数。キューの滞留状況を把握するのに重要 |
| ApproximateNumberOfMessagesNotVisible | 処理中（まだ削除されていない）メッセージの数 |
| ApproximateNumberOfMessagesDelayed | DelaySeconds により遅延中のメッセージ数 |
| ApproximateNumberOfMessages | SQSキュー属性の一つ。Visible、Delayed、NotVisibleをまとめた情報をAPIで取得可能 |

# Organizations

## 委任管理者アカウント

管理アカウント (Management Account) が持つ管理権限の一部を、特定のサービスについて他のメンバーアカウントに委任できる仕組み。

管理アカウントに運用権限を集中させすぎるとセキュリティリスクや運用負荷が高まるため、特定のサービスの管理を別のアカウントに任せられる仕組みが「委任管理者」。AWS ConfigやAWS Security Hub、AWS Service Catalogなどで活用。

# AWS Config

## AWS Config アグリゲーター

複数のアカウントやリージョンに分散している AWS Config の設定データやコンプライアンス情報を一元的に集約 する仕組み。

# AWS Security Hub

## 信頼されたアクセス

OrganizationsとSecurity Hubを統合するための仕組み。有効化することでOrganizations経由でSecurity Hubを有効化しセキュリティ検出結果（Findings）を委任管理者アカウントに集約可能。
