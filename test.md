AWS Organizations の基本構造
[AWS]Organizations におけるマルチアカウント構成のベストプラクティスについて
AWS Organizations の OU 設計のベストプラクティスを学ぶ | DevelopersIO

AWS IAM Identity Center で IAM アカウントを統一する

AWS アカウントのサインインに「IAM Identity Center」をお勧めする理由

「AWS で複数環境運用のベストプラクティスを紹介！マルチアカウントで安全に環境を運用しましょう 😎」

AWS Organizations のユースケースで学ぶ AWS アカウント管理のベストプラクティス | DevelopersIO

AWS Organizations は複数の AWS アカウントを一元管理でき、セキュリティ、アクセス、請求の境界を提供しながら、リソースの独立性と分離を実現します Amazon Web Services。
主要な 3 つのコンポーネント

1. AWS Organizations - すべてのアカウントを一元管理し、組織フォルダ（OU）を最大 5 階層まで作成可能で、一括請求やサービスコントロールポリシー（SCP）の適用ができます Ragate
2. AWS IAM Identity Center（旧 AWS SSO） - 複数の AWS アカウントに対してシングルサインオンができ、各アカウントに IAM ユーザーを作成する必要がなくなります Ragate
3. Service Control Policies（SCP） - 組織全体にセキュリティポリシーを適用可能
   OU（組織単位）の設計ベストプラクティス
   OU は会社の組織構造ではなく、機能や共通のコントロールに基づいて設計すべきです AWS。推奨される基本的な OU は：

- Security OU: セキュリティサービス用で、ログアーカイブ、セキュリティ読み取り専用アクセス、セキュリティツール、ブレークグラスのアカウントを作成 AWS
- Infrastructure OU: ネットワークや IT サービスなど共有インフラストラクチャサービスに使用 AWS
- Workloads OU: 本番環境、開発環境などの実際のワークロード用
- Suspended OU: 利用停止されたアカウントを格納し、すべてのアクションを拒否する SCP を適用 ClassMethod
- Policy Staging OU: 本番環境に影響しない方法で、OU の設定をテストしポリシーの適用結果を確認するための場所 Amazon Web Services
  アカウント管理のベストプラクティス
  管理アカウント
  管理アカウントは管理タスクにのみ使用し、すべての AWS リソースは組織内の他のアカウントに保存すべきです AWS。理由は：
- 管理アカウントには SCP を適用できない
- 請求が理解しやすくなる
  メンバーアカウント
  アカウントの分離基準は「プロダクト/サービスごと」かつ「環境（本番/開発）ごと」が推奨されます Zenn
  ルートアクセス管理を有効にして、メンバーアカウントのルートユーザー認証情報のモニタリングと削除を行い、ルートユーザー認証情報の復旧を防ぎます AWS
  ユーザー追加の推奨方法
  マルチアカウント構成の最大のデメリットは、AWS アカウントごとの IAM ユーザー認証情報の管理が煩雑になることです。そのため、AWS IAM Identity Center を用いて認証/認可の管理を 1 つの AWS アカウントに集約するのがベストプラクティスです Zenn
  IAM Identity Center でのユーザー追加手順

1. IAM Identity Center の有効化
   - 管理アカウントで IAM Identity Center を有効化
   - 東京リージョンなど任意のリージョンを選択
2. グループの作成 グループを作成し（例：accountA-admin、accountB-readonly）、そこにユーザーを追加します Zenn
3. ユーザーの追加 ユーザー名（1 ～ 100 文字）を設定し、パスワード設定手順のメールを送信するか、ワンタイムパスワードを生成します。メールアドレスは一意である必要があります AWS
4. アクセス権限セットの作成 IAM Identity Center コンソールでアクセス権限セットを作成し、ユーザーやグループに AWS アカウントへのアクセス許可を付与します AWS re:Post
5. アカウントへの割り当て \* グループと権限セットを各 AWS アカウントに割り当て
     google chat
   https://dev.classmethod.jp/articles/google-hangouts-chat-integration-with-aws-lambda/

