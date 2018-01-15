---
title: Copia de datos entre Data Lake Store y Azure SQL Database mediante Sqoop | Microsoft Docs
description: Uso de Sqoop para copiar datos entre Data Lake Store y Azure SQL Database
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 2611b50114a9fae003fe6e77300780a9af0698cc
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copia de datos entre Almacén de Data Lake y Base de datos SQL de Azure mediante Sqoop
Aprenda a usar Apache Sqoop para importar y exportar datos entre Azure SQL Database y Data Lake Store.

## <a name="what-is-sqoop"></a>¿Qué es Sqoop?
Las aplicaciones de macrodatos son una opción natural para procesar datos no estructurados y semiestructurados, como registros y archivos. Pero también puede que sea necesario procesar datos estructurados que se almacenan en bases de datos relacionales.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) es una herramienta diseñada para transferir datos entre bases de datos relacionales y un repositorio de macrodatos, como Data Lake Store. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS) como Azure SQL Database a Data Lake Store. Después, puede transformar y analizar los datos mediante cargas de trabajo de macrodatos y exportar los datos a un RDBMS. En este tutorial, use una instancia de Azure SQL Database como la base de datos relacional para importar y exportar.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md)
* **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Consulte [Creación de un clúster de HDInsight con Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md). En este artículo se supone que tiene un clúster de HDInsight Linux con acceso a Almacén de Data Lake.
* **Azure SQL Database**. Para obtener instrucciones sobre cómo crear una, vea [Creación de una Base de datos SQL de Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>¿Obtener información más rápidamente con vídeos?
[Vea este vídeo](https://mix.office.com/watch/1butcdjxmu114) para saber cómo copiar datos entre los blobs de Azure Storage y Data Lake Store mediante DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Creación de tablas de ejemplo en Azure SQL Database
1. Para empezar, cree dos tablas de ejemplo en Azure SQL Database. Use [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio para conectarse a Azure SQL Database y ejecute las consultas siguientes:

    **Crear Tabla1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Crear Tabla2**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. En **Tabla1**, agregue algunos datos de ejemplo. Deje **Tabla2** vacía. Los datos de **Tabla1** se importarán a Almacén de Data Lake. Luego se importarán los datos de Almacén de Data Lake a **Tabla2**. Ejecute el siguiente fragmento de código:

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Uso de Sqoop en un clúster de HDInsight con acceso a Almacén de Data Lake
Un clúster de HDInsight ya tiene los paquetes de Sqoop disponibles. Si ha configurado el clúster de HDInsight para utilizar Data Lake Store como almacenamiento adicional, puede usar Sqoop (sin cambios de configuración) para importar o exportar datos entre una base de datos relacional (en este ejemplo, Azure SQL Database) y una cuenta de Data Lake Store.

1. Para este tutorial, se supone que ha creado un clúster de Linux, por lo que debe usar SSH para conectarse al clúster. Consulte [Conexión a un clúster de HDInsight basado en Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Compruebe que puede acceder a la cuenta de Almacén de Data Lake desde el clúster. Ejecute el siguiente comando desde el símbolo del sistema de SSH:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Esto debería proporcionar una lista de archivos o carpetas en la cuenta del Almacén de Data Lake.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importación de datos de Azure SQL Database a Data Lake Store
1. Navegue al directorio donde están disponibles los paquetes de Sqoop. Normalmente, se encuentran en `/usr/hdp/<version>/sqoop/bin`.
2. Importe datos de **Tabla1** a la cuenta de Almacén de Data Lake. Use la sintaxis siguiente:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Tenga en cuenta que el marcador de posición **sql-database-server-name** representa el nombre del servidor donde se ejecuta la Base de datos SQL de Azure. **sql-database-name** representa el nombre de la base de datos real.

    Por ejemplo,


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Compruebe que los datos se han transferido a la cuenta de Almacén de Data Lake. Ejecute el siguiente comando:

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Debería ver la siguiente salida.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Cada archivo **part-m-*** corresponde a una fila de la tabla de origen, **Table1**. Puede ver el contenido de los archivos part-m-* para comprobarlo.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exportación de datos de Data Lake Store a Azure SQL Database
1. Exporte los datos de la cuenta de Data Lake Store a la tabla vacía, **Table2**, en Azure SQL Database. Use la sintaxis siguiente.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Por ejemplo,


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Compruebe que los datos se han cargado en la tabla de SQL Database. Use [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio para conectarse a Azure SQL Database y ejecute la siguiente consulta.

        SELECT * FROM TABLE2

    El resultado debe ser el siguiente.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Consideraciones de rendimiento sobre el uso de Sqoop

Para optimizar el rendimiento del trabajo de Scoop con el fin de copiar datos en el Data Lake Store, consulte el [documento de rendimiento de Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Otras referencias
* [Copiar datos de los blobs de Azure Storage en el Almacén Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
