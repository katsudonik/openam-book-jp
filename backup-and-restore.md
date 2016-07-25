## OpenAMの設定のバックアップとリストア

OpenAMは、設定データをディレクトリ(LDAP)サーバーとファイルに保存します。ディレクトリサーバー間で設定データをレプリケーションすることにより、サイト内のOpenAMサーバー間で設定データを共有することできます。サーバーの損失から、または重大な管理上のエラーから回復するために、ディレクトリサーバーの設定データと設定ファイルをバックアップをしておくことをお勧めします。

この章では、OpenAMの設定をバックアップとリストアするために、ローカルの設定ファイルとローカルの(組み込み)設定データストアのデータをバックアップ/リストアする方法について解説します。設定データストアとして、外部のディレクトリサーバーを使用する場合の、設定データのバックアップ/リストアの方法については、外部ディレクトリサーバーのマニュアルを参照して下さい。

OpenDJをディレクトリサーバーとして使用する場合は、OpenDJの管理ガイドの「Backing Up and Restoring Data」の章により詳細な説明があります。

設定データがレプリケーションされているOpenAMの配備では、以下の点を考慮する必要があります:

- ディレクトリサーバーのレプリケーションは、レプリケーションされたデータがどこでも同じであることを保証するために、新しい変更を機械的に適用します。古いバックアップデータをリストアすると、レプリケーションは古いデータに新しい変更を適用します。  
これには、管理者の作業ミスによる新しい変更も含まれています。管理者のエラーからリストアするには、エラーを修理するようにレプリケーションされる変更を行うか、エラーの以前の状態に全てのレプリカをリストアするかのいずれかにより、この現象を回避しなければならりません。

- ディレクトリサーバーのバックアップとリストアを実施する際は、レプリケーションのパージ(削除)操作がバックアップする全てのデータの有効な生存期間に影響を与えることを認識して下さい。レプリケーションは、履歴データに基づいて、発生する競合を解決します。ディレクトリサーバーが最終的にこの履歴データを削除しなかった場合、利用可能な全てのスペースを埋めるまでデータが増大する可能性があります。 したがって、ディレクトリサーバーは古い履歴データを削除します。 OpenDJは、デフォルトで3日以上経過した履歴データを削除します。  
ディレクトリサーバーは、履歴データのギャップを検出すると、レプリケーション操作を正常に完了できない可能性があります。したがって、バックアップからリストアした全てのデータがレプリケーションパージ遅延よりも古くないことを確認する必要があります。そうしないと、リストア操作が、その間に発生した全ての変更を失い、バックアップから全てのサーバーをリストアする必要があるような、レプリケーションを停止を招く可能性があります。

この章では、以下のバックアップとリカバリの方法について解説します。

1. サーバー障害からのリカバリ:

    - 全てのサーバー設定データをバックアップする
    - 全てのサーバー設定データをリストアする

2. 重大なの管理者エラーからのリカバリ:

    - 設定データのみをエクスポートする
    - 重大なエラーの後に設定データをリストアする

#### 手順. 全てのサーバー設定データをバックアップする

この手順は、サーバーに保存されている全ての設定データをバックアップします。このバックアップは、障害が発生したサーバーをリストアするために保持しておきます。

次の条件が該当する場合、この手順を使用します:

- OpenAMが組み込みのOpenDJ内に設定データを保存している。
- 組み込みのOpenDJのファイルがOpenAMの設定ファイルと同じ場所に配置されている。

OpenAMが組み込みのOpenDJ内にCTSデータを保存する場合、リストア操作が現在のCTSのデータを古いデータで上書きします。また、バックアップからリストアした後、ユーザーが再度認証を要求さることがあります。  

配備内に長時間存在するセッションがあり、現在セッションフェイルオーバーを使用しない場合は、セッションフェイルオーバーを有効にすることにより、再認証の範囲を制限することができます。詳細については、OpenAMのセッションフェイルオーバーの設定を参照して下さい。

外部設定ディレクトリサーバーを使用する場合、外部ディレクトリサービスに保存されている設定データをバックアップ、リストアするには、外部ディレクトリサーバーのマニュアルを参照して下さい。

> **重要**  
> 先に進む前に、この章の導入の段落での説明を理解して下さい。ディレクトリのバックアップの有効性は、レプリケーションパージ遅延(OpenDJディレクトリサーバーのデフォルトでは3日間)に依存します。
> また、バージョンの異なるOpenAMのバックアップから設定データをリストアしてはいけません。設定データの構造は、バージョン毎に変更される可能性があります。

バックアップしたいOpenAMサーバー毎に次の手順を実施します:

1. OpenAMが実行されるコンテナを停止。
2. OpenAMの設定ファイル(ログファイルとロックファイルを除く設定ディレクトリ)をバックアップ  
   次の例では、デフォルトの設定の場所を使用しています。$HOMEは、OpenAMが配備されているWebコンテナを実行するユーザーのホームディレクトリで、OpenAMはApache Tomcatのopenam配下にデプロイされています:  

   ```
   $ cd $HOME
   $ zip \
   --recurse-paths \
   /path/to/backup-`date -u +i5%F-%m-%S`.zip \
   openam .openamcfg/AMConfig_path_to_tomcat_webapps_openam_ \
   --exclude openam/openam/debug/* openam/openam/log/* openam/openam/stats* \
    openam/opends/logs/* openam/opends/locks/*
    ...
   $ ls /path/to/backup-2014-12-01-12-00.zip
   /path/to/backup-2014-12-01-12-00.zip
   
   ```
   
   バックアップは、パージ遅延の終了時まで有効です。

3. OpenAMが実行されるコンテナを開始。