lambda 内容
AWS】AWS からのお知らせ通知をチャットで受け取る方法！

https://dev.classmethod.jp/articles/notify-aws-billing-for-cloudformaiton/

https://tech.omablo.com/archives/928

import json
import urllib.request

def lambda_handler(event, context):
message_detail = event['Records'][0]['Sns']['Message']
message = {'text': message_detail}
url = '＜ Google Chat の Webhook URL ＞'
headers = {'Content-Type': 'application/json; charset=UTF-8'}
byte_encoded = json.dumps(message).encode('utf-8')
req = urllib.request.Request(url=url, data=byte_encoded, headers=headers)
response = urllib.request.urlopen(req)

CloudWatch アラームを Lambda で Google Chat に投稿する

https://qiita.com/Yusuke0122/items/a9177e4bb156626a19d3

リソースが全部消えているか確認
全リージョンから全リソースを一括検索する方法【タグエディター】

AWS
請求ダッシュボード

Cloud trail 関連
AWS CloudTrail の料金体系とコストアラートからユーザー特定に関する記事を検索しますね。次に、コストアラートから使用ユーザーを特定する手順について検索します。次に、CloudTrail ログからユーザーを特定する方法について検索します。CloudTrail の料金体系とコストアラートからユーザー特定の手順に関する記事をご紹介します。
CloudTrail の料金体系について
CloudTrail の基本的な料金体系は、各リージョンの管理イベントの最初のコピーは無料で配信され、2 つ目以降の証跡には料金が発生します。詳しくは以下の記事が参考になります：

1. 【基礎から学ぶ】AWS CloudTrail の課金体系をまとめてみた https://blog.serverworks.co.jp/tech/2017/10/20/cloudtrai-cost/
   - 証跡の設定方法による課金の違いや、具体的な計算例が丁寧に解説されています
2. AWS 公式: CloudTrail の料金 https://aws.amazon.com/jp/cloudtrail/pricing/
   - CloudTrail Lake のデータインジェスト、保持、分析の料金体系が詳しく記載されています
3. AWS 公式: CloudTrail 証跡のコスト管理 https://docs.aws.amazon.com/ja_jp/awscloudtrail/latest/userguide/cloudtrail-trail-manage-costs.html \* コスト効率の良い設定方法が説明されています
   コストアラートから使用ユーザー特定の手順
   CloudTrail の推定請求額の監視には Amazon CloudWatch の請求アラームまたは AWS Budgets を使用し、コスト増加時の原因特定には以下の手順が一般的です：
4. Cost Explorer でコスト確認 https://docs.aws.amazon.com/ja_jp/awscloudtrail/latest/userguide/cloudtrail-costs.html
   - 使用タイプでグループ化してどの CloudTrail 機能にコストがかかったか確認できます
5. Athena でユーザー特定   基本的な手順を解説した記事：
   - CloudTrail のログを Athena を使って調査してみる https://dev.classmethod.jp/articles/cloudtrail-athena/
     - useridentity フィールドを確認することで API を実行したユーザーを特定できます
   - Athena テーブルクエリを使用して CloudTrail ログを検索する https://repost.aws/ja/knowledge-center/athena-tables-search-cloudtrail-logs
     - CloudTrail の月ごとのコスト増加を特定するクエリ例が紹介されています
6. 具体的なクエリ例の記事：
   _ Amazon Athena で特定の IAM ユーザーアクセスキーを使用したリクエストを抽出 https://techblog.nhn-techorus.com/archives/22828
   _ アクセスキーからユーザーを特定する方法が解説されています
   コスト急増の実例記事（参考）
   CloudTrail Lake や CloudTrail Insights を使用する際は、証跡ログのサイズを事前に確認し、コスト監視を適切に設定することが重要です：

