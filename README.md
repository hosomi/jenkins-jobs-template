# jenkins-jobs-template

Jenkins Job パイプライン(Jenkinsfile)の基礎的なテンプレート。  
環境の構築から行い、基本的な Jenkins の操作、パイプラインの構文、シンタックスを定義してジョブを実行して結果を確認できます。  
　  
``番号順で行うと環境設定からパイプラインの基本的な構文、実行結果の確認方法等理解できます。``  
　  
## 1. Setup

### 1.1 Node

:exclamation: ``ご注意：Node は本番環境を想定して構築している内容ではありません。``

### 1.1.1 Jenkins master

[こちら](documents/setup-master) の手順で Jenkins master を Dcoker で構築します。 
また、基本的な Docker 操作も記載しています。  
master のセットアップが終わったら 1.1.2 Jenkins slave 構築前に [Swarm plugin](documents/plugins/swarm/) を追加してください。  


### 1.1.2 Jenkins slave  

[こちら](documents/setup-slave-linux) の手順で Jenkins slave を Dcoker で構築します。    

### 2. Jenkinsfile エディター

テキストエディタなら何でも良いですが、Visual Studio Code を推奨します。  
　  
* [Visual Studio Code – コード エディター | Microsoft Azure](https://azure.microsoft.com/ja-jp/products/visual-studio-code/)
* [JenkinsFile Support - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ivory-lab.jenkinsfile-support)  Jenkinsfile をサポート、比較的更新が多いものを選択。

## 3. プラグイン導入

テンプレートの動作を確認する為に追加で必要なプラグインです。  
（Required に :heavy_check_mark: が付いているものは必須です。）  

| plugins | Required | Description 
| ----- | :---: | ---- 
| [Swarm](https://plugins.jenkins.io/swarm/) | :heavy_check_mark: | 通常は master から slave に接続しますが、このプラグインは slave から master に接続できるようになります。  [[追加手順]](documents/plugins/swarm/)  
| [Blue Ocean](https://www.jenkins.io/doc/book/blueocean/) | :heavy_check_mark: | パイプラインの視覚化で利用します。  [[追加手順と利用方法]](documents/plugins/blue-ocean/)  
| [NodeJS](https://plugins.jenkins.io/nodejs/) | - | Jenkins から NodeJS を利用できるようになります。  [[追加手順と利用方法]](documents/plugins/nodejs/)  


## 4. Templates

* 基本的なパイプラインジョブ作成手順は[こちら](documents/pipeline-script-basic/)を参照してください。  
* 定義を Pipeline script from SCM を選択して作成する手順は[こちら](documents/pipeline-script-from-scm/)を参照してください。  
　  
何れも master ノードで hello world を表示する手順です。  
　  
---

凡例：  
 * Jenkinsfile : :page_facing_up: アイコンのリンク先にパイプラインスクリプトを添付しています。  
 * Script Path : Pipeline script from SCM を選択して Script Path の内容を貼り付けて実行できます。  
 * Results : :page_with_curl: に結果のスクリーンショットを添付しています。  

### 4.1 Syntax

このテンプレートで利用する主な Syntax です。  
何れも master ノードで動作する Jenkinsfile です。  


| Syntax | Jenkinsfile | Script Path | Description | Results
| ----- | :---------: | ----------- | ----------- | :---:
| [node](https://www.jenkins.io/doc/book/pipeline/#node) | [:page_facing_up:](templates/syntax/syntax-node.Jenkinsfile) | templates/syntax/syntax-node.Jenkinsfile | パイプラインの作業をノードブロック内に制限します、必須ではありませんが意図的に指定ノードのみで作業させたいときに指定する重要な Synatax です。  | [:page_with_curl:](documents/syntax/node/node-01.png) 
| [stage](https://www.jenkins.io/doc/book/pipeline/#stage)  | [:page_facing_up:](templates/syntax/syntax-stage.Jenkinsfile) | templates/syntax/syntax-stage.Jenkinsfile | ビルド、テスト、デプロイ等のタスク単位で進捗状況を視覚化または表示する単位で纏める事ができます。当解説では主に視覚化を目的で Blue Ocean プラグインを利用しています。   | [:page_with_curl:](documents/syntax/stage/stage-01.png) 
| [parallel](https://www.jenkins.io/doc/book/pipeline/syntax/#parallel) | [:page_facing_up:](templates/syntax/syntax-parallel.Jenkinsfile) | templates/syntax/syntax-parallel.Jenkinsfile | 並列で各タスク処理するように指定する Syntax です。[Jenkinsfile](templates/syntax/syntax-parallel.Jenkinsfile) は並列で複数のリポジトリから git clone してビルド → テスト → デプロイするパターンのテンプレートです。   |  [:page_with_curl:](documents/syntax/parallel/parallel-01.png) 


その他にも多数の Syntax があります、公式の [Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/) を参照ください。



### 4.2 single node

単一ノードのみで動作するテンプレートです。


| Title | Node | Syntax | Jenkinsfile | Script Path | Description | Results
| ----- | ---- | ---- | :---------: |----------- |-----------  | :---:
| hello world | master | [node](https://www.jenkins.io/doc/book/pipeline/#node), [sh](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script)  | [:page_facing_up:](templates/single-node/master-hello-world.Jenkinsfile) | templates/single-node/hello-world.Jenkinsfile | master ノードのシェルスクリプトで echo します。 | [:page_with_curl:](documents/single-node/master-hello-world/master-hello-world-01.png), [:page_with_curl:](documents/single-node/master-hello-world/master-hello-world-02.png) 
| hello world | linux-slave | " | [:page_facing_up:](templates/single-node/linux-slave-hello-world.Jenkinsfile) | template/single-node-only/linux-slave-hello-world.Jenkinsfile | linux-salve のシェルスクリプトで echo します。  [[作成手順補足]](documents/scm-linux-slave/)  | [:page_with_curl:](documents/single-node/linux-slave-hello-world/linux-slave-hello-world-01.png), [:page_with_curl:](documents/single-node/linux-slave-hello-world/linux-slave-hello-world-02.png) 

### 4.3 multi nodes

複数ノードで動作するテンプレートです。 


| Title | Nodes | Syntax | Jenkinsfile | Script Path | Description | Results
| ----- | ---- | ---- | :---------: |----------- |----------- | :---: 
| multiple nodes hello world | master, linux-slave | [node](https://www.jenkins.io/doc/book/pipeline/#node), [sh](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script)  | [:page_facing_up:](templates/multi-node/master-linux-node-hello-world.Jenkinsfile) | templates/multi-node/master-linux-node-hello-world.Jenkinsfile |  master → linux-slave の順で echo します。 | [:page_with_curl:](documents/multi-node/master-linux-node-hello-world/master-linux-node-hello-world-01.png), [:page_with_curl:](documents/multi-node/master-linux-node-hello-world/master-linux-node-hello-world-02.png)
| multiple nodes stage hello world | master, linux-slave | [node](https://www.jenkins.io/doc/book/pipeline/#node), [stage](https://www.jenkins.io/doc/book/pipeline/#stage), [sh](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script)  | [:page_facing_up:](templates/multi-node/master-linux-node-hello-world-stage.Jenkinsfile) | templates/multi-node/master-linux-node-hello-world-stage.Jenkinsfile |  master → linux-slave の順でステージブロックで echo します。 [[ステージブロック補足]](documents/pipelines/stage-block/) | [:page_facing_up:](documents/multi-node/master-linux-node-hello-world-stage/master-linux-node-hello-world-stage-01.png), [:page_facing_up:](documents/multi-node/master-linux-node-hello-world-stage/master-linux-node-hello-world-stage-02.png) 
| multiple nodes stage parallel hello world | master, linux-slave | [node](https://www.jenkins.io/doc/book/pipeline/#node), [stage](https://www.jenkins.io/doc/book/pipeline/#stage), [parallel](https://www.jenkins.io/doc/book/pipeline/syntax/#parallel), [sh](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script)  | [:page_facing_up:](templates/multi-node/master-linux-node-stage-parallel.Jenkinsfile) | templates/multi-node/master-linux-node-stage-parallel.Jenkinsfile |  master, linux-slave の並列で echo します。[[parallel 補足]](documents/pipelines/parallel-syntax/) | [:page_facing_up:](documents/multi-node/master-linux-node-stage-parallel/master-linux-node-stage-parallel-01.png), [:page_facing_up:](documents/multi-node/master-linux-node-stage-parallel/master-linux-node-stage-parallel-02.png)


### 5. example

より実用的なパイプラインジョブの example です。  
追加でプラグイン導入、slave の環境設定が必要になります。  
  
| example | Nodes | Syntax | Jenkinsfile | Script Path | Description | Results
| ----- | ---- | ---- | :---------: |----------- |----------- | :---: 
| Angular build | linux-slave | [deleteDir](https://www.jenkins.io/doc/pipeline/steps/workflow-basic-steps/#deletedir-recursively-delete-the-current-directory-from-the-workspace), [checkout](https://www.jenkins.io/doc/pipeline/steps/workflow-scm-step/), nodejs, [dir](https://www.jenkins.io/doc/pipeline/steps/workflow-basic-steps/#dir-change-current-directory), [archiveArtifacts](https://www.jenkins.io/doc/pipeline/steps/core/#archiveartifacts-archive-the-artifacts), [fingerprint](https://www.jenkins.io/doc/pipeline/steps/core/#fingerprint-record-fingerprints-of-files-to-track-usage) |  [:page_facing_up:](templates/example/example-linux-slave-angular-build.Jenkinsfile) | templates/example/example-linux-slave-angular-build.Jenkinsfile | linux-slave で Angular10 のプロジェクトをビルドして成果物に保存して指紋もとります。 [NodeJS のプラグインが必要です。](https://plugins.jenkins.io/nodejs/)  | [:page_facing_up:](documents/example/example-angular-build/example-angular-build-01.png), [:page_facing_up:](documents/example/example-angular-build/example-angular-build-02.png), [:page_facing_up:](documents/example/example-angular-build/example-angular-build-03.png)
| .NET Core build | linux-slave | [deleteDir](https://www.jenkins.io/doc/pipeline/steps/workflow-basic-steps/#deletedir-recursively-delete-the-current-directory-from-the-workspace), [checkout](https://www.jenkins.io/doc/pipeline/steps/workflow-scm-step/), [archiveArtifacts](https://www.jenkins.io/doc/pipeline/steps/core/#archiveartifacts-archive-the-artifacts), [fingerprint](https://www.jenkins.io/doc/pipeline/steps/core/#fingerprint-record-fingerprints-of-files-to-track-usage) | [:page_facing_up:](templates/example/example-linux-slave-example-linux-slave-dotnet-build.Jenkinsfile) | templates/example/example-linux-slave-example-linux-slave-dotnet-build.Jenkinsfile | linux-slave で .NET Core のプロジェクトをビルドして成果物に保存して指紋もとります。  [linux-slave に .NET Core SDK のインストールが必要です。](documents/example/example-dotnetcore-build/) | [:page_facing_up:](documents/example/example-dotnetcore-build/example-dotnetcore-build-01.png), [:page_facing_up:](documents/example/example-dotnetcore-build/example-dotnetcore-build-02.png), [:page_facing_up:](documents/example/example-dotnetcore-build/example-dotnetcore-build-03.png) 


---

## :book: リファレンス：  

* [Jenkins](https://www.jenkins.io/)  
* [Jenkins User Documentation](https://www.jenkins.io/doc/)  
  * [Getting started with Pipeline](https://www.jenkins.io/doc/book/pipeline/getting-started/)
  * [Pipeline](https://www.jenkins.io/doc/book/pipeline/)
  * [Pipeline Examples](https://www.jenkins.io/doc/pipeline/examples/)
  * [Using a Jenkinsfile](https://www.jenkins.io/doc/book/pipeline/jenkinsfile/)    










　  
　  
　  
　  
　  
　  

* * *

###### :copyright: 商標について

<sup>当ドキュメントに記載されている会社名、システム名、製品名は一般に各社の登録商標または商標です。</sup>  
<sup>なお、本文および図表中では、「™」、「®」は明記しておりません。</sup>  

###### 免責事項  
<sup>当ドキュメント上の掲載内容については細心の注意を払っていますが、その情報に関する信頼性、正確性、完全性について保証するものではありません。</sup>  
<sup>掲載された内容の誤り、および掲載された情報に基づいて行われたことによって生じた直接的、また間接的トラブル、損失、損害については、筆者は一切の責任を負いません。</sup>  
<sup>また当ドキュメント、およびドキュメントに含まれる情報、コンテンツは、通知なしに随時変更されます。</sup>  


