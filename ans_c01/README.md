## 1. VPCの基本と基盤
*   **VPCコンポーネント**: サブネット、ルートテーブル、インターネットゲートウェイ(IGW)の役割を理解すること。
*   **IPアドレス**: プライベート、パブリック、Elastic IPの違い。特に、VPC外の通信におけるNATゲートウェイとNATインスタンスの使い分け。
*   **ENI (Elastic Network Interface)**: インスタンスにアタッチされる仮想ネットワークインターフェース。
*   **セキュリティ**:
    *   **Security Groups**: インスタンスレベルのステートフルなファイアウォール。
    *   **Network ACLs**: サブネットレベルのステートレスなファイアウォール。

## 2. DNS と DHCP (Amazon Route 53)
*   **Amazon Route 53 Resolver**:
    *   VPCのベースアドレスに「+2」したIP（または `169.254.169.253`）で動作する。
    *   PHZ（プライベートホストゾーン）、VPC内部DNS、パブリックDNSの名前解決を処理する。
*   **VPC DNS 属性**:
    *   `enableDnsSupport`: AWS提供のDNSサーバーへのクエリを有効にする。
    *   `enableDnsHostname`: インスタンスにパブリックホスト名を割り当てる。PHZを使用するには両方の属性を `true` にする必要がある。
*   **DHCPオプションセット**:
    *   ドメイン名、ネームサーバー、NTPサーバーなどを定義する。
    *   **一度作成すると編集不可**。変更には新規作成とVPCへの再関連付けが必要。
*   **Route 53 Resolver Endpoints**:
    *   **Inbound**: オンプレミスからAWS内の名前解決を行う。
    *   **Outbound**: AWSからオンプレミスのDNSサーバーへ条件付き転送（Conditional Forwarding）を行う。
    *   VPNやDirect Connect経由で通信し、VPC内のENIとして実装される。

## 3. 負荷分散 (Elastic Load Balancing)
*   **Application Load Balancer (ALB)**:
    *   レイヤー7（HTTP/HTTPS）で作動。パスベースやホストベースのルーティングをサポート。
    *   **SNI (Server Name Indication)** を使用して、1つのリスナーで複数のSSL証明書を管理可能。
*   **Network Load Balancer (NLB)**:
    *   レイヤー4（TCP/UDP/TLS）で作動。数百万リクエスト/秒の超高性能。
    *   **各AZに1つの固定IP（またはEIP）**を持ち、ホワイトリスト登録に適している。
    *   **Zonal DNS**: 各AZのノードごとに特定のDNS名を持つ。
*   **Gateway Load Balancer (GWLB)**:
    *   レイヤー3で作動し、サードパーティ製仮想アプライアンスを透明に挿入する。
    *   **GENEVEプロトコル**（UDP 6081）を使用してトラフィックをカプセル化する。
*   **交差ゾーン負荷分散 (Cross-Zone Load Balancing)**:
    *   ALBはデフォルトで有効（無料）、NLB/GWLBはデフォルトで無効（有効化にはAZ間データ転送料金が発生）。
*   **Proxy Protocol**: ロードバランサーが接続を終了する場合に、送信元/送信先のIP情報を保持するために使用（NLB/CLBで利用可能）。

## 4. Route 53 高度な機能
*   **エイリアスレコード (Alias Records)**:
    *   CNAMEと違い、**Zone Apex（ドメイン名そのもの）に使用可能**。
    *   AWSリソース（ELB, S3, CloudFront等）のIP変更を自動認識する。
*   **ルーティングポリシー**:
    *   **Weighted（加重）**: 割合（%）で分散。A/Bテスト等に利用。
    *   **Latency-based（レイテンシー）**: 遅延が最小のリージョンへ誘導。
    *   **Failover（フェイルオーバー）**: アクティブ/パッシブ構成。正常なリソースへ切り替える。
    *   **Geolocation（位置情報）**: ユーザーの場所（大陸・国）に基づく。
    *   **Geoproximity（近接地域）**: ユーザーとリソースの物理的距離に基づく。バイアス値で範囲調整が可能。
*   **ヘルスチェック**:
    *   パブリックリソースにはHTTP/HTTPS/TCPチェックを使用。
    *   **プライベートリソース**: CloudWatchアラームと連携したヘルスチェックを使用する。

## 5. ハイブリッド接続とネットワーク拡張
*   **AWS Direct Connect (DX)**:
    *   1/10/100 Gbpsの専用線、または1Gbps未満のホスト接続。
    *   **BGP**プロトコルでルート交換。802.1Q VLANタグでトラフィックを分離。
    *   **接続タイプ**:
        *   **専用接続 (Dedicated Connection)**: 1, 10, 100 Gbps のポート速度を提供し、ユーザーが物理ポートを占有します。
        *   **ホスト接続 (Hosted Connection)**: 50 Mbps から最大 10 Gbps の速度をAWSパートナー経由で提供します。
    *   **仮想インターフェイス (VIF) の種類**:
        *   **Public VIF**: S3やDynamoDBなどのAWSパブリックサービスへのグローバルな接続を可能にします。BGPセッションで最大1000個のプレフィックスを受信可能です。
        *   **Private VIF**: VPC内部のリソース（EC2, RDS等）への接続用です。1つのVIFで最大100個のプレフィックスをAWS側にアドバタイズできます。
        *   **Transit VIF**: **Transit Gatewayと接続するために必須**のVIFです。
    *   **Direct Connect Gateway (DXGW)**:
        *   複数のリージョンや複数のAWSアカウントにまたがるVPC/TGWへの接続を統合するグローバルなリソースです。
        *   **Public VIFとの統合はできません**。VPC（Private VIF経由）またはTGW（Transit VIF経由）への接続にのみ使用されます。
    *   **SiteLink**: Direct Connectのロケーション間をAWSネットワーク経由で直接通信させ、オンプレミス拠点間を接続する機能です。
