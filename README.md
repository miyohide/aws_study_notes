# これは何か

AWS Certified Solutions Architect - Professionalを受験するに伴い、学んだことをまとめたお勉強メモです。

# [Amazon Kinesis](https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-2017-amazon-kinesis)

ストリームデータを収集・処理するためのフルマネージドサービス群。以下のものがある。

- Amazon Kinesis Video Streams
    - AWSへ動画をビデオストリーミングする。
- Amazon Kinesis Data Streams
    - ストリームデータを収集して順番通りにリアルタイム処理を実現するときに使用するサービス。
    - ストリーミングデータを処理するために、シャードを使う。シャードは読み取りに対して最大5つのトランザクションをサポート。最大層データ読み取りレートは2MB/秒、書き込みは最大1,000レコード秒、最大1MB/秒。
    - シャードが不十分な場合にはシャードを分割するか、インスタンスサイズの増強を実施する。
- Amazon Kinesis Data Firehose
    - ストリームデータをS3やRedshift、Elasticserarch Serviceなどへ配信
- Amazon Kinesis Data Analytics
    - Kinesis Data Streams, Kinesis Data FirehoseのストリーミングデータをSQLクエリーやApache Flinkなどを使用して分析できる。

Kinesisにデータを送信する側をプロデューサー、Kinesisのデータを受信して処理する側をコンシューマーと呼ぶ。

プロデューサー側の開発にはKinesis Producer Library（KPL）を、コンシューマー側の開発にはKinesis Client Library（KCL）を使用することができる。

# [Amazon Application Migration Service（AWS MGN）](https://aws.amazon.com/jp/application-migration-service/)

ソースサーバーを物理インフラ、仮想インフラ、クラウドインフラからAWSでネイティブに実行するように自動的に変換してアプリをクラウドに移行することを支援するツール。

# [Amazon Simple Storage Service（S3）](https://www.slideshare.net/AmazonWebServicesJapan/20190220-aws-black-belt-online-seminar-amazon-s3-glacier)

- ユーザーがシンプルに使うことができる、インターネット対応のストレージサービス。

S3の特徴。

- オブジェクトストレージ
    - S3バケットに格納されているデータをそのまま編集することはできない。
    - 更新する場合は上書き更新する必要がある。
    - 頻繁に更新するデータを扱うケースには向いていない
- インターネット対応
    - HTTP／HTTPSプロトコルでAPIリクエストを実行することでオブジェクトをアップロードしたりダウンロードしたりする。
- 無制限にデータを保存
- 柔軟なセキュリティ設定

データレイクのサービスのデータ元としてS3が利用される。データレイクのサービスは以下のものがある。

- AWS Glue
    - データ加工、データカタログの作成を行う
