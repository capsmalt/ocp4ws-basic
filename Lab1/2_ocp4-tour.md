# 2. OCP4クラスターへのログインと動作確認
## 2-1. 諸注意
### 2-1-1. OpenShift4へのログイン方法
- **ocコマンド** : kubectlをwrapしたOpenShiftを制御するCLIでログイン
  - `oc login <OCP_MASTER_API>`: 任意のユーザーでログイン
  - `oc get pods -n default`: defaultネームスペースのPodを一覧表示
  - `kubectl get pods -n default` : kubectlも使用可
  - etc.
- **OCPコンソール** : OpenShiftの専用コンソール画面にブラウザからログイン
  - PodやDeployment，Secretなどのワークロードや，ServiceやPVCなどの作成および編集
  - ワークロードやNodeの状態確認やモニタリング
  - カタログからのミドルウェア，アプリケーションのデプロイ
  - クラスター設定やネームスペース，ロール管理などのAdmin作業
  - etc.
### 2-1-2. 事前準備
- For Self-handson
  - OpenShift Container Platform 4.Xクラスター環境
  - ocコマンドのセットアップ
  - system:adminの権限
- For Workshop参加者
  - 踏み台サーバー(Bastion Server)へのアクセス情報
  - OpenShift4クラスターへのアクセス情報

**以降の手順は，Workshop参加者を対象とした内容で記載しています。**  
**Self-handsonとして実施される方は，適宜ご自身の環境情報に読み替えて実施ください。**

## 2-2. OCP4へのログイン
### 2-2-1. ocコマンドによるログイン(oc login)
1. 踏み台サーバー(Bastion Server)にSSHでログインします。

  ```
  $ ssh -i <Private_Key> <Bastion_User_ID>@<Bastion_Server_IP>
  
  y
  ```

  >**※注意: ワークショップ参加者の方は，必ず自身に割当てられた <Bastion_User_ID>，<Bastion_Servier_IP>，<Private_Key> を使用してください。**  
  >
  >
  >例) 「踏み台サーバー(Bastion Server)」のSSHログイン情報
  > - `<Bastion_User_ID>`: **User00**
  > - `<Bastion_Server_IP>`: **1.2.3.4**
  > - `<Private_Key>`: **bs-key.pem**
  >
  >実行例) 
  >```
  >$ ssh -i bs-key.pem User00@1.2.3.4
  >```

2. OCP4クラスターにocコマンドでログインします。

  ```
  $ oc login <OpenShift_API>

  Username: "<User_ID>" を入力
  Password: "<User_PW>" を入力
  ```

  >**※注意: ワークショップ参加者の方は，必ず自身に割当てられた <OpenShift_API>，<User_ID>，<User_PW> を使用してください。**  
  >
  >
  >例) 「OpenShift_API」へのログイン情報
  > - `<OpenShift_API>`: **https://api.group00-ocp4ws-basic.capsmalt.org:6443**
  > - `<User_ID>`: **User00**
  > - `<User_PW>`: **ocppass**
  >
  >実行例) 
  >```
  >$ oc login https://api.group00-ocp4ws-basic.capsmalt.org:6443  
  >Username: User00
  >Password: ocppass
  >```

### 2-2-2. ブラウザからOCP4コンソールへのログイン
ブラウザ(Chrome or Firefox)からOpenShiftのコンソールにログインします。

>**注意: ワークショップ参加者の方は，必ず自身に割当てられた <OpenShift_Console>，<User_ID>，<User_PW> を使用してください。**  
>例) 「OpenShift4コンソール」のログイン情報
> - `<OpenShift_Console>`: **https://console-openshift-console.apps.group00-ocp4ws-basic.capsmalt.org**
> - `<User_ID>`: **User00**
> - `<User_PW>`: **ocppass**
>
>実行例)
> - ブラウザで https://console-openshift-console.apps.group00-ocp4ws-basic.capsmalt.org にアクセス
>   - capsmalt's group を選択
>   - User00 / ocppass を入力してログイン

## 2-3. OCP4クラスターの動作確認
### 2-3-1. Nodeの確認
### 2-3-2. ワークロードの確認
### 2-3-3. モニタリング機能の確認

---
以上で，OCP4クラスターへのログインと動作確認は完了です。  
次に [コンテナイメージのビルドとデプロイ](3_ocp4-build-deploy.md) のハンズオンに進みます。
