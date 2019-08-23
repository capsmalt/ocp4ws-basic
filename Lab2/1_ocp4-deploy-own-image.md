# 1. 既存のコンテナイメージのデプロイ
## 1-1. 諸注意
### 1-1-1. 既存コンテナイメージをデプロイする方法について
- 既存コンテナイメージ -> OpenShift4上にデプロイ
- Dockerfile -> コンテナイメージビルド -> OpenShift4上にデプロイ
  - s2ibuildできる環境が必要
  - cri-o + buildah環境(RHEL8)が必要

### 1-1-2. 事前準備
## 1-2. 既存コンテナイメージをOpenShift4にデプロイ
Lab1ではソースコードとbuidler imageを合体させてコンテナイメージを作成し，OpenShift4上にデプロイしました。  
Lab2の最初のステップでは，**既にコンテナイメージ化済**のターミナルアプリケーションをOpenShift4上にデプロイする手順を実施します。

1. プロジェクトを選択します
    
    プロジェクトは，**必ずご自身のログイン時のユーザー名 (例: "user01a")** のものを選択してください。    
    Home > Project > user01a (例)
    
    ![](images/create_application_using_existedImage_1.png)

1. **Add > Deploy Image** のように選択します

    ![](images/create_application_using_existedImage_2.png)

1. **Namespace**(プロジェクト名)，と**Image Name** を指定します
    - Namespace: `各自の作成済プロジェクト(例: user01a)`
    - Image Name: `quay.io/openshiftlabs/workshop-terminal:2.4.0`

    ![](images/create_application_using_existedImage_3.png)

1. **検索ボタン** をクリックし，Name(workshop-terminal)を確認して，**Deploy** を選択します

    ![](images/create_application_using_existedImage_4.png)

## 1-3. Routerの作成と動作確認
### 1-3-1. 外部アクセスのためのRouterを作成
1. 外部からアクセスするための **Route** を作成します

    Networking > Routes > Create Route を選択し，以下を指定した後 **Create** を選択します
    - Name: `Route名(例: workshop-terminal)`
    - Service: `対象アプリ用のService(例: workshop-terminal)`
    - Target Port: `10080 → 10080(TCP)`

    ![](images/create_route_for_existedImage.png)

### 1-3-2. アプリケーションの動作確認
1. Location欄にあるリンクを開きます
    例: `http://workshop-terminal-myprj.apps.OpenShift41-ipi-0611.k8show.net`

    ![](images/create_route_for_existedImage_result.png)

1. Terminalアプリが表示されることを確認します

    ![](images/create_route_for_existedImage_result_2.png)

## 1-4. [Trial works] - OpenShift4上にアプリをデプロイ2
お題: 

「**workshop-terminalアプリの特定バージョン(2.10.2)を新規にデプロイして，Routerの振り先を変更してみよう**」

ヒント:

```
- 既存Project名(Namespace): <yourID>
- ContainerImage: quay.io/openshiftlabs/workshop-terminal:2.10.2
- 新規デプロイ時に指定する名前: <yourID>-workshop-terminal
- Service名: <yourID>-workshop-terminal
  - Routeからの振り先Service
- Route名: workshop-terminal
  - 振り先を指定する
```

---
以上で，既存のコンテナイメージのデプロイは完了です。  
次に [Jenkinsベースのビルドパイプラインの利用](2_OpenShift4-jenkins-pipeline.md) のハンズオンに進みます。
