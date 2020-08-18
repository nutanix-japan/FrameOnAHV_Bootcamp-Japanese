.. _deploycca:

----------------------------------------------
Xi Frame Cloud Connector Appliance（CCA）の導入
----------------------------------------------

Frameコントロールプレーンは完全にクラウドで提供されます。
そのためオンプレミスにデスクトップを配置してを実行するには、次の図に示すようにプロキシVMが必要となります。

.. figure:: images/00.png

オンプレミスのCCAにより、クラウドで提供されるFrameプラットフォームサービスがオンプレミスのPrism Central APIと通信できるようになります。CCAは、Frameプラットフォームからのリクエストに基づいて、仮想マシンを作成、開始、停止、および削除するコマンドをPrism Centralに転送します。そのため、CCAはPrism Centralと同じVLAN上にある必要があります。

CCAの初期構成の後、AHVクラウドアカウントがFrameプラットフォームに正常に登録されると、Workload Cloud Connectorアプライアンス（WCCA、Prismではワークロードプロキシとも呼ばれます）が自動的にオンプレミス側に生成されます。WCCAにより、Frameプラットフォームはオーケストレーション情報をすべてのユーザーワークロードVM（サンドボックス、実稼働インスタンス、およびユーティリティサーバー）に送信できます。このアプライアンスがないと、エンドユーザーはワークロードVMに接続できません。

**この演習では、Frame Cloud Connectorアプライアンスをデプロイし、オンプレミスのNutanixクラスターとFrameコントロールプレーン間の接続を構成します。**

