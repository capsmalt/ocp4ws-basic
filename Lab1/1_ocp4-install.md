# 1. OCP4クラスターの構築
## 1-1. 諸注意
### 1-1-1. OpenShift4のインストールについて
### 1-1-2. 事前準備
## 1-2. AWS環境の準備
## 1-3. Red Hatアカウントとインストーラの準備
## 1-4. OpenShift4のインストール

1. AWSアカウントの作成
1. IAMユーザー作成(AdministratorAccessポリシーをセット)
1. sshキーペアを作成
    ```
    ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_ocp
    ```
1. アクセスキー取得
1. シークレットアクセスキー取得
1. AWS CLI取得
    - https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/install-bundle.html
1. AWS CLIを使ってAWSアカウント関連のセットアップ
    ```
    $ aws configure
    ```
    - アクセスキー
    - シークレットアクセスキー
    - デフォルトリージョン
    - etc.
    
1. AWSリソース制限緩和 (新規アカウント作成時はリソース利用可能量が小さいので注意)
   - Elastic IP
   - EC2
   - VPC
   - NLB
   - etc.

    >参考:
    >
    >OCPが必要とするリソース計算ツール
    >https://access.redhat.com/labsinfo/ocplimitscalculator


|タイプ|対象のサービス|リージョン|期待するLimit値
|:---:|:---|:---|:---:|
|VPC|VPC Elastic IPアドレス|アジアパシフィック(東京)|4|
|VPC|AZあたりのNATゲートウェイ|アジアパシフィック(東京)|1|
|VPC|リージョンあたりのゲートウェイVPC|アジアパシフィック(東京)|1|
|EC2|EC2インスタンス  (m4.large)|アジアパシフィック(東京)|3|
|EC2|EC2インスタンス  (m4.xlarge)|アジアパシフィック(東京)|3|
|ELB|Network Load Balancer|アジアパシフィック(東京)|5|

上記はOCP4 on AWS 1クラスターだけ作る場合
(最新情報は都度確認ください。上記は2019/6/26時点のものです。)

## OCP4インストーラおよびクライアントCLIの取得
IPIを使用してK8s(OCP)クラスターを構築します。
1. OCP4を構築するための Get startedページを開きます

    ==> https://cloud.redhat.com/openshift/install

    (※Red Hat ID 未所持の場合は，新規に作成します。)

1. インストール先のプラットフォームは， **AWS** を選択します
1. インストール方法は，**Installer-Provisioned Infrastructure** を選択します
1. インストーラおよびクライアントのCLIをダウンロードします
    - ファイルサーバー(https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/) から以下の2つを取得できます
        - **openshift-install**
          - OCPをインストールするためのCLI (Mac/Linux用あり)
            - Mac: https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-install-mac-4.1.3.tar.gz
            - Linux: https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-install-linux-4.1.3.tar.gz
        - **oc**
          - OCPを制御するためのCLI (Mac/Linux/Windows用あり)
            - Mac: https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-mac-4.1.3.tar.gz
            - Linux: https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux-4.1.3.tar.gz
            - Windows: https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-windows-4.1.3.zip
    >Tips:
    >
    >ocコマンドやopenshift-installコマンドは，/usr/local/binに入れずに，直下のディレクトリで実行しても構いません。ocコマンドは頻繁に使用するため今回はパスを通しておくことをおすすめします。
    >
    >また，homebrewで `brew install openshift-cli`のようにして導入することもできます。(Mac)

## OCP4インストール用Configの作成
1. インストール用の設定ファイルを作成します

    ```
    $ openshift-install create install-config
    ```
    
    上記実行後に，OCPインストール先となるAWS環境情報をインタラクティブに入力(選択)します。
    - provider: aws
    - region: <任意>
    - domain: <Route53で事前取得したドメイン>
    - cluster name: 任意
    - aws access key: <IAMで確認>
    - aws secret key: <IAMで確認>
    - pull secret: <cloud.redhat.comから取得>

         (※**Pull Secret** はRed Hat IDごとに異なります)  
    >参考: [AWSに導入する場合のカスタマイズ設定](https://docs.openshift.com/container-platform/4.1/installing/installing_aws/installing-aws-customizations.html#installation-configuration-parameters_install-customizations-cloud)


1. インストール用の設定ファイルをコピーして保管しておきます

    ```
    $ cp -p install-config.yaml{,.bak}
    ```
    
    >Tips:
    >
    >openshift-installコマンドでクラスター構築を行った際に，install-config.yamlはdeleteされてしまうため，何らかの形でバックアップを取っておくことをおすすめします。
    
    
1. OCP4クラスターをAWS上にインストールします

    ```
    $ openshift-install create cluster
    ```
    
    >Tips:
    >
    >OCP4クラスターが完成するまでに40分ほどかかります。  
    >内部的にはTerraformを使用して，AWSのCloud Provider APIを叩くことでAWSリソースを準備しています。クラスターを構成する主なリソースは以下です。用意できない場合は事前に[AWS問合せ窓口で制限緩和のリクエスト](https://console.aws.amazon.com/support/cases#/create)を投げておきましょう。
    >- EC2: 6つ
    >    - Master: 3つ (m4.xlarge)
    >    - Worker: 3つ (m4.large))
    >- Elastic IP: 3つ
    >- VPC: 1つ
    >- NLB(Network Load Balancer): 2つ
    >- NATゲートウェイ: 3つ

---
以上で，OCP4クラスターの構築は完了です。  
次に [OCP4クラスターの動作確認](2_ocp4-tour.md) のハンズオンに進みます。