- 4 日で$62,000 請求されそうになった話(CloudTrail Lake) https://qiita.com/nix/items/c41796997dbcc553c799
- AWS CloudTrail Insights でガツンと請求された話し https://qiita.com/nix/items/f3127c427f1603082d5f
  これらの記事を参考に、CloudTrail の料金体系を理解し、コストアラート時には適切にユーザーを特定できるようにしておくことをお勧めします。

AWS CUR の使い方について調べますね。AWS CUR（Cost and Usage Report / コストと使用状況レポート）の使い方について、役立つ記事をいくつかご紹介します。
公式ドキュメント
AWS CUR は、AWS の最も包括的なコストと使用状況データを提供し、所有する S3 バケットに CSV 形式で 1 日 1 回更新されます。
AWS 公式ドキュメント（日本語）:

- AWS コストと使用状況レポートとは - CUR の概要説明
- レポートの作成 - CUR の作成手順
  実践的な解説記事
  初心者向け:
- AWS Cost and Usage Reports(CUR)を作成してみた - Qiita \* マネジメントコンソールからの CUR 作成手順をスクリーンショット付きで解説
  実践的な活用例:
- CUR による AWS コスト構造の継続的モニタリング - Zenn
  - Glue や Athena を使って CUR データを分析し、コスト配賦を実現する実装例を紹介
- AWS コストと使用状況レポート(CUR)を分析しやすく可視化 - New Relic \* S3 に出力された CUR を Lambda 経由でログとして取り込み、可視化する方法を解説
  これらの記事を参考にすれば、CUR の作成から活用まで理解できると思います！

AWS CUR を Google Chat に送る方法について調べますね。AWS CUR を Google Chat に送る方法についてご説明します。直接的な CUR 連携の記事は見つかりませんでしたが、一般的な AWS から Google Chat への通知方法を応用できます。
基本的な構成
S3 → Lambda → Google Chat という構成が基本になります。

1. Google Chat の Webhook URL を作成
1. Google Chat でスペース（チャットルーム）を開く
1. スペース名の横の矢印 → 「Webhook を管理」をクリック
1. Webhook を追加し、URL を取得
1. Lambda 関数を作成
   Python で Google Chat の Webhook URL に POST リクエストを送信する Lambda 関数を作成します。
   import json
   import urllib.request

def lambda_handler(event, context): # S3 イベントから CUR ファイル情報を取得
bucket = event['Records'][0]['s3']['bucket']['name']
key = event['Records'][0]['s3']['object']['key']

    message_detail = f'新しいCURレポートが生成されました\nバケット: {bucket}\nファイル: {key}'
    message = {'text': message_detail}

    url = '【Google ChatのWebhook URL】'
    headers = {'Content-Type': 'application/json; charset=UTF-8'}
    byte_encoded = json.dumps(message).encode('utf-8')

    req = urllib.request.Request(
        url=url,
        data=byte_encoded,
        headers=headers
    )
    response = urllib.request.urlopen(req)
    return response.read()

3. S3 トリガーを設定
1. Lambda 関数に「トリガーを追加」
1. トリガー: S3 を選択
1. バケット: CUR が出力される S3 バケットを指定
1. イベントタイプ: s3:ObjectCreated:\*
1. プレフィックス/サフィックス: 必要に応じて設定
   参考記事
   基本的な Lambda と Google Chat の連携については以下が参考になります：

- AWS Lambda から Google Hangouts Chat に投稿してみた - DevelopersIO
- GoogleChat のチャットルームへメッセージを投稿する Lambda 関数
  この構成で、CUR が S3 に出力されるたびに Google Chat に通知が送られるようになります！

AWS の Organizations で適切な権限を付与する方法について説明します。
基本的なアプローチ

1. Service Control Policies (SCP)の活用
   SCP は組織内のアカウントに対する権限の上限を設定します。
   推奨される設計方針:

