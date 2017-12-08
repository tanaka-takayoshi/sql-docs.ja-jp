---
title: "Red Hat Enterprise Linux 上の SQL Server の無人インストール |Microsoft ドキュメント"
description: "SQL Server スクリプトのサンプル - Red Hat Enterprise Linux 上の無人インストール"
author: edmacauley
ms.author: edmacauley
manager: jhubbard
ms.date: 07/17/2017
ms.topic: article
ms.prod: sql-linux
ms.technology: database-engine
ms.translationtype: MT
ms.sourcegitcommit: ea75391663eb4d509c10fb785fcf321558ff0b6e
ms.openlocfilehash: 8b97ec59acf463f73a9277566a89f32a2498a856
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="sample-unattended-sql-server-installation-script-for-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 用のサンプル: SQL Server を無人インストール スクリプト

[!INCLUDE[tsql-appliesto-sslinux-only](../includes/tsql-appliesto-sslinux-only.md)]

このサンプル バッシュ スクリプトは、対話型の入力なし Red Hat Enterprise Linux (RHEL) 上の SQL Server 2017 RC2 をインストールします。 データベース エンジン、SQL Server エージェント、SQL Server コマンド ライン ツールのインストールの例を紹介し、インストール後の手順を実行します。 必要に応じて、フルテキスト検索をインストールし、管理ユーザーを作成できます。

> [!TIP]
> SQL Server をインストールする最も簡単な方法に従う場合は、無人インストール スクリプトを使用する必要はありません、 [Red Hat のクイック スタート チュートリアル](quickstart-install-connect-red-hat.md)です。 その他のセットアップについては、次を参照してください。 [Linux 上の SQL Server のインストールのガイダンス](sql-server-linux-setup.md)です。

## <a name="prerequisites"></a>前提条件

