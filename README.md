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

# [AWS CodeCommit](https://www.slideshare.net/AmazonWebServicesJapan/20201020-aws-black-belt-online-seminar-aws-codecommit-aws-codeartifact)

Gitリポジトリを提供するサービス。AWSCodeCommitPowerUserという権限がある。ソースコードが更新されたなどのタイミングでAmazon SNSやAWS Chatbotに通知を飛ばしたりAWS Lambdaの関数を実行するイベントを設定できる。

# [AWS CodeBuild](https://www2.slideshare.net/AmazonWebServicesJapan/20201125-aws-black-belt-online-seminar-aws-codebuild)

ビルドプロセスを提供。`buildspec.yml`に処理内容を記載する。

ソースはS3やCodeCommit、GitHub（含むEnterprise）、Bitbucket。

ローカルえージョンとをインストールすることで実行することが可能。

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
