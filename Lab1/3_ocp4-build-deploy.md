# 3. コンテナイメージのビルドとデプロイ
## 3-1. 諸注意
### 3-1-1. OpenShift4におけるデプロイについて
OpenShiftでは，いくつかの方法でアプリケーションをクラスター上にデプロイすることができます。

- 既存のDockerイメージを使ってデプロイする方法
- ソースコードとS2I(ツール)を使ってビルド&デプロイする方法
- ソースコードとDockerfileを使ってビルド&デプロイする方法

### 3-1-2. 事前準備
- 踏み台サーバー(Bastion Server)へのアクセス情報
- OpenShift4クラスターへのアクセス情報

>自身でハンズオンを実施される場合は，事前に以下を準備ください。
> - OpenShift4クラスター環境
> - ocコマンドのセットアップ

## 3-2. OpenShift4コンソールへのログイン
ブラウザを立ち上げて **OpenShift4コンソール** に接続します。

>ログイン方法を再確認したい場合は，[OpenShift4クラスターへのログインと動作確認](2_ocp4-tour.md) または，[ブラウザからOpenShift4コンソールへのログイン](2_ocp4-tour.md#2-2-2-%E3%83%96%E3%83%A9%E3%82%A6%E3%82%B6%E3%81%8B%E3%82%89OpenShift4%E3%82%B3%E3%83%B3%E3%82%BD%E3%83%BC%E3%83%AB%E3%81%B8%E3%81%AE%E3%83%AD%E3%82%B0%E3%82%A4%E3%83%B3)を参照ください。

## 3-3. コンテナイメージのビルドとデプロイ
S2I(Source-to-Image)というツールを使って以下の2つのコンポーネントからコンテナイメージを生成し，コンテナアプリケーションをデプロイします。

- **リポジトリURL** : GitHubなどソースコード格納場所
- **S2I Builder Image** : S2Iスクリプトが含まれているDockerイメージ

またOpenShift4は，カタログ機能(Developer Catalog)を備えています。JavaやPython，nginxなどのS2I Builder Imageをカタログ上で選択，あるいはカタログ上に追加でき，アプリケーションを簡単にOpenShift4上にデプロイできます。

新規にプロジェクトを作成し，サンプルのコードとPython用のS2I Builder Imageを使ってコンテナイメージを作成し，アプリケーションをOpenShift4上にデプロイしてみましょう。

### 3-3-1. プロジェクト(Namespace)の作成
OpenShift4コンソールで各自のプロジェクトを作成しましょう。  

1. [Home] > [Projects] > [Create Project] を選択します。  

    ![](images/ocp4-lab1-3-create-project.png)

    >コンソール右上のユーザー名が自身の<User_ID>であることを確認しましょう

1. プロジェクト名(例: `blog-user00` )を指定し，**Create** を選択します。  
    
    プロジェクト名には，**必ずご自身のログイン時のユーザー名 (例: "user00-lab1-3")** を指定してください。  
    複数人でクラスターを共有しているため，他の人と重複しないプロジェクト名を指定する必要があります。  

    ![](images/ocp4-lab1-3-create-project-blog.png)

    >Tips:
    >
    >OpenShift4ではプロジェクトを作成することで，新規Namespace(=プロジェクト名)が生成されます。NamespaceはK8sクラスターを論理的に分離させることが可能なK8sリソースの一種です。例えば，アプリA用のNamespaceを`ns_appa`，アプリB用のNamespaceを`ns_appb`のように作成することで，同一のK8sクラスター内に存在するns_appaとns_appbが干渉しないように構成することも可能です。

    ![](images/ocp4-lab1-3-create-project-result.png)

### 3-3-2. カタログでソースを指定してビルド&デプロイ
1. [Catalog] > [Developer Catalog] > [Python] テンプレート を選択します

    ![](images/ocp4-lab1-3-devcatalog-python.png)

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

1. アプリケーションのリポジトリなどを指定して，OpenShift4上にアプリケーションをデプロイします。

    - Namespace: `blog-user00`
    - Version: `python:3.6`
    - Name:`任意の名前(例: blog-user00)`
    - Git Repoaitory: `https://github.com/openshift-katacoda/blog-django-py` 
    - Create route: `デフォルト(チェックを外した状態)`

    最初に，**[Create Application]**　を選択します。  
    ![](images/ocp4-lab1-3-devcatalog-python-create.png)
    
    次に，**[リポジトリなどいくつかの項目]** を指定し，最後に **[Create]** を選択します。  

    ![](images/ocp4-lab1-3-devcatalog-python-create-repo.png)
    
    以上の手順で，blogアプリケーションをOpenShift4上にデプロイできました。  
    
    >Tips:  
    >
    >下図のように，デプロイ直後は "0 of 1 pods" のように動作準備中の状態です。  
    >
    >![](images/ocp4-lab1-3-blog-pods-status-0of1.png)  
    >
    >少し待つと，下図のように "1 of 1 pods" のように正常に動作した状態を確認できます。  
    >
    >![](images/ocp4-lab1-3-blog-pods-status-1of1.png)  
    >
    
### 3-3-3. blogアプリケーションのデプロイ状況を確認
1. [Workloads] > [Pods] > [blog-user00(指定したアプリ名)] のように選択します。

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
    例: `http://mypyroute-myprj.apps.OpenShift41-ipi-0611.k8show.net`

    ![](images/access_application.png)

1. Pythonアプリのサンプルページが表示されることを確認します

    ![](images/access_application_result.png)
 
## 3-4. [Trial works] - OpenShift4上にアプリをデプロイ
お題: 

「**PythonのBlogアプリをS2Iビルドしてみよう**」

コンテンツ:

```
- Project名(NameSpace): blog-<yourID>
- BaseImage(BuilderImage): Python 3.5
- Git Repository: https://github.com/openshift-katacoda/blog-django-py
- Routes名: blog
```

---
以上で，コンテナイメージのビルドとデプロイ は完了です。  
