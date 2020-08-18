.. _framerightsize:

------------------------------------
Prism Proを使用したデスクトップの適切なサイズ設定
------------------------------------

.. figure:: images/operationstriangle.png

Prism Proにより、監視、分析、アクションといったIT運用の業務サイクルをスマートに自動化することができます。Prism Proを使用すると、IT管理者は機械学習エンジンX-FITおよび自動化エンジンX-Playの機能を使用できます。これにより、機械学習データを基にして日常の業務フローを自動化し、運用業務を効率化します。

この演習では、仮想マシンのメモリーリソースが制限されている場合に、PrismがIT管理者の監視、分析、および自動化にどのように役立つかを学習します。

ラボのセットアップ
++++++++++++++++

この演習を完了するには、これまでの演習手順で作成された仮想マシンを使用する必要があるため、必ずゴールドイメージの構築と最適化の演習を完了してください。

#. **Prism Central** を開き **VMs** ページに移動します。**GTSPrismOpsLabUtilityServer** のIPアドレスをメモします。

   .. figure:: images/init1.png

#. ブラウザーで新しいタブを開き
 http://`<GTSPrismOpsLabUtilityServer_IP_ADDRESS>`/alerts に移動します。VMを初めて使用する場合は、VMにログインする必要がある場合があります。 **Prism Central IP**、 **Username** と **Password** 入力して **Login** をクリックします。

   .. figure:: images/init2.png

#. アラート設定ページにアクセスしたら、タブを開いたままにします。この演習の後半で使用します。

   .. figure:: images/init2b.png

#. 別のタブで http://`<GTSPrismOpsLabUtilityServer_IP_ADDRESS>`/ にアクセスして、このUIで演習を完了させます。

   .. figure:: images/init3.png

Prism ProのX-FITによる非効率なリソースの検出
+++++++++++++++++++++++++++++++++++++++++++

Prism Proは機械学習X-FITを使用して、クラスター内で実行されているVMの動作を検出および監視します。

また、Prism Proは機械学習を使用してデータを分析し、非効率であると学習されたVMを以下の分類を適用します。

  * **Overprovisioned:** 割り当てられたリソースの最小量を使用していると特定されたVM
  * **Inactive:** 一定期間電源がオフになっているVM、またはCPU、メモリー、またはI/Oリソースを消費しない状態と特定されたVM
  * **Constrained:** リソースの追加でパフォーマンスが向上する可能性があると特定されたVM
  * **Bully:** リソースを豊富に使用しており、他のVMに影響を与えていると特定されたVM

#. **Prism Central** から :fa:`bars` **> Dashboard** に移動します。

#. ダッシュボードのVM Efficiencyウィジェットを確認します。このウィジェットは、Prism Proの機械学習X-FITがクラスター内で検出した非効率的なVM数を表示します。ウィジェットの下部にある View All Inefficeint VMsリンクをクリックして詳細を確認します。

   .. figure:: images/ppro_58.png

#. Prism ProがこれらのVMを検知した理由について表示されています。リストビューのいずれかを選択して、Efficiency Detail 列のテキストにカーソルを合わせると説明全体が表示されます。

   .. figure:: images/ppro_59.png

#. 管理者は、リソースの効率に関するVMのリストを確認することができます。リソースが多すぎる、または少なすぎるVMでは、個々のVMのリソースサイズを変更する場合があります。以下に示すいくつかの方法で実行リソースの変更を実行できます。

   * **Manually:** 管理者は、PrismまたはESXiの場合はvCenterを介してVM構成を編集し、割り当てられたリソースを変更します。
   * **X-Play:** X-Playの自動プレイブックを使用して、トリガーなどによりVMのサイズを自動的に変更します。この演習の後半で実際に演習を行ないます。
   * **Automation:** PowerShellやREST-APIなどによる自動化を使用して、VMのサイズを変更します。

   Prism Proは、この機械学習データを使用して、VM、ホスト、およびクラスターのメトリックデータのベースラインまたは予想範囲を生成することもできます。X-FITアルゴリズムは、これらのエンティティの通常の動作を学習し、それをさまざまなグラフで表すことができます。メトリック値がこの予想範囲から逸脱すると、Prism Proは異常を発生させます。

#. 次に「bootcamp_good」でキーワード検索をして bootcamp_good_1 を選択し、VMを見てみましょう。

   .. figure:: images/ppro_61.png

#. メトリック > CPU使用率 に移動します。青色の線と、その周りの水色の領域に注目してください。青色の線はCPU使用率です。水色の領域は、このVMの予想されるCPU使用率の範囲です。この特定のVMは、毎日同時刻にアップグレードされるアプリケーションを実行しているという使用パターンを説明しています。X-FITがこの使用パターンから周期を検出し、それに応じて予想範囲を予想したことを理解してください。この場合、使用量が予想範囲をはるかに超えているため、このVMに異常が発生しています。また「過去24時間」から、さらに時間範囲を短くして、グラフをさらに詳しく調べることもできます。

   .. figure:: images/ppro_60.png

#. このような状況の場合に、アラートポリシーを設定したい場合には **“Alert Setting”** をクリックします。

