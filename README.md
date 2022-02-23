# デプロイパターンの種類

- In-Place
    - 稼働中の環境を新しいアプリケーションで更新する
- Linear（線形）
    - 毎分n%ずつ新環境の割合を増やすなど、直線的に更新する
- Canary
    - 最初は10％、数分後に全てなど、割合によって段階的にリリース
- Blue/Green
    - 現行バージョン環境とは別に新バージョン環境を構築し、リクエスト送信先を切り替える
- Rolling
    - サーバーをいくつかのグループに分けて、グループごとにIn-Place更新をする
- Immutable
    - 現バージョンとは別に新バージョンを構築する
- All at once
    - すべてのサーバーで同時にIn-Place更新をする

| 方式 | 失敗時の影響 | 時間 | ダウンタイム | ELB暖気 | DNS切替 | ロールバック | デプロイ先 |
| ---- | ---------- | --- | ---------- | ------ | ------- | ---------- | --------- |
| All at once | ダウンタイム発生 | ● | ダウンタイム発生 | 不要 | なし | 再デプロイ | 既存 |
| Rolling | 1バッチ分だけサービスアウト | ●● | なし | 不要 | なし | 再デプロイ | 既存 |
| Rolling with additional batch | 最初のバッチであれば最小 | ●●● | なし | 不要 | なし | 再デプロイ | 新規＋既存 |
| Immutable | 最小 | ●●●● | なし | 不要 | なし | 再デプロイ | 新規 |
| URL swap | 最小 | ●●●● | なし | 必要 | あり | URL swap | 新規 |
| Route53による切替 | 最小 | ●●●● | なし | 必要 | あり | URL swap | 新規 |

# [AWS CodeCommit](https://www.slideshare.net/AmazonWebServicesJapan/20201020-aws-black-belt-online-seminar-aws-codecommit-aws-codeartifact)

Gitリポジトリを提供するサービス。AWSCodeCommitPowerUserという権限がある。ソースコードが更新されたなどのタイミングでAmazon SNSやAWS Chatbotに通知を飛ばしたりAWS Lambdaの関数を実行するイベントを設定できる。

# [AWS CodeBuild](https://www2.slideshare.net/AmazonWebServicesJapan/20201125-aws-black-belt-online-seminar-aws-codebuild)

ビルドプロセスを提供。`buildspec.yml`に処理内容を記載する。

ソースはS3やCodeCommit、GitHub（含むEnterprise）、Bitbucket。

ローカルエージェントをインストールすることで実行することが可能。

# [AWS CodeDeploy](https://www.slideshare.net/AmazonWebServicesJapan/20210126-aws-black-belt-online-seminar-aws-codedeploy)

S3やGitHubからEC2、オンプレ、ECS、Lambdaにリビジョンをデプロイする。`appspec.yml`に記載する。

# [AWS CodePipeline](https://www2.slideshare.net/AmazonWebServicesJapan/20201111-aws-black-belt-online-seminar-aws-codestar-aws-codepipeline)

コードが変更されるたびにビルド・テスト・デプロイを実施する。CodeCommit、CodeBuild、CodeDeployを組み合わせてフローを作るもの。

# [AWS CodeStar](https://www2.slideshare.net/AmazonWebServicesJapan/20201111-aws-black-belt-online-seminar-aws-codestar-aws-codepipeline)

プロジェクトテンプレートを選択して名前を決めるとCI/CDパイプラインを自動的に作成してくれるサービス。

# [AWS CodeArtifact](https://www.slideshare.net/AmazonWebServicesJapan/20201020-aws-black-belt-online-seminar-aws-codecommit-aws-codeartifact)

ソフトウェアパッケージを保存して配信できるサービス。

# [Amazon CodeGuru](https://www.slideshare.net/AmazonWebServicesJapan/20200804-aws-black-belt-online-seminar-amazon-codeguru)

以下の2つの機能からなる。

