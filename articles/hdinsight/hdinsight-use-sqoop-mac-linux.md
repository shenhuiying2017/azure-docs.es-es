---
title: Apache Sqoop con Hadoop - Azure HDInsight | Microsoft Docs
description: Aprenda a usar Apache Sqoop para realizar importaciones y exportaciones entre Hadoop en HDInsight y Azure SQL Database.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop,sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: larryfr
ms.openlocfilehash: 35dcbb91e6af1480685c9fd5b829c54277c1c605
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Uso de Apache Sqoop para importar y exportar datos entre Hadoop en HDInsight y SQL Database

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar Apache Sqoop para realizar importaciones y exportaciones entre un clúster de Hadoop en Azure HDInsight y Azure SQL Database o la base de datos de Microsoft SQL Server. En los pasos descritos en este documento se usa el comando `sqoop` directamente desde el nodo principal del clúster de Hadoop. Usaremos SSH para conectarnos al nodo principal y ejecutar los comandos de este documento.

> [!IMPORTANT]
> Los pasos de este documento solo funcionan con clústeres de HDInsight que usan Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="install-freetds"></a>Instalación de FreeTDS

1. Use SSH para conectarse al clúster de HDInsight. Por ejemplo, el siguiente comando se conecta al nodo primario principal de un clúster denominado `mycluster`:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use el siguiente comando para instalar FreeTDS:

    ```bash
    sudo apt --assume-yes install freetds-dev freetds-bin
    ```

    FreeTDS se usa en varios pasos para la conexión con la base de datos SQL.

## <a name="create-the-table-in-sql-database"></a>Creación de la tabla en la base de datos SQL

> [!IMPORTANT]
> Si usa el clúster de HDInsight y una instancia de SQL Database creados en [Creación del clúster y la base de datos SQL](hdinsight-use-sqoop.md), omita los pasos de esta sección. La base de datos y la tabla se crearon como parte de los pasos del documento [Creación del clúster y la base de datos SQL](hdinsight-use-sqoop.md).

1. Desde la sesión de SSH, use el comando siguiente para conectarse al servidor de la base de datos SQL.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Recibirá una salida similar al texto siguiente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. En el símbolo del sistema `1>`, escriba la siguiente consulta:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
    GO
    ```

    Cuando se haya especificado la instrucción `GO`, se evaluarán las instrucciones anteriores. En primer lugar, se crea la tabla **mobiledata** y, a continuación, se agrega un índice agrupado a ella (es necesario para la base de datos SQL).

    Use la siguiente consulta para comprobar que se ha creado la tabla:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Verá un resultado similar al siguiente texto:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. Entrar `exit` at the `1>` .

## <a name="sqoop-export"></a>Exportación de Sqoop

1. Desde la conexión SSH con el clúster, use el siguiente comando para comprobar que Sqoop puede ver la instancia de SQL Database:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Cuando se le solicite, escriba la contraseña para el inicio de sesión de SQL Database.

    Este comando devuelve una lista de bases de datos, incluida la base de datos **sqooptest** que ha creado anteriormente.

2. Use el siguiente comando para exportar los datos desde **hivesampletable** a la tabla **mobiledata**:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    Este comando indica a Sqoop que se conecte a la base de datos **sqooptest**. Luego Sqoop exporta datos de **wasb:///hive/warehouse/hivesampletable** a la tabla **mobiledata**.

    > [!IMPORTANT]
    > Use `wasb:///` si el almacenamiento predeterminado del clúster es una cuenta de Azure Storage. Use `adl:///` si es una instancia de Azure Data Lake Store.

3. Una vez completado el comando, use el siguiente comando para conectarse a la base de datos mediante TSQL:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P -p 1433 -D sqooptest
    ```

    Una vez conectado, utilice las instrucciones siguientes para comprobar que los datos se exportaron a la tabla **mobiledata** :

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata
    GO
    ```

    Debería ver una lista de los datos de la tabla. Escriba `exit` para salir de la utilidad de tsql.

## <a name="sqoop-import"></a>Importación de Sqoop

1. Use el siguiente comando para importar datos de la tabla **mobiledata** de SQL Database al directorio **wasb:///tutorials/usesqoop/importeddata** de HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Los campos de los datos se separan mediante un carácter de tabulación y las líneas terminan con un carácter de nueva línea.

2. Una vez completada la importación, use el siguiente comando para enumerar los datos en el nuevo directorio:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Uso de SQL Server

También puede utilizar Sqoop para importar y exportar datos de SQL Server, tanto en el centro de datos como en una máquina Virtual hospedada en Azure. Las diferencias entre el uso de la base de datos SQL y SQL Server son:

* Tanto HDInsight como SQL Server deben estar en la misma red Azure Virtual Network.

    Para obtener un ejemplo, vea el documento [Conexión de HDInsight a la red local](./connect-on-premises-network.md).

    Para más información sobre cómo usar HDInsight con redes Azure Virtual Network, vea el documento [Extensión de las funcionalidades de HDInsight con Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Para más información sobre Azure Virtual Network, vea el documento [Información general sobre Virtual Network](../virtual-network/virtual-networks-overview.md).

* SQL Server estar configurado para permitir la autenticación SQL. Para más información, vea el documento [Choose an Authentication Mode (Elegir un modo de autenticación)](https://msdn.microsoft.com/ms144284.aspx).

* Es posible que tenga que configurar SQL Server para aceptar conexiones remotas. Para más información, vea el documento [How to troubleshoot connecting to the SQL Server database engine (Solución de problemas de conexión al motor de base de datos de SQL Server)](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Cree la base de datos **sqooptest** en SQL Server mediante una utilidad como **SQL Server Management Studio** o **tsql**. Los pasos para usar la CLI de Azure solo funcionan para Azure SQL Database.

    Use las siguientes instrucciones Transact-SQL para crear la tabla **mobiledata**:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Al conectarse a SQL Server desde HDInsight, es posible que tenga que usar la dirección IP de SQL Server. Por ejemplo:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limitaciones

* Exportación masiva: con HDInsight basado en Linux, el conector Sqoop que se utiliza para exportar datos a Microsoft SQL Server o Base de datos SQL Azure no es compatible actualmente con las inserciones masivas.

* Procesamiento por lotes: con HDInsight basado en Linux, cuando se usa `-batch` al realizar inserciones, Sqoop realiza varias inserciones en lugar de procesar por lotes las operaciones de inserción.

## <a name="next-steps"></a>Pasos siguientes

Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

* [Uso de Oozie con HDInsight][hdinsight-use-oozie]: use la acción Sqoop en un flujo de trabajo de Oozie.
* [Análisis de la información de retraso de vuelos con HDInsight][hdinsight-analyze-flight-data]: use Hive para analizar la información de retraso de los vuelos y luego use Sqoop para exportar los datos a una base de datos SQL de Azure.
* [Carga de datos en HDInsight][hdinsight-upload-data]: busque otros métodos para cargar datos en HDInsight o Azure Blob Storage.

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