#. 必要に応じて一部の構成を変更できます。この例では、行動異常のしきい値を変更して、10％から70％の異常値は無視されます。それ以外の異常値の場合は警告アラートを通知するように設定されています。このVMのCPU使用率が95％を超えた場合は、静的しきい値をアラートクリティカルよして設定しています。

   .. figure:: images/ppro_25.png

#. **Cancel** をクリックして、ポリシー作成を終了します。

X-PlayによりVMメモリーを自動的に増やす
++++++++++++++++++++++++++++++++++++++++++++++++++++++++

次に、これらの非効率性のいくつかを解決するために自動化されたアクションを実行する方法を見てみましょう。この演習では、VMが利用できるメモリーが制限されていると想定し、このVMの適切なサイズを自動的に変更する方法を示します。また、カスタムチケットシステムを使用して、典型的な運用業務がService Nowなどのチケットシステムとどのように統合できるかを説明します。

#. **Prism Central** から 演習でプロビジョニングされたVMのいずれかを選択します。この例では **ABC - VM** というVMを使用します。

   .. note::

      Frameの管理ポータルの **Status** ページを使用して **Production** デスクトップVMの **Machine ID** を検索し、関連する **Machine ID** を **Prism Central** でフィルタリングできます。

   .. figure:: images/rs1.png

#. 後でX-Playを使用してメモリーを増加させるため、VMの現在の **メモリーサイズ** を確認しておいてください。この値を確認するためには **Properties** ウィジェット内を下にスクロールする必要がある場合があります。

   .. figure:: images/rs2.png

#. 検索バーを使用して **Action Gallery** に移動します。

   .. figure:: images/rs3.png

#. リストから **REST API** を選択します。actions menuから **Clone** をクリックします。

   .. figure:: images/rs4.png

#. プレイブックで使用してサービスチケットを生成できるアクションが作成されています。*Initials* に次のように入力し、URLフィールドに <GTSPrismOpsLabUtilityServer_IP_ADDRESS> を入力します。 **Copy** をクリックします。

   - **Name:** *Initials* - サービスチケットの生成
   - **Method:** POST
   - **URL:** http://<GTSPrismOpsLabUtilityServer_IP_ADDRESS>/generate_ticket/
   - **Request Body:** ``{"vm_name":"{{trigger[0].source_entity_info.name}}","vm_id":"{{trigger[0].source_entity_info.uuid}}","alert_name":"{{trigger[0].alert_entity_info.name}}","alert_id":"{{trigger[0].alert_entity_info.uuid}}"}``
   - **Request Header:** Content-Type:application/json;charset=utf-8

   .. figure:: images/rs5.png

#. 検索バーを使用して **Playbooks** に移動します。

   .. figure:: images/rs6.png

#. 次に、サービスチケットの生成を自動化するプレイブックを作成します。テーブルビューの上部にある **Create Playbook** をクリックします。

   .. figure:: images/rs7.png

#. トリガー設定のため **Alert** を選択します。

   .. figure:: images/rs8.png

#. 自動化されたメモリーの変更を実行するため、アラートポリシーとして **VM {vm_name} Memory Constrained** を検索して選択します。

   .. figure:: images/rs9.png

#. *Specify VMs* ラジオボタンを選択し、演習用に作成したVMを選択します。これにより、VMで発生したアラートのみがこのハンドブックをトリガーします。

   .. figure:: images/rs10.png

#. まず、このアラートのチケットを生成します。左側の **Add Action** をクリックし **Generate Service Ticket** を選択します。注：演習では、完全なワークフローを説明するために独自の発券システムをセットアップしましたが、Service Nowに対しては、すぐに使えるService Nowアクションもあります。

   .. figure:: images/rs11.png

#. 作成した **Generate Service Ticket** アクションの生成の詳細が自動的に入力されることを確認してください。

   .. figure:: images/rs12.png

#. 次に、チケットがX-Playによって作成されたことを通知します。 **Add Action** をクリックして、Emailのアクションを選択します。Emailアクションの以下の項目を入力します。次に例を示します。Messageの <GTSPrismOpsLabUtilityServer_IP_ADDRESS> をIPアドレスに置き換えてください。

   - **Recipient:** - ご自身のメールアドレスを入力します。
   - **Subject :** - ``Service Ticket Pending Approval: {{trigger[0].alert_entity_info.name}}``
   - **Message:** - ``The alert {{trigger[0].alert_entity_info.name}} triggered Playbook {{playbook.playbook_name}} and has generated a Service ticket for the VM: {{trigger[0].source_entity_info.name}} which is now pending your approval. A ticket has been generated for you to take action on at http://<GTSPrismOpsLabUtilityServer_IP_ADDRESS>/ticketsystem``

   .. figure:: images/rs13.png

#. **Save & Close** ボタンをクリックし “*Initials* - Generate Service Ticket for Constrained VM”. という名前で保存します。 **必ず ‘Enabled’ トグルを有効にしてください。**

   .. figure:: images/rs14.png

#. 次に、もう1つのプレイブックを作成します。これは、サービスチケットを解決するときに呼び出すものであり、影響を受けるVMにメモリーを追加してメールを送信する必要があります。テーブルビューの上部にある **Create Playbook** をクリックします。

   .. figure:: images/rs15.png