- Linux 上の SQL Server を実行するには、少なくとも 3.25 GB のメモリを必要とします。
- ファイル システムである必要があります**XFS**または**EXT4**です。 その他のファイル システム**BTRFS**、サポートされていません。
- その他のシステム要件については、次を参照してください。 [Linux に SQL Server のシステム要件](sql-server-linux-setup.md#system)です。

## <a name="sample-script"></a>サンプル スクリプト
サンプル スクリプトをファイルに保存し、それをカスタマイズする、スクリプトで変数の値を置き換えます。 設定できますスクリプト変数のいずれかの環境変数として、スクリプト ファイルから削除する限り、します。

```bash
#!/bin/bash -eu

# Use the following variables to control your install:

# Password for the SA user (required)
MSSQL_SA_PASSWORD='<YourStrong!Passw0rd>'

# Product ID of the version of SQL server you're installing
# Must be evaluation, developer, express, web, standard, enterprise, or your 25 digit product key
# Defaults to developer
MSSQL_PID='evaluation'

# Install SQL Server Agent (recommended)
SQL_INSTALL_AGENT='y'

# Install SQL Server Full Text Search (optional)
# SQL_INSTALL_FULLTEXT='y'

# Create an additional user with sysadmin privileges (optional)
# SQL_INSTALL_USER='<Username>'
# SQL_INSTALL_USER_PASSWORD='<YourStrong!Passw0rd>'

if [ -z $MSSQL_SA_PASSWORD ]
then
  echo Environment variable MSSQL_SA_PASSWORD must be set for unattended install
  exit 1
fi

echo Adding Microsoft repositories...
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server.repo
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo

echo Running yum update -y...
sudo yum update -y

echo Installing SQL Server...
sudo yum install -y mssql-server

echo Running mssql-conf setup...
sudo MSSQL_SA_PASSWORD=$MSSQL_SA_PASSWORD \
     MSSQL_PID=$MSSQL_PID \
     /opt/mssql/bin/mssql-conf -n setup accept-eula

echo Installing mssql-tools and unixODBC developer...
sudo ACCEPT_EULA=Y yum install -y mssql-tools unixODBC-devel

# Add SQL Server tools to the path by default:
echo Adding SQL Server tools to your path...
echo PATH="$PATH:/opt/mssql-tools/bin" >> ~/.bash_profile
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc

# Optional SQL Server Agent installation:
if [ ! -z $SQL_INSTALL_AGENT ]
then
  echo Installing SQL Server Agent...
  sudo yum install -y mssql-server-agent
fi

# Optional SQL Server Full Text Search installation:
if [ ! -z $SQL_INSTALL_FULLTEXT ]
then
    echo Installing SQL Server Full-Text Search...
    sudo yum install -y mssql-server-fts
fi

# Configure firewall to allow TCP port 1433:
echo Configuring firewall to allow traffic on port 1433...
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload

# Example of setting post-installation configuration options
# Set trace flags 1204 and 1222 for deadlock tracing:
#echo Setting trace flags...
#sudo /opt/mssql/bin/mssql-conf traceflag 1204 1222 on

# Restart SQL Server after making configuration changes:
echo Restarting SQL Server...
sudo systemctl restart mssql-server

# Connect to server and get the version:
counter=1
errstatus=1
while [ $counter -le 5 ] && [ $errstatus = 1 ]
do
  echo Waiting for SQL Server to start...
  sleep 5s
  /opt/mssql-tools/bin/sqlcmd \
    -S localhost \
    -U SA \
    -P $MSSQL_SA_PASSWORD \
    -Q "SELECT @@VERSION" 2>/dev/null
  errstatus=$?
  ((counter++))
done

# Display error if connection failed:
if [ $errstatus = 1 ]
then
  echo Cannot connect to SQL Server, installation aborted
  exit $errstatus
fi

# Optional new user creation:
if [ ! -z $SQL_INSTALL_USER ] && [ ! -z $SQL_INSTALL_USER_PASSWORD ]
then
  echo Creating user $SQL_INSTALL_USER
  /opt/mssql-tools/bin/sqlcmd \
    -S localhost \
    -U SA \
    -P $MSSQL_SA_PASSWORD \
    -Q "CREATE LOGIN [$SQL_INSTALL_USER] WITH PASSWORD=N'$SQL_INSTALL_USER_PASSWORD', DEFAULT_DATABASE=[master], CHECK_EXPIRATION=ON, CHECK_POLICY=ON; ALTER SERVER ROLE [sysadmin] ADD MEMBER [$SQL_INSTALL_USER]"
fi

echo Done!
```

## <a name="running-the-script"></a>スクリプトを実行します。

スクリプトを実行するには

1. 同様に、覚えやすい名前で保存し、サンプルをお気に入りのテキスト エディターに貼り付け`install_sql.sh`です。

1. カスタマイズ`MSSQL_SA_PASSWORD`、 `MSSQL_PID`、および変更するには、その他の変数のいずれか。

1. スクリプトを実行可能ファイルとしてのマークを付ける

   ```bash
   chmod +x install_sql.sh
   ```

1. スクリプトを実行します。

   ```bash
   ./install_sql.sh
   ```

## <a name="understanding-the-script"></a>スクリプトを理解します。

バッシュ スクリプトでは、最初の手順には、いくつかの変数が設定されます。  このサンプルのように、スクリプト変数または環境変数のいずれかを指定できます。  変数``` MSSQL_SA_PASSWORD ```は**必要**SQL Server インストールでは、その他は、スクリプト用に作成されたカスタム変数です。  サンプル スクリプトは、次の手順を実行します。

1. Microsoft の鍵は、公開キーをインポートします。

1. SQL Server とコマンド ライン ツールの Microsoft のリポジトリを登録します。

1. ローカル リポジトリを更新します。

1. SQL Server をインストールする

1. SQL Server の構成、```MSSQL_SA_PASSWORD```し使用許諾契約書を自動的に同意します。

1. SQL Server コマンド ライン ツールの使用許諾契約書に同意自動的にしたり、インストール、および unixodbc デベロッパー パッケージをインストールします。

1. SQL Server コマンド ライン ツールを使いやすくするためのパスに追加します。

1. 場合、SQL Server エージェントのインストール スクリプト変数```SQL_INSTALL_AGENT```で既定で設定されます。

1. 場合、SQL Server フルテキスト検索を必要に応じてインストール変数```SQL_INSTALL_FULLTEXT```が設定されています。

1. ファイアウォール上で、システム、別のシステムから SQL Server に接続するために必要な tcp ポート 1433 のブロックを解除します。

1. 必要に応じてデッドロック トレースのトレース フラグを設定します。 (行のコメントを解除する必要があります)

1. SQL Server がインストールされているようになりました、運用するために、プロセスを再起動します。

1. すべてのエラー メッセージを非表示にするときに SQL Server が正しくインストールされていることを確認します。

1. 場合、新しいサーバー管理者のユーザーを作成する```SQL_INSTALL_USER```と```SQL_INSTALL_USER_PASSWORD```がどちらも設定します。

## <a name="next-steps"></a>次の手順

複数の無人インストールを簡略化し、適切な環境変数を設定するスタンドアロン バッシュ スクリプトを作成します。  サンプル スクリプトを使用し、それら独自バッシュ スクリプトで変数のいずれかを削除することができます。

```bash
#!/bin/bash
export MSSQL_SA_PASSWORD='<YourStrong!Passw0rd>'
export MSSQL_PID='evaluation'
export SQL_INSTALL_AGENT='y'
export SQL_INSTALL_USER='<Username>'
export SQL_INSTALL_USER_PASSWORD='<YourStrong!Passw0rd>'
export SQL_INSTALL_AGENT='y'
```

ようバッシュ スクリプトを実行します。
```bash
. ./my_script_name.sh
```

Linux 上の SQL Server に関する詳細については、次を参照してください。 [SQL Server on Linux の概要](sql-server-linux-overview.md)です。
