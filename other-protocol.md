## OpenAMが対応するフェデレーションプロトコル

OpenAMは、あらゆるサービスへのSSOを実現できるように、これまで多くのフェデレーションプロトコルに対応してきました。SAML 1.0の実装から始まり、WS-Federation 1.1やID-FF 1.2などの多くのプロトコルを実装してきました。さらにOpenAM 10.0.0ではOAuth 2.0を、OpenAM 11.0.0ではOpenID Connect 1.0を実装しています。

図. OpenAMのフェデレーションプロトコル対応状況（出典：http://www.slideshare.net/ForgeRock/openam-an-introduction）

![図. OpenAMのフェデレーションプロトコル対応状況](http://image.itmedia.co.jp/ait/articles/1406/13/openam04_fig01.png)

OpenAMが対応している主なフェデレーションプロトコルとその概要は、以下の通りです。

|プロトコル|概要|
|---|---|
|SAML 1.0 / 1.1 / 2.0|標準化団体OASIS Openによって策定された、認証情報を安全に交換するためのXMLベースのプロトコル。歴史あるプロトコルのため、多くのアイデンティティ管理ベンダーによって実装されており、提供されているサービスやソフトウェアが多い。Google AppsやSalesforceなどのクラウドサービス、学認（Shibboleth）などとの連携が可能。|
|Liberty ID-FF 1.1 / 1.2|Liberty Allianceによって策定されたフェデレーションプロトコル。SAML 1.0をベースに仕様が策定されたが、最終的にSAML 2.0に統一されている。|
|WS-Federation 1.1|標準化団体OASIS Openによって策定されたフェデレーションプロトコル。Webサービス向けのセキュリティ仕様「WS-Security」の構成要素。マイクロソフトなどが主な推進者。Active Directory Federation Serviceなどの、マイクロソフトが提供するサービスやソフトウェアと連携する場合に利用されることが多い。|
|OAuth 2.0|標準化団体IETFによって策定された、WebサービスのAPIへのアクセスを認可する方法を定めたプロトコル。認証ではなく、認可（どのリソースにアクセスできるか）について規定している点で他のプロトコルとは異なる。Facebook、Google Apps、Windows Liveなどさまざまなサービスで実装されている。|
|OpenID Connect 1.0|標準化団体OpenID Foundationによって策定されたREST/JSONベースのプロトコル。OAuth 2.0をベースに認証目的でも利用できるように拡張している。野村総合研究所、グーグルなどにより開発が開始され、2014年2月に最終承認された現在最も新しいフェデレーションプロトコル。多くの企業がサポートを表明しており、今後普及していく可能性が高い。グーグル、マイクロソフト、セールスフォースなどが既に一部のサービスでサポートを開始し、国内でもヤフー、ミクシィなどのサービスで実装されている。グーグルは、2015年4月までに全面的にOpenID Connectに移行することを予定している。|

表. OpenAMが対応している主なフェデレーションプロトコル