- CodeGuru Profiler
    - JavaやJVM言語で開発されたアプリのパフォーマンスを可視化し、パフォーマンスの問題の原因を診断する。
- CodeGuru Reviewer
    - Javaのコードの自動レビュー。

# [AWS CloudFormation](https://www.slideshare.net/AmazonWebServicesJapan/20200826-aws-black-belt-online-seminar-aws-cloudformation-238501102)

JSON/YAML形式で記述されたテンプレートをもとにスタックと呼ばれるAWSリソースの集合体を自動構築する。

AWSTemplateFormatVersion、Parameters、Resources、Mappings、Conditions、Outputsなどのセクションが存在する。

# [AWS OpsWorks](https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-2017-aws-opsworks)

Chef/Puppetを使用できるマネージドサービス。Chef用のAWS OpsWorks for Chef Automate、Puppet用のAWS OpsWorks for Puppet Enterpriseがある。

# [AWS Elastic Beanstalk](https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-2017-aws-elastic-beanstalk)

アプリのコード以外の環境をAWSが構築するサービス。CloudFormationスタック、起動設定、オートスケーリンググループ、スケーリングポリシーとCloudWatchアラーム、セキュリティグループ、Application Load Balancer。

対応プラットフォームは、.NET、Docker、GlassFish、Go、Java、Node.js、PHP、Python、Ruby、Tomcat。

各リソースを管理するためにサービスロールと呼ばれるIAMロールが必要。デフォルトではaws-elasticbeanstalk-service-roleがあり、以下のAWS管理ポリシーがアタッチされている。

- AWSElasticBeanstalkEnhancedHealth
    - インスタンスや環境の正常性確認のためのポリシー
- AWSElasticBeanstalkService
    - 環境を作成、更新するためのポリシー

## EB CLI

Elastic Beanstalk用のCLI。以下のコマンドがある。

- eb init
    - アプリケーションを作成。実行すると`.elasticbeanstalk/config.yml`ファイルが作成される。
- eb create
    - アプリケーションの環境を作成する。
- eb deploy
    - 環境の更新デプロイを実行する。

## .ebextensions

Elastic Beanstalk設定ファイルを格納しているディレクトリ。`.ebextensions`以下に拡張子`.config`なJSON/YAMLファイルを作成しておくと環境のカスタマイズが可能。

# [AWS SAM](https://www.slideshare.net/AmazonWebServicesJapan/20190814-aws-black-belt-online-seminar-aws-serverless-application-model-165314501)

CloudFormationの拡張機能。Lambda/API Gateway/DynamoDB/S3/Step Functionsなどを組み合わせたサーバーレスアプリの構築を自動化する。専用のCLIを使用する。

シンプルな例としては、以下の3ステップ。

1. `sam init`
    - サーバーレスアプリの初期処理。プロジェクトの初期ファイルと`template.yaml`を作成する
2. `sam build`
    - デプロイするための準備をする。ローカルでのテストも実行できるようになる。
3. `sam deploy`
    - AWSリソースを構築する。CloudFormationが実行される。

`sam local start-api`でローカルでサーバーレスアプリのテストができる。

# [VPC](https://www.slideshare.net/AmazonWebServicesJapan/20201021-aws-black-belt-online-seminar-amazon-vpc)

押さえておきたい用語。

- ルートテーブル
    - パケットがどこに向かえば良いかを示すもの。
    - VPC作成時にデフォルトで1つルートテーブルが作成される。
- インターネットゲートウェイ
    - インターネットへの出口となるもの。VPCをインターネットに接続する仮想ルーター。
- NATゲートウェイ
    - プライベートサブネットのリソースがインターネットまたはAWSクラウドへ通信するために必要。
- セキュリティグループ
    - VPC内のリソースにアタッチされているElastic Network Interface（ENI）に設定する。
    - ステートフル。
    - デフォルトはアウトバウンドのみ許可
