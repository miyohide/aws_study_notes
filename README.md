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
