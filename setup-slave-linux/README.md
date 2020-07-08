# Jenkins Slave Linux

Jenkin Slave として CentOS7.x 系で構築します。    
master と同様に Docker image を利用します。  

:link: [centos - Docker Hub](https://hub.docker.com/_/centos)    

## 1 CentOS image download

``docker pull centos:centos7``（CentOS7 最新） :  

```
> docker pull centos:centos7
centos7: Pulling from library/centos
524b0c1e57f8: Pull complete
Digest: sha256:e9ce0b76f29f942502facd849f3e468232492b259b9d9f076f71b392293f1582
Status: Downloaded newer image for centos:centos7
docker.io/library/centos:centos7
```


## 2 Docker image を確認する

``docker images`` :

```
> docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
jenkins/jenkins     lts                 60f81923d099        2 weeks ago         658MB
centos              centos7             b5b4d78bc90c        8 weeks ago         203MB
```

この時点では jenkins master イメージと今回ダウンロードした centos の 2 つあります。

## 3 Jenkins master の ip address を確認

slave から master に接続するので master の ip address の情報が必要になります。  
事前に確認してメモしておきます。  


### 3.1 Jenkins master の CONTAINER ID を調べる


``docker ps -a``:

```
> docker ps -a
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                              NAMES
13920882163e        jenkins/jenkins:lts   "/sbin/tini -- /usr/…"   6 days ago          Up 7 minutes        0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   magical_albattani
```

CONTAINER ID をメモしておく。


### 3.2 Jenkins master のコンテナを調べる

先程メモした CONTAINER ID から調べる。

``docker inspect [CONTAINER ID]``:

```
> docker inspect 13920882163e
[
    {
        "Id": "13920882163e7cade54622f29b2be93e23e770f2de1a051afab59eefd0ba1c61",
        "Created": "2020-06-27T10:37:50.477212166Z",

省略

            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "5175fdac81213d6804fa4d091e65b071ae9766d3e58c4f410dad389ea69b50d4",
                    "EndpointID": "4eecbb1b53dc194c82b42b840e33150473ab730247acd5bdaeb2e4b8cc252030",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

IPAddress の項目が Jenkins master の ip address です。  
この例の場合、172.17.0.2 となります。







## 4 CentOS7 のコンテナを起動

``docker run -itd --privileged -p 2222:22 --name centos7 centos:centos7 /sbin/init --login``:  

```
> docker run -itd --privileged -p 2222:22 --name centos7 centos:centos7 /sbin/init --login
534e21afeda8b07399c625a28e05a107580035f808faab0951207c1e7f4a3b03
```

| オプション | 内容
| ----- | ----- 
| -itd  | コンテナのプロセスに tty を割り当ててバックグランドでコンテナを起動。
| -p | ポート指定(ssh のみ 2222(ホスト) -> 22(コンテナ))。
| --privileged | systemctl を使えるようにする、/sbin/init とセット。  
| –-name | コンテナの名前を指定。  
| /sbin/init | /sbin/init を PID=1 のメインプロセスにして systemctl を使用できるようにする。  
| --login | ログインプロセスを通す。  

## 4.1 コンテナが起動しているか確認する

``docker ps -a`` :

```
> docker ps -a
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                              NAMES
534e21afeda8        centos:centos7        "/sbin/init"             26 seconds ago      Up 26 seconds       0.0.0.0:2222->22/tcp                               centos7
13920882163e        jenkins/jenkins:lts   "/sbin/tini -- /usr/…"   6 days ago          Up 7 minutes        0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   magical_albattani
```

jenkins master と今回起動した centos7 の 2 つあります。  
STATUS の項目で Up が起動済みです、Exited は停止状態です。  

## 5 CentOS7 のコンテナに SSH ログイン(tty)

コンテナの CentOS7 にパッケージの追加と設定を行う為、SSH ログイン(tty) します。

``docker exec -it centos7 /bin/bash --login``:  

```
> docker exec -it centos7 /bin/bash
[root@534e21afeda8 /]#
```

CentOS7 の Bash がコマンドを待ち受ける状態になります。  


### 5.1 CentOS7 のコンテナに必要なパッケージをインストール

JDK8 と Git Client を yum でインストールします。

``yum install -y java-1.8.0-openjdk.x86_64 git``: 


```bash
[root@534e21afeda8 /]# yum install -y java-1.8.0-openjdk.x86_64 git
Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
 * base: ftp-srv2.kddilabs.jp
 * extras: ftp-srv2.kddilabs.jp
 * updates: ftp-srv2.kddilabs.jp
Resolving Dependencies

省略

Complete!
```

### 5.2 JAVA_HOME を設定

salve を起動するアプリケーションは Java のランタイムだけではなく JAVA_HOME の設定が必要です。  

#### 5.2.1 /etc/profile に JAVA_HOME を設定 

``vi /etc/profile``:  

```bash
[root@534e21afeda8 jre]# vi /etc/profile
```

vi のキーボード操作 

``shift + g`` 最終行に移動。  
``o`` 一行追加して編集モード。  
``export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.252.b09-2.el7_8.x86_64/jre`` 入力または貼り付けてください。  
``esc`` 一旦入力を終了。  
``:`` コマンドモードに移行。  
``wq `` 保存して終了（wq の次に enter）。  


#### 5.2.2 /etc/profile を読み込む

一時的に環境変数を読み込む。  

``source /etc/profile``:  

```bash
[root@534e21afeda8 jre]# source /etc/profile
```


### 5.3 Jenkins slave を起動する

#### 5.3.1 Slave を起動するアプリケーションを git clone して取得


``git clone https://github.com/hosomi/swarm-launcher.git``:

```bash
[root@534e21afeda8 /]# git clone https://github.com/hosomi/swarm-launcher.git
Cloning into 'swarm-launcher'...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 18 (delta 1), reused 2 (delta 0), pack-reused 12
Unpacking objects: 100% (18/18), done.
```

#### 5.3.2 git clone したアプリケーションの設定ファイルを編集

swarm-launcher の設定ファイルを編集する

```bash
[root@534e21afeda8 /]# cd swarm-launcher/
[root@534e21afeda8 swarm-launcher]# vi build.gradle
```

args の項目を編集します。


| args | 説明 | 設定値
| ----- | ----- | -----
| -master  | Jenkins master の URL | 3.2 でしらべた ip address、今回の例の場合、http://172.17.0.2:8080  
| -name | スレーブの名前のプレフィックス | ``linux-slave``  
| -username | Jenkins にログインするID | Jekins にログインするユーザ ID を設定。  
| -password | Jenkins にログインするID のパスワード | Jekins にログインするユーザ ID のパスワードを設定。  
| -fsroot | スレーブのワークスペースルートパス | ``/root``  
| -labels | スレーブのラベル | ["linux-slave","example1"].join(" ")  

色が変わっている箇所はそのまま入力してください。  
その他の項目は必須ではありません、[こちら](https://github.com/hosomi/swarm-launcher) の説明の内容を確認して必要であれば編集してください。  


#### 5.3.3 実行するファイルを実行権限を付与


``chmod +x gradlew``:

```bash
[root@534e21afeda8 swarm-launcher]# chmod +x gradlew
```


#### 5.3.4 起動

``./gradlew``:  

```bash
[root@534e21afeda8 swarm-launcher]# ./gradlew
Downloading https://services.gradle.org/distributions/gradle-4.6-bin.zip
......................................................................
Unzipping /root/.gradle/wrapper/dists/gradle-4.6-bin/4jp4stjndanmxuerzfseyb6wo/gradle-4.6-bin.zip to /root/.gradle/wrapper/dists/gradle-4.6-bin/4jp4stjndanmxuerzfseyb6wo
Set executable permissions for: /root/.gradle/wrapper/dists/gradle-4.6-bin/4jp4stjndanmxuerzfseyb6wo/gradle-4.6/bin/gradle

省略

INFO: Connected
<-------------> 0% EXECUTING [18s]
> :start
> IDLE
> IDLE
```

正常に起動できた場合、IDLE 状態で待機状態になります。  
ブラウザから Jenkins ログイン（``http://localhost:8080``）して Slave が追加されている確認。  


![Slave 確認](setup-slave-linux-01.png)    

表示されていたら作業は完了です。  
Slave を終了するには ``CTRL + C`` で停止できます。  



---

これからの作業はオプション。  

### 6 slave 起動を自動化

docker start 毎に手動で slave 起動は面倒なので自動化します。  
既に slave が起動済みの場合、 ``CTRL + C`` で終了させます。  

### 6.1 サービス起動するシェルスクリプトファイルを作成

``vi /swarm-launcher/slave.sh``:

```bash
[root@54d1669b20a1 /]# vi /swarm-launcher/slave.sh
```

slave.sh のファイル内容：  

```bash
#!/bin/bash

cd /swarm-launcher
./gradlew
```

### 6.2 Unit 定義ファイルを作成

``vi /etc/systemd/system/jenkins-slave.service``:  

```bash
[root@54d1669b20a1 /]# vi /etc/systemd/system/jenkins-slave.service
```

jenkins-slave.service のファイル内容：  

```ini
[Unit]
Description = Jenkins slave service

[Service]
User = root
EnvironmentFile=/etc/systemd/env
ExecStart = /swarm-launcher/slave.sh
Restart = always
Type = simple

[Install]
WantedBy = multi-user.target
```

### 6.3 EnvironmentFile を作成

``vi /etc/systemd/env``:  

```bash
[root@54d1669b20a1 /]# vi /etc/systemd/env
```

env ファイルの内容：  

```ini
JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.252.b09-2.el7_8.x86_64/jre"
```

### 6.4 サービス起動するシェルスクリプトファイルに実行権限を付与

``chmod +x /swarm-launcher/slave.sh`` 

```bash
[root@54d1669b20a1 /]# chmod +x /swarm-launcher/slave.sh
```

### 6.4 サービスに登録、自動起動有効化
　  
``systemctl enable jenkins-slave``:  

```bash
[root@54d1669b20a1 /]# systemctl enable jenkins-slave
Created symlink from /etc/systemd/system/multi-user.target.wants/jenkins-slave.service to /etc/systemd/system/jenkins-slave.service.
```
　  
### 6.5 サービスを起動

``systemctl start jenkins-slave``:  

```
[root@54d1669b20a1 /]# systemctl start jenkins-slave
```

### 6.6 サービスが起動しているか確認

``systemctl status jenkins-slave``:  

```bash
[root@54d1669b20a1 /]# systemctl status jenkins-slave
● jenkins-slave.service - Jenkins slave service
   Loaded: loaded (/etc/systemd/system/jenkins-slave.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2020-07-05 03:28:42 UTC; 19min ago
 Main PID: 420 (slave.sh)
   CGroup: /docker/54d1669b20a12b175754c26d7b18eadcfa692a156e909316152f6adf55d38653/system.slice/jenkins-slave.service
           ├─420 /bin/bash /swarm-launcher/slave.sh
           ├─421 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.252.b09-2.el7_8.x86_64/jre/bin/java -Dorg.gradle.appname=...
           ├─442 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.252.b09-2.el7_8.x86_64/jre/bin/java -XX:+HeapDumpOnOutOfM...
           └─477 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.252.b09-2.el7_8.x86_64/jre/bin/java -Dfile.encoding=UTF-8...
           ‣ 420 /bin/bash /swarm-launcher/slave.sh

Jul 05 03:28:45 54d1669b20a1 slave.sh[420]: Jul 05, 2020 3:28:45 AM hudson.remoting.jnlp.Main$CuiListener status
Jul 05 03:28:45 54d1669b20a1 slave.sh[420]: INFO: Handshaking
Jul 05 03:28:45 54d1669b20a1 slave.sh[420]: Jul 05, 2020 3:28:45 AM hudson.remoting.jnlp.Main$CuiListener status
Jul 05 03:28:45 54d1669b20a1 slave.sh[420]: INFO: Connecting to 172.17.0.2:50000
Jul 05 03:28:45 54d1669b20a1 slave.sh[420]: Jul 05, 2020 3:28:45 AM hudson.remoting.jnlp.Main$CuiListener status
Jul 05 03:28:45 54d1669b20a1 slave.sh[420]: INFO: Trying protocol: JNLP4-connect
Jul 05 03:28:45 54d1669b20a1 slave.sh[420]: Jul 05, 2020 3:28:45 AM hudson.remoting.jnlp.Main$CuiListener status
Jul 05 03:28:45 54d1669b20a1 slave.sh[420]: INFO: Remote identity confirmed: 5d:3c:67:85:6e:68:74:04:66:42:...d4:e2
Jul 05 03:28:46 54d1669b20a1 slave.sh[420]: Jul 05, 2020 3:28:46 AM hudson.remoting.jnlp.Main$CuiListener status
Jul 05 03:28:46 54d1669b20a1 slave.sh[420]: INFO: Connected
Hint: Some lines were ellipsized, use -l to show in full.
```

● が緑色で表示されていれば正常に起動済みです。  
次回以降は ``docker start centos7`` のみで slave も起動します。  





　  
　  
　  
　  
　  
　  
　  
　  

* * *

###### :copyright: 商標について

<sup>当ドキュメントに記載されている会社名、システム名、製品名は一般に各社の登録商標または商標です。</sup>  
<sup>なお、本文および図表中では、「™」、「®」は明記しておりません。</sup>  

###### 免責事項  
<sup>当ドキュメント上の掲載内容については細心の注意を払っていますが、その情報に関する信頼性、正確性、完全性について保証するものではありません。</sup>  
<sup>掲載された内容の誤り、および掲載された情報に基づいて行われたことによって生じた直接的、また間接的トラブル、損失、損害については、筆者は一切の責任を負いません。</sup>  
<sup>また当ドキュメント、およびドキュメントに含まれる情報、コンテンツは、通知なしに随時変更されます。</sup>  