- ネットワークACL
    - サブネットを対象に設定する。
    - ステートレス。
    - デフォルトは全て許可

## VPCエンドポイント

VPCエンドポイントを使うことで、インターネットゲートウェイを買いサイズにAWSの各サービスのAPIに直接リクエストを実行できる。

ゲートウェイエンドポイントとインターフェースエンドポイントがある。

### ゲートウェイエンドポイント

以下の二つだけが対応。

- S3
- Dynamo DB

### インターフェースエンドポイント

上記二つ以外と覚えれば良い。

# AWS CLI

## `aws configure`

AWSの認証情報やデフォルトリージョンを設定する。認証情報は以下に格納される。

- Linux/macOS : ~/.aws/credentials
- Windows : C:¥Users¥USERNAME¥.aws¥credentials

リージョンコードは`.aws/config`に保存される。

環境などを示すプロファイルを指定することができる。

```
# 設定時
$ aws configure --profile prod
# 実行時
$ aws s3 ls --profile prod
```

# [IAM](https://www.slideshare.net/AmazonWebServicesJapan/20190129-aws-black-belt-online-seminar-aws-identity-and-access-management-iam-part1)、[IAM Part2](https://www.slideshare.net/AmazonWebServicesJapan/20190130-aws-black-belt-online-seminar-aws-identity-and-access-management-aws-iam-part2)

## IAMポリシー

アクセス権限を設定するドキュメント。JSON形式。

AWS Policy generatorを使うことでブラウザ上でJSONを書かなくても作成することができる。

