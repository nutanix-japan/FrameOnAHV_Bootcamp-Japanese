.. _framefiles:

------------------------------
Xi Frameでのファイル操作
------------------------------

Xi Frameのセッション内のユーザー環境は、デフォルトでは *システムの内容を保持しない非永続的* な環境です。つまり、C:ドライブの変更内容は、そのセッション内でのみ仮想デスクトップに保持され、ユーザーデータは毎回保持されません。ユーザーファイルや設定はファイルサーバーなどに個別に保持します。Persistentの設定していない限り、再起動後はデータが保存されていません。

**この演習では、Frame環境でのファイル操作ついて学習します。**

アップロードとダウンロード
+++++++++++++++++++++++++

管理者によりポリシーで許可されている場合、Frameのデスクトップとローカルとの間で簡単に素早くファイルを移動させることができます。

#. **Xi Frame** ポータルにて **Launchpad** ボタンをクリックします。

   .. figure:: images/1.png

#. **Launchpad** からデスクトップを起動します。

#. ステータスバーの右側にあるアイコングループから上矢印の雲のアイコンをクリックして、ファイルをアップロードします。または、ブラウザのセッションウインドウにドラックアンドドロップすることもできます。

   .. figure:: images/2.png

#. Frameのデスクトップ内のエクスプローラーの **Uploads** フォルダーからファイルを開くことができます。

   .. note::

     ``C:\Users\Frame\`` の中に **Uploads** と **Download Now** フォルダーを見つけることができます。

   .. figure:: images/old3.png

#. Frameのデスクトップからファイルをダウンロードするには、ファイルを **Download Now** フォルダーに保存するだけでローカル側のダウンロードフォルダーに自動的にダウンロードされます。または、仮想デスクトップのエクスプローラー内の任意のファイルを右クリックして、 **Download with Frame** を選択してダウンロードすることもできます。

   .. figure:: images/4.png

クラウドストレージの利用
+++++++++++++++++++

Frameのローンチパッド（ユーザーのFrameポータル画面）上で各クラウドストレージとの認証設定をしておくことで、仮想デスクトップからGoogleドライブ、Dropbox、OneDrive、またはBoxに再ログインなしでシンプルに接続できるようになります。

#. セッションを一時切断するため、仮想デスクトップの右下にある :fa:`gear` **> Disconnnect > Disconnect** をクリックします。

#. Launchpadの右上にある **User Name** メニューを選択し **Edit** をクリックします。

   .. figure:: images/5.png

#. **Storage providers** から任意のクラウドストレージの **+** をクリックして、Frameからアクセスできるようにアカウント認証を行ないます。

   .. figure:: images/6.png

#. 左上の **Go back** でローンチパッドに戻り **Resume** をクリックして、切断されたセッションを再開します。

#. Frameのデスクトップ内のエクスプローラーを開き、クラウドストレージがネットワークドライブ(例：G:ドライブ等)として自動的にマウントされていることを確認します。

   .. figure:: images/7.png

#. クラウドストレージからドキュメントを開きます。クラウドストレージがネットワークドライブとしてマウントされている場合、メタデータのみが同期されます。実ファイルは同期されません。ファイルを開くと、ファイルは一時的にFrameのセッションに転送されて使用されます。ファイルを保存すると、そのファイルはクラウドストレージに保存されます。

Nunitx Filesの利用
+++++++++++++++++++

Nutanix Filesは仮想デスクトップソリューションで必要なユーザープロファイルとデータを管理することを容易に提供でき、スケーラブルなファイルサービスを提供するのに最適です。クラウドサービスで提供されているFrameのデスクトップの場合は、VPN、VPC/VNetピアリング、ダイレクトコネクトなどのWANネットワーキングソリューションを導入すると、ファイル共有などの企業リソースに安全にアクセスする仕組みを提供することができます。

   .. figure:: images/8.png

本環境ではFrameデスクトップがActive Directoryと連携されていないため、ファイルの単純な部門共有を行い、ファイル分析機能を有効にします。

時間とリソース節約のため、Nutanix Filesインスタンスは既にクラスターにデプロイされています。Nunitax Filesのデプロイの概要については `ここ <http://youtube.com>`_ をクリックして下さい。

#. **Prism Element > File Server > Share/Export** から **+ Share/Export** をクリックします。

   .. figure:: images/9.png

#. **Basic** タブにて次の項目を入力して、 **Next** をクリックします。

   - **Name** - *Initials*\ **-DepartmentShare**
   - **Description** - Fiesta Engineering Share
   - **File Server** - BootcampFS
   - **Select Protocol** - SMB

   .. note::

      本環境では単一ノードでFilesインスタンスを展開するため、共有タイプ **Standard** と **Distributed** の選択はありません。

#. 次の項目を入力して **Next > Create** を選択します。

   - **Enable Access Based Enumeration (ABE)** を選択します。
   - **Self Service Restore** を選択します。

   .. note::

     アクセスベースの列挙（ABE）は、Microsoft Windows（SMBプロトコル）ユーザーがファイルサーバー上のコンテンツを参照するときに、読み取りアクセス権を持つファイルとフォルダーのみを表示するようにします。

     SMB共有でのWindowsの以前のバージョン機能のセルフサービスリストアをサポートしています。

     これらの機能はどちらも、Shar/Exportした単位で有効/無効にできます。

#. **Prism Element > File Server > File Server** から **BootcampFS** を選択し **Protect** をクリックします。

   .. figure:: images/10.png

     デフォルトのセルフサービスリストアスケジュールを確認します。この機能は、Windowsの以前のバージョン機能のスナップショットスケジュールを設定します。Windowsの以前のバージョン機能をサポートすることで、エンドユーザーは、ストレージやバックアップの管理者に依頼することなく、ファイルの変更をロールバックできます。

        .. note::

          クォータ、アンチウィルスソフト連携、モニタリングなどを含むFiles機能の詳細については `Nutanix Files Guide <https://portal.nutanix.com/#/page/docs/details?targetId=Files-v3_6:Files-v3_6>`_ をご覧下さい。

#. Frameのデスクトップ内のエクスプローラーから ``\\BootcampFS.ntnxlab.local\Initials-DepartmentShare\`` にアクセスできることを確認します。資格情報の入力が求められたら、次の認証情報を入力します。

   - **User Name** - ntnxlab.local\\devuser01
   - **Password** - nutanix/4u
   - **Remember my credentials** を選択します。

#. Frameのデスクトップ内でブラウザーを開き、サンプルデータをダウンロードして共有フォルダーに配置します。

   - **PHX clusterを使用している場合** - http://10.42.194.11/workshop_staging/peer/SampleData_Small.zip
   - **RTP clusterを使用している場合** - http://10.55.251.38/workshop_staging/peer/SampleData_Small.zip

#. zipファイルの内容を共有フォルダーに展開し、いくつかのファイルを開きます。

   .. figure:: images/11.png

#. **Prism Element > File Server** から **BootcampFS** を選択し、 **File Analytics** をクリックします。

   .. figure:: images/12.png

#. **Enable File Analytics** （ファイル分析を有効にする）ように求められた場合は、NTNXLAB\\Administratorの資格情報を入力し、 **Enable** をクリックします。

   .. figure:: images/13.png


**ファイル分析機能を確認して下さい。Xi Frameのワークショップは以上となります。お疲れ様でした。**
