.. _framegoldimage:

------------------------------------
ゴールドマスターの構築と最適化
------------------------------------

Xi FrameはクライアントVMのイメージとなるサンドボックスと呼ばれるゴールドマスターを作成します。

バニラクライアントオペレーティングシステム(カスタマイズされていない、提供されたままのOS)は、通常、物理デバイス（ラップトップPCやデスクトップPCなど）で動作することを前提としています。ハードウェアデバイスに直接接続され、
ホスト上の高負荷な仮想マシンから影響を受ける（ノイジーネイバー）ことが想定されていません。
そのため、そのOSを仮想マシンとしてインストールすると想定外の動作をする可能性があり、仮想環境上で動作させるための最適化が必要です。
Nunitaxのパフォーマンス/ソリューションエンジニアリングチームでは、さまざまな最適化とテストを行いました。

.. figure:: images/19.png

ご覧のとおり、CitrixのOS最適化ツール（Citrix Optimizer）を適用すると、ノードあたりのデスクトップ集約率が48％向上し、VMwareのOS最適化の推奨事項に従うと集約率が57％まで向上します。
どちらの最適化方法も、ハイパーバイザーとは無関係であり、OSゲスト内のサービスを調整する方法です。
本演習ではCitrix Optimizerを使って最適化を行いますが、実際の案件の場合は環境に応じて最適化設定をおこなってください。

**この演習では、仮想マシンにFrame Agentをインストールし、Citrix Optimizerを使用して仮想マシンのOSを最適化します。**

仮想マシンのデプロイ
++++++++++++++

#. **Prism Central** から :fa:`bars` **> Virtual Infrastructure > VMs** を選択します。

   .. figure:: images/1.png

#. **Create VM** を選択します。

#. 割り当てられているクラスターを選択し **OK**　をクリックします。

#. 次の項目を入力します。

   - **Name** - *Initials*\ -GoldImage
   - **Description** - (オプション) VMの説明
   - **vCPU(s)** - 2
   - **Number of Cores per vCPU** - 1
   - **Memory** - 4 GiB

   - **+ Add New Disk** を選択します。
       - **Type** - DISK
       - **Operation** - Clone from Image Service
       - **Image** - Win10v1903.qcow2
       - **Add** を選択します。

   - **Add New NIC** を選択します。
       - **VLAN Name** - Primary
       - **Add** を選択します。

#. **Save** をクリックして、仮想マシンを作成します。

#. 作成した仮想マシンを選択して **Power On** をクリックします。

.. _FramePausingUpdates:

Windows Update の一時停止
++++++++++++++++++++++++

**Windows 10** イメージの構築を開始する前に、Windows Updateが進行中でないことを確認することが重要です。Windows Updateが進行中の場合、クローン作成で問題が発生する可能性があります。

#. Prism上の仮想マシンのコンソールを開くか、RDP経由で仮想マシンに接続します。

   - **User Name** - Nutanix
   - **Password** - nutanix/4u

#. **Start > Settings > Updates & Security > Windows Update** を開き **Pause Updates for 7 Days** をクリックします。

   .. figure:: images/24.png

Citrix Optimizerの実行
++++++++++++++++++++++++

#. Prism上の仮想マシンのコンソールを開くか、RDP経由で仮想マシンに接続します。

   - **User Name** - Nutanix
   - **Password** - nutanix/4u

#. 仮想マシン内で  https://ntnx.how/CitrixOptimizer  をダウンロードして、ファイルを展開します。

#. **CitrixOptimizer.exe** を右クリックして **Run as Administrator** を選択します。

   .. figure:: images/12.png

#. ゴールドマスターに使用されているWindowsビルドバージョンに基づいて、推奨される最適化のテンプレートを選択します。

   .. figure:: images/13.png

#. **Select All** を選択し **Analyze** をクリックします。

   .. figure:: images/14.png

#. **View Results** をクリックして、最適化に関するレポートを表示します。

#. **Citrix Optimizer** に戻り **Done > Optimize** をクリックし、選択した最適化の内容を適用します。

   .. figure:: images/15.png

#. ツールの処理が完了したら **View Results** をクリックして、更新されたレポートを表示できます。

#. 結果を確認して **ゴールドマスターの仮想マシンを再起動** します。

..   Running VMware OS Optimization Tool
      +++++++++++++++++++++++++++++++++++

      #. Within the VM console, download https://ntnx.how/VMwareOSOptimizationTool and extract to a directory.

      #. Right-click **VMwareOSOptimizationTool.exe** and select **Run as Administrator**.

      #. Click the **Select All** checkbox. Scroll down to **Cleanup Jobs** and un-select the 4 available optimizations. Click **Analyze**.

         .. figure:: images/16.png

         .. note::

            The Cleanup Jobs are excluded from this exercise as they can be time consuming to apply.

      #. Note the outstanding optimizations not applied in the **Analysis Summary** pane.

         .. figure:: images/17.png

      #. Click **Optimize** to apply the remaining optimizations.

         .. figure:: images/18.png

      #. Review the results and then **restart your Gold Image VM**.

