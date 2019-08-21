# 3. コンテナイメージのビルドとデプロイ
## 3-1. 諸注意
### 3-1-1. OpenShift4におけるデプロイについて
OpenShiftでは，いくつかの方法でアプリケーションをクラスター上にデプロイすることができます。

- 既存のDockerイメージを使ってデプロイする方法
- ソースコードとS2I(ツール)を使ってビルド&デプロイする方法
- ソースコードとDockerfileを使ってビルド&デプロイする方法

### 3-1-2. 事前準備
- ocコマンド

## 3-2. OCP4コンソールへのログイン
1. ブラウザを立ち上げて **OCPコンソール** に接続します

    各自のOCPコンソールログイン情報を確認してください ==> http://bit.ly/ocp4ws-memo
    
   ![](images/console_login_capsmalt.png)

    >Tips:
    >
    >もし以下図のようなエラーが出て場合は例外追加を行ってください。
    >
    >![](images/console_login_error_1.png)
    >
    >![](images/console_login_error_2.png)


## 3-3. コンテナイメージのビルドとデプロイ
S2I(Source-to-Image)というツールを使って以下の2つのコンポーネントからコンテナイメージを生成し，コンテナアプリケーションをデプロイします。

- **リポジトリURL** : GitHubなどソースコード格納場所
- **S2I Builder Image** : S2Iスクリプトが含まれているDockerイメージ

またOCPは，カタログ機能(Developer Catalog)を備えています。JavaやPython，nginxなどのS2I Builder Imageをカタログ上で選択，あるいはカタログ上に追加でき，アプリケーションを簡単にOCP上にデプロイできます。

新規にプロジェクトを作成し，サンプルのコードとPython用のS2I Builder Imageを使ってコンテナイメージを作成し，アプリケーションをOCP上にデプロイしてみましょう。

### 3-3-1. プロジェクト(Namespace+α)の作成
1. Home > Projects > Create Project を選択します

    ![](images/create_project.png)

1. プロジェクト名を指定し，**Create** を選択します
    
    プロジェクト名には，**必ずご自身のログイン時のユーザー名 (例: "user01a")** を指定してください。

    ![](images/create_project_input_projName.png)

    >Tips:
    >
    >OCPではプロジェクトを作成することで，新規Namespace(=プロジェクト名)が生成されます。NamespaceはK8sクラスターを論理的に分離させることが可能なK8sリソースの一種です。例えば，アプリA用のNamespaceを`ns_appa`，アプリB用のNamespaceを`ns_appb`のように作成することで，同一のK8sクラスター内に存在するns_appaとns_appbが干渉しないように構成することも可能です。

    ![](images/create_project_result.png)

### 3-3-2. カタログでソースを指定してビルド&デプロイ
1. Catalog > Developer Catalog > Python を選択します

    ![](images/developer_catalog.png)

    >Tips:
    >
    >Developer CatalogからPythonアプリケーションを作成することで以下のリソースが作成されます。
    >- Build config
    >    - Gitリポジトリからソースコードをビルド
    >- Image stream
    >    - ビルド済イメージのトラッキング
    >- Deployment config    
    >    - イメージ変更の際に新リビジョンにロールアウト
    >- Service
    >    - クラスター内にワークロードを公開
    >- Route
    >    - クラスター外にワークロードを公開

1. **Create Application** を選択して，任意の**アプリケーション名** と **Gitリポジトリ** を指定します

    - Name:`任意の名前(例: mypyapp)`
    - Git Repoaitory: `https://github.com/sclorg/django-ex.git` 
      - ("Try Sample↑" をクリックするとURLがセットされます)

    ![](images/developer_catalog_choose_python_1.png)


    ![](images/developer_catalog_choose_python_2.png)
    
1. 最後に **Create** を選択します

    >Tips:
    >
    >指定した名前のアプリケーション(コンテナ)が動作するPodがデプロイされました。
    >OCPコンソールで，Workloads > Pods > アプリ名-x-xxxx のように辿ることで確認できます。


    下図のように，デプロイ直後は **"0 of 1 pods"** のように動作準備中の状態です。
    
    ![](images/developer_catalog_deploy_pod_result_1.png)

    少し待つと，下図のように **"1 of 1 pods"** のように正常に動作した状態を確認できます。
    
    ![](images/developer_catalog_deploy_pod_result_2.png)
    
    
### 3-3-3. 外部からアクセスするための Route を作成
1. Networking > Routes > Create Route を選択します



1. **Route名**，対象アプリ用の**Service**，**Port** を指定します
    - Name: **`ログイン時に使用したご自身のユーザー名 (例: user01a)`**
    - Service: `mypyapp`
    - Target Port: `8080 → 8080(TCP)`
1. 最後に **Create** を選択します

    >Tips:
    >
    >Networking > Routes > ルート名 のように辿ることで確認できます。

### 3-3-4. アプリケーションの動作確認
1. Networking > Routes > ルート名 を選択し，Location欄にあるリンクを開きます
    例: `http://mypyroute-myprj.apps.ocp41-ipi-0611.k8show.net`

    ![](images/access_application.png)

1. Pythonアプリのサンプルページが表示されることを確認します

    ![](images/access_application_result.png)
 
## 3-4. [Trial works] - OCP上にアプリをデプロイ
お題: 

「**PythonのBlogアプリをS2Iビルドしてみよう**」

ヒント:

```
- Project名(NameSpace): blog-<yourID>
- BaseImage(BuilderImage): Python 3.5
- Git Repository: https://github.com/openshift-katacoda/blog-django-py
- Routes名: blog
```

---
以上で，コンテナイメージのビルドとデプロイ は完了です。  
