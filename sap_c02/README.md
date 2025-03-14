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
- Amazon Managed Service for Apache Flink（旧Amazon Kinesis Data Analytics）
    - Kinesis Data Streams, Kinesis Data FirehoseのストリーミングデータをSQLクエリーやApache Flinkなどを使用して分析できる。

Kinesisにデータを送信する側をプロデューサー、Kinesisのデータを受信して処理する側をコンシューマーと呼ぶ。

プロデューサー側の開発にはKinesis Producer Library（KPL）を、コンシューマー側の開発にはKinesis Client Library（KCL）を使用することができる。

| サービス | **Kinesis Data Streams** | **Kinesis Data Firehose** | **Kinesis Data Analytics** |
|----------|------------------------|--------------------------|--------------------------|
| **目的** | データのリアルタイム収集・処理 | データの蓄積・転送 | データのリアルタイム分析・変換 |
| **データ処理** | **アプリがデータを取得・処理** | **AWSが自動的に転送** | **SQL/Flinkでデータを処理** |
| **保持期間** | 最大 **365日** | **なし（即時転送）** | **なし（即時処理）** |
| **データ処理の柔軟性** | **カスタム処理（アプリ側で実装）** | **シンプルな変換（Lambda経由）** | **SQL/Flinkでストリーミング処理** |
| **ユースケース** | - **リアルタイムアプリ**（チャット、金融取引）<br>- **ログ/メトリクス収集**<br>- **ML向けデータストリーム** | - **ログやイベントのS3転送**<br>- **ETL前処理**<br>- **バッチデータ保存** | - **異常検知**<br>- **リアルタイム集計**<br>- **データフィルタリング** |

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
    - 抽出、変換、ロード（ETL）を行う完全マネージド型のサービス。分析用データの準備とロードを簡単にする。