#. トリガーとして **Manual** を選択します。注：この演習用に作成したチケットシステムは、手動トリガーによって提供されるトリガーAPIを呼び出しますが、このAPIは公開されていません。5.17では、これと同じ動作を実現できるパブリックAPIを公開するWebhook Triggerを導入しています。Service Nowなどのツールは、このWebhookを使用してPrism Centralにコールバックし、プレイブックをトリガーできます。

   .. figure:: images/rs16.png

#. このプレイブックはVMに適用されるため、ドロップダウンから **VM** エンティティタイプを選択します。

   .. figure:: images/rs17.png

#. 左側 **Add Action** をクリックし、VMの **VM Add Memory** アクションを選択します。

   .. figure:: images/rs18.png

#. 以下の画面に従って空欄を設定してください。次に、自動アクションが実行されたことを誰かに通知します。 **Add Action** クリックしてメールアクションを追加します。

   .. figure:: images/rs19.png

#. メールアクションのフィールドに入力します。次に例を示します。

   - **Recipient:** - メールアドレスを入力します。
   - **Subject :** - ``Playbook {{playbook.playbook_name}} was executed.``
   - **Message:**``{{playbook.playbook_name}} has run and has added 1GiB of Memory to the VM {{trigger[0].source_entity_info.name}}.``

   .. note::

      独自の件名メッセージを作成することができます。上記は一例です。「parameters」を使用してメッセージを充実させることができます。

   .. figure:: images/rs20.png

#. 最後に、チケットサービスにコールバックして、チケットサービスでチケットを解決します。 **Add Action** をクリックして、REST API アクションを追加します。URLフィールドの <GTSPrismOpsLabUtilityServer_IP_ADDRESS> を置き換えて、次の値を入力します。

   - **Method:** PUT
   - **URL:** http://<GTSPrismOpsLabUtilityServer_IP_ADDRESS>/resolve_ticket
   - **Request Body:** ``{"vm_id":"{{trigger[0].source_entity_info.uuid}}"}``
   - **Request Header:** Content-Type:application/json;charset=utf-8

   .. figure:: images/rs21.png

#. **Save & Close** ボタンをクリックしてください。 “*Initials* - Resolve Service Ticket” という名前で保存します。 **必ず「Enabled」トグルを有効にしてください。**

   .. figure:: images/rs22.png

#. 次に、ワークフローをトリガーします。 事前に開いていた ** http://`<GTSPrismOpsLabUtilityServer_IP_ADDRESS>`/alerts**  を使用して、セットアップで開いたタブに移動します。 **VM Memory Constrained** Radioを選択しVMを入力します。 **Simulate Alert** ボタンをクリックします。これにより、VMのメモリー制限のアラートをシミュレートします。

   .. figure:: images/rs23.png

#. 最初のプレイブックに記載したメールアドレス宛にメールを受信する必要があります。5分ほどかかる場合があります。

   .. figure:: images/rs24.png

#. メール内のリンクをクリックして、チケットシステムにアクセスします。または、ブラウザの新しいタブから http://`<GTSPrismOpsLabUtilityServer_IP_ADDRESS>`/ticketsystem に移動して、チケットシステムに直接アクセスできます。

   .. figure:: images/rs25.png

#. VM用に作成されたチケットを特定し、縦のドットアイコンをクリックして Actions メニューを表示します。 **Run Playbook** をクリックします。

   .. figure:: images/rs26.png

#. 作成した2番目のプレイブック **`Initials` - Resolve Service Ticket**　を選択して、このチケットを実行します。

   .. figure:: images/rs27.png

#. Prism Centralコンソールを開いたまま、前のタブに切り替えます。 **`Initials` - Resolve Service Ticket** プレイブックを開き、ビューの上部にある **Plays** タブをクリックして、このプレイブックで実行されたプレイを確認します。表でプレイのタイトルをクリックして、詳しく見てください。

   .. figure:: images/rs29.png

#. このビューを展開して、各項目の詳細を表示できます。エラーがあった場合、それらもこのビューに表示されます。

   .. figure:: images/rs30.png

#. VMに戻って、メモリーが実際に1GiB増加したことを確認できます。

   .. figure:: images/rs31.png

#. また、プレイブックが実行されたことを知らせるメールが届きます。

   .. figure:: images/rs32.png

本章のまとめ
.........

- Prism Proは、IT OPSをよりスマートで自動化するためのソリューションです。 インテリジェントな検出から自動修復までのIT OPSプロセスをカバーしています。

- X-FITは、異常検出や非効率さの検出などのスマートIT OPSをサポートする当社の機械学習エンジンです。

- 企業向けのIFTTT（あるWebサービスとあるWebサービス間を自動的に連携する機能）であるX-Playは、日常の運用タスクの自動化を可能にするエンジンです。

- X-Playにより、管理者は毎日のタスクを数分のステップで自動化することができます。

- X-Playは広範囲にわたり、お客様の既存のAPIとスクリプトをプレイブックの一部として使用でき、お客様の既存のワークフローと統合できます。
