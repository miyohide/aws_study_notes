# これは何か

AWS Certified Solutions Architect - Professionalを受験するに伴い、学んだことをまとめたお勉強メモです。

# [Amazon Kinesis](https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-2017-amazon-kinesis)

ストリームデータを収集・処理するためのフルマネージドサービス群。以下のものがある。

- Amazon Kinesis Video Streams
    - AWSへ動画をビデオストリーミングする。
- Amazon Kinesis Data Streams
    - ストリームデータを処理するためのアプリを独自に構築
    - ストリーミングデータを処理するためのKinesisシャードが不十分な場合にはシャードを分割するか、インスタンスサイズの増強を実施する。
- Amazon Kinesis Data Firehose
    - ストリームデータをS3やRedshift、Elasticserarch Serviceなどへ配信
- Amazon Kinesis Data Analytics
    - ストリームデータをSQLクエリやApache Flinkでリアルタイムに分析

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
- Amazon EMR
    - Apache Hadoop/Sparkなどの分析やデータ加工処理用のOSSをマネージドサービスとして提供
- Amazon Redshift
    - マネージドデータウェアハウスサービス
- Amazon SageMaker
    - S3に蓄積したデータを使って、継続的に推論モデルを作成することができる
- Amazon Athena
    - S3に築成したデータについて、直接SQLクエリでの分析ができる
- Amazon QuickSight
    - さまざまなデータソースのデータをグラフなどで可視化できるBIサービス
- AWS Lake Formation
    - S3とGlueやAthenaを使用したデータレイクを素早く構築することができる