- [Amazon EMR](https://dev.classmethod.jp/articles/cm-advent-calendar-2015-getting-started-again-aws-emr/)
    - Amazon Elastic MapReduceの略。Apache Hadoop/Sparkなどの分析やデータ加工処理用のOSSをマネージドサービスとして提供
    - EC2インスタンスでクラスターを生成する。クラスターには以下の３種類がある。タスクノードはオプション扱いのため、スポットインスタンスで割り当て、そのほかはオンデマンドインスタンスで割り当てる。
        - マスターノード
        - コアノード
        - タスクノード
- Amazon Redshift
    - マネージドデータウェアハウスサービス
    - シングルAZ配置のみをサポート
    - 平均パフォーマンスやデータ保全性を損なう恐れがあるため、Redshiftにスポットインスタンスを使用することはできない
- Amazon SageMaker
    - S3に蓄積したデータを使って、継続的に推論モデルを作成することができる
- Amazon Athena
    - S3に蓄積したデータについて、直接SQLクエリでの分析ができる
    - RedshiftやEMRとの大きな違いとして、AthenaはクラスタノードやEC2インスタンスを持たない。
    - S3上のデータをスキャンした量に応じて課金される
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

## Amazon S3 Transfer Acceleration

アップロードのパフォーマンスの向上に役立つ。クライアントとS3バケットの間で長距離にわたるファイル転送を高速、簡単、安全に行えるようになる。CloudFrontの世界中に分散したエッジロケーションを利用。

## S3 Replication Time Control（S3 RTC）

S3のレプリケーション機能の一つ。レプリケーション時間のSLAを提供する。通常はオブジェクトのコピーが非同期で行われどのくらいの時間で完了するかは保証されていないが、S3 Replication Time Controlは99.99％の確率で15分以内にレプリケーションを完了することを保証する。

see. https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/userguide/replication-time-control.html

## S3 Access Point

S3バケット内のデータへのアクセスを簡単にかつ安全に管理するための機能。複数のアプリやユーザーが異なるアクセス権限ポリシーを持つ場合に便利。

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

継続的なモニタリングは提供されるが、スキャンは提供されない。

# [EC2プレイスメントグループ](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/placement-groups.html)

ネットワークパフォーマンスの向上や物理サーバー障害時の影響範囲を限定させるためにインスタンスをグループ化するもの。

- クラスタープレイスメントグループ
    - HPCのようなワークロード向け
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

リージョンごとに証明書をリクエストあるいはインポートする必要がある。つまり、各リージョンごとに証明書を取得する必要がある。

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

パーティションキーを変えずに検索したい場合は、LSIを、パーティションキーを変えて検索したいならGSIを選択する。

| 項目 | LSI（ローカルセカンダリインデックス） | GSI（グローバルセカンダリインデックス） |
|------|---------------------------------|---------------------------------|
| **パーティションキー** | **元のテーブルと同じ** | **変更可能（異なるPKも可）** |
| **ソートキー** | 変更可能 | 変更可能 |
| **作成タイミング** | **テーブル作成時のみ** | **後から追加OK** |
| **保存場所** | **元のテーブルと同じパーティション** | **独立したパーティション** |
| **データサイズ制限** | 1パーティションキーあたり **最大10GB** | **制限なし** |
| **一貫性** | **強い整合性（Strongly Consistent Read）可** | **結果整合性（Eventually Consistent）** |
| **書き込みコスト** | **テーブルと同じ** | **追加の書き込みコストが発生** |
| **読み取り速度** | **パフォーマンスが良い**（同じパーティション内） | **検索対象が広いため遅くなる可能性あり** |
| **ユースケース** | - 既存のパーティションキーを維持したまま、異なるソートキーで検索<br>- ユーザーごとの時系列データの検索 | - パーティションキー自体を変更したい<br>- 全体データを異なるキーで検索（国別ランキング、注文ID検索） |

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
        - 以下の3つのHTTPリクエストヘッダーを全て含める必要がある
            - x-amz-server-side-encryption-customer-algorithm
            - x-amz-server-side-encryption-customer-key
            - x-amz-server-side-encryption-customer-key-MD5

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

データ転送の高速化のためには以下の方法がある。

1. 一度に複数の書き込み操作を実行する
2. 小容量ファイルのバッチ転送
3. 転送中にファイル上で他の操作を実行しない
4. ローカルネットワークの使用を減らす
5. 不要なホップの削除

また、ファイルインターフェイスを利用する以外にS3インターフェイスを利用することで高速にファイル転送が可能（ファイルインターフェイスが25MB/s〜40MB/sのところ、S3インターフェイスは250MB/s〜400MB/s）。


# [AWS CloudFormation](https://pages.awscloud.com/rs/112-TZM-766/images/20200826_AWS-BlackBelt_AWS-CloudFormation.pdf)

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

サポートしていないリソースをプロビジョニングするためにはカスタムリソース機能を使う。スタックを作成、更新、削除するたびにCloudFormationがそれを実行する。SNSトピックにサブスクライブするか、Lambdaにロジックを実装することでリソースの作成が可能。

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

# インスタンスストア

[インスタンスストアとEBSの違いを教えてください](https://aws.amazon.com/jp/premiumsupport/knowledge-center/instance-store-vs-ebs/)が詳しい。

インスタンスストアはスナップショットやAMIなどによってバックアップを取得することができないため、バックアップの方法としては以下の法皇を取る。

- 新しいEBSボリュームを作成して、インスタンスストアボリュームのデータをそのEBSボリュームにコピーする
- EBSボリュームに保存されている個々のファイルをバックアップする

# [Amazon Elastic File System](https://www.slideshare.net/AmazonWebServicesJapan/2018070420190520-renewed-aws-black-belt-online-seminar-amazon-elastic-file-system-amazon-efs)

- NFSアクセスを提供する分散ストレージ。
- 複数のEC2からNFSを使ってネットワーク経由でアクセス
- `amazon-efs-utils`パッケージを使用することでマウントヘルパーを使って簡単にマウントコマンドを実行することが可能。
    - 暗号化オプション「`-o tls`」を追加することで暗号化可能。

# [API Gateway](https://www.slideshare.net/AmazonWebServicesJapan/20190514-aws-black-belt-online-seminar-amazon-api-gateway-230113575)

REST、HTTP、WebSocket APIを作成、公開、維持、モニタリング、及びセキュア化するためのAWSサービス。

以下の機能を有する。

- 認証
    - IAM
        - Great fro users / roles already within your AWS account, + resource policy for cross account
        - Leverages signature v4
    - Custome Authorizer
        - Great for 3rd party tokens
        - use Lambda
    - Cognito User Pool
        - manage your own user pool
        - must implement authorization in the backend
- SSL証明書の設定
- リソースポリシー（API実行の許可／拒否）
- メソッド許可
    - APIに対してリクエストを実行できるユーザーを制御できる。IAM認証やCognitoオーソライザーやLambdaオーソライザー
- Lambdaオーソライザー
    - Lambda関数を使用してAPIへのアクセスを制御する機能。以下の二種類がある。
    - トークンベースのLambdaオーソライザー
        - JWTやOAuthトークンなどのトークンで発信者IDを受け取る
    - リクエストパラメータベースのLambdaオーソライザー
        - ヘッダー、クエリ文字列パラメータ、stageVariables、$context変数の組み合わせで発信者IDを受け取る
- スロットリング
    - APIの実行数はデフォルトでリージョンごとに10,000回/秒まで。
- 使用量プラン
    - リクエスト数に応じた課金請求や顧客ごとに制御回数を設けたいときに使用
- APIキーの管理
- メッセージの変換
- マッピングテンプレート
- GETリクエストのキャッシュ
- ログの記録
- APIのインポート、エクスポート
- ステージ変数
    - ステージごとに異なる値を設定でき、`${stageVariables.xxxx}`で利用することができる。
    - [[API Gateway + Lambda]ステージとエイリアスを使ってバージョン管理してみた](https://dev.classmethod.jp/articles/version-management-with-api-gateway-and-lambda/)を参照
- Cross-origin resource sharing(CORS)の有効化
    - ブラウザで実行sれているスクリプトから開始されるクロスオリジンHTTPリクエストを制限するブラウザのセキュリティ機能。
    - REST APIのリソースが非シンプルクロスオリジンのHTTPリクエストを受け取る場合に有効にする必要がある。

API GatewayはHTTPエンドポイントをサポートしていない。

統合タイプは以下のものがある

- AWS
    - AWSのサービスアクションを公開する
    - 統合リクエスト・統合レスポンスを設定
- AWS_PROXY
    - 統合リクエスト・統合レスポンスの設定は不要
    - リクエストをLambda関数へ渡す
- HTTP
    - バックエンドのHTTPエンドポイントを公開
    - 統合リクエスト・統合レスポンスを設定
- HTTP_PROXY
    - 統合リクエスト・統合レスポンスの設定は不要
    - リクエストをHTTPエンドポイントに渡し、レスポンスをクライアントへ返す
- MOCK
    - リクエストをバックエンドに送信することなくレスポンスを返す

エンドポイント種類は以下の3つ。

- Edge-Optimized(default)
    - CloudFront Edgeを通してリクエストを受け付ける
- Regional
    - 同じリージョンからのクライアントに対して
- Private
    - VPC内

API Gatewayのタイムアウトの上限は29秒。引き上げできない。

# [Direct Connect](https://dev.classmethod.jp/articles/re-introduction-2020-direct-connect/)

- プライベート仮想インターフェイス
    - プライベートIPを使ってVPCにアクセスする
- パブリック仮想インターフェイス
    - パブリックIPアドレスを使用してすべてのAWSパブリックサービスにアクセスする
- トランジット仮想インターフェイス
    - Direct Connectゲートウェイに関連づけられた1つまたは複数のVPCトランジットゲートウェイにアクセスする

## Direct Connect Gateway

Direct Connectの構成のうち、仮想インターフェース（VIF）とVPCの仮装プライベートゲートウェイ（VGW）の間に追加するコンポーネントで、以下の特徴を持ち、VIFとVGWは同一リージョンに1対1で設定するという制限を緩和することができる。

- Direct Connect GatewayをいずれかのAWSリージョンに作成すると、AWSの全リージョンに複製されて相互接続できる
- 複数のCIFおよびVGWが接続できる。

詳細は[[新機能] AWS Direct Connect Gatewayで世界中のAWSリージョンとプライベート接続する](https://dev.classmethod.jp/articles/direct-connect-gateway/)を参照のこと。

## 回復性レベル

- 最大回復性
    - ロケーションごとに複数のCustomer RouterやDX Routerを配置し、ロケーションを複数個持つ。デバイスの障害、接続の障害、ロケーションの障害があっても回復できる。
- 高い回復性
    - ロケーションごとには1つのCustomer RouterやDX Routerを配置し、ロケーションを複数個持つ。デバイスの障害、接続の障害があった場合は回復できる。
- 開発とテスト
    - ロケーションごとに複数のCustomer RouterやDX Routerを配置し、ロケーションは1つ。デバイスの障害、接続の障害があっても回復できる。

## Link Aggregation Group（LAG）

複数のConnectionを集約し一つの論理インタフェースとして提供する。以下の条件がある。

- すべての接続は専用接続でなければならず、ポート速度は1Gbpsまたは10Gbps
- LAGのすべての接続では同じ帯域幅を使用する
- LAGでは最大４個の接続を行うことができる
- LAGのすべての接続は同じDirect Connectエンドポイントで終了する必要がある

## Direct Connect接続を介して暗号化された接続を確立する

see. https://repost.aws/ja/knowledge-center/create-vpn-direct-connect

デフォルトではDirect Connect接続は暗号化しない。MACsecを利用するか、AWS Site-to-Site VPNを作成するか。

# [Storage Gateway](https://pages.awscloud.com/rs/112-TZM-766/images/20170125_AWS-BlackBelt-StorageGateway_20170223Update.pdf)

| 種類 | サービス概要 |
| ---- | --------- |
| ファイルゲートウェイ | S3へのNFSやSMBなどのプロトコルによるIFを提供する。オンプレのサーバーから直接S3のファイルへアクセスできる |
| ボリュームゲートウェイ（Gateway-Stored Volumes） | オンプレのデータのスナップショットを非同期にS3にバックアップする。データ全体への低テンシーアクセスが必要な場合に選択。 |
| ボリュームゲートウェイ（Gateway-Cached Volumes） | データ全体をS3に保存し、頻繁にアクセスするもののコピーをオンプレ側に保存 |
| テープゲートウェイ | 物理テープライブラリの代替として使用する。バックアップ先はS3およびGlacier |

# DR構成パターン

| 種類 | 概要 | コスト | RTO | RPO |
| ---- | ---- | ---- | ---- | ---- |
| バックアップ＆リストア | バックアップデータはS3に保存し、DRサイト側にレプリ。DR時はDR側でS3のバックアップからシステムを復旧 | 低い | 長い | 最終バックアップ保存タイミングまで |
| パイロットライト | DR側でスペックの低いスタンバイDBを立てて通常時はデータ同期のみ。DR時はDR側でAPを起動とDBをスケールアップする。 | 少し低い | 少し長い | 最終データ同期タイミングまで |
| ウォームスタンバイ | DR側でスペックの低い構成を常時起動し、データをレプリケーション。DR時はサーバーをスケールアップし、DNSの名前解決先をDR側に切り替える | 少し高い | 少し短い | 最終データ同期タイミングまで |
| マルチサイト（ホットスタンバイ） | DR側で完全に同じスペックの構成を常時起動し、データをレプリケーション。DR時はDNSの名前解決先をDR側に切り替える | 高い | 短い | 最終データ同期タイミングまで |

# [VPCエンドポイント](https://docs.aws.amazon.com/ja_jp/vpc/latest/privatelink/vpc-endpoints.html)

VPCエンドポイントを使うことで、インターネットゲートウェイを経ずにAWSの各サービスのAPIに直接リクエストを実行できる。

ゲートウェイエンドポイントとインターフェースエンドポイントがある。

## ゲートウェイエンドポイント

以下の二つだけが対応。料金は発生しないが、オンプレミスや別リージョンからのゲートウェイエンドポイントへの直接的なアクセスはできない。

- S3
- Dynamo DB

## インターフェースエンドポイント

対象サービスはさまざま。S3にも適用可能。

# [EC2で自分のIPアドレスを使用する（BYOIP）](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ec2-byoip.html)

以下の作業をする。

1. RSAキーペアを作成し、自己証明X.509証明書を作成する
2. 自己証明書をRDAPレコードのコメントにアップロードする
3. RIRにROAオブジェクトを作成する
4. アドレス範囲をプロビジョニングする

# プレイスメントグループ

インスタンス群内での通信最適化を行うためにとられる設定。

- クラスタープレイスメントグループ
    - 単一のAZ内のインスタンスを論理的にグループ化したもの
    - 同じリージョン内の複数のVPCにまたがることができる
    - TCP/IPトラフィックのフローあたりのスループット上限が高くなって、ネットワークの帯域幅が上がる
- パーティションプレイスメントグループ
    - 障害に対応するためのプレイスメントグループ。グループをパーティションと呼ばれる論理的なセグメントに分割する。
- スプレッドプレイスメントグループ
    - それぞれ独自のネットワーク、電源がある異なるラックに1つ1つのインスタンスを分割する

# [Amazon Elastic Block Store](https://www.slideshare.net/AmazonWebServicesJapan/20190320-aws-black-belt-online-seminar-amazon-ebs/)

- EC2インスタンスにアタッチして使用するブロックレベルのストレージサービス
- アタッチするEC2インスタンスと同じAZに作成する
- EC2は複数のEBSを接続することは可能。EBSを複数のインスタンスで共有することはできない
    - 複数のEC2インスタンスにアタッチすることができるマルチアタッチ機能が提供されるようになった。
        - ボリュームタイプはプロビジョン度IOPS（io1）である必要があるなど、いくつかの制約がある
- ボリュームの暗号化は作成時に決定する。既存のボリュームの暗号化の有効/無効化は変更できない。
    - 暗号化されていないスナップショットからボリュームを作成するときは暗号化を有効化できる
- AMIはLinuxの場合、コミュニティAMIからカスタムAMIを作成することもできる。Windowsの場合は、`describe-platform-version`から返されたストックのElastic Beanstalk AMIが必要。

# [Amazon Elastic File System](https://www.slideshare.net/AmazonWebServicesJapan/2018070420190520-renewed-aws-black-belt-online-seminar-amazon-elastic-file-system-amazon-efs)

- NFSアクセスを提供する分散ストレージ。
- 複数のEC2からNFSを使ってネットワーク経由でアクセス
- `amazon-efs-utils`パッケージを使用することでマウントヘルパーを使って簡単にマウントコマンドを実行することが可能。
    - 暗号化オプション「`-o tls`」を追加することで暗号化可能。

# [Amazon Aurora Serverless](https://aws.amazon.com/jp/rds/aurora/serverless/)

AuroraのオンデマンドのAuto Scaling設定。アプリのニーズに応じて自動的に起動、シャットダウン、容量のスケールアップ／スケールダウンする。以下のユースケースがある。

- 使用頻度の低いアプリ
- 新規アプリ（必要なインスタンスサイズがわからない）
- 可変ワークロード
- 予測不可能なワークロード
- 開発およびテスト用データベース
- マルチテナントアプリ

# Amazon Aurora Global Database

単一のAmazon Auroraデータベースを複数のAWSリージョンにまたあgって運用できる。パフォーマンスに影響を与えずにデータをレプリケートし、各リージョンでレイテンシーを提言してローカル読み取りを高速化し、リージョン規模の停止からの災害復旧を実現する。

# CloudFrontのOrigin Access Identity（OAI）

S3を公開状態にすることなく、S3へのアクセスをCloudFrontからのリクエストに絞るための仕組み。CloudFrontに署名付きURLまたは署名付きCookieを作成してS3バケット内のファイルへのアクセスを制限してからOAIという特別なCloudFrontユーザーを作成してS3バケットポリシーに設定する。

参考：[CloudFormation で OAI を使った CloudFront + S3 の静的コンテンツ配信インフラを作る](https://dev.classmethod.jp/articles/s3-cloudfront-with-oai-by-cloudformation/)

S3バケットポリシーでPrincipalとしてOAIを指定するには以下のような記述をする。

```
"Principal": {
    "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity OAIのID"
}
```

# Elastic Beanstalk

## デプロイポリシーオプション

- All at once
    - 新しいバージョンを全てのインスタンスに同時に展開する
- Rolling
    - EC2インスタンスを複数のバッチに分割し、アプリの新しいバージョンを一度に1つのバッチにデプロイする。このことで、残りのインスタンスは古いアプリを実行した状態になる。
- Rolling with additional batch
    - 新しいバージョンをバッチで展開するが、最初にインスタンスの新しいバッチを起動して、展開プロセス中に完全な容量を確保する
- Immutable
    - 変更不可能な更新を実行して、古いバージョンを起動しているインスタンスと並行しながら別のAutoScallingグループにあるアプリの新しいバージョンを起動している新しいインスタンスのフルセットを起動する。

# [AWS SAM](https://www.slideshare.net/AmazonWebServicesJapan/20190814-aws-black-belt-online-seminar-aws-serverless-application-model-165314501)

CloudFormationの拡張機能。Lambda/API Gateway/DynamoDB/S3/Step Functionsなどを組み合わせたサーバーレスアプリの構築を自動化する。専用のCLIを使用する。

シンプルな例としては、以下の3ステップ。

1. `sam init`
    - サーバーレスアプリの初期処理。プロジェクトの初期ファイルと`template.yaml`を作成する
2. `sam build`
    - デプロイするための準備をする。ローカルでのテストも実行できるようになる。
3. `sam deploy`
    - AWSリソースを構築する。CloudFormationが実行される。

コマンドは他にも以下のものがある。

- sam validate
    - SAMテンプレートの検証
- sam local invoke
    - Lambda関数のローカル実行
- sam local generate-event
    - テスト用イベントの生成
- sam local start-lambda
    - ローカルエンドポイントの作成
- sam local start-api
    - APIエンドポイントの作成
- sam package
    - アプリをZip形式にパッケージングしS3にアップロードする
- sam logs
    - AWSにデプロイしたLambda関数のログを出力
- sam publish
    - 完成したアプリの保管

## テンプレートについて

CloudFormationテンプレートのAWS::Serverless TransformセクションにAWS SAMのバージョンを指定する。

```yaml
Transform: AWS::Serverless-2016-10-31

Globals:
  set of globals
Description:
  String
Metadata:
  template metadata
Parameters:
  set of parameters
Mappings:
  set of mappings
Conditions:
  set of conditions
Resources:
  set of resources
Outputs:
  set of outputs
```

Resources以下のTypeは`AWS::Serverless::Api`、`AWS::Serverless::Application`、`AWS::Serverless::Function`、`AWS::Serverless::SimpleTable`などがある。

# [Amazon Inspector](https://dev.classmethod.jp/articles/re-introduction-2020-inspector/)

EC2インスタンスにAmazon Inspectorエージェントをインストールして、ネットワーク到達性やプラットフォームの脆弱性を診断し、潜在的なセキュリティ上の問題を発見するもの。

# [AWS Trusted Advisor](https://pages.awscloud.com/rs/112-TZM-766/images/20180711_AWS-BlackBelt_TrustedAdvisor_public.pdf)

AWS環境を分析して、環境を最適化するための推奨ベストプラクティスを提供する。ベストプラクティスは5つのカテゴリに分類。

- コスト最適化
    - 使われていないリソースを検出する他、利用状況を分析して、コスト削減が期待できる割引プランをおすすめ
- パフォーマンス
    - パフォーマンス低下につながる使い方をしていないかどうかチェックする他、もっと効率が良いAWSの機能を使う余地がある場合にお知らせ
- セキュリティ
    - セキュリティリスクのある設定になっていないかどうかチェックする他、現在よりもセキュリティを高められる水晶の設定があればおすすめ
- フォールトトレンラス
    - 過去データから復旧できるようにバックアップを取得しているか、AWSの機能を活用して冗長構成を取れているかなどの点をチェックする
- サービス制限
    - 現在の利用状況と制限値を比較して差分をチェックする

全機能を利用するためには技術サポートプラン「ビジネス」もしくは「エンタープライズ」の契約が必要。

# AWS DataSync

AWSの各種ストレージサービスとのデータ転送を簡単に、自動で高速に行えるサービス。S3以外にもEFSやFSxなど他のストレージサービスへの移行がサポートされていたり、帯域制御の設定も可能。

# [DDoS対策](https://dev.classmethod.jp/articles/check-ddos-by-aws-shield/)

- AWS Shieldの利用
    - Standardでレイヤー3、レイヤー4のDDoSから自動で保護される。明示的な設定不要で無料。
    - AdvancedでEC2、ELB、CloudFront。Global Accelerator、Route 53に対するアプリレベルの検出と緩和策の提供、SRTによる支援。月額3000ドルの有償機能。
- CloudFrontやRoute 53等の世界中に分散配置されたエッジロケーションを活用する
- WAFを活用してアプリ固有の高度なHTTPリクエストを一定のレベルで止めたり、リアルタイムで固有のルールを調整する
    - WAFはCloudFrontとALBと統合。Auto ScalingやEC2との統合ではない。
- ALBやAutoScalingなどを組み合わせてリクエストを捌き切る
- CloudWatchメトリックスでDDoSを検知する

# [AWS Systems Manager](https://www.slideshare.net/AmazonWebServicesJapan/20200212-aws-black-belt-online-seminar-aws-systems-manager)

機能概要

- パラメータストア
    - 設定パラメータの集中管理用データストア
- Run Command
    - サーバ群の上でコマンドを実行する
- Session Manager
    - SSMを使ったサーバへリモートアクセスする
- Patch Manager
    - サーバ群に指定ルールに基づきパッチを適用する
- Maintenance Windows
    - 自動化処理のスケジュールと順序の管理

まずはマネージドインスタンスにすることが必要。マネージドインスタンスにするには

1. SSM Agentの導入
2. SSM APIへの経路確保（SSM Agentからのアウトバウンド経路を確保する）
3. IAMロール付与
    - 必須としてAmazonSSMManagedInstanceCore

# AWS Control Tower

Organizations機能やOrganizationsと連携できるサービスを用いてAWSのベストプラクティスに基づいたルールを設定し、各アカウントの統制を制御するサービス。マルチアカウント管理に向いている。

クラスメソッドさんの[「「AWS Control Towerを利用したマルチアカウント管理とセキュリティ統制」JAWS DAYS 2021登壇資料 #jawsug #jawsdays #jawsdays2021 #jawsdays2021_C」](https://dev.classmethod.jp/articles/jaws-days-2021-control-tower/)をみて理解を深める。

ランディングゾーンと呼ばれる、Well-ArchitectedによるマルチアカウントのAWS環境でセキュリティとコンプライアンスのベストプラクティス集。ランディングゾーンを設定すると、必須のガードレールが有効化される。強く推奨されるガードレールというものもあるが、これはデフォルトは無効化。


# AWS SWF（Simple Workflow Service）

視覚的ワークフローを使用して分散アプリやマイクロサービスのコンポーネントをコーディネートするサービス。よく似たものにStep Functionsがあるが、SWFは、以下の場合に使用する。

- プロセスにおいて介入する外部信号が必要な場合
- 結果を親に返す子プロセスを起動する場合
- Amazon Mechanical Turkを使う場合

# Route 53

レイテンシーベースのルーティングに基づいて地理的な場所にデータを送信するのに加えて、リージョン内のリソース間で加重ベースのルーティングを使用したい場合、①加重ベースのルーティングを作成する②レイテンシーベースのルーティングを設定するの順で作業をする。

# AWS Global Accelerator

グローバルロードバランサー。以下の機能がある。

- ジオロケーションに基づいたバランシング
- アプリケーションモニタリングに基づいたバランシング
- 重み付けに基づいたバランシング

また、一位のスタチックなAnycast IPアドレスを二つ割り当てることで、クライアント側には一切の変更なしでアクセス可能。

以下の特徴が挙げられる。

- 通信レイテンシーの改善
- 静的IPによるアプリへの単一エントリの提供
- アプリの保護

詳細はクラスメソッドさんのブログ記事[「AWS再入門ブログリレー2022 AWS Global Accelerator 編」](https://dev.classmethod.jp/articles/re-introduction-2022-globalaccelerator/)を参照。

# [Amazon Data Lifecycle Manager](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/snapshot-lifecycle.html)

EBSスナップショットとEBS-backed AMIの作成、保持、削除を自動化できる。リソースタグを使用してバックアップするEBSボリュームを識別する。

# EC2のインスタンスがimpaired

インスタンスが動いているホストマシンで何らかの問題が発生している状態をimpairedと呼ぶ。この時は、該当のインスタンスを停止・起動して、ホストを移動する必要がある。

# AWS Data Pipeline

ノード間でのデータ移動やETL処理を実行するマネージドサービス。一般的なスケジューラーの機能を持ち、オンプレの処理にも使える。データの変換と加工については、別途実装が必要。

スケジュールされたパイプラインに関連付けられる項目には3つのタイプが存在する。

- パイプラインコンポーネント
    - パイプラインのビジネスロジックを表し、パイプライン定義のさまざまなセクションによって表される。ワークフローのデータソース、アクティビティ、スケジュール、前提条件を指定する。
- インスタンス
    - Data Pipelineはパイプラインを実行する際、パイプラインコンポーネントをコンパイルして一連のアクション可能なインスタンスを作成する。
- 試行
    - 失敗したオペレーションを再試行する。

# AWS OpsWorks

## スタック

共通の目的を持ち、論理的に一緒に管理する必要がああるAWSリソースのグループ。

## レイヤー

特定の目的を果たす一連のEC2インスタンスを表す。

# CloudFrontエッジキャッシュから提供されるキャッシュヒット率の向上策

CloudFrontとApplication Load Balancer間のSSL/TLSハンドシェイクを失敗させることなく改善させるためには、以下のことを実施する。

- オリジンを静的コンテンツと動的コンテンツに分割させる
    - 動的コンテンツはCookieとAuthrozationヘッダーが必須
    - 静的コンテンツはCookieとAuthorzationヘッダーは不要
- CloudFrontとApplication Load Balancerが同じSSL証明書を利用しているため、Hostヘッダーを保持する必要がある。

# AppStream 2.0

フルマネージドなアプリケーションストリーミングサービス。WorkSpacesは仮想デスクトップという形でユーザーに仮想マシン全体を公開するが、AppStream 2.0は特定のアプリケーションのみを公開する。

# AppSync

GraphQL APIの開発を容易にするフルマネージド型サービス。マネージド型のGraphQLサブスクリプションによりAppSyncではWebsocketsを介してデータの更新をリアルタイムでプッシュできる。

# EC2 Dedicated Hosts

- ホストのアフェニティ設定
    - Hostの場合、特定のホストで起動したインスタンスが停止しても、常に同じホストで再開される
    - Offの場合、使用可能な任意のホスト上で再起動できる。最後に実行したDedicated Host上でベストエフォートベースでの再起動を試みる。
- 自動配置（複数のDedicated Hostsがある場合にホストと紐づけられていないインスタンスを自動的に配置するか）
    - 有効の場合、インスタイプ設定が一致するすべてのターゲット未指定のインスタンス軌道が受け入れられる
    - 無効の場合、一意のホストIDを指定するホストテナンシーインスタンス起動のみが受け入れられる。規定の設定。

# VPCのDHCPオプションセット

VPC内のEC2インスタンスが仮想ネットワーク経由で通信するために使用するネットワーク構成のグループ。DNSサーバやドメイン名等を指定することが可能。

変更することはできないので、変更したい場合は新しいDHCPオプションセットを作成してVPCに関連づける必要がある。

# VPCのCIDRを拡張する

セカンダリCIDRを追加することで、「一度作成したVPCのCIDRは変更できない」という制限を取り払う。セカンダリCIDRには以下の条件がある。

- CIDRのサイズは/28から/16の間
- すでに割り当てているCIDRと重複する範囲で割り当てはできない
- ピアリング接続があるVPCの場合、ピアリングしたVPCと重複したCIDRは割り当てできない
- プライマリCIDRの範囲によって割り当て可能なセカンダリCIDRの範囲は制限される

# Amazon Pinpoint

顧客とのコミュニケーションを管理するためのマルチチャネル（メール、SMS、プッシュ通知、ボイスメッセージ）・マーケティングサービス。ターゲットユーザーにカスタマイズしたメッセージを送ったりすることができる。

# AWS Lambda 予約された同時実行 vs. プロビジョニングされた同時実行

| 比較項目                     | 予約された同時実行 (Reserved Concurrency) | プロビジョニングされた同時実行 (Provisioned Concurrency) |
|-----------------------------|--------------------------------|----------------------------------|
| **概要**                     | 特定の関数に同時実行数を予約し、最低限のスロットを確保する | 実行環境を事前にウォームアップしておき、コールドスタートを排除 |
| **目的**                     | 他の関数に影響を与えず、一定の処理能力を確保 | 低遅延で即座にリクエストを処理する |
| **コールドスタートの回避**     | できない（通常のLambdaと同様に発生） | できる（事前に環境を立ち上げるため） |
| **スロットの予約**           | **あり**（設定した分だけは必ず確保） | **あり**（事前に環境を確保） |
| **バースト時のスケール**     | 予約した分を超えたリクエストはキューイングされるか拒否される | 予約した分を超えたリクエストは通常のLambdaと同じくスケール |
| **適用範囲**                 | **特定の関数のみ** | **特定の関数・特定のバージョンまたはエイリアスのみ** |
| **コスト**                   | **無料**（ただし予約した分は他の関数に割り当てられない） | **追加コスト発生**（プロビジョニング時間に対して料金が発生） |
| **用途**                     | 重要なLambda関数のリソースを確保し、他の関数の影響を受けないようにする | **リアルタイム処理**、低レイテンシーを要求されるアプリケーション（APIバックエンドなど） |
| **設定方法**                 | AWS マネジメントコンソール、CLI、API で設定 | AWS マネジメントコンソール、CLI、API で設定 |

# AWS Elastic Disaster Recovery

オンプレやクラウドのシステムをAWSに迅速に復旧できるディザスタリカバリサービス。

## AWS DRS と他のDRサービスの比較

| サービス | AWS DRS（Elastic Disaster Recovery） | AWS Backup | CloudEndure Disaster Recovery |
|----------|--------------------------------------|------------|-------------------------------|
| **目的** | システム全体のディザスタリカバリ | データのバックアップ | 高度なディザスタリカバリ |
| **レプリケーション方式** | **ブロックレベルの継続的レプリケーション** | スナップショットベース | ブロックレベルのレプリケーション |
| **RTO** | 数分 | 数時間～1日 | 数分 |
| **RPO** | 数秒 | 数時間～1日 | 数秒 |
| **コスト** | フェイルオーバー時のみ課金 | ストレージコストがかかる | フェイルオーバー時のみ課金 |
| **対応環境** | **オンプレミス / 他クラウド → AWS** | AWSのデータ保護 | オンプレミス / 他クラウド → AWS |

# AWS Firewall Manager

複数のAWSアカウントやリージョンにまたがるセキュリティルールを一元管理できるサービス。特に、組織全体で統一したセキュリティポリシーを適用したい場合に役立つ。

## 主な機能

### 1. セキュリティポリシーの集中管理

- AWS WAF、AWS Shield Advanced、VPC セキュリティグループ、AWS Network Firewall などの設定を一括管理。
- AWS Organizationsと統合し、複数アカウントへ一斉適用可能。

### 2. 新規リソースへの自動適用

- 新たに作成されたアカウントやVPCに対して、自動的にセキュリティルールを適用。

### 3. コンプライアンスの可視化

- 各アカウントやリソースが定めたポリシーに準拠しているか監視し、コンプライアンス違反を検出。

### 4. 分散型の攻撃防御

- DDoS対策（AWS Shield Advanced）やWAFルールをグローバルに適用し、攻撃から保護。

## 活用例

- 企業が全社的に統一したWAFルールを適用。
- AWS Organizationsを利用して、複数アカウントのVPCセキュリティグループを一括管理。
- 新しく作成されたAWSアカウントに対して自動的にセキュリティポリシーを適用し、管理コストを削減。

# AWS Savings Plans？

一定の使用量を事前にコミットすることで**従量課金よりも割引価格でAWSのサービスを利用できる**料金プランです。特に、EC2やFargate、Lambdaなどのコンピューティングサービスのコスト削減に役立ちます。

## **Savings Plansの種類**

AWS Savings Plansには、以下の2種類があります。

### **1. Compute Savings Plans（コンピュートセービングスプラン）**

- **割引対象:**
  - **EC2（インスタンスタイプ、リージョン、OSを問わず適用）**
  - **AWS Fargate**
  - **AWS Lambda**
- **適用範囲:** 柔軟に適用（リージョンやインスタンスタイプの変更に対応）
- **割引率:** **最大66%**（通常料金比）

➡ **長期間にわたってEC2やFargateを柔軟に使いたい場合に適している。**

### **2. EC2 Instance Savings Plans（EC2インスタンスセービングスプラン）**
- **割引対象:**
  - **特定のEC2インスタンスタイプとリージョン**
- **適用範囲:** 選択したインスタンスファミリー（例: `m5.large`）に適用
- **割引率:** **最大72%**（通常料金比）

➡ **特定のインスタンスファミリーを長期間利用する場合に最適。**

## **Savings Plansの契約オプション**

Savings Plansは、以下の3つの支払い方法を選択できます。

1. **全額前払い（All Upfront）**
   - 最も高い割引率を得られる。
   - 契約開始時に全額支払う。

2. **一部前払い（Partial Upfront）**
   - 割引率は中程度。
   - 一部を前払いし、残りを時間単位で支払う。

3. **前払いなし（No Upfront）**
   - 最も柔軟だが、割引率は低め。
   - 1時間ごとに分割して支払う。

## **Savings Plansとリザーブドインスタンス（RI）の違い**

|  | **Savings Plans** | **リザーブドインスタンス（RI）** |
|---|---|---|
| 割引の適用範囲 | Compute Savings Plansなら柔軟に適用 | 指定したインスタンスタイプ・リージョン |
| 割引率 | 最大72% | 最大75% |
| Fargate/Lambda対応 | **○（Compute Savings Plansのみ）** | **×** |
| 柔軟性 | 高い（インスタンスタイプ変更OK） | 低い（特定のインスタンスに固定） |
| 支払い方法 | 3種類（全額前払い / 一部前払い / 前払いなし） | 3種類（全額前払い / 一部前払い / 前払いなし） |