*   **Transit Gateway**
    *   **主なアタッチメント**:
        *   VPC、VPN、Direct Connect Gateway、Transit Gateway間のピアリング接続などが含まれます。
    *   **ルート制御**:
        *   **ルートテーブル**: 各アタッチメントは1つのルートテーブルに関連付けられ、トラフィックの転送先を決定します。
        *   **ルート伝播 (Propagation)**: VPCなどの接続先から自動的にルートをTGWルートテーブルに学習させることができます。
    *   **Transit Gateway Connect**:
        *   SD-WANなどのサードパーティ製仮想アプライアンスとTGWを接続するためのアタッチメントです。
        *   **GREトンネルプロトコル**をサポートし、トンネルあたり最大5 Gbpsの帯域幅を提供します（複数トンネルで拡張可能）。
        *   動的ルーティングのために**BGP**が必須要件となります。
    *   **Appliance Mode**: TGWにおいて、ステートフルなセキュリティアプライアンス（GWLB経由など）を通るトラフィックの非対称ルーティングを防ぐために有効化します。
* **DX と Transit Gateway の統合**
    *   **接続フロー**: **Transit VIF** → **Direct Connect Gateway** → **Transit Gateway** の順に接続されます。
    *   **ASN (自治システム番号) の制限**:
        *   DX Gateway と Transit Gateway の **ASNは必ず異なるものである必要があります**。両方にデフォルトの `64512` を使用すると関連付けに失敗します。
    *   **拡張性**:
        *   1つの専用接続 (Dedicated Connection) で最大4つの Transit VIF をサポートします。
        *   1つの Direct Connect Gateway に最大 **6つの Transit Gateway** をアタッチできます。
    *   **パフォーマンス**:
        *   Transit VIF では、MTU 1500 および **8500 (ジャンボフレーム)** をサポートしています。
        *   高速な障害検知のために **BFD (Bidirectional Forwarding Detection)** を使用でき、約1秒でのフェイルオーバーが可能です。
*   **VPCエンドポイント**:
    *   **Gateway Endpoints**: S3とDynamoDB用。ルートテーブルのターゲットとして設定。
    *   **Interface Endpoints (PrivateLink)**: 多くのAWSサービス用。ENIとして実装され、**プライベートDNS**を有効にすることでデフォルトDNS名を解決可能。
*   **VPC Lattice**: サービス間通信の簡素化。複雑なピアリングやTGWなしでサービスを公開・発見できる。
*   **AWS Cloud WAN**: 複数のリージョンにまたがるグローバルネットワークをNetwork Managerで一元管理・可視化する。
* **Site-to-Site VPN**
    * オンプレミスとAWS VPCをIPSecで安全に接続するサービス。接続先はVirtual Private GatewayかAWS Transit Gateway
        * 最近はTransit Gatewayが主流（1 VPC = 1 Virtual Private Gatewayなので）
    * AWSは自動で2本のトンネルを提供。冗長性確保のため。オンプレ側も2つのPeer設定が必要。
    * BGP（動的ルーティング）を推奨。
* **ルーティングとBGP**
    * **BGP経路選択アルゴリズム**
        1. 最長一致
        2. Local Preference高い
        3. AS_PATH短い
        4. MED低い
        5. eBGP > iBGP
## 6. パフォーマンスと最適化
*   **AZ間データ転送**: 同一AZ内は無料だが、AZをまたぐと料金が発生する（NLBの交差ゾーン負荷分散など）。
*   **コスト最適化**: Spoke VPCからHub VPCへのインターフェースエンドポイントへのアクセス時、 inter-AZデータ転送料を避けるためにAZ固有のDNSエンドポイントを使用する戦略。

## 7. セキュリティとトラフィック制御
* **AWS Network Firewall**
    * フルマネージド型のL3〜L7対応ステートフルファイアウォール
* **AWS WAF**
    * L7フィルタリング

## 8. マルチリージョン設計
* **AWS Global Accelerator**
    * AWSのグローバルAnycastネットワークを利用して、世界中のユーザー、複数リージョンのアプリケーションへ最適経路でルーティングするL4サービス
    * 固定IPが必要な場合によく選択される。（2つの固定IPアドレスが提供される）
## 9. モニタリング・トラブルシュート
* **VPC Flow Logs**
    * VPC愛のIPトラフィックのメタデータを記録する
* **Traffic Mirroring**
    * ENIのトラフィックをコピーして別のインスタンスへ送信
* **Reachability Analyzer**
    * AWS内部ルーティングの論理的な到達可否を解析。通信は実際には流さない。
