.. _frameflow_secure_desktops:

---------------------------
Flowによるデスクトップの保護
---------------------------

Flowのセキュリティポリシーは、VMが相互に通信することを制限しながら、インバウンドおよびアウトバウンドアクセスを許可、制限することができます。また、攻撃から防御するためにVM間のトラフィックを制限することが重要となるWebサーバーやデスクトップなどのアプリケーションに最適です。

**この演習では、デスクトップVMにアプリケーションポリシーに適用し、デスクトップは通常の受信トラフィックおよび送信トラフィックがありますが、デスクトップ間のトラフィックはブロックするようにします。**

デスクトップVMの分類
++++++++++++++++++++++++++++

#. **Prism Central** から :fa:`bars` **> Virtual Infrastructure > Categories** を選択します。

#. **AppType** のチェックボックスを選択し **Actions > Update** をクリックします。

   .. figure:: images/1.png

#. :fa:`plus-circle` アイコンをクリックして、カテゴリ値を追加します。

#. *Initials*-**FrameDesktops** と入力します。

   .. figure:: images/2.png

#. **Save** をクリックします。

#. **AppTier** のチェックボックスを選択し **Actions > Update** をクリックします。

#. :fa:`plus-circle` アイコンをクリックして、カテゴリ値を追加します.

#. *Initials*-**Frame-W10NP** と入力します。

   .. figure:: images/3.png

#. **Save** をクリックします。

   次に、すべてのFrameデスクトップがテスト用に実行されていることを確認する必要があります。また **Prism Central** 内のどの **frame-instance-prod...** VMが環境に対応するのか決定する必要があります。

#. Frame管理ポータルのサイドバーから **Capacity** を選択し **Minimum number of instances** を **3** に増やして **Save** をクリックします。これにより、新しいイメージが公開されると、3つすべてのVMが確実に起動されます。

   .. figure:: images/3g.png

   .. note::

      構成によっては **Buffer instances** を **0** に減らす場合もあります。

#. サイドバーから **Status** を選択し、**Sandbox** VMの **Machine ID** をコピーします。

   .. figure:: images/3c.png

#. **Prism Central > Virtual Infrastructure > VMs** から、対象のサンドボックスを特定するために **Machine ID** でフィルタリングします。

   .. figure:: images/3d.png

#. VMを選択し **Actions > Manage Categories** をクリックします。Frameアカウントに対応する **FrameAccountID** の値をコピーします。 **Cancel** をクリックします。

   .. figure:: images/3h.png

#. **Prism Central > Virtual Infrastructure > VMs** から **Category** の **FrameAccountID** でフィルターして、対象のFrame VMを抽出します。 **frame-instance-prod...** VMを全て選択し **Actions > Manage Categories** をクリックします。

   .. figure:: images/3i.png

#. 検索バーで **AppType:**\ *Initials*\ **-FrameDesktops** を指定します。

#. :fa:`plus-circle` アイコンをクリックして、 **AppTier:**\ *Initials*\ **-Frame-W10NP** を追加して **Save** をクリックします。

   .. figure:: images/3j.png

デスクトップのセキュリティポリシーの作成
++++++++++++++++++++++++++++++++++

#. **Prism Central** から :fa:`bars` **> Policies > Security Policies** を選択します。

#. **Create Security Policy > Secure Applications (App Policy) > Create** をクリックします。

#. 次の項目を入力します。

   - **Name** - *Initials*-FrameDesktops
   - **Purpose** - Restrict unnecessary traffic between Frame desktops
   - **Secure this app** - AppType: *Initials*-FrameDesktops
   - **Filter the app type by category** は **選択しない** で下さい。

   .. figure:: images/6.png

#. **Next** をクリックします。

#. **Create App Security Policy** ウィザードでメッセージが表示されたら **OK** をクリックします。

#. セキュリティポリシーをより詳細に構成できるようにするには、すべてのデスクトップグループに同じルールを適用するのではなく **Set rules on App Tiers, instead** をクリックします。

   .. figure:: images/7.png

#. **+ Add Tier** をクリックします。

#. ドリップダウンから **AppTier:**\ *Initials*-**Frame-W10NP** を選択します。

#. **AppTier:Default** に対して、手順7〜8を繰り返します。

   .. figure:: images/8.png

   次に アプリケーションとの通信を制御する **Inbound** を定義します。この場合、すべての受信トラフィックを許可します。

#. ポリシー編集ページの左側から **Inbound** を **Whitelist Only** から **Allow All** に変更します。

   .. figure:: images/9.png

#. 前の手順を繰り返し **Outbound** を **Allow All** に変更します。

#. デスクトップ間通信を定義するには **Set Rules within App** をクリックします。

   .. figure:: images/10.png

#. **AppTier:**\ *Initials*\ **-Frame-W10NP** をクリックし **No** を選択して、このTierのVM間の通信を禁止します。これにより、デスクトップ間の通信がブロックされます。
   .. figure:: images/11.png

#. 一方で **AppTier:**\ *Initials*-**PD** がまだ選択されていますので **AppTier:Default** の右側にある :fa:`plus-circle` アイコンをクリックしてください。

#. 次の項目を入力して、Frameデスクトップと **Default** のVM間のTCPポート **7680** での通信を許可し、ピアツーピアでのWindows更新を許可します。

   - **Protocol** - TCP
   - **Ports** - 7680

   .. figure:: images/12.png

#. **Save** をクリックします。

#. **Next** をクリックして、セキュリティポリシーを確認します。

#. **Save and Monitor** をクリックして、ポリシーを保存します。

デスクトップのセキュリティポリシーの確認
++++++++++++++++++++++++++++++++

#. Frame管理ポータルに戻ります。サイドバーから **Status** を選択し、デスクトップVMの **Private IP** をメモします。

   .. figure:: images/12a.png

#. **Launchpad** をクリックして、 Frame **Desktop** にログインします。

#. デスクトップ内で **Command Prompt** を開き、 ``ping -t ANOTHER-FRAME-VM-IP`` にてデスクトップ間の通信を確認します。

   .. figure:: images/13.png

   デスクトップ間でpingできますか？なぜですか？

#. **Prism Central > Policies > Security Policies** から *Initials*\ **-FrameDesktops** ポリシーを選択します。

#. **Actions > Apply** をクリックします。

   .. figure:: images/14.png

#. **APPLY** を選択し **OK** をクリックして、デスクトップセキュリティポリシーを適用します。

   デスクトップ間の継続的なpingはどうなりますか？

この章のまとめ
+++++++++

- アプリケーションポリシーを使用すると、デスクトップなどの仮想インフラストラクチャーやアプリケーションを保護できます。
- この演習では、フローを使用してデスクトップ間のトラフィックをブロックしました。これは、デスクトップVM間の不要なアクセスを防止し、ネットワーク上のマルウェアの拡散を防止するために実装できる簡単なポリシーです。
- 監視モードは、定義されたアプリケーションへのトラフィックを視覚化するために使用されますが、適用モードはポリシーを適用します。
