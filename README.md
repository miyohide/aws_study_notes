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
