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



