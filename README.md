# ハンズオン概要
本ハンズオンは，OpenShift4の基礎編です。  
インフラ向け応用編(Operator他)および開発向け応用編(CodeReady他)のワークショップも不定期開催しています。

OpenShift4基礎編では以下を学びます。
- OpenShift4クラスターの構築 (本日は実施しません。クラスターは構築済です。)
- OpenShift4クラスターへのログインと動作確認
- コンテナイメージのビルドとデプロイ
- Jenkinsベースのビルドパイプラインの使用

### Lab1: [OpenShift4クラスター構築，ログインと動作確認，コンテナイメージのビルド&デプロイ](Lab1)
### Lab2: [Jenkinsベースのビルドパイプラインの使用，その他](Lab2)

# ハンズオン環境
本ハンズオンは，Kubernetesクラスター(OpenShift4)の動作環境としてAWSを使用します。今回は構築済です。  

OpenShift4クラスターに対するCLI操作をを行う際は，クライアントPCから，踏み台サーバー(Bastion Server)にSSH接続し，**ocコマンド** を使って制御します。  
`クライントPC <--SSH--> 踏み台サーバー <--oc--> OpenShift4クラスター`

![](images/handson-env.png)

GUI操作は，クライアントPCのブラウザ(**Chrome/Firefox推奨**)を使用します。  

# 前提
- SSH用ツール (EC2への接続する際に使用します)
- ブラウザ (Google Chrome or Firefox)
- ブラウザでの接続テスト
  - 下記リンクにブラウザからアクセスし，メモ帳アプリに正常アクセスできるか確認してください。
  - http://bit.ly/connectivity-test
  - **接続できない環境の場合はハンズオンを実施できない場合があります (サポートが必要な場合は担当者まで連絡ください)**

# 注意事項
OpenShift4クラスター接続情報など当日の連絡事項 (Etherpad) ==> 当日ご案内します

# タイムテーブル
Red Hat OpenShift Container Platform 4 ワークショップ

|Time|Agenda|Content|
|:---:|:---|:---|
|13:00-13:30|受付||
|(30min)|<講義> OpenShift4概要 と ハンズオン環境への接続|OpenShift4の特徴紹介||
|(60min)|<ハンズオン> Lab1 <br>|OpenShift4インストール手順確認<br>ログインと動作確認<br>コンテナイメージのビルド&デプロイ|
|(15min)|Break||
|(30min)|<講義> OpenShift4でのアプリケーションデプロイ概要|S2I (Source to Image) <br> CI/CD|
|(45min)|<ハンズオン> Lab2 <br>|Jenkinsベースのビルドパイプライン<br>その他コンテンツ|
|(-17:00)|アンケート記入，QA，フリーディスカッションタイム||
