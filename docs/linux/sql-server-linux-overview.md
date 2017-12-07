---
title: "SQL Server on Linux の概要 |Microsoft ドキュメント"
description: "ここでは、SQL Server on Linux を実行し、方法の詳細について説明します。"
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 10/02/2017
ms.topic: article
ms.prod: sql-non-specified
ms.prod_service: database-engine
ms.service: 
ms.component: sql-linux
ms.suite: sql
ms.custom: 
ms.technology: database-engine
ms.assetid: 9dcc6a90-0add-42c2-815b-862e4e2a21ac
ms.workload: Active
ms.openlocfilehash: b598357bb8ebe17ad15fb10e1d74c21c169c1da8
ms.sourcegitcommit: 531d0245f4b2730fad623a7aa61df1422c255edc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="sql-server-on-linux"></a>SQL Server on Linux

SQL Server 2017 は Linux で実行できます。 オペレーティング システムに関係なく、多くの同様な機能とサービスとともに同じSQL データベースエンジンが動いています。

## <a name="install"></a>インストール

作業を開始するには、次のクイック スタート チュートリアルのいずれかを使用して Linux に SQL Server をインストールします。

- [Red Hat Enterprise Linux にインストールします。](quickstart-install-connect-red-hat.md)
- [SUSE Linux Enterprise Server をインストールします。](quickstart-install-connect-suse.md)
- [Ubuntu をインストールします。](quickstart-install-connect-ubuntu.md)
- [Docker で実行します。](quickstart-install-connect-docker.md)
- [Azure での SQL VM プロビジョニング](/azure/virtual-machines/linux/sql/provision-sql-server-linux-virtual-machine?toc=%2fsql%2flinux%2ftoc.json)

> [!NOTE]
> Docker 自体は複数のプラットフォームで実行されます。つまり、Linux、Mac、および Windows で Docker イメージを実行できます。

## <a name="connect"></a>接続

インストール後に、Linux コンピューター上の SQL Server インスタンスに接続します。 ローカルまたはリモートでと、さまざまなツールとドライバーを接続することができます。 クイック スタート チュートリアルでは、 [sqlcmd](sql-server-linux-setup-tools.md)コマンド ライン ツールをどのように使うかを説明しています。 その他のツールは次のとおりです。

| ツール | チュートリアル |
|-----|-----|
| Visual Studio Code (VS Code) | [SQL Server on Linux で VS Codeを使用します。](sql-server-linux-develop-use-vscode.md) |
| SQL Server Management Studio (SSMS) | [Windows で SSMS を使用して Linux 上の SQL Server に接続します。](sql-server-linux-develop-use-ssms.md) |
| SQL Server Data Tools (SSDT) | [SQL Server on Linux で SSDT を使用します。](sql-server-linux-develop-use-ssdt.md) |

## <a name="explore"></a>探索

SQL Server 2017 では、Linux を含むサポートされているすべてのプラットフォームで基となるデータベース エンジンは同じです。 非常に多くの既存の機能と機能は、Linux で同じように動作します。 この領域のドキュメントでは、Linux の観点からこれらの機能の一部を公開しています。 Linux 固有の要件がある領域も含んでいます。

SQL Server を使い慣れている場合は、一般的なガイドラインとこのリリースの既知の問題を確認するために[リリース ノート](sql-server-linux-release-notes.md)を参照してください。 [SQL Server 2017 の新機能](../sql-server/what-s-new-in-sql-server-2017.md)だけでなく[SQL Server on Linux の新機能](sql-server-linux-whats-new.md)も参照してください。

##  <a name="infotipmediageneralinfotippng-engage-with-the-sql-server-engineering-team"></a>![info_tip](./media/general/info_tip.png) SQL Server エンジニアリング チームと連携する

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): データベースの管理の質問
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): 開発の質問
- [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?category=sqlserver): 技術的な質問
- [Microsoft Connect](https://connect.microsoft.com/SQLServer/Feedback): バグ、および要求機能の報告
- [Reddit](https://www.reddit.com/r/SQLServer/): SQL Server の議論