Frame Guest Agentのインストール
++++++++++++++++++++++++++++++++
Frame Guest Agent（以下FGA）は、Frameで管理するワークロードVM
（サンドボックス、実稼働インスタンス、ユーティリティサーバー）にインストールするFrameコンポーネントです。
FGAは、エンドユーザーのエンドポイントデバイスとFrame管理のワークロードVMの間にH.264ベースのFrame Remoting Protocol（FRP）を実装して、画面転送を行ないます。NVIDIA GPUがワークロードVM内で利用できる場合、FGAはNVIDIAグラフィックカードのNVENCのH.264エンコーダーを利用して、ワークロードVMのCPU負荷を軽減します。
さらに、FGAはFrameプラットフォームのブローカー機能と連携して、ワークロードVMへのアクセスを求めるエンドユーザーのリクエストが確実に許可されるようにします。
FGAは、セッション設定ポリシー（ローカルとFrameデスクトップ間のクリップボード機能、クリップボード機能の双方向・片方かの制御、ファイルのアップロード/ダウンロード、印刷、タイムアウトパラメータ、QoSパラメータなど）も適用します。また、個人用ドライブ、エンタープライズプロファイルディスクのマウントとアンマウント、およびクラウドストレージ連携を処理します。

.. note::

  Nutanix Guest Tools（Frame Guest Agentではない）をゴールドマスターにインストールすることはできません。これは、Frameバックプレーンとワークロードインスタンス間の通信の問題を引き起こす可能性があるためです。イメージにすでにNutanixGuest Toolsがインストールされている場合は、Nutanix Guest Toolsをアンインストールする前にVirtIOドライバーをインストールする必要があります。VirtIOドライバーをインストールせずにNutanix Guest Toolsを削除しようとすると、仮想マシンが起動しなくなります。

#. **Prism Central** からゴールドマスターの仮想マシンを選択し、IPアドレスをメモを取ります。

#. **Actions > Update** をクリックします。

   .. figure:: images/2.png

#. **Disks > CD-ROM** を選択し :fa:`pencil` から次の項目を選択します。

   - **Operation** - Clone from Image Service
   - **Image** - FrameGuestAgentInstaller_1.0.2.2_7930.iso

#. **Update > Save** をクリックします。

#. **RDP経由で** 仮想マシンに接続します。　

   .. note::

      Frame Guest Agentがインストールされると、AHV VNCコンソールから仮想マシンにアクセスできなくなります。

#. 仮想マシンのOSのタイムゾーンをUTCに更新します。**Sync Now** をクリックして、仮想マシンの時刻が正確であることを確認します。

   .. figure:: images/20.png

#. **重要：** **Control Panel** から、インストールされている **Microsoft Visual C++ Redistributable** をアンインストールします。

   .. figure:: images/22.png

#. Frameデスクトップ内で **D:\\FrameGuestAgentInstall_1.0.2.2_7930.exe** を起動すると、FGAのインストーラが起動します。

#. 使用許諾契約に同意し **Install** をクリックします。

   .. figure:: images/21.png

#. プロンプトが表示されたら **Restart** をクリックしてインストールを完了します。

#. 約60秒後、リモートデスクトップ経由で仮想マシンに接続し、PowerShellで以下を実行します。（これは、ゴールデンイメージをクリーンなSysPrep状態にするために行われます）。

   .. note::

    別のユーザーがログイン中というプロンプトが表示された場合は **Yes** をクリックして **Nutanix** ユーザーのままログインを続行します。

   .. code-block:: PowerShell

    Start-Process -FilePath "C:\Windows\System32\Sysprep\Sysprep.exe" -ArgumentList "/oobe /shutdown /generalize /unattend:C:\ProgramData\Frame\Sysprep\Unattend.xml" -Wait -NoNewWindow

   Sysprepが完了すると、マシンの電源が自動的にオフになります。

#. 仮想マシンの :fa:`eject` から、Frame Guest Agent installer.isoイメージを **取り出し** ます。

   .. figure:: images/23.png

Xi Frameのワークロードに使用するゴールドマスターが正常に作成されました。

本章のまとめ
+++++++++

- Frame用にカスタマイズされたWindows 10のゴールドマスターは簡単に作成できます。

- EUCにおけるイメージのOS最適化ツールは、ハイパーバイザー固有の機能ではなく、仮想デスクトップのパフォーマンスを向上させ、ホストの集約率を高めるために簡単に適用できるものです。
