.. title:: Nutanix Frame on AHV Bootcamp

.. toctree::
   :maxdepth: 2
   :caption: End User Computing - Xi Frame
   :name: _eucframe
   :hidden:

   goldimage/goldimage
   deploycca/deploycca
   manage/manage
   flow_secure_desktops/flow_secure_desktops
   prismops/prismops_rightsize_frm_lab/prismops_rightsize_euc_lab
   framefiles/framefiles

.. toctree::
  :maxdepth: 2
  :caption: Appendix
  :name: _appendix
  :hidden:

  tools_vms/windows_tools_vm
  tools_vms/linux_tools_vm
  appendix/glossary

.. _getting_started:

---------------
はじめに
---------------

Xi Frameを使用したエンドユーザーコンピューティングのワークショップへようこそ！このワークショップでは、Nutanix HCIがVDIのワークロードとして理想的なプラットフォームであることを体験して頂き、またクラウドで提供されるXi Frameと完全に統合されていることを体験して頂きます。Nutanix HCIの並列でのスケーラビリティや一貫したパフォーマンスなど、仮想デスクトップの展開にもたらす利点とその他の様々な利点について理解します。

- オンプレミスの仮想デスクトップ管理を容易にするAHV
- デスクトップイメージの更新を含む高速なデスクトッププロビジョニング
- ユーザーデータ、ユーザープロファイル提供するためのNutanix Filesを使用したファイルサービス
- AHVネイティブのマイクロセグメンテーション
- Prism Ops（Prism Pro）による豊富な監視と自動化機能

アジェンダ
++++++

- ゴールデンイメージの準備
- Frameの管理
- Filesの利用
- Flowの利用
- Prism Ops（Prism Pro）の利用

事前準備
+++++++++++++

- 使用する *パスワード* をメモします。
- ラボ環境にログインします。（後述の接続情報をご参照下さい。）

環境の詳細
+++++++++++++++++++

このワークショップは、Nutanixが提供するPOC環境で実行することを目的としています。演習を完了するために必要なすべての必要なイメージ、ネットワーク、仮想マシンがクラスターに既にプロビジョニングされています。

ネットワーク情報
..........

提供されるPOCクラスターは、標準の命名規則に従います。

- **Cluster Name** - POC\ *XYZ*
- **Subnet** - 10.**21**.\ *XYZ*\ .0
- **Cluster IP** - 10.**21**.\ *XYZ*\ .37

例:

- **Cluster Name** - POC055
- **Subnet** - 10.21.55.0
- **Cluster IP** - 10.21.55.37

ワークショップ全体を通して、XYZをサブネットの正しいオクテットに置き換える必要があるインスタンスが複数あります。次に例を示します。

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - IPアドレス
     - 説明
   * - 10.21.\ *XYZ*\ .37
     - Nutanix Cluster Virtual IP
   * - 10.21.\ *XYZ*\ .39
     - **PC** VM IP, Prism Central
   * - 10.21.\ *XYZ*\ .40
     - **DC** VM IP, NTNXLAB.localドメインコントローラー

各クラスターは、2つのVLANで構成されています。

.. list-table::
  :widths: 25 25 10 40
  :header-rows: 1

  * - ネットワーク名
    - IPアドレス
    - VLAN
    - DHCPスコープ
  * - Primary
    - 10.21.\ *XYZ*\ .1/25
    - 0
    - 10.21.\ *XYZ*\ .50-10.21.\ *XYZ*\ .124
  * - Secondary
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .132-10.21.\ *XYZ*\ .149
..  * - User01-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User02-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User03-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User04-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User05-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User06-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User07-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User08-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User09-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User10-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200
  * - User11-Network
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .150-10.21.\ *XYZ*\ .200

クレデンシャル情報
...........

.. note::

  *<Cluster Password>* は、各クラスター毎に異なるため、インストラクターよりお伝えします。

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - クレデンシャル
     - ユーザー名
     - パスワード
   * - Prism Element
     - admin
     - *<Cluster Password>*
   * - Prism Central
     - admin
     - *<Cluster Password>*
   * - Controller VM
     - nutanix
     - *<Cluster Password>*
   * - Prism Central VM
     - nutanix
     - *<Cluster Password>*

各クラスターには専用のドメインコントローラーVM **DC** があり **NTNXLAB.local** ドメインにてADサービスを提供します。ドメインには、次のユーザーとグループが登録されています。

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - グループ
     - ユーザー名
     - パスワード
   * - Administrators
     - Administrator
     - nutanix/4u
   * - SSP Admins
     - adminuser01-adminuser25
     - nutanix/4u
   * - SSP Developers
     - devuser01-devuser25
     - nutanix/4u
   * - SSP Consumers
     - consumer01-consumer25
     - nutanix/4u
   * - SSP Operators
     - operator01-operator25
     - nutanix/4u
   * - SSP Custom
     - custom01-custom25
     - nutanix/4u
   * - Bootcamp Users
     - user01-user25
     - nutanix/4u

ラボへのアクセス手順
+++++++++++++++++++

ラボ環境となるNutanix Hosted POC環境には様々な方法でアクセスができます。Nutanix社員は、Global VPNでもアクセスできます。

ラボのアクセスユーザーのクレデンシャル情報
...........................

PHXクラスター:
**Username:** PHX-POCxxx-User01 (up to PHX-POCxxx-User20), **Password:** *<インストラクターから提供>*

RTPクラスター:
**Username:** RTP-POCxxx-User01 (up to RTP-POCxxx-User20), **Password:** *<インストラクターから提供>*

Frame VDIによるアクセス
..........................

Login : https://frame.nutanix.com/x/labs

**Nutanix社員** - **NUTANIXドメイン** のクレデンシャル

**Nutanix社員以外** - **ラボアクセスユーザーLab Access User** のクレデンシャル

Parallels VDIによるアクセス
..........................

PHXクラスター Login to: https://xld-uswest1.nutanix.com

RTP クラスター Login to: https://xld-useast1.nutanix.com

**Nutanix社員** - **NUTANIXドメイン** のクレデンシャル

**Nutanix社員以外** - **ラボアクセスユーザーLab Access User** のクレデンシャル

Pulse Secure VPNによるアクセス
..........................

PHX Based Clusters Login to: https://xld-uswest1.nutanix.com

RTP Based Clusters Login to: https://xld-useast1.nutanix.com

**Nutanix社員** - **NUTANIXドメイン** のクレデンシャル

**Nutanix社員以外** - **ラボアクセスユーザーLab Access User** のクレデンシャル

Pulse Secure Clientツールを画面からダウンロードしてインストールします。

Pulse Secure Clientで接続情報を **Add** します。

PHXの場合:

- **Type** - Policy Secure (UAC) または Connection Server
- **Name** - X-Labs - PHX
- **Server URL** - xlv-uswest1.nutanix.com

RTPの場合:

- **Type** - Policy Secure (UAC)または Connection Server
- **Name** - X-Labs - RTP
- **Server URL** - xlv-useast1.nutanix.com


Nutanixソフトウェアバージョン情報
++++++++++++++++++++
- **AHVバージョン** - AHV 20170830.337
- **AOSバージョン** - 5.17.0.3
- **Prism Centralバージョン** - 5.17.0.3