#### 手順. 全てのサーバー設定データをリストアする

この手順は、「全てのサーバーの設定データをバックアップする」で説明した通りバックアップされている、サーバーの全ての設定データをリストアします。障害の発生したサーバーを再構築する際に、この手順が適用できます。

次の条件が該当する場合、この手順を使用します:

- OpenAMが組み込みのOpenDJ内に設定データを保存している。
- 組み込みのOpenDJのファイルがOpenAMの設定ファイルと同じ場所に配置されている。

OpenAMが組み込みのOpenDJ内にCTSデータを保存する場合、リストア操作が現在のCTSのデータを古いデータで上書きします。また、バックアップからリストアした後、ユーザーが再度認証を要求さることがあります。  

配備内に長時間存在するセッションがあり、現在セッションフェイルオーバーを使用しない場合は、セッションフェイルオーバーを有効にすることにより、再認証の範囲を制限することができます。詳細については、OpenAMのセッションフェイルオーバーの設定を参照して下さい。

外部設定ディレクトリサーバーを使用する場合、外部ディレクトリサービスに保存されている設定データをバックアップ、リストアするには、外部ディレクトリサーバーのマニュアルを参照して下さい。

> **重要**  
> 先に進む前に、この章の導入の段落での説明を理解して下さい。ディレクトリのバックアップの有効性は、レプリケーションパージ遅延(OpenDJディレクトリサーバーのデフォルトでは3日間)に依存します。
> また、バージョンの異なるOpenAMのバックアップから設定データをリストアしてはいけません。設定データの構造は、バージョン毎に変更される可能性があります。

リストアするためには、各OpenAMサーバーで次の手順に従って下さい。失敗した後でOpenAMをリストアする場合は、バックアップからOpenAMをリストアする前に問題を調査する上で必要な、すべての設定とログファイルのコピーを作成していることを確認します。

1. OpenAMが実行されるコンテナを停止。
2. 設定ディレクトリ内のファイルを必要に応じてリストア(レプリケーションパージ遅延よりも新しい、有効なバックアップから復元することを確認):

   ```
   $ cd $HOME
   $ unzip /path/to/backup-2014-12-01-12-00.zip
   Archive:  /path/to/backup-2014-12-01-12-00.zip
   replace openam/.configParam? [y]es, [n]o, [A]ll, [N]one, [r]ename: 
   A
   ...
   ```
3. OpenAMが実行されるコンテナを開始。

#### 手順. 設定データのみをエクスポートする

LDAPデータ交換フォーマット(LDIF)は、LDAPディレクトリのデータを保存するための標準的な、テキストベースのフォーマットです。ディレクトリデータを変更するために、LDIFの抜粋を使用することができます。  

この手順では、OpenAMの設定データのみのLDIFバックアップを取得します。重大な設定エラーからリストアするとき、このLDIFデータを使用します:

1. 設定データをエクスポートする前に、OpenAMの設定が正しい作業順序であることを確認します。
2. 設定データだけ(CTSデータを除く)をエクスポートするタスクを実行するために、OpenDJのexport-ldifコマンドを使用します。
   OpenAMを停止せずに、このコマンドを実行することができます。
   OpenAMの設定ファイルを含むディレクトリの下にあるOpenDJのコマンドを検索します。
   ディレクトリマネージャのバインドパスワードは、OpenAMの管理者(amadmin)のパスワードと同じです:

   ```
   $ $HOME/openam/opends/bin/export-ldif \
    --port 4444 \
    --hostname openam.example.com \
    --bindDN "cn=Directory Manager" \
    --bindPassword password \
    --backendID userRoot \
    --includeBranch dc=openam,dc=forgerock,dc=org \
    --excludeBranch ou=tokens,dc=openam,dc=forgerock,dc=org \
    --ldifFile /path/to/backup-`date -u +%F-%m-%s`.ldif \
    --start 0 \
    --trustAll
   Export task 20141208113331302 scheduled to start Dec 8, 2014 11:33:31 AM CET
   ```

   タスクが完了すると、LDIFファイルが出力されます:

   ```
   $ ls /path/to/*.ldif
   /path/to/backup-2014-12-08-12-1418034808.ldif
   ```

#### 手順. 重大なエラーの後に設定データをリストアする

重大な設定エラーとは、OpenAMの設定ツール(管理コンソールまたはssoadmコマンドのような)を使用して、簡単にリストアすることができないエラーです。

手動で以前の状態に設定データをリストアすることによって、重大な設定エラーから回復するには、この手順を使用します。この手順では、「設定データのみをエクスポートする」に記載されている通りにエクスポートされたLDIFデータを使用します。

1. 設定の問題の原因となったLDAPの変更を特定するために、OpenDJの変更ログを確認して下さい。  
   OpenDJの変更ログは、レプリケーションされたディレクトリサーバーのデータに加えられた変更を読み取ることができる、外部変更ログメカニズムを提供します。
   変更ログを読み取る方法については、OpenDJ管理ガイドの「Change Notification For Your Applications」の章を参照してください。

2. 変更ログのデータに基づいて、設定エラーを元に戻す変更内容を決定します。

3. 変更ログに含まれまれていない削除されたコンテンツについては、設定エラーの前に設定データの以前の作業状態を特定するため、「設定データのみをエクスポートする」でエクスポートしたLDIFを使用します。

4. 管理上のエラーの前のデータの状態にリストアしてエラーを修復できるように、設定データを修正するためのLDIFを準備します。

5. 変更を適用するために、OpenDJのldapmodifyコマンドを使用します。
   ディレクトリデータに変更を加える手順については、OpenDJの開発者ガイドの「ディレクトリの更新」の章を参照して下さい。