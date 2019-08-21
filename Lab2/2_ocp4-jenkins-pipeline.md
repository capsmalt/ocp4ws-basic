# 2. Jenkinsベースのビルドパイプラインの利用
自動化ツールとして有名なOSSのJenkinsを使ったビルドパイプラインを体験してみましょう。

## 2-1. 諸注意
### 2-1-1. OpenShift4におけるデリバリーパイプラインについて
- S2I (Source to Image)
- Jenkins Pipeline
- Tekton Pipeline

### 2-1-2. 事前準備
## 2-2. OCP4上でのJenkinsビルドパイプラインの利用
今回はCLI操作をメインに使用して進めてみましょう。

### 2-2-1. Jenkinsコンテナのインストール
1. 自身用の新規プロジェクトを作成します  **(例: user01a-jenkins)**

    ```
    $ oc new-project user01a-jenkins (<== ご自身のプロジェクト名)
    $ oc project
    Using project "user01a-jenkins" on server XXXXXXX
    
    上記のように出力確認できればOKです
    ```
    
1. Jenkinsテンプレートを使用してJenkinsのインスタンスをデプロイします

    ```
    $ oc get templates -n openshift
    $ oc get templates -n openshift | grep jenkins

    jenkins-ephemeral: 永続化なし <== 今回はこちらを使用
    jenkins-persistent: 永続化あり

    $ oc new-app jenkins-ephemeral -n user01a-jenkins  # -n 作成したプロジェクト名
    $ oc get pods
    $ oc project
    ```

### 2-2-2. Jenkinsへのパイプライン設定を構成
1. Jenkinsにパイプライン設定(nodejs-sample-pipeline)を入れます

    ```
    $ oc create -f https://raw.githubusercontent.com/openshift/origin/master/examples/jenkins/pipeline/nodejs-sample-pipeline.yaml
    
    $ oc get buildconfigs
    nodejs-sample-pipeline  # oc createで作成されたPipeline
    
    $ oc get buildconfig/nodejs-sample-pipeline -o yaml　# 中身を確認

### 2-2-3. ビルドパイプラインの実行
1. パイプラインを使用してビルドします

    ```
    $ oc start-build nodejs-sample-pipeline
    ```

## 2-2-4. ビルドパイプラインの動作確認
1. JenkinsのUIに接続してパイプラインの進捗状況を確認します

    ```
    $ oc get route
    出力結果のLocation情報をコピーしてブラウザで確認します
    ```
    
    OCPのログイン情報を使用してJenkinsのUIにログインします
    
    ![](images/jenkins_login_1.png)
    
    htpasswdを選択し，その後ログイン情報を入力します(例: user01a, ocpuser)
    
    ![](images/jenkins_login_2.png)
    
    **自身のプロジェクト名** を選択します(例: user01a-jenkins)
    
    ![](images/jenkins_ui_1.png)

    **プロジェクト名/パイプライン名** を選択します (例: user01a-jenkins/nodejs-sample-pipeline)
    
    ![](images/jenkins_ui_2.png)

    時間経過とともにパイプラインのステージがだんだん右側に伸びていくことが確認できます

    ![](images/jenkins_pipeline.png)

    ※パイプラインのステージの書き方は，前述の `oc get buildconfig/nodejs-sample-pipeline -o yaml`で確認できます

>Tips:
>
>ちなみに，Lab1ではOCPコンソール上でGUI操作で上記と同様の作業を行っていました。
>具体的には，**カタログ(Developer Catalog)** からPythonテンプレートを選択して，ソースコードとbuilder image(Python)を合体させることでコンテナイメージを作成し，デプロイしていました。
>
>また，OCPではJenkinsに限らず，ランタイムや他ミドルウェア，ソフトウェアなど多数のテンプレートを用意しています。自身(自社)でよく使うテンプレートを自作してカタログ上に追加することも可能です。その場合は，S2Iスクリプトを書く必要があります。

---
以上で，Jenkinsベースのビルドパイプラインの利用は完了です。  
