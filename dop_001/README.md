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
