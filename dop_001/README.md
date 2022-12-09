# これは何か

AWS Certified DevOps Engineer - Professional(DOP-C01)取得のための個人的勉強用メモです。

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