- デフォルト拒否アプローチ: 必要な権限のみを明示的に許可
- 段階的な適用: OU（組織単位）ごとに適切な SCP を適用
- 最小権限の原則: 必要最小限の権限のみを付与
  SCP の例:
  {
  "Version": "2012-10-17",
  "Statement": [
  {
  "Effect": "Deny",
  "Action": [
  "organizations:LeaveOrganization"
  ],
  "Resource": "\*"
  }
  ]
  }

2. 組織構造の設計
   Root
   ├── Security OU
   │ └── セキュリティ関連アカウント
   ├── Production OU
   │ └── 本番環境アカウント
   ├── Development OU
   │ └── 開発環境アカウント
   └── Sandbox OU
   └── 実験用アカウント
   各 OU に応じた SCP を適用することで、環境ごとに異なる制限を設けます。
3. 具体的な権限設定のベストプラクティス
   本番環境（Production OU）:

- リージョン制限（必要なリージョンのみ許可）
- 高リスクなサービスの制限
- MFA 必須化
- ルートユーザーの使用制限
  開発環境（Development OU）:
- コスト制限
- 特定サービスへのアクセス制限
- リソースタグの必須化
  セキュリティアカウント:
- CloudTrail、GuardDuty、Security Hub の設定変更禁止
- ログの削除禁止

4. IAM との組み合わせ
   SCP はあくまで「上限」を設定するものです。実際の権限付与は各アカウント内の IAM で行います。
   実際の権限 = SCP で許可された範囲 ∩ IAM で許可された範囲
5. 推奨される具体的な SCP 例
   リージョン制限:
   {
   "Version": "2012-10-17",
   "Statement": [
   {
   "Effect": "Deny",
   "Action": "_",
   "Resource": "_",
   "Condition": {
   "StringNotEquals": {
   "aws:RequestedRegion": [
   "ap-northeast-1",
   "us-east-1"
   ]
   }
   }
   }
   ]
   }
6. 管理のポイント

- 段階的なロールアウト: 一度にすべての SCP を適用せず、影響を確認しながら段階的に
- テスト環境での検証: 本番適用前に必ずテスト
- ドキュメント化: どの OU にどの SCP が適用されているか明確に記録
- 定期的なレビュー: 権限設定が適切か定期的に見直し

7. AWS Control Tower の活用
   より高度な管理が必要な場合は、AWS Control Tower の利用も検討してください。ガードレールと呼ばれる事前定義されたポリシーセットが利用できます。
   具体的な要件があれば、より詳細な SCP の例や OU 構造の設計についてアドバイスできますので、お知らせください。

AWS Control Tower について、詳細と参考情報を調べてお伝えします。## AWS Control Tower の詳細解説
AWS Control Tower について、詳細情報と参考記事をまとめてご説明します。
AWS Control Tower とは
AWS Control Tower は、セキュリティとコンプライアンスのベストプラクティスに基づいた、マルチアカウント AWS 環境を自動でセットアップするマネージドサービスです。AWS Organizations の機能を拡張し、ベストプラクティスからの逸脱を防ぐためにコントロール（ガードレール）を適用します。
主要な機能

1. ランディングゾーン（Landing Zone）
   ランディングゾーンは、セキュリティとコンプライアンスのベストプラクティスに基づく、優れたアーキテクチャ設計の複数アカウント環境です。
   自動的に作成されるアカウント:

- 管理アカウント（Management Account）: 組織全体を管理
- 監査アカウント（Audit Account）: すべてのアカウントの監査を実施
- ログアーカイブアカウント（Log Archive Account）: すべてのアカウントのログを集約・管理

2. コントロール（ガードレール）
   コントロールは、AWS 環境全体に継続的なガバナンスを提供する高レベルのルールで、予防コントロール、検出コントロール、プロアクティブコントロールの 3 種類があります。
   コントロールの種類:

