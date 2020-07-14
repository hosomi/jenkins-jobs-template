# Hello, World! Linux Slave

Linux Slave で Hello, World! します。  
基本的には[パイプラインジョブ（Pipeline script from SCM）の作成手順](../pipeline-script-from-scm/)と同じです。  

## パイプランジョブ作成の基本情報を設定

1. 作成するジョブ名に ``linux-slave-only-from-scm`` を入力。
1. 作成するジョブの種類から ``パイプライン`` を選択。
1. OK ボタンをクリック。

## Pipeline script from SCM の基本情報を設定

| 大項目 | 項目 | 設定する内容
| ----- | ----- | -----
| 定義 | - | Pipeline script from SCM
| リポジトリ | SCM | Git
| ” | リポジトリ URL | https://github.com/hosomi/jenkins-jobs-template.git
| ” | 認証情報 | - なし
| ビルドするブランチ | 	ブランチ指定子 (空欄はすべてを指定)	 | */master
| Script Path | - | templates/single-node/linux-slave-hello-world.Jenkinsfile


## 実行結果確認

echo している内容は master と同じですが、
linux-slave 上で動作している事を確認してください。  

![実行結果確認](scm-linux-slave-01.png)   





　  
　  
　  
　  
　  
　  
　  
　  

* * *

###### :copyright: 商標について

<sup>当ドキュメントに記載されている会社名、システム名、製品名は一般に各社の登録商標または商標です。</sup>  
<sup>なお、本文および図表中では、「™」、「®」は明記しておりません。</sup>  

###### 免責事項  
<sup>当ドキュメント上の掲載内容については細心の注意を払っていますが、その情報に関する信頼性、正確性、完全性について保証するものではありません。</sup>  
<sup>掲載された内容の誤り、および掲載された情報に基づいて行われたことによって生じた直接的、また間接的トラブル、損失、損害については、筆者は一切の責任を負いません。</sup>  
<sup>また当ドキュメント、およびドキュメントに含まれる情報、コンテンツは、通知なしに随時変更されます。</sup>  