.. note::

   CCAの作成プロセスは、[Prism Central 5.11.1のリリース以降、100％自動化されています。この演習では、以下の手動に従って、アプライアンスVMをデプロイして構成します。

Frameアカウントの作成
+++++++++++++++++++++++++++

この演習では、My Nutanixの資格情報を使用して、Xi Frameトライアルサービスを利用します。

#. My Nutanix資格情報を使用して、ブラウザで https://my.nutanix.com にログインします。

#. **Xi Cloud Services > Xi Frame** から **Start Trial** をクリックします。

   .. figure:: images/0a.png

   .. note::

    　トライアルを既に開始している場合は **Launch** をクリックします。

#. 必要な追加情報を入力し **Continue** をクリックして30日間の試用を有効にします。

#. **Start your 30 day free trial** をクリックします。最大5ユーザーまでデスクトップを30日間提供することができます。

   .. figure:: images/0b.png

5. Frame管理ポータルで、左側のメニューから **Organizations** を選択し **Create Organization** をクリックします。任意の名前を入力して **Create** をクリックします。

   .. figure:: images/0c.png

   .. note::

      Frameプラットフォームは、管理、クラウドリソース、およびアカウント情報を登録するために、以下の3つ階層を使用します。

      **Customers**

      Customers（顧客層）は、Frameプラットフォーム内の最上位の層です。Nutanix社との利用契約を結ぶ事業体の「マスターアカウント」です。

      **Organizations**

      Organizations（組織層）は、Frameプラットフォーム内で2番目に高い層です。ユースケースによっては、1顧客配下に多数の組織が作成される場合があります。企業は、Organizations（組織層）を使用して、社内のさまざまな部門に固有の環境をセットアップできます。この演習では、アカウントを保持する1組織を作成し、AHVクラスタリソースを紐付けます。

      **Accounts**

      ワークロードVMを提供する場所です。また、ユーザー用のローンチパッドを作成する場所でもあります。エンドユーザーがFrameにログインすると、ワークロードVMに接続するために組織下にある各アカウントに関連付けられた各アカウント毎のローンチパッドにアクセスします。

Prismサービスアカウントの追加
++++++++++++++++++++++++++++

#. **Prism Central** から :fa:`bars` **> Prism Central Settings > Local User Management** を選択します。

#. Frame CCAの新しいサービスアカウントを作成するため、次の項目を入力して **Save** をクリックします。

   - **Username** - *Initials*\ -FrameSvc
   - **First Name** - *Initials* Frame
   - **Last Name** - Service Account
   - **Email** - (e-mail address)
   - **Password** - nutanix/4u
   - **Roles** - **User Admin** と **Prism Central Admin** を選択します。

   .. figure:: images/1.png

Frameカテゴリーの追加
+++++++++++++++++++++

FrameはPrism CentralのCategoriesを使用して、Cloud Connector Applianceが、FrameアカウントのサンドボックスとデスクトップVMの作成に使用されるテンプレートイメージを識別できるようにします。

.. note::

   カテゴリと値の作成は、Prism Centralインスタンスごとに1回だけ実行する必要があります。

#. **Prism Central** から :fa:`bars` **> Virtual Infrastructure > Categories**　を選択します。

   .. figure:: images/2.png

#. 利用可能なカテゴリーを確認します。 **FrameRole** が存在しない場合は **New Category** をクリックして、次の項目を入力します。

   - **Name** - FrameRole
   - **Purpose** - Allowing resource access based on Application Team
   - **Values**
   
    - Instance
    - Template
    - MasterTemplate

   .. note::

      :fa:`plus` ボタンで値を追加できます。

   .. figure:: images/2b.png

#. **Save** をクリックします。

#. **Prism Central** から :fa:`bars` **> Virtual Infrastructure > VMs** をクリックし *Initials*\ **-GoldImage** VMを選択します。

#. **Actions > Manage Categories** を選択し **FrameRole:MasterTemplate** の値をVMに追加します。Frame CCAは、このカテゴリー値を持つVMをあとで検索します。 **Save** をクリックします。

   .. figure:: images/2c.png

CCA VMの作成
+++++++++++++++++++

CCAは、ディスクイメージではなく、起動可能なISOイメージとして配布されます。

#. **Prism Central** から :fa:`bars` **> Virtual Infrastructure > VMs**　を選択します。

#. **Create VM** をクリックします。

#. 割り当てられたクラスターを選択し **OK** をクリックします。

#. 次の項目を入力します。

   - **Name** - *Initials*-FrameCCA
   - **Description** - (オプション) VMの説明
   - **vCPU(s)** - 1
   - **Number of Cores per vCPU** - 2
   - **Memory** - 4 GiB

   - **Disks > CD-ROM** :fa:`pencil`
      - **Operation** - Clone from Image Service
      - **Image** - FrameCCA-2.1.6.iso
      - **Update** を選択します。

   - Select **+ Add New Disk**
      - **Type** - DISK
      - **Operation** - Allocate on Storage Container
      - **Storage Container** - Default
      - **Size** - 0.1 GiB
      - **Add** を選択します。

   - Select **Add New NIC**
      - **VLAN Name** - Primary
      - **Add** を選択します。

#. **Save** をクリックしてVMを作成します。

#. VMを選択し **Actions > Power On** をクリックします。

   .. note::

      デフォルトでは、CCAはDHCPサーバーからIPアドレスを取得しようとします。静的IPアドレスを設定する場合は、コンソールを使用してCCA VMにアクセスします。

CCAの設定
+++++++++++++++++++

#. Prismで *Initials*\ **-FrameCCA** VM の **IP Address** をメモし、新しいブラウザータブを開いて \http://<*CCA-IP*>/ にアクセスし、Cloud Connector構成ウィザードを開きます。

   .. figure:: images/3.png

   .. note::

      my.nutanix.comはCookieを使用しますので、上記と同じブラウザセッションを使用ください。

#. 以下のフィールドに入力し **Log In** をクリックして、CCAをオンプレミスのNutanix環境に接続します。

   - **Username** - 既に作成している *Initials*\ -FramceSvc account
   - **Password** - nutanix/4u
   - **Prism Central URL** - \https://<*Prism Central IP*>:9440

   .. figure:: images/4.png

#. **Select Cluster** で、次の項目を入力して **Next** をクリックします。

   - **Cluster for virtual desktops** - *割り当てられているクラスター*
   - **Network for virtual desktops** - Primary
   - **Cloud account name** - *Initials*\ -\ *Cluster-Name*

   .. figure:: images/5b.png

   .. note::

      **Enable enterprise profiles and personal drives** は演習では使用しないので選択する必要はありません。

#. **Define Instance Types** で、既存のプロファイルを **AHV 2vCPU 4GB** に編集します。さらに図のように **Instance Type** を追加します。 **Next** をクリックします。インスタンスタイプは、アプリケーションを実行するために起動されるVM構成です。パブリッククラウド環境では、そのクラウドプロバイダーで利用可能なインスタンスタイプ（AWS t.2largeなど）がマッピングされます。

   .. figure:: images/6.png

#. **Select Sandbox Templates** で *Initials*\ **-GoldImage** VMが表示されます。 **OS** ドロップダウンから **Windows 10** を指定します。 **Next** をクリックします。

   .. figure:: images/7.png

#. 最後のステップでは、オンプレミスのAHVをFrameバックプレーンに接続します。 **Connect to Frame** を選択して **My Nutanixでサインイン** します。ログイン後、事前に作成された **nutanix.com Customer** を選択し **Finish**　をクリックします。

   .. figure:: images/8.png

   .. note::

      現時点では、クラスターに接続した後は、Cloud Connector Applianceの構成を変更することはできません。


#. **Go to Frame** をクリックして、Frame管理ポータルに接続します。左側のメニューから **Organizations** を選択し :fa:`ellipsis-v` **> Cloud Accounts** をクリックして、AHVクラウドアカウントの作成におけるステータスを表示します。

   .. figure:: images/9.png

   .. note::

      **Add Cloud Account** をクリックして、AWS、Azure、およびGCPリソースを追加するためのウィザードを表示します。これらはすべて同じFrame管理ポータルから一括管理できます。

#. ステータス **C** は、アカウントの作成中であることを示しています。Prism Centralは、CCA構成中に指定されたデスクトップVLANにワークロードプロキシVM( **frame-workload-proxy-####** )をプロビジョニングします。ステータスが **R** に変わると正常にプロビジョニングされています。確認後、次の演習に進みます。

   .. figure:: images/10.png

   .. note::

      ブラウザのページ更新が必要となる場合があります。

   これで、Frameを使用してAHV上のデスクトップのプロビジョニングを開始する準備ができました！