以下のようなもの。

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::foobar/*"
        }
    ]
}
```

Action/Resourceには`*`を指定することが可能。

以下の三種類が存在する。

- AWS管理ポリシー
    - AWSが作成して管理しているポリシー
- カスタマー管理ポリシー
    - ユーザーが作成して管理するポリシー
- インラインポリシー
    - 他のグループ、ユーザー、ロールにアタッチする必要のない場合、ポリシー単体としては作成せず、埋め込む形で利用する。

以下のロジックで判定される。

- 拒否されていなければ、許可されているかどうかが評価される
- 許可されていなければ拒否される。

## ARN（Amazon Resource Name）

AWSリソースを一意に識別するもの。以下の書式。

```
arn:aws:サービス名:リージョンコード:アカウントID:リソースID
```

## ポリシー変数

ポリシーを記述する時にリソースや条件キーの正確な値がわからない時、プレースホルダーを指定できる機能のことを指す。

## CloudFrontのOrigin Access Identity（OAI）

S3を公開状態にすることなく、S3へのアクセスをCloudFrontからのリクエストに絞るための仕組み。

参考：[CloudFormation で OAI を使った CloudFront + S3 の静的コンテンツ配信インフラを作る](https://dev.classmethod.jp/articles/s3-cloudfront-with-oai-by-cloudformation/)

S3バケットポリシーでPrincipalとしてOAIを指定するには以下のような記述をする。

```
"Principal": {
    "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity OAIのID"
}
```

## GetRole/PassRole

EC2インスタンスにIAMロールをアタッチするIAMユーザに対してもAPIアクションへの許可が必要。そのAPIアクションがGetRole/PassRole。

# Amazon Cognito

Webアプリやモバイルアプリに安全に認証を提供するサービス。

## ユーザープール

Cognitoのサービスリソースの一つ。以下のことができる。

- サインアップ、サインインを簡単にアプリに実装
- カスタマイズ可能な組み込みサインインUI
- ユーザープロファイルの管理
- MFA、本人確認などにユーザー認証に一般的な機能
- Lambdaトリガー
- Web IDフェデレーション（Facebook, Google, Amazonなど）

## IDプール

Cognitoのサービスリソースの一つ。外部ユーザーなどがAWSサービスを使えるように一時的なAWS credentialsを発行してくれる。

1. GetIDでCognito ID PoolからCognito IDを取得
2. GetCredentials ForIdentityによってIAMロールに対してAssumeRoleがリクエストされる。これで一時的なアクセスキーID、シークレットアクセスキー、トークンがアプリに渡される。

# Amazon Systems Managerパラメータストア

パラメータストアにパラメータを書き込み、GetParameterで取得して使用する。暗号化のタイプはSecureString。

# AWS Secrets Manager

シークレット情報のローテーションが必要なケースなどで使う。設定された周期でLambda関数を自動実行し、データベースのパスワードを変更してSecrets Managerのシークレット情報を更新する。

# AWS Certificate Manager

独自ドメインのアプリでの証明書の管理、設定の機能を有する。

ドメイン所有者の確認はメール認証かCNAME認証で行う。

# AWS CloudHSM

暗号化に使用するキーの管理に専用ハードウェアが必要となる場合に使用。

# AWS Key Management Service（KMS）

暗号化するためのキーを作成し、管理する。暗号化に使用したデータキーはCMK（Customer Master Key）によって暗号化される。この方法をエンベロープ暗号化という。

# S3オブジェクトの暗号化の種類

- クライアントサイド暗号化
    - CSE-KMS
        - クライアントで暗号化してからアップロードする方法。KMSで管理しているCMKを使用して暗号化する。
    - CSE-C
        - オンプレミスキーサーバーなどを作成されたキーを使用して暗号化する。
- サーバーサイド暗号化
    - SSE-S3
        - S3が管理するキーによるサーバー側暗号化を行う。
    - SSE-KMS
        - KMSが管理しているキーを使ったサーバー側暗号化。
    - SSE-C
        - ユーザー指定キーによるサーバー側暗号化。

# RDSインスタンスの暗号化

KMSと連携して暗号化する。バックアップスナップショットも暗号化される。

他のリージョンでリストアする場合は、リストア先のリージョンでもCMKを用意しておき、スナップショットのクロスリージョンコピーをする際にコピー先リージョンのCMKを選択する。

他のアカウントでリストアするときは、CMKのキープリシーで他アカウントへの許可を設定する。

# SQSメッセージの暗号化

KMSと連携して行う。

# 認証情報の優先順位

1. コードのオプションやパラメータで指定されたアクセスキー情報
2. 環境変数
3. .aws/credentials
4. EC2のIAMロール（インスタンスプロファイル）

# [Amazon Elastic Block Store](https://www.slideshare.net/AmazonWebServicesJapan/20190320-aws-black-belt-online-seminar-amazon-ebs/)

- EC2インスタンスにアタッチして使用するブロックレベルのストレージサービス
- アタッチするEC2インスタンスと同じAZに作成する
- EC2は複数のEBSを接続することは可能。EBSを複数のインスタンスで共有することはできない

# [Amazon Elastic File System](https://www.slideshare.net/AmazonWebServicesJapan/2018070420190520-renewed-aws-black-belt-online-seminar-amazon-elastic-file-system-amazon-efs)

- NFSアクセスを提供する分散ストレージ。
- 複数のEC2からNFSを使ってネットワーク経由でアクセス
- `amazon-efs-utils`パッケージを使用することでマウントヘルパーを使って簡単にマウントコマンドを実行することが可能。

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

## S3 Select

S3に格納したデータに対してSQL文を発行して必要なデータだけを取得できるようにする

## バージョニングとオブジェクトロック

S3バケット内のバージョン管理ができるのがバージョニング。バージョニングはバケット単位で有効にできる。

バージョンを指定してのダウンロードや削除操作は`GetObjectVersion`、`DeleteObjectVersion`アクションへの許可が必要。

オブジェクトロックは削除や上書きを禁止するために用いる機能。バージョニングが有効である必要あり。

オブジェクトロックは以下の二種類がある。

- コンプライアンスモード
    - 保持期間を設定したオブジェクトは全てのユーザーからの削除を拒否する
- ガバナンスモード
    - 保持期間を設定したオブジェクトは全てのユーザーからの削除を拒否できるが、IAMでガバナンスモードの解除の権限を持っていれば解除が可能。

## Amazon S3 Glacier

保存しておくことが主目的、アプリなどからアクセスする必要があまりないものはGlacierを使って保管コストを下げることが可能。

取り出しには以下三種類ある。

- 迅速
    - 通常1〜5分で取り出される
- 標準
    - 通常3〜5時間で取り出される
- 大容量
    - 通常5〜12時間で取り出される。

## マルチパートアップロードAPI

1つのサイズが大きいオブジェクトをアップロードするとき、効率化を図るために複数のパーツに分散にして並列アップデートをすること。

## アクセスコントロールリスト（ACL）

被付与者にバケットやオブジェクトへのアクセスを許可するもの。

よくあるのは、パブリックアスセスの許可

```shell
$ aws s3 cp sample.txt s3://bucketname/sample.txt --acl public-read
```

## バケットポリシー

アクセスコントロールリストよりより詳細なアクセス権限を設定したい場合に使用。

## 署名付きURL

特定の人にだけS3のオブジェクトをダウンロードして欲しいときに使用。オブジェクトのURLにAWSAccessKeyId、Signature、Expiresが付与されている。

## CORS

一つのドメインでロードされ、他のドメインにあるリソースと相互作用するときに使用。こんな感じで設定する。

```json
[
    {
        "AllowedHeaders": [
            "Authorization"
        ],
        "AllowedMethods": [
            "GET"
        ],
        "AllowedOrigins": [
            "http://hogehoge.s3-web-ap-aaaa.amazonaws.com"
        ],
        "ExposeHeaders": [],
        "MaxAgeSeconds": 3000
    }
]
```

## [整合性](https://aws.amazon.com/jp/premiumsupport/knowledge-center/data-integrity-s3/)

オブジェクトが正しくアップロードできたかチェック。ETagが利用できそうだけれども、利用すべきではない。

1. `openssl md5 -binary hoge.txt | base64`でMD5を取得
2. `aws s3api put-object`の`--content-md5`で上記1.で設定した値を指定する。

# [AWS Storage Gateway](https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-2017-aws-storage-gateway)

オンプレに仮想マシンをデプロイして、保存したデータが自動的にS3を使用するようにする。以下の三種類がある。

- ファイルゲートウェイ
    - NFS/SMBプロトコルで接続できる
- ボリュームゲートウェイ
    - iSCISプロトコルで接続できる
- テープゲートウェイ
    - 仮想テープライブラリとして接続できる

# [Amazon CloudFront](https://www.slideshare.net/AmazonWebServicesJapan/20190730-aws-black-belt-online-seminar-amazon-cloudfront)

世界中にあるエッジロケーションを使用してコンテンツをキャッシュしたり配信するためのサービス。S3と組み合わせたり、静的コンテンツはS3、動的コンテンツはその他からというような複数のオリジンにも対応している。

# [Amazon RDS](https://www.slideshare.net/AmazonWebServicesJapan/20180425-aws-black-belt-online-seminar-amazon-relational-database-service-amazon-rds-96509889)

データーベースサービス。以下のエンジンを利用可能。

- MySQL
- PostgreSQL
- MariaDB
- Oracle
- Microsoft SQL Server

リードレプリカを追加で5つまで作成することができ、他のリージョンにも作成することができる。

# [Amazon Aurora with MySQL Compatibility](https://www.slideshare.net/AmazonWebServicesJapan/20190424-aws-black-belt-online-seminar-amazon-aurora-mysql)、[Amazon Aurora with PostgreSQL Compatibility](https://www.slideshare.net/AmazonWebServicesJapan/20190828-aws-black-belt-online-seminar-amazon-aurora-with-postgresql-compatibility-168930538)

データーベースサービスだが、AWSがユーザーのニーズを満たすために開発し、MySQLの5倍のスループット、PostgreSQLの3倍のスループットがある。

リードレプリカを最大15個まで作成可能。

## Aurora Serverless

Auroraキャパシティユニット（ACU）に応じて時間課金が発生。1ACUあたり2GBのメモリとそれに応じたCPU性能が提供。負荷に応じて自動的にスケーリング。オプションで、アイドル状態が続いたときには停止して時間課金を停止することも可能。

# [Amazon ElastiCache](https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-2017-amazon-elasticache-84060910)

AWSクラウドでの分散インメモリキャッシュ環境のセットアップ、管理、およびスケーリング、冗長構成等を容易に構築することができるサービス。MemcachedとRedisが存在。

Memcachedはマルチスレッド、RedisはレプリケーションやPub/Subをサポートしている。

# [Amazon DynamoDB](https://www.slideshare.net/AmazonWebServicesJapan/20170809-black-belt-dynamodb)

フルマネージドなNoSQLデータベースサービス。リージョンを選択してテーブルを作成する。パーティション（データの保存先）を分散させることで水平スケーリングを可能とする。

データを一意として扱うために、作成時にプライマリキーを決定しておく必要がある。プライマリキーは以下2つのパターンがある。

- パーティションキーのみ
- パーティションキーとソートキー

1つのデータのことを項目と言い、複数の属性を持ちます。パーティションキー、ソートキー以外の属性は項目ごとにあってもなくても構わない。

パーティションキー、ソートキーの属性は必須で、テーブル作成時にデータ型を決めておく必要がある。データ型は文字列、数値、バイナリから選択。他の属性ではブール値、マップ、リストなどからも設定できる。

テーブル操作でよく使われるオペレーション

- GetItem
    - パーティションキーを条件として指定し1件のアイテムを取得
    - ProjectionExpressionで取得する属性を指定する
    - ConsistentReadで強力な整合性を指定できる
- TransactGetItems
    - トランザクションにて最大25件取得
- PutItem
    - 1件のアイテムを書き込む
- UpdateItem
    - 1件のアイテムを更新
    - 属性の指定はExpressionAttributeNames、更新する値はExpressionAttributeValuesを使う。
    - ConditionExpressionを使用すると条件付きのUpdateItemが可能
        - これを利用することでオプティミスティックロックも実装可能
- TransactWriteItems
    - トランザクションにてCreate、Update、Delete制御
- DeleteItem
    - 1件のアイテムを削除
    - ReturnValuesで削除前の項目を取得することができる
- Query
    - パーティションキーとソートキーの複合条件にマッチするアイテム群を取得
    - 最大1MBのデータを取得可能
- BatchGetItem
    - 最大100アイテム（ただし16MBを超えないアイテム数）を並列で取得
- BachWriteItem
    - 最大25件のバッチ処理（入力／削除）。最大1MB
- Scan
    - テーブルを総ナメする
    - FilterExpressionでフィルタリングが可能
    - 最大1MBのデータを取得可能
        - 1MBを超えたらLastEvaluatedKeyにNull以外が返る。ExclusiveStartKeyにLastEvaluatedKeyを指定して再読み取りすることで再度Scanを行う必要がある。

請求モードは以下2つのパターン。

- オンデマンドキャパシティモード
    - 読み込み回数とサイズ、書き込み回数とサイズに応じて請求が発生
    - 強い整合性での読み込みは倍のコストがかかる
- プロビジョニング済みキャパシティモード
    - Read Capacity Unit（RCU）とWrite Capacity Unit（WCU）をプリビジョニング（確保、設定）するモード。
    - WCUは最大1KBの項目を1秒間に1回書き込む
    - RCUは最大4KBの項目を1秒間に2回結果整合性で読み込むか、1秒間に1回強力な生整合性で読み込むこと

整合性について。

- 結果整合性
    - 項目を書き込んだ直後、更新した直後、削除した直後に別のプロセスから読み込むと、まだ書き込まれていない、更新されていない、削除されていないという未反映の項目を読み込む可能性がある
- 強力な整合性
    - 更新された項目をリアルタイムで読み込む

- Local Secondary Index（LSI）
    - ソートキー以外に絞り込み検索を行うキーを持つことができる
    - パーティションキーが同一で、他のアイテムからの検索のために利用
    - テーブル作成時に作っておく必要あり。あとから追加できない。
- Global Secondary Index（GSI）
    - パーティションキー属性の代わりとなる
    - パーティションキーを跨いで検索を行うためのインデックス
    - あとからでも作成することが可能

DynamoDBストリームは項目が更新されたことをトリガーとして項目の更新情報がストリームに格納され、様々なイベント処理を行うことができる。デフォルトでは無効。作成時でもあとからでも有効化できる。

DynamoDBストリームに格納することができる情報を指定する必要がある。以下のものから。

- キーのみ
- 新しいイメージ
- 古いイメージ
- 新旧イメージ

DynamoDBストリームはDynamoDBグローバルテーブルの作成時に有効にする必要がある。

バックアップはポイントインタイムリカバリで過去35日間分のバックアップを作成可能。この範囲であればいつの時点のテーブルにも戻れる。

DynamoDB Accelerator（DAX）はインメモリキャッシュを使ってDynamoDBテーブルへのレイテンシーを短縮する機能。

# [AWS Lambda](https://www.slideshare.net/AmazonWebServicesJapan/20190402-aws-black-belt-online-seminar-lets-dive-deep-into-aws-lambda-part1-part2/)

イベントの発生に応じてプログラムを実行する環境を提供するクラウドサービス。以下の特徴がある。

- 実行環境はNode、Java、Python、.NET、Go、Ruby、＋カスタムランタイム（独自定義）
- メモリは128MB〜10GBまで。CPUはメモリに応じて割り当て。
- タイムアウトはデフォルト3秒、最長15分。
- 以下のイベントがある。
    - Alexa
    - API Gateway
    - S3
    - CloudWatchイベント
    - CloudWatch Logs
    - CloudFormation
    - CodeCommit
    - Cognito
    - Config
    - DynamoDB
    - Application Load Balancer
    - AWS IoT
    - Kinesis Data Streams
    - Simple Email Service
    - Simple Notifications Service
    - SQS
    - Step Functions
- 以下のような制限がある。
    - 同時実行数はリージョンごとに1,000
    - 関数とレイヤーの合計容量は75GB
    - 関数ごとのデプロイパッケージはZIPで50MB、解凍後で250MB
    - /tmpディレクトリは512MB

イベントにはプッシュイベントとプルイベントがある。

- プッシュイベント
    - Lambdaに対してイベントが送信され、送信元のイベントがLambdaを実行
    - 送信元からのInvokeFunctionアクションの許可が必要
    - 例えばS3イベントやAPI Managementイベント
- プルイベント
    - Lambaがイベント元になっているリソースにデータを取りに行く
    - 例えばDynamoDBイベントやSQSイベント

## Lambdaレイヤー

外部ライブラリなどの依存関係を他のLambda関数と共有化できる。最大5つのレイヤー。

## バージョニングとエイリアス

特定のLambda関数のバージョンに対するポインタのようなものがエイリアス。バージョンに対する名前をつけることができる。

# [API Gateway](https://www.slideshare.net/AmazonWebServicesJapan/20190514-aws-black-belt-online-seminar-amazon-api-gateway-230113575)

REST、HTTP、WebSocket APIを作成、公開、維持、モニタリング、及びセキュア化するためのAWSサービス。

以下の機能を有する。

- 認証
- SSL証明書の設定
- リソースポリシー（API実行の許可／拒否）
- メソッド許可
    - APIに対してリクエストを実行できるユーザーを制御できる。IAM認証やCognitoオーソライザーやLambdaオーソライザー
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
