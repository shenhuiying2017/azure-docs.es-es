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
ms.date: 01/04/2018
ms.author: larryfr
ms.openlocfilehash: a0a63c414bc68f5125b65e288d78fb546c376c04
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Uso de Apache Sqoop para importar y exportar datos entre Hadoop en HDInsight y SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar Apache Sqoop para realizar importaciones y exportaciones entre un clúster de Hadoop en Azure HDInsight y Azure SQL Database o la base de datos de Microsoft SQL Server. En los pasos descritos en este documento se usa el comando `sqoop` directamente desde el nodo principal del clúster de Hadoop. Usaremos SSH para conectarnos al nodo principal y ejecutar los comandos de este documento.

> [!IMPORTANT]
> Los pasos de este documento solo funcionan con clústeres de HDInsight que usan Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> En los pasos descritos en este documento se supone que ya ha creado una instancia de Azure SQL Database llamada `sqooptest`.
>
> Este documento proporciona instrucciones T-SQL que se usan para crear y consultar una tabla en SQL Database. Hay muchos clientes que pueden usar estas instrucciones con SQL Database. Se recomiendan los siguientes clientes:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [código de Visual Studio](../../sql-database/sql-database-connect-query-vscode.md)
> * La utilidad [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)

## <a name="create-the-table-in-sql-database"></a>Creación de la tabla en SQL Database

> [!IMPORTANT]
> Si usa el clúster de HDInsight y una instancia de SQL Database creados en [Creación del clúster y la base de datos SQL](hdinsight-use-sqoop.md), omita los pasos de esta sección. La base de datos y la tabla se crearon como parte de los pasos del documento [Creación del clúster y la base de datos SQL](hdinsight-use-sqoop.md).

Use un cliente SQL para conectarse a la base de datos `sqooptest` en SQL Database. A continuación, use la siguiente instrucción SQL para crear una tabla llamada `mobiledata`:

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

## <a name="sqoop-export"></a>Exportación de Sqoop

1. Use SSH para conectarse al clúster de HDInsight. Por ejemplo, el siguiente comando se conecta al nodo primario principal de un clúster denominado `mycluster`:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use el comando siguiente para comprobar que Sqoop puede ver su instancia de SQL Database:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Cuando se le solicite, escriba la contraseña para el inicio de sesión de SQL Database.

    Este comando devuelve una lista de bases de datos, incluida la base de datos **sqooptest** usada anteriormente.

3. Para exportar datos de la tabla **hivesampletable** de Hive a la tabla **mobiledata** de SQL Database, use el siguiente comando:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Para comprobar que los datos se han exportado, use la siguiente consulta en el cliente SQL para ver los datos exportados:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Este comando muestra 50 filas que se importaron en la tabla.

## <a name="sqoop-import"></a>Importación de Sqoop

1. Use el siguiente comando para importar datos de la tabla **mobiledata** de SQL Database al directorio **wasb:///tutorials/usesqoop/importeddata** de HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Los campos de los datos se separan mediante un carácter de tabulación y las líneas terminan con un carácter de nueva línea.

    > [!IMPORTANT]
    > La ruta de acceso `wasb:///` funciona con clústeres que usan Azure Storage como almacenamiento de clúster predeterminado. En clústeres que usen Azure Data Lake Store, utilice en su lugar `adl:///`.

2. Una vez completada la importación, use el siguiente comando para enumerar los datos en el nuevo directorio:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Uso de SQL Server

También puede usar Sqoop para importar y exportar datos desde SQL Server. Las diferencias entre el uso de SQL Database y SQL Server son:

* Tanto HDInsight como SQL Server deben estar en la misma red Azure Virtual Network.

    Para obtener un ejemplo, vea el documento [Conexión de HDInsight a la red local](./../connect-on-premises-network.md).

    Para más información sobre cómo usar HDInsight con redes Azure Virtual Network, vea el documento [Extensión de las funcionalidades de HDInsight con Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md). Para más información sobre Azure Virtual Network, vea el documento [Información general sobre Virtual Network](../../virtual-network/virtual-networks-overview.md).

* SQL Server estar configurado para permitir la autenticación SQL. Para más información, vea el documento [Choose an Authentication Mode (Elegir un modo de autenticación)](https://msdn.microsoft.com/ms144284.aspx).

* Es posible que tenga que configurar SQL Server para aceptar conexiones remotas. Para más información, vea el documento [How to troubleshoot connecting to the SQL Server database engine (Solución de problemas de conexión al motor de base de datos de SQL Server)](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Use las siguientes instrucciones Transact-SQL para crear la tabla **mobiledata**:

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
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limitaciones

* Exportación masiva: con HDInsight basado en Linux, el conector Sqoop que se usa para exportar datos a Microsoft SQL Server o Azure SQL Database no admite inserciones masivas.

* Procesamiento por lotes: con HDInsight basado en Linux, cuando se usa `-batch` al realizar inserciones, Sqoop realiza varias inserciones en lugar de procesar por lotes las operaciones de inserción.

## <a name="next-steps"></a>pasos siguientes

Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

* [Uso de Oozie con HDInsight](../hdinsight-use-oozie.md): use la acción Sqoop en un flujo de trabajo de Oozie.
* [Análisis de la información de retraso de vuelos con HDInsight](../hdinsight-analyze-flight-delay-data.md): use Hive para analizar la información de retraso de los vuelos y luego use Sqoop para exportar los datos a una base de datos SQL de Azure.
* [Carga de datos en HDInsight](../hdinsight-upload-data.md): busque otros métodos para cargar datos en HDInsight o el almacenamiento de blobs de Azure.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
