# jenkins-jobs-template

Jenkins Job パイプライン(Jenkinsfile)の基礎的なテンプレート。  
環境の構築から行い、基本的な Jenkins の操作、パイプラインの構文、シンタックスを定義してジョブを実行して結果を確認できます。  
　  
　  
``随時更新中``  



---

:book: 参考：  

* [Jenkins](https://www.jenkins.io/)  
* [Jenkins User Documentation](https://www.jenkins.io/doc/)  
  * [Pipeline](https://www.jenkins.io/doc/book/pipeline/)
* [Using a Jenkinsfile](https://www.jenkins.io/doc/book/pipeline/jenkinsfile/)    

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


| plugins | Description 
| ----- | ---- 
| Swarm plugin | 通常は master から slave に接続しますが、このプラグインは slave から master に接続できるようになります。  [[追加手順]](documents/plugins/swarm/)  
| Blue Ocean | パイプラインの視覚化で利用します。  [[追加手順と利用方法]](documents/plugins/blue-ocean/)  

## 4. Template

基本的なパイプラインジョブ作成手順は[こちら](documents/pipeline-script-basic/)を参照してください。  
また、定義を Pipeline script from SCM を選択して作成する手順は[こちら](documents/pipeline-script-from-scm/)を参照してください。  
何れも master ノードで hello world を表示する手順です。  
　  
### 4.1 sigle node

sigle ノードのみで動作するテンプレートです。

凡例：  
 * Jenkinsfile : :page_facing_up: アイコンのリンク先にパイプラインスクリプトを添付しています。  
 * Script Path : Pipeline script from SCM を選択して Script Path の内容を貼り付けて実行できます。  


| Title | Node | Keyword | Jenkinsfile | Script Path | Description 
| ----- | ---- | ---- | :---------: |----------- |----------- 
| hello world | master | [node](https://www.jenkins.io/doc/book/pipeline/#node), [sh](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script)  | [:page_facing_up:](templates/single-node/master-hello-world.Jenkinsfile) | templates/single-node/hello-world.Jenkinsfile | master ノードのシェルスクリプトで echo します。 
| hello world | linux-slave | " | [:page_facing_up:](templates/single-node/linux-slave-hello-world.Jenkinsfile) | template/single-node-only/linux-slave-hello-world.Jenkinsfile | linux-salve のシェルスクリプトで echo します。  [[作成手順補足]](documents/scm-linux-slave/)  

　  
### 4.2 multi nodes

| Title | Nodes | Keyword | Jenkinsfile | Script Path | Description 
| ----- | ---- | ---- | :---------: |----------- |----------- 
| multiple nodes hello world | master, linux-slave | [node](https://www.jenkins.io/doc/book/pipeline/#node), [sh](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script)  | [:page_facing_up:](templates/multi-node/master-linux-node-hello-world.Jenkinsfile) | templates/multi-node/master-linux-node-hello-world.Jenkinsfile |  master → linux-slave の順で echo します。 
| multiple nodes stage hello world | master, linux-slave | [node](https://www.jenkins.io/doc/book/pipeline/#node), [stage](https://www.jenkins.io/doc/book/pipeline/#stage), [sh](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script)  | [:page_facing_up:](templates/multi-node/master-linux-node-hello-world-stage.Jenkinsfile) | templates/multi-node/master-linux-node-hello-world-stage.Jenkinsfile |  master → linux-slave の順でステージブロックで echo します。 [[ステージブロック補足]](documents/pipelines/stage-block/)
| multiple nodes stage parallel hello world | master, linux-slave | [node](https://www.jenkins.io/doc/book/pipeline/#node), [stage](https://www.jenkins.io/doc/book/pipeline/#stage), [parallel](https://www.jenkins.io/doc/book/pipeline/syntax/#parallel), [sh](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script)  | [:page_facing_up:](templates/multi-node/master-linux-node-stage-parallel.Jenkinsfile) | templates/multi-node/master-linux-node-stage-parallel.Jenkinsfile |  master, linux-slave の並列で echo します。[[parallel 補足]](documents/pipelines/parallel-syntax/)





　  
　  
　  
　  
　  
　  

* * *

###### :copyright: 商標について

<sup>当ドキュメントに記載されている会社名、システム名、製品名は一般に各社の登録商標または商標です。</sup>  
<sup>なお、本文および図表中では、「™」、「®」は明記しておりません。</sup>  

###### 免責事項  
<sup>当ドキュメント上の掲載内容については細心の注意を払っていますが、その情報に関する信頼性、正確性、完全性について保証するものではありません。</sup>  
<sup>掲載された内容の誤り、および掲載された情報に基づいて行われたことによって生じた直接的、また間接的トラブル、損失、損害については、筆者は一切の責任を負いません。</sup>  
<sup>また当ドキュメント、およびドキュメントに含まれる情報、コンテンツは、通知なしに随時変更されます。</sup>  