- [Amazon EMR](https://dev.classmethod.jp/articles/cm-advent-calendar-2015-getting-started-again-aws-emr/)
    - Amazon Elastic MapReduceの略。Apache Hadoop/Sparkなどの分析やデータ加工処理用のOSSをマネージドサービスとして提供
- Amazon Redshift
    - マネージドデータウェアハウスサービス
    - シングルAZ配置のみをサポート
- Amazon SageMaker
    - S3に蓄積したデータを使って、継続的に推論モデルを作成することができる
- Amazon Athena
    - S3に築成したデータについて、直接SQLクエリでの分析ができる
- Amazon QuickSight
    - さまざまなデータソースのデータをグラフなどで可視化できるBIサービス
- AWS Lake Formation
    - S3とGlueやAthenaを使用したデータレイクを素早く構築することができる

## [S3のストレージクラス](https://dev.classmethod.jp/articles/should_i_choice_s3_storage_class/)

- Standard
    - 標準。
- Standard-IA
    - 存続時間が長く、あまり頻繁にアクセスされないデータ向け
- Intelligent-Tiering
    - 存続時間が長く、アクセスパターンが不明なデータ向け
- One Zone-IA
    - 重要度が低い、存続時間が長く、あまり頻繁にアクセスされないデータ向け。
- Glacier
    - 長期アーカイブデータ向け。取得時間は数分から数時間
- Glacier Deep Archive
    - 長期アーカイブデータ向け。取得時間は12時間

# [VPCとサブネットでIPv6を有効化する](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/get-started-ipv6.html)

1. IPv6 CIDRブロックをVPCとサブネットに紐づける
2. ルートテーブルを更新する
    - パブリックサブネットの場合はインターネットゲートウェイを設定する
    - プライベートサブネットの場合はEgress-Onlyインターネットゲートウェイを設定する
3. セキュリティグループ、ネットワークACLを設定する
4. IPv6アドレスをEC2インスタンスに割り当てる

# [Systems Manager Automation](https://aws.amazon.com/jp/blogs/news/webinar-bb-aws-systems-manager-2020/)

EC2および他のAWSリソースの一般的なメンテナンスとデプロイのタスクを簡素化。

実施できる主なタスク

- Amazon EventBridgeを使用して自動化タスクおよびワークフローに関する通知を受信
- EC2またはSystems Managerコンソールを使用して自動化の進捗状況および実行の詳細を監視
- Systems Managerオートメーションドキュメントで、マネージドインスタンスやAWSリソースで実行するアクション（オートメーションワークフロー）を定義する

# [AWS Config](https://aws.amazon.com/jp/blogs/news/webinar-bb-aws-config-2019/)

AWSリソースの設定を評価、監査、審査できるサービス。設定が継続的にモニタリングおよび記録され、望まれる設定に対する記録された設定の評価を自動的に実行する。

# [EC2プレイスメントグループ](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/placement-groups.html)

ネットワークパフォーマンスの向上や物理サーバー障害児の影響範囲を限定させるためにインスタンスをグループ化するもの。

- クラスタープレイスメントグループ
    - HPCのような枠ロード向け
    - インスタンス群を高バイセクションバンド幅セグメントに配置
    - インスタンスタイプに制限あり。
- パーティションプレイスメントグループ
    - HDFSやKafkaのような大規模な分散システム向け
    - 各パーティションにはラックが対応
- スプレッドプレイスメントグループ
    - 物理ラックの単位でインスタンスを配置

# [Amazon Systems Managerパラメータストア](https://www.slideshare.net/AmazonWebServicesJapan/20200212-aws-black-belt-online-seminar-aws-systems-manager)

パラメータストアにパラメータを書き込み、GetParameterで取得して使用する。暗号化のタイプはSecureString。

# AWS Secrets Manager

シークレット情報のローテーションが必要なケースなどで使う。設定された周期でLambda関数を自動実行し、データベースのパスワードを変更してSecrets Managerのシークレット情報を更新する。

Systems Managerパラメータストアと同じような機能だが、Secrets Managerシークレットの方が性能上限が高い。ただし有料。

# AWS Certificate Manager

独自ドメインのアプリでの証明書の管理、設定の機能を有する。ドメイン所有者の確認はメール認証かCNAME認証で行う。

使い方のサンプルは[AWS Certificate Managerを利用してSSL/TLS認証書をリクエストしてCloudFrontに統合してみた。](https://dev.classmethod.jp/articles/lim-aws-certificate-manager/)を参照。

# [Amazon DynamoDB](https://www.slideshare.net/AmazonWebServicesJapan/20170809-black-belt-dynamodb)

フルマネージドなNoSQLデータベースサービス。リージョンを選択してテーブルを作成する。パーティション（データの保存先）を分散させることで水平スケーリングを可能とする。

データを一意として扱うために、作成時にプライマリキーを決定しておく必要がある。プライマリキーは以下2つのパターンがある。

- パーティションキーのみ
- パーティションキーとソートキー

1つのデータのことを項目と言い、複数の属性を持つ。パーティションキー、ソートキー以外の属性は項目ごとにあってもなくても構わない。パーティションキー、ソートキーの属性は必須で、テーブル作成時にデータ型を決めておく必要がある。データ型は文字列、数値、バイナリから選択。他の属性ではブール値、マップ、リストなどからも設定できる。

## LSIとGSIについて

- Local Secondary Index（LSI）
    - ソートキー以外に絞り込み検索を行うキーを持つことができる
    - パーティションキーが同一で、他のアイテムからの検索のために利用
    - テーブル作成時に作っておく必要あり。あとから追加できない。
    - 作成時にプライマリーキーで使用したパーティションキーと合わせて別にソートキーを指定する
- Global Secondary Index（GSI）
    - パーティションキー属性の代わりとなる
    - パーティションキーを跨いで検索を行うためのインデックス
    - あとからでも作成することが可能
    - 作成時にプライマリーキーで使用したパーティションキーとは別のキーを指定する

# S3オブジェクトの暗号化の種類

[Amazon S3の暗号化について調べてみた。](https://dev.classmethod.jp/articles/lim-s3-sse-2021/)が詳しい。

- クライアントサイド暗号化
    - CSE-KMS
        - クライアントで暗号化してからアップロードする方法。KMSで管理しているCMKを使用して暗号化する。
    - CSE-C
        - オンプレミスキーサーバーなどを作成されたキーを使用して暗号化する。
- サーバーサイド暗号化
    - SSE-S3
        - S3が管理するキーによるサーバー側暗号化を行う。
        - `"x-amz-server-side-encryption": "AES256"`ヘッダーを設定して利用する
    - SSE-KMS
        - KMSが管理しているキーを使ったサーバー側暗号化。
        - `"x-amz-server-side-encryption": "aws:kms"`ヘッダーを設定して利用する
    - SSE-C
        - ユーザー指定キーによるサーバー側暗号化。

# [AWS Database Migration Service](https://aws.amazon.com/jp/blogs/news/webinar-bb-awsdatabasemigrationservice-2021/)

データベースのデータ連携を支援するサービス。異機種間のデータ連携も対応。テーブル定義を補助するツールも提供。

テーブルマッピング機能を利用して、データソース、ソーススキーマ、データ、タスク実行中に必要な全ての変換を指定する複数のルールタイプを設定することが可能。

また、AWS Database Migration Serviceコンソールから移行タスクを定義して実行する詳細設定も可能。

テーブルマッピングをJSON形式で作成されるためそのファイルを利用して移行を実行することも可能。

# AWS Snowファミリー

| 項目 | Snowcone | Snowball edge storage optimized | Snowball edge compute optimized | Snowmobile |
| ---- | ---- | ---- | ---- | ---- |
| HDDストレージ | 8TB | 80TB | 42TB | 100PB |
| SSDストレージ | 14TB | 1TB | 7.68TB | なし |


# AWS CloudFormation

デフォルトでは、スタックの削除時にリソースとそのすべてのコンテンツを削除する。DeletionPolicyを指定した場合、その指示に従う。DeletionPolicyの値は以下の通り。

- Retain
    - スタックを削除する際に、リソースやコンテンツを削除せずに保持する。これはあらゆるリソースタイプに追加することができる。
- Snapshot
    - スタックを削除する際にリソースのスナップショットを作成する。スナップショットをサポートするのは以下のリソース。
        - AWS::EC2::Volume
        - AWS::ElastiCache::CacheCluster
        - AWS::ElastiCache::ReplicationGroup
        - AWS::Neptune::DBCluster
        - AWS::RDS::DBCluster
        - AWS::RDS::DBInstance
        - AWS::Redshift::Cluster

# 移行パス。7つのR

| 名称 | 概要 |
| ---- | ---- |
| リロケート | 既存オンプレミスのアーキテクチャそのままをAWSに移行（VMWare Cloud on AWSなどの利用を想定） |
| リホスト | 既存オンプレミスのアーキテクチャそのままをAWSに移行 |
| リプラットフォーム | OSやミドルのバージョンアップやRDBMSエンジンの変更、RDSの採用など |
| リファクタリング | AZやAutoScalingなどのクラウドならではの機能を取り入れた構成への変更やLambdaなどのサーバーレスを取り入れたクラウド最適化 |
| リバーチェス | SaaSやパッケージの適用 |
| リテイン | クラウド移行せず残置 |
| リタイア | システムの統廃合による廃止 |
