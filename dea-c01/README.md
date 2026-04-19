# AWS GlueのApache Spark ETLジョブにて「デバイスに空き容量がありません」

- Spark UIとAWS Glueメトリクスを使用して、Sparkエグゼキューたのデータスキューをモニタリングする
- `--write-shuffle-files-to-s3`ジョブパラメータを有効にし、ソルティング手法を使用する

# AWS GlueによるIcebergテーブルのパフォーマンスアップ

- AWS Glueデータカタログを使用して、スケジュールに基づいてIcebergテーブルの列レベルの統計を生成する
- AWS Glueデータカタログを使用して、Icebergテーブルを自動的にコンパクションする

# AWS DataSync

オンプレミスのストレージシステムとAWSのストレージサービスとの間で大規模なデータを高速かつ安全に移動させるためのサービス

# EMR

## EMR ステップのランタイムロール

EMRステップごとに個別のIAMロール（ランタイムロール）を指定することで、ジョブ単位でAWSリソースへのアクセス権限を決め細かく制御できる

# Amazon Kinesis Data Streams vs Amazon Data Firehose

| 比較項目 | Amazon Kinesis Data Streams (KDS) | Amazon Data Firehose (ADF) |
| :--- | :--- | :--- |
| **主な用途** | **リアルタイム処理・カスタム分析**<br>ミリ秒単位の応答が必要なアプリ基盤 | **データロード・アーカイブ**<br>ストレージや分析基盤への自動転送 |
| **データ変換** | **コンシューマー側で実装**<br>読み取るアプリケーション側でロジックを記述 | **Lambda統合による自動変換**<br>配信過程で形式変換や加工が可能 |
| **配信・運用管理** | **シャードによる管理**<br>オンデマンドモードまたはプロビジョニングが必要 | **フルマネージド（サーバーレス）**<br>スケーリングやリトライもすべて自動 |
| **レイテンシ** | リアルタイム（通常200ms未満） | ニアリアルタイム（最小60秒または1MB） |
| **データの再利用** | **可能**<br>複数のコンシューマーが同じデータを読み取れる | **不可**<br>一度配信先へ送るとFirehose内には残らない |
| **主な宛先** | Lambda, KCL(EC2/ECS), Managed Service for Apache Flink | S3, Redshift, OpenSearch, Splunk, HTTP Endpointなど |

# Amazon AppFlow

Salesforce、SlackなどのさまざまなSaaSアプリとS3やRedshiftなどのAWSサービス間でデータ転送を安全に自動化するフルマネージドサービス

# Amazon Redshift Spectrum

S3上のデータをRedshiftに取り組みすることなく、直接クエリする機能

# Apache Hudi

データレイク（S3やHDFSなど）上でデータベースのようや更新や削除およびACIDトランザクションを実現するためのオープンソースストレージフレームワーク
