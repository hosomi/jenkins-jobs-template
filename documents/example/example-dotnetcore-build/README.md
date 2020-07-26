# example .NET Core Build
　  

## 1. .NET Core SDK を linux-slave にインストール

linux-slave に .NET Core をインストールしてビルドできるようにします。  
公式のドキュメントを参考にインストールします。  
　  
:link: [CentOS に .NET Core をインストールする - .NET Core | Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/core/install/linux-centos)  
　  
``linux-slave は常に root 権限で作業します、その為本番環境を想定している作業方法ではありません。``


### 1.1 linux-slave に tty

事前に作成した Docker の centos7 に ssh ログインする。

``docker exec -it centos7 /bin/bash --login``:  

```cmd
> docker exec -it centos7 /bin/bash --login
[root@54d1669b20a1 /]#
```

### 1.2 linux-slave の OS バージョンを確認する

Docker イメージは CentOS7 ですが、念の為バージョンを確認します。

``cat /etc/redhat-release``:  

```bash
[root@54d1669b20a1 /]# cat /etc/redhat-release
CentOS Linux release 7.8.2003 (Core)
```

CentOS7(7.8.2003) と確認できました。


### 1.3 インストール

[CentOS に .NET Core をインストールする - .NET Core | Microsoft Docs - CentOS7✔️](https://docs.microsoft.com/ja-jp/dotnet/core/install/linux-centos#centos-7-) を参考にインストールします。  


#### 1.3.1 署名キーを追加

``rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm``:  

``bash
[root@54d1669b20a1 /]# rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
Retrieving https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:packages-microsoft-prod-1.0-1    ################################# [100%]
``

#### 1.3.2 署名キーを追加SDK のインストール

``yum install dotnet-sdk-3.1``:  

途中何度か確認のプロンプトが表示されます、内容を確認して ``y`` を入力してください。  


```bash
[root@54d1669b20a1 /]#  yum install dotnet-sdk-3.1
Loaded plugins: fastestmirror, ovl
Determining fastest mirrors
 * base: ftp.iij.ad.jp
 * extras: ftp.iij.ad.jp
 * updates: ftp.iij.ad.jp
base                                                                                        | 3.6 kB  00:00:00
extras                                                                                      | 2.9 kB  00:00:00
packages-microsoft-com-prod                                                                 | 3.0 kB  00:00:00
updates                                                                                     | 2.9 kB  00:00:00
(1/3): extras/7/x86_64/primary_db                                                           | 205 kB  00:00:00
(2/3): updates/7/x86_64/primary_db                                                          | 3.0 MB  00:00:00
(3/3): packages-microsoft-com-prod/primary_db                                               | 190 kB  00:00:00
Resolving Dependencies
--> Running transaction check
---> Package dotnet-sdk-3.1.x86_64 0:3.1.302-1 will be installed
--> Processing Dependency: netstandard-targeting-pack-2.1 >= 2.1.0 for package: dotnet-sdk-3.1-3.1.302-1.x86_64
--> Processing Dependency: dotnet-runtime-3.1 for package: dotnet-sdk-3.1-3.1.302-1.x86_64
--> Processing Dependency: dotnet-targeting-pack-3.1 for package: dotnet-sdk-3.1-3.1.302-1.x86_64
--> Processing Dependency: dotnet-apphost-pack-3.1 for package: dotnet-sdk-3.1-3.1.302-1.x86_64
--> Processing Dependency: aspnetcore-runtime-3.1 for package: dotnet-sdk-3.1-3.1.302-1.x86_64
--> Processing Dependency: aspnetcore-targeting-pack-3.1 for package: dotnet-sdk-3.1-3.1.302-1.x86_64
--> Running transaction check
---> Package aspnetcore-runtime-3.1.x86_64 0:3.1.6-1 will be installed
---> Package aspnetcore-targeting-pack-3.1.x86_64 0:3.1.3-1 will be installed
---> Package dotnet-apphost-pack-3.1.x86_64 0:3.1.6-1 will be installed
---> Package dotnet-runtime-3.1.x86_64 0:3.1.6-1 will be installed
--> Processing Dependency: dotnet-hostfxr-3.1 >= 3.1.6 for package: dotnet-runtime-3.1-3.1.6-1.x86_64
--> Processing Dependency: dotnet-runtime-deps-3.1 >= 3.1.6 for package: dotnet-runtime-3.1-3.1.6-1.x86_64
---> Package dotnet-targeting-pack-3.1.x86_64 0:3.1.0-1 will be installed
---> Package netstandard-targeting-pack-2.1.x86_64 0:2.1.0-1 will be installed
--> Running transaction check
---> Package dotnet-hostfxr-3.1.x86_64 0:3.1.6-1 will be installed
--> Processing Dependency: dotnet-host >= 3.1.6 for package: dotnet-hostfxr-3.1-3.1.6-1.x86_64
---> Package dotnet-runtime-deps-3.1.x86_64 0:3.1.6-1 will be installed
--> Processing Dependency: libicu for package: dotnet-runtime-deps-3.1-3.1.6-1.x86_64
--> Running transaction check
---> Package dotnet-host.x86_64 0:3.1.6-1 will be installed
---> Package libicu.x86_64 0:50.2-4.el7_7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================
 Package                               Arch          Version              Repository                          Size
===================================================================================================================
Installing:
 dotnet-sdk-3.1                        x86_64        3.1.302-1            packages-microsoft-com-prod         69 M
Installing for dependencies:
 aspnetcore-runtime-3.1                x86_64        3.1.6-1              packages-microsoft-com-prod        7.5 M
 aspnetcore-targeting-pack-3.1         x86_64        3.1.3-1              packages-microsoft-com-prod        1.5 M
 dotnet-apphost-pack-3.1               x86_64        3.1.6-1              packages-microsoft-com-prod         68 k
 dotnet-host                           x86_64        3.1.6-1              packages-microsoft-com-prod         40 k
 dotnet-hostfxr-3.1                    x86_64        3.1.6-1              packages-microsoft-com-prod        148 k
 dotnet-runtime-3.1                    x86_64        3.1.6-1              packages-microsoft-com-prod         29 M
 dotnet-runtime-deps-3.1               x86_64        3.1.6-1              packages-microsoft-com-prod        2.8 k
 dotnet-targeting-pack-3.1             x86_64        3.1.0-1              packages-microsoft-com-prod        3.4 M
 libicu                                x86_64        50.2-4.el7_7         updates                            6.9 M
 netstandard-targeting-pack-2.1        x86_64        2.1.0-1              packages-microsoft-com-prod        2.1 M

Transaction Summary
===================================================================================================================
Install  1 Package (+10 Dependent packages)

Total download size: 119 M
Installed size: 310 M
Is this ok [y/d/N]: y
Downloading packages:
warning: /var/cache/yum/x86_64/7/packages-microsoft-com-prod/packages/aspnetcore-targeting-pack-3.1.3.rpm: Header V4 RSA/SHA256 Signature, key ID be1229cf: NOKEY
Public key for aspnetcore-targeting-pack-3.1.3.rpm is not installed
(1/11): aspnetcore-targeting-pack-3.1.3.rpm                                                 | 1.5 MB  00:00:01
(2/11): dotnet-apphost-pack-3.1.6-x64.rpm                                                   |  68 kB  00:00:00
(3/11): dotnet-host-3.1.6-x64.rpm                                                           |  40 kB  00:00:00
(4/11): dotnet-hostfxr-3.1.6-x64.rpm                                                        | 148 kB  00:00:00
(5/11): aspnetcore-runtime-3.1.6-x64.rpm                                                    | 7.5 MB  00:00:02
(6/11): dotnet-runtime-deps-3.1.6-centos.7-x64.rpm                                          | 2.8 kB  00:00:00
(7/11): dotnet-runtime-3.1.6-x64.rpm                                                        |  29 MB  00:00:15
(8/11): libicu-50.2-4.el7_7.x86_64.rpm                                                      | 6.9 MB  00:00:01
(9/11): dotnet-targeting-pack-3.1.0-x64.rpm                                                 | 3.4 MB  00:00:05
(10/11): netstandard-targeting-pack-2.1.0-x64.rpm                                           | 2.1 MB  00:00:02
(11/11): dotnet-sdk-3.1.302-x64.rpm                                                         |  69 MB  00:00:31
-------------------------------------------------------------------------------------------------------------------
Total                                                                              3.5 MB/s | 119 MB  00:00:33
Retrieving key from https://packages.microsoft.com/keys/microsoft.asc
Importing GPG key 0xBE1229CF:
 Userid     : "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
 Fingerprint: *************************************************
 From       : https://packages.microsoft.com/keys/microsoft.asc
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
  Installing : dotnet-targeting-pack-3.1-3.1.0-1.x86_64                                                       1/11
  Installing : aspnetcore-targeting-pack-3.1-3.1.3-1.x86_64                                                   2/11
  Installing : dotnet-apphost-pack-3.1-3.1.6-1.x86_64                                                         3/11
  Installing : netstandard-targeting-pack-2.1-2.1.0-1.x86_64                                                  4/11
  Installing : libicu-50.2-4.el7_7.x86_64                                                                     5/11
  Installing : dotnet-runtime-deps-3.1-3.1.6-1.x86_64                                                         6/11
  Installing : dotnet-host-3.1.6-1.x86_64                                                                     7/11
  Installing : dotnet-hostfxr-3.1-3.1.6-1.x86_64                                                              8/11
  Installing : dotnet-runtime-3.1-3.1.6-1.x86_64                                                              9/11
  Installing : aspnetcore-runtime-3.1-3.1.6-1.x86_64                                                         10/11
  Installing : dotnet-sdk-3.1-3.1.302-1.x86_64                                                               11/11
This software may collect information about you and your use of the software, and send that to Microsoft.
Please visit http://aka.ms/dotnet-cli-eula for more information.
Welcome to .NET Core!
---------------------
Learn more about .NET Core: https://aka.ms/dotnet-docs
Use 'dotnet --help' to see available commands or visit: https://aka.ms/dotnet-cli-docs

Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. The data is anonymous and doesn't include command-line arguments. The data is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_CLI_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.

Read more about .NET Core CLI Tools telemetry: https://aka.ms/dotnet-cli-telemetry

Configuring...
--------------
A command is running to populate your local package cache to improve restore speed and enable offline access. This command takes up to one minute to complete and only runs once.
  Verifying  : dotnet-runtime-3.1-3.1.6-1.x86_64                                                              1/11
  Verifying  : dotnet-sdk-3.1-3.1.302-1.x86_64                                                                2/11
  Verifying  : dotnet-targeting-pack-3.1-3.1.0-1.x86_64                                                       3/11
  Verifying  : dotnet-host-3.1.6-1.x86_64                                                                     4/11
  Verifying  : dotnet-hostfxr-3.1-3.1.6-1.x86_64                                                              5/11
  Verifying  : libicu-50.2-4.el7_7.x86_64                                                                     6/11
  Verifying  : dotnet-runtime-deps-3.1-3.1.6-1.x86_64                                                         7/11
  Verifying  : netstandard-targeting-pack-2.1-2.1.0-1.x86_64                                                  8/11
  Verifying  : dotnet-apphost-pack-3.1-3.1.6-1.x86_64                                                         9/11
  Verifying  : aspnetcore-targeting-pack-3.1-3.1.3-1.x86_64                                                  10/11
  Verifying  : aspnetcore-runtime-3.1-3.1.6-1.x86_64                                                         11/11

Installed:
  dotnet-sdk-3.1.x86_64 0:3.1.302-1

Dependency Installed:
  aspnetcore-runtime-3.1.x86_64 0:3.1.6-1              aspnetcore-targeting-pack-3.1.x86_64 0:3.1.3-1
  dotnet-apphost-pack-3.1.x86_64 0:3.1.6-1             dotnet-host.x86_64 0:3.1.6-1
  dotnet-hostfxr-3.1.x86_64 0:3.1.6-1                  dotnet-runtime-3.1.x86_64 0:3.1.6-1
  dotnet-runtime-deps-3.1.x86_64 0:3.1.6-1             dotnet-targeting-pack-3.1.x86_64 0:3.1.0-1
  libicu.x86_64 0:50.2-4.el7_7                         netstandard-targeting-pack-2.1.x86_64 0:2.1.0-1

Complete!
```

#### 1.3.3 インストールされた確認する

``dotnet --version``: 

```bash
[root@54d1669b20a1 /]# dotnet --version
3.1.302
```

バージョンが表示されていれば正常にインストールされています。










　  
　  
　  
　  
　  
　  

* * *

###### :copyright: 商標について

<sup>当ドキュメントに記載されている会社名、システム名、製品名は一般に各社の登録商標または商標です。</sup>  
<sup>なお、本文および図表中では、「™」、「®」は明記しておりません。</sup>  

###### 免責事項  
<sup>当ドキュメント上の掲載内容については細心の注意を払っていますが、その情報に関する信頼性、正確性、完全性について保証するものではありません。</sup>  
<sup>掲載された内容の誤り、および掲載された情報に基づいて行われたことによって生じた直接的、また間接的トラブル、損失、損害については、筆者は一切の責任を負いません。</sup>  
<sup>また当ドキュメント、およびドキュメントに含まれる情報、コンテンツは、通知なしに随時変更されます。</sup>  







