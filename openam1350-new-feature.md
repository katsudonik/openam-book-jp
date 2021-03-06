### OpenAM 13.5.0新機能

2016年7月16日にOpenAM 13.5.0がリリースされました。このリリースでは、以下の機能が追加されています。

* プッシュ認証(パスワードレス多要素認証)  
AppleやGoogleのプッシュ通知サービスを利用したプッシュ通知認証によるパスワードレスな多要素認証が実現できるようになりました。

* 新しい楕円曲線デジタル署名アルゴリズムのサポート  
JWSの署名アルゴリズムとしてRFC 7518(JSON Web Algorithms specification)に規定されているECDSAを追加しました。

* JCEKSキーストア  
デフォルトのキーストアをJKSからJCEKSに変更しています。これにより、ロードバランサの背後に複数のOpenAMサーバーが配備されているサイト構成において、他のOpenAMサーバーで生成されたJWTを復号化し、検証するといったことが可能になります。

* トランザクションIDヘッダの導入  
ForgeRockプラットフォーム全体でトランザクションIDを伝播するための、HTTPヘッダーの付加機能をサポートしました。

* OAuth 2.0/OpenID Connect1.0の機能強化  
JWTを使用したステートレス機能などOAuth 2.0/OpenID Connect 1.0に関する様々な機能を強化しています。

* 性能改善  
性能改善のためのいくつかのオプションの追加しています。

* 継続的なXUI化  
11.0から始まったXUIリプレースの適用範囲の拡大しています。

* ポリシーのエクスポート/インポート用のssoadmコマンド  
JSON形式でポリシーをエクスポート/インポートするためのssoadmコマンドが提供されました。

* ElasticsearchとJMS用の監査イベントハンドラ  
ElasticsearchとJMSへの監査ログの出力機能を追加しています。ユーザーと管理者のアクティビティログを、ElasticsearchやApache ActiveMQなどのJMSブローカーに送信することが可能です。
