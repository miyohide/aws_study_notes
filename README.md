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

S3やGitHubからEC2、オンプレ、ECS、Lambdaにリビジョンをデプロイする。`appspec.yml`に記載する。仕様は[リファレンス](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/application-specification-files.html)を参照。

フックの仕組みがあり、以下のタイミングで処理を実行可能。[CodeDeployフックのベストプラクティス](https://dev.classmethod.jp/articles/best-practice-of-code-deploy-hooks/)がわかりやすい。

1. ApplicationStop
    - デプロイ対象のアプリケーションを正常に停止するなどを行う。
        - ミドルウェアの再起動はBeforeInstall/AfterInstallで行う
    - 初回デプロイ時には実行されない。
1. DownloadBundle
    - リビジョンファイルをコピーする
1. BeforeInstall
    - ファイルの暗号化やバックアップの作成などを行う
    - ミドルウェアの停止を行う
1. Install
    - リビジョンファイルを最終的な送信先フォルダにコピーする
1. AfterInstall
    - アプリの設定やファイルの許可の変更に使用
    - ミドルウェアの起動を行う
1. ApplicationStart
    - ApplicationStopで停止したサービスを再起動
1. ValidateService
    - デプロイが正常に完了したことを検証

# [AWS CodePipeline](https://www2.slideshare.net/AmazonWebServicesJapan/20201111-aws-black-belt-online-seminar-aws-codestar-aws-codepipeline)

コードが変更されるたびにビルド・テスト・デプロイを実施する。CodeCommit、CodeBuild、CodeDeployを組み合わせてフローを作るもの。

Amazon SNSと連携することで、パイプラインのステージに対して承認アクションを追加することができる。

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

CloudFormationではAWSアカウントごとにエクスポート名はリージョン内で一意である必要がある。

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

ライフサイクルポリシーを設定することができ、Elastic Beanstalk上に保存される。Elastic Beanstalkはバージョンアップのたびに古いバージョンは別途保管される。使用しなくなったバージョンは削除しないと制限に到達して新しいバージョンを作成できなくなる。ライフサイクルポリシーを設定すると、使用されていない古いバージョンを定期的に削除してアプリの新しいバージョンを作成するということが可能。

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

## cron.yaml

ワーカー環境のキューに定期ジョブを自動的に設定するための設定ファイル。

## env.yaml

アプリケーション環境の環境名、ソリューションスタック、環境リンクを設定するためのファイル。

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

## [AWS IAMユーザーに対して一時的な認証情報をリクエストする(STS:GetSessionToken)](https://dev.classmethod.jp/articles/temp-credentials-request-against-iam-user/)

AWS IAMユーザーに対して一時的な認証情報をリクエストする

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

# Amazon Cognito Sync

複数のデバイスで同じアプリを利用しているとき、アプリ関連のユーザーデータをデバイス間で同期することができる。

現状ではAWS AppSyncを利用することが推奨される。

# [Amazon Systems Managerパラメータストア](https://www.slideshare.net/AmazonWebServicesJapan/20200212-aws-black-belt-online-seminar-aws-systems-manager)

パラメータストアにパラメータを書き込み、GetParameterで取得して使用する。暗号化のタイプはSecureString。

# AWS Secrets Manager

シークレット情報のローテーションが必要なケースなどで使う。設定された周期でLambda関数を自動実行し、データベースのパスワードを変更してSecrets Managerのシークレット情報を更新する。

Systems Managerパラメータストアと同じような機能だが、Secrets Managerシークレットの方が性能上限が高い。ただし有料。

# AWS Certificate Manager

独自ドメインのアプリでの証明書の管理、設定の機能を有する。

ドメイン所有者の確認はメール認証かCNAME認証で行う。

# AWS CloudHSM

暗号化に使用するキーの管理に専用ハードウェアが必要となる場合に使用。

# [AWS Key Management Service（KMS）](https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-aws-key-management-service-kms)

暗号鍵の作成、管理、運用のためのサービス。AWSサービスと統合。SDKとの連携で独自アプリデータも暗号化できる。最大4KBのデータ暗号化。

- Customer Master Key（CMK）
    - KMS上のAES256ビットの鍵
    - 最大4KBのデータを暗号化
- Envelope Encryption
    - マスターキーをデータ暗号化に直接利用するのではなく、マスターキーで暗号化した暗号キーで対象オブジェクトを暗号化／復号化する。
    - 暗号化したデータとCMKで暗号化されたデータキーを一緒に保管する
    - 大量のデータの暗号化が可能
- Customer Data Key（CDK）
    - 実際の暗号化対象オブジェクトの暗号化／復号化に使用されるAES256ビットの鍵

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

# RDSインスタンスの暗号化

KMSと連携して暗号化する。バックアップスナップショットも暗号化される。

他のリージョンでリストアする場合は、リストア先のリージョンでもCMKを用意しておき、スナップショットのクロスリージョンコピーをする際にコピー先リージョンのCMKを選択する。

他のアカウントでリストアするときは、CMKのキープリシーで他アカウントへの許可を設定する。

# SQSメッセージの暗号化

KMSと連携して行う。

# [AWS Encryption SDK](https://aws.amazon.com/jp/blogs/news/how-to-use-the-new-aws-encryption-sdk-to-simplify-data-encryption-and-improve-ap/)

大容量のデータを暗号化するために利用する。エンベロープ暗号化が特徴。

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

ビューアとCloudFront間においてHTTPS通信を設定するには以下の設定のいずれかが必要。

- CloudFrontのビューアプロトコルポリシーにRedirect HTTP to HTTPSを設定
- CloudFrontのビューアプロトコルポリシーにHTTPS Onlyを設定

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
    - UnprocessedItemsに読み込めなかったアイテムの情報が含まれる
- BachWriteItem
    - 最大25件のバッチ処理（入力／削除）。最大1MB
    - UnprocessedItemsに書き込めなかったアイテムの情報が含まれる
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

項目データへのアクセスに対するセキュリティはIAMポリシーで制御可能。

DynamoDBテーブルでTTLを有効にすると、テーブルの項目に対して有効期限を定義することができる。有効期限が切れると項目を自動的に削除することができる。

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

非同期呼び出し処理の試みが3回とも失敗した場合、LambdaはSQSキュー、またはSNSトピックにイベントを送信できる。DeadLetterConfig(DLQ)パラメーターにおいてSQSキューのARNを指定することで、処理できないイベントについてはSNSトピックやSQSキューに送信することができる。

イベントにはプッシュイベントとプルイベントがある。

- プッシュイベント
    - Lambdaに対してイベントが送信され、送信元のイベントがLambdaを実行
    - Lambda関数のリソースポリシーで送信元からのInvokeFunctionアクションの許可が必要
        - Actionに`lambda:InvokeFunction`
    - 例えばS3イベントやAPI Managementイベント
- プルイベント
    - Lambaがイベント元になっているリソースにデータを取りに行く
    - Lambdaに割り当てるIAMロールにアタッチするIAMポリシーでイベントリソースに対しての権限を許可する必要がある
    - 例えばDynamoDBイベントやSQSイベント

## [IAMポリシーとリソースポリシー](https://dev.classmethod.jp/articles/policies-for-lambda/)

- IAMポリシー
    - 信頼ポリシー
        - AWS LambdaにLambda関数の実行を任せるためのもの
        - CloudWatch Logsにログを出力するためのlogs:CreateLogGroup、logs:CreateLogStream、logs:PutLogEvents
        - lambda.amazonaws.comに対してsts:AssumeRoleするための信頼性ポリシーも必要
        - 管理ポリシーAWSLambdaBasicExecutionRoleも用意されている
    - アクセスポリシー
        - Lambda関数が他のAWSリソースにアクセスすることを許可するためのもの
- リソースポリシー
    - 誰がそのLambda関数を呼び出せるかを定義する

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

# コンテナの実行

- [Amazon Elastic Container Registry（ECR）](https://www.slideshare.net/AmazonWebServicesJapan/202112-aws-black-belt-online-seminar-elastic-container-registory) : コンテナイメージの保存場所
    - ECRレジストリにログインする方法のポイントは`aws ecr get-login-password`を利用する
    - 具体的には`aws ecr get-login-password --region xxxx | docker login --username AWS --password-stdin 123456789012.dkr.ecr.xxxx.amazonaws.com`でログインする
- [ECS](https://www.slideshare.net/AmazonWebServicesJapan/202112-aws-black-belt-online-seminar-elastic-container-registory) : クラウドでコンテナを本番環境利用するためのオーケストレーター
    - EC2タイプとFargateタイプが存在する
        - EC2タイプはEC2インスタンスのクラスターを起動させてコンテナを実行する
        - Fargateタイプはコンテナ環境をサーバーレスに実現する。EC2インスタンスのメンテナンスをユーザーが行う必要がない。
    - 主な構成要素は以下のもの
        - タスク定義 : タスクを構成するコンテナ群定義（イメージとかCPU、メモリ、IAMロールなど）
        - クラスター : 実行環境の境界、IAM権限の境界
        - タスク : タスク定義に基づき起動されるコンテナ群
        - サービス : タスク実行コピー数を定義。ELBと連携したり起動タイプ（EC2/Fargate）を設定
    - タスク配置戦略は以下のものがある
        - binpack
            - CPUまたはメモリの最小利用可能量に基づいてタスクを配置する
        - random
            - タスクをランダムに配置する
        - spread
            - 指定された値に基づいてタスクを均等に配置する。値はinstanceIdまたはコンテナインスタンスに適用される任意のプラットフォームまたはカスタム属性
- [EKS](https://www.slideshare.net/AmazonWebServicesJapan/202110-aws-black-belt-online-seminar-amazon-elastic-kubernetes-service-amazon-eks) : AWSが提供しているKubernetes環境のマネージドサービス
- [Fargate](https://www.slideshare.net/AmazonWebServicesJapan/202108-aws-black-belt-online-seminar-ecs-fargate) : コンテナ環境をサーバーレスに実現する

# [Amazon Simple Queue Service （SQS)](https://www.slideshare.net/AmazonWebServicesJapan/20190717-aws-black-belt-online-seminar-amazon-simple-queue-service)

ほぼ無制限のスケーラビリティを備えたフルマネージドな分散メッセージキュー。以下の構成要素からなる。

- プロデューサー : キューにメッセージを送信するアプリケーション
- コンシューマ : キューのメッセージを取得するアプリケーション
- メッセージ : プロデューサーが生成するデータ。最大256KB。
    - このサイズを超えたい場合は、Amazon SQL Extended Client Libraryを利用する
    - Extended Client Libraryを利用することで以下のことが簡単にできる
        - メッセージを常にS3に保存するか、256KBを超えた場合にのみS3に保存するかを選べる
        - S3バケットを介したメッセージのやりとり
- キュー : メッセージをキューイングする。最大14日間保存可能。

キューにはスタンダードキューとFIFOキューの二種類が存在。

| -          | スタンダードキュー | FIFOキュー |
| ---------- | --------------- | --------- |
| スループット | ほぼ無制限         | max 300 件 / 秒 |
| 配信方式     | 少なくとも1回の配信 | 1回のみ配信 |
| 配信順序     | ベストエフォート   | 順序性を保つ | 

キューのメッセージ取得方法は以下二つがある。

- ショートポーリング
    - 即応答。メッセージがない場合は「空」を応答
- ロングポーリング
    - 最大20秒(ReceiveMessageWaitTimeSecondsで指定）メッセージの受信を待つ。
    - メッセージがない場合はタイムアウトとなり「空」を応答

以下の機能がある

- 可視性タイムアウト
    - コンシューマが取得したメッセージに対して指定された期間（デフォルト30s）他のコンシューマからの同一メッセージへのアクセスをブロック
    - 同一メッセージの処理を防ぐ
    - VisibilityTimeoutで指定
- 遅延キューとメッセージタイマー
    - メッセージをキューに送信してから一定時間経過後に利用可能になる
    - 遅延キューはキュー全体に、メッセージタイマーは特定のメッセージに対して指定するもの
    - DelaySecondsで指定
- Dead Letter Queue(DLQ)
    - 正しく処理できないメッセージの分離先として利用されるキュー
    - 同一リージョン、同一アカウント、同一タイプ（スタンダードキュー/FIFOキュー）で作成する必要あり。
- サーバーサイド暗号化を利用したメッセージの暗号化
    - AWS Key Management Serviceで管理されているキーを使用してSQSキュー内のメッセージの内容を保護
- キューのアクセス制御
    - IAMポリシーとAmazon SQSポリシーのいずれか、または両方でアクセス制御を実現。
    - IAMポリシーはIAMユーザー、IAMロールに対して適用、Amazon SQSポリシーはキューに対して適用。
- メッセージ属性を利用したメタ情報の格納
    - メッセージ本文とは別にメタデータ等を保持することができる。max 10個。
    - メッセージ属性の暗号化は対象外。

主なAPI

- CreateQueue
    - キューを新規作成する
- SetQueueAttributes
    - 既存のキューの属性を変更する
- GetQueueAttributes
    - 既存のキューの属性を取得して確認する
- GetQueueUrl
    - キューにAPIリクエストを実行するときにエンドポイントを取得
- ListQueues
    - 指定したリージョンのキューURLの一覧を取得できる
    - QueuenamePrefixを使って名前でフィルタリングも可能
- DeleteQueue
    - キューを削除する。メッセージがあってもお構いなし。
- SendMessage
    - プロデューサーがメッセージを送信する
- ReceiveMessage
    - コンシューマがメッセージを受信、ポーリングする
- DeleteMessage
    - コンシューマが処理を終了したらメッセージを削除する。
    - メッセージを削除する際に使うのは受信ハンドル（ReceiptHandle）
- DeleteMessageBatch
    - 複数の（最大10個）のメッセージを削除する
- PurgeQueue
    - キューのメッセージを全て削除するが、キュー自身は残す。

# [Amazon Simple Notification Service(SNS)](https://www.slideshare.net/AmazonWebServicesJapan/20190604-aws-black-belt-online-seminar-amazon-simple-notification-service-sns)

パブリッシャー（CloudWatch AlarmやRDS Eventなど）が送信したメッセージをあらかじめサブスクリプション設定済みのサブスクライバー（Lambda関数やメール、モバイルアプリ、HTTPリクエストなど）にプッシュする。

1つのメッセージを複数のサブスクライバーにプッシュし並列処理をする設計をファンアウトと呼ぶ。

サブスクリプションを設定するとき、rawメッセージを有効にするとSNSにパブリッシュされたメッセージをそのまま送信する。無効の場合は属性情報と合わせてJSONエンコードされる。

サブスクリプションごとにフィルターポリシーを設定することが可能。メッセージ属性に指定したフィルター条件を含んでいる場合のみそのサブスクリプションにメッセージが送信される。

主なAPI

- CreateTopic
    - 新規Topicの作成
- Delete Topic
    - Topicの削除
- ListTopic
    - Topicのリスト
- Subscribe
    - 特定のTopicに新規Subscriptionを登録
- ConfirmSubscriptin
    - Subscription確認メッセージへの応答
- UnSubscribe
    - 登録済みのSubscriptionの解除
- Publish
    - Topicに新しいmessageをpublish

# [Amazon MQ](https://www.slideshare.net/AmazonWebServicesJapan/20210317-aws-black-belt-online-seminar-amazon-mq)

オープンソースメッセージブローカー（Apache ActieMQ/RabbitMQ）向け完全マネージド型サービス。

EFSを利用することでAmazon MQの可用性を高めることが可能。

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

# [AWS Step Functions](https://www.slideshare.net/AmazonWebServicesJapan/20190522-aws-black-belt-online-seminar-aws-step-functions)

ワークフローを作成・制御することができるサービス。ASL（Amazon States Language）と呼ばれるJSON形式の言語でワークフローを定義する。

ASLには以下のものを設定することができる。

- Comment
    - コメントを入力できる
- StarAt
    - 一番最初に実行するStateを指定する
- TimeoutSeconds
    - 秒数を指定する。ステートマシン全体の実行時間がこの秒数を超えるとタイムアウトエラーとなり実行が終了する
- Version
    - ASLのバージョンを指定
- States
    - ステートマシンを構成するStateを指定する

ステートマシンの実行方法としては以下のものが利用可能。

- Amazon CloudWatch Events
- Amazon API Gateway
- マネジメントコンソール
- AWS CLI
- 各種SDK

ステートマシンから呼び出し可能なAWSサービスは以下のもの。

- Lambda
- DynamoDB
- Batch
- ECS
- SNS
- SQS
- Glue
- SageMaker
- etc
- Activity
    - 自身で定義したサービスを紐付けることができる

データの入出力は以下の方法を取る。

- データの入力（InputPath）
    - 定義内のInputPathフィールドでStateに引き渡す内容を指定。
    - 例えば`"InputPath": "$.numbers"`と定義内で記述。`$.numbers`はJsonPath構文。平たく言うとJSON内の項目。
- データの入力（Parameters）
    - 定義内のParametersフィールドでStateに引き渡す内容を指定。
    - InputPathと異なりJSONが構成される。
- 処理結果の受け取り（ResultPath）
    - 定義内のResultPathフィールドで結果を受け取るフィールド名を指定
- データの出力（OutputPath）
    - 次のStateに対して引き渡す（出力する）データを指定。
    - 定義内のOutputPathフィールドで次のStateに引き渡す内容を指定。

State Typeは以下のものがある。

- Task
    - 単一の処理を行う
- Wait
    - 指定した時間の間、処理をストップする
- Pass
    - 入力をそのまま出力へ渡す
- Parallel
    - 並列に処理を実行する
- Choice
    - 一定の条件により分岐する
- Fail
    - 実行結果を失敗として終了する
- Succeed
    - 実行結果を成功として終了する

# [Amazon CloudWatch](https://www.slideshare.net/AmazonWebServicesJapan/20190326-aws-black-belt-online-seminar-amazon-cloudwatch)

AWSのモニタリングサービス。

## CloudWatch Metrics

AWSの各サービスの数値情報をモニタリングできる。カスタムメトリックスとして独自の数値情報を送信することも可能。その際は、PutMetricDataを使用する。

基本は1分、カスタムメトリックスの高解像度を利用して最短で1秒でメトリックスデータを生成する。

## CloudWatch Alarms

CloudWatch Metricsをモニタリングしてアラームを発行可能。条件を指定して自動アクションの実行が可能。

アラームの状態は`OK`（定義された閾値を下回っている）、`ALARM`（定義された閾値を上回っている）、`INSUFFICIENT_DATA`（判定不能。データ不足のため状態を判定できない）の三種類。

## CloudWatch Logs

AWSおよび構築したシステムのログファイルの監視、保存、アクセス。エージェント経由でログメッセージをCloudWatchエンドポイントに転送。S3へのログのエクスポートが可能。

エージェント経由でログを送信する場合、IAMロールには以下二つのAWS管理ポリシーのアタッチが必要。

- CloudWatchAgentAdminPolicy
    - セットアップが終わった後はCloudWatchAgentServerPolicyに変更する
- AmazonSSMManagedInstanceCore

# VPCフローログ

VPCないのElastic Network Interface（ENI）を通過するIPトラフィックに関する情報（送信元IPアドレス・ポート、送信先IPアドレス・ポート、プロトコル、転送パケット数、バイト数、許可／拒否）をキャプチャできる機能。
S3やCloudWatch Logsへ出力することができる。

# [AWS CloudTrail](https://www.slideshare.net/AmazonWebServicesJapan/20210119-aws-black-belt-online-seminar-aws-cloudtrail)

AWSアカウント上で行われたAPIアクションのほとんどを記録。ログをS3バケットに保存する。

# [AWS X-Ray](https://www.slideshare.net/AmazonWebServicesJapan/20200526-aws-black-belt-online-seminar-aws-xray)

アプリケーションの潜在的なバグを特定したり、パフォーマンスのボトルネックを特定できる。

Lambdaにおいては、アクティブトレースを有効にすることではじめられる。必要なIAMロールは以下2つのアクション。

- xray:PutTraceSegments
- xray:PutTelemetryRecords

X-Ray SDKがあり、これを埋め込むことで、AWSの各サービスの呼び出しやHTTP/HTTPSリクエスト、データベースへのリクエストなどがトレースされる。

トレースの構成要素であり、個々のサービスに対応するものを「セグメント」と呼ぶ。また、セグメントの構成要素であり、ここのリモートコールやローカル処理に対応するのを「サブセグメント」と呼ぶ。

AWS X-RayデーモンはUDPポート2000でトラフィックをリッスンし、未加工のセグメントデータを収集して、AWS X-Ray APIに中継するソフトウェアアプリです。

## Lambdaデバッグに必要な環境変数

以下の3つの環境変数。[ドキュメントはこちら](https://docs.aws.amazon.com/ja_jp/ja_jp/lambda/latest/dg/configuration-envvars.html#configuration-envvars-runtime)。

- _X_AMZN_TRACE_ID
    - X-Rayトレースヘッダー
- AWS_XRAY_CONTEXT_MISSING
    - X-Rayトレースの場合、LambdaはX-Ray SDKからランタイムエラーがスローされ内容にLOG_ERRORに設定する。
- AWS_XRAY_DAEMON_ADDRESS
    - X-Rayトレーシングの場合、X-RayデーモンのIPアドレスとポート