- 予防的コントロール（Preventive）: ポリシー違反につながるアクションを禁止するため、アカウントがコンプライアンスを維持できます。SCP を使用して実装されます
- 検出的コントロール（Detective）: アカウント内のリソースの非準拠を検出し、ダッシュボードを通じてアラートを提供します。AWS Config を使用して実装されます
- プロアクティブコントロール（Proactive）: AWS CloudFormation Hooks を活用し、CloudFormation によってプロビジョニングされる前にコンプライアンスに違反しているリソースを未然に特定しブロックします
  ガイダンスのカテゴリ:
- 必須（Mandatory）: 新しいランディングゾーンを作成すると、AWS Control Tower がすべての必須ガードレールをデフォルトで有効にします
- 強く推奨（Strongly Recommended）
- 選択的（Elective）
  AWS Control Tower では、400 以上のプリセットから要件を選択してコントロール設定ができます。

3. Account Factory
   Account Factory は、事前に承認されたアカウント設定で新規アカウントのプロビジョニングを標準化するのに役立つ、設定可能なアカウントテンプレートです。
4. ダッシュボード
   ダッシュボードでは、中央のクラウド管理者のチームがランディングゾーンを継続的に監視でき、プロビジョニングされているアカウント、ポリシーの適用に対して有効にされているコントロール、非準拠リソースなどを確認できます。
   ベストプラクティス
   コントロール適用のベストプラクティス
   3 種類すべてのコントロールを組み合わせて有効化する多層防御アプローチをとることで、多層的で回復力のあるコンプライアンス態勢を確立できます。
   推奨される実装パターン:
5. 段階的な導入: ガードレール自体一度設定すれば変更することも少ないので、適用に時間がかかる点はそれほど気になりません
6. コンプライアンスフレームワークとの連携: NIST 800-53、CIS AWS Benchmark、PCI-DSS などのコンプライアンスフレームワークごとに統制をグループ化し、特定のコンプライアンス目標を達成するコントロールを簡単に有効化できます
7. 自動修復の実装: AWS Control Tower の検出コントロールと AWS Systems Manager Automation を組み合わせて活用することで、セキュリティイベントの検出と修復を自動化できます
8. カスタムコントロールの追加: AWS Control Tower の管理するコントロールとは別に、追加のコントロールが必要な場合は、サービスコントロールポリシー（SCP）やカスタム AWS Config ルールなどのリソースを活用して追加のポリシーを定義できます
   メリット
9. セットアップの自動化: ベストプラクティスに則った環境を構築でき、導入後も自社にあわせたカスタマイズや調整は必要なものの、セットアップの自動化、ガードレール設定のサポートなどで効率的に運用できます
10. セキュリティとコンプライアンスの強化: ガードレールによるルール適用で、ポリシー違反やリスクを事前に防止できます
11. 運用負荷の軽減: 複数アカウントを一元管理できるため、運用コストを削減できます
    注意点
12. コスト: Control Tower 自体に追加料金はかかりませんが、ランディングゾーン、必須コントロール設定のために構成した AWS サービス（AWS Service Catalog、CloudTrail、Config、CloudWatch、SNS、S3、VPC など）のコストが発生します
13. カスタマイズの必要性: 企業によってランディングゾーンに求められる要件は異なるため、自社の要件とマッチしない点はカスタマイズする必要があります
14. 既存アカウントの取り込み: 既存の AWS アカウントを Control Tower に登録する際には、いくつかの制約や準備作業が必要です
    参考記事
    実装方法や詳細については、以下の記事が参考になります:

- 公式ドキュメント: AWS Control Tower ユーザーガイド
- ベストプラクティス: AWS Control Tower でコントロールを適用する際のベストプラクティス
- 実装例: Control Tower を使ったガードレール展開方法 | DevelopersIO
- 基礎解説: AWS Control Tower を基本から理解する | クラスメソッド
  AWS Control Tower は、マルチアカウント環境を適切に管理し、セキュリティとコンプライアンスを維持するための強力なツールです。特に組織が成長段階にある場合や、複数のプロジェクトやチームで AWS を利用する場合に有効です。
