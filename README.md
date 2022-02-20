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

# [AWS Storage Gateway](https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-2017-aws-storage-gateway)

オンプレに仮想マシンをデプロイして、保存したデータが自動的にS3を使用するようにする。以下の三種類がある。

- ファイルゲートウェイ
    - NFS/SMBプロトコルで接続できる
- ボリュームゲートウェイ
    - iSCISプロトコルで接続できる
- テープゲートウェイ
    - 仮想テープライブラリとして接続できる


