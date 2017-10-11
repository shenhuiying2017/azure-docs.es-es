---
title: Carga de datos en Azure SQL Data Warehouse | Microsoft Docs
description: "Conozca los escenarios comunes para la carga de datos en Almacenamiento de datos SQL. Estos incluyen el uso de PolyBase, el almacenamiento de blobs de Azure, archivos planos y el envío de discos. También puede usar herramientas de otros fabricantes."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: c4199a387f5cdbd477a5e348e48ba8e8b5900075
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Carga de datos en Almacenamiento de datos SQL de Azure
Resumen de las opciones de escenario y recomendaciones para cargar datos en Almacenamiento de datos SQL.

La parte más difícil de carga de datos suele ser la preparación de los datos para la carga. Azure simplifica la carga mediante el almacenamiento de blobs de Azure como almacén de datos común para muchos de los servicios y usando Data Factory de Azure para organizar la comunicación y el movimiento de datos entre los servicios de Azure. Estos procesos se integran con la tecnología PolyBase, que usa el procesamiento paralelo masivo (MPP) para cargar datos en paralelo desde el almacenamiento de blobs de Azure en Almacenamiento de datos SQL. 

Para ver tutoriales que cargan bases de datos de ejemplo, vea [Carga de datos de ejemplo en SQL Data Warehouse][Load sample databases].

## <a name="load-from-azure-blob-storage"></a>Carga desde el almacenamiento de blobs de Azure
La manera más rápida de importar datos en Almacenamiento de datos SQL es usar PolyBase para cargar datos desde el almacenamiento de blobs de Azure. PolyBase usa el procesamiento paralelo masivo (MPP) de Almacenamiento de datos SQL para cargar datos en paralelo desde el almacenamiento de blobs de Azure. Para usar PolyBase, puede usar comandos T-SQL o una canalización de Data Factory de Azure.

### <a name="1-use-polybase-and-t-sql"></a>1. Usar PolyBase y T-SQL
Resumen del proceso de carga:

1. Mueva los datos a Azure Blob Storage o Azure Data Lake Store y almacénelos en archivos de texto.
2. Configure objetos externos en Almacenamiento de datos SQL para definir la ubicación y el formato de los datos.
3. Ejecute un comando T-SQL para cargar los datos en paralelo en una nueva tabla de base de datos.

<!-- 5. Schedule and run a loading job. --> 

Para ver un tutorial, consulte [Carga de datos de Azure Blob Storage en SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="2-use-azure-data-factory"></a>2. Usar Data Factory de Azure
Para usar PolyBase de manera más sencilla, puede crear una canalización de Data Factory de Azure que use PolyBase para cargar datos del almacenamiento de blobs de Azure en Almacenamiento de datos SQL. Esto se configura rápido porque no es necesario definir los objetos T-SQL. Si necesita consultar los datos externos sin importarlos, use T-SQL. 

Resumen del proceso de carga:

1. Mueva los datos al almacenamiento de blobs de Azure y almacénelos en archivos de texto. Azure Data Factory no admite actualmente la conectividad de ADLS con PolyBase.
2. Cree una canalización de Data Factory de Azure para introducir los datos. Use la opción PolyBase.
4. Programe y ejecute la canalización.

Para ver un tutorial, consulte [Carga de datos de Azure Blob Storage en SQL Data Warehouse (Azure Data Factory)][Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)].

## <a name="load-from-sql-server"></a>Carga desde SQL Server
Para cargar datos desde SQL Server en Almacenamiento de datos SQL, puede usar Integration Services (SSIS), transferir archivos planos o enviar discos a Microsoft. Siga leyendo para ver un resumen de los distintos procesos de carga y los vínculos a tutoriales.

Para planear una migración de datos completa de SQL Server a SQL Data Warehouse, consulte la [información general sobre migración][Migration overview]. 

### <a name="use-integration-services-ssis"></a>Uso de Integration Services (SSIS)
Si ya usa paquetes de Integration Services (SSIS) para cargarlos en SQL Server, puede actualizarlos para usar SQL Server como origen y Almacenamiento de datos SQL como destino. Esto es rápido y fácil de hacer, y es una buena opción si no desea migrar el proceso de carga para usar datos que ya están en la nube. La desventaja es que la carga será más lenta que con PolyBase porque este SSIS no realiza la carga en paralelo.

Resumen del proceso de carga:

1. Revisar el paquete de Integration Services para que apunte a la instancia de SQL Server para el origen y a la base de datos de Almacenamiento de datos SQL para el destino.
2. Migre el esquema a Almacenamiento de datos SQL si aún no está allí.
3. Cambie la asignación en los paquetes para usar solamente los tipos de datos que admite Almacenamiento de datos SQL.
4. Programe y ejecute el paquete.

Para ver un tutorial, consulte [Carga de datos de SQL Server en Azure SQL Data Warehouse (SSIS)][Load data from SQL Server to Azure SQL Data Warehouse (SSIS)].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Uso de AZCopy (recomendado para datos de menos de 10 TB)
Si el tamaño de los datos es menor que 10 TB, puede exportar los datos de SQL Server a archivos planos, copiar los archivos al almacenamiento de blobs de Azure y luego usar PolyBase para cargar los datos en Almacenamiento de datos SQL.

Resumen del proceso de carga:

1. Use la utilidad de línea de comandos bcp para exportar datos de SQL Server a archivos planos.
2. Usar la utilidad de línea de comandos AZCopy para copiar datos de archivos planos en el almacenamiento de blobs de Azure.
3. Use PolyBase para cargar datos en Almacenamiento de datos SQL

Para ver un tutorial, consulte [Carga de datos de Azure Blob Storage en SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="use-bcp"></a>Uso de bcp
Si tiene una pequeña cantidad de datos, puede usar bcp para cargar directamente en Almacenamiento de datos SQL de Azure.

Resumen del proceso de carga:

1. Use la utilidad de línea de comandos bcp para exportar datos de SQL Server a archivos planos.
2. Use bcp para cargar datos de archivos planos directamente en Almacenamiento de datos SQL.

Para ver un tutorial, consulte [Carga de datos de SQL Server en Azure SQL Data Warehouse (archivos planos)][Load data from SQL Server to Azure SQL Data Warehouse (bcp)].

### <a name="use-importexport-recommended-for--10-tb-data"></a>Uso de importación/exportación (recomendado para datos de menos de 10 TB)
Si el tamaño de los datos es menor que 10 TB y quiere moverlos a Azure, se recomienda que use el servicio de envío de discos [Import/Export][Import/Export]. 

Resumen del proceso de carga

1. Use la utilidad de línea de comandos bcp para exportar datos de SQL Server a archivos planos o discos transferibles.
2. Envíe los discos a Microsoft.
3. Microsoft carga los datos en Almacenamiento de datos SQL

## <a name="load-from-hdinsight"></a>Carga desde HDInsight
El Almacenamiento de datos SQL admite la carga de datos desde HDInsight a través de PolyBase. El proceso es el mismo que para cargar datos desde el Almacenamiento de blobs de Azure: mediante PolyBase para conectarse a HDInsight para cargar los datos. 

### <a name="1-use-polybase-and-t-sql"></a>1. Usar PolyBase y T-SQL
Resumen del proceso de carga:

1. Mueva los datos a HDInsight y almacénelos en formato de archivos de texto, ORC o Parquet.
2. Configure objetos externos en Almacenamiento de datos SQL para definir la ubicación y el formato de los datos.
3. Ejecute un comando T-SQL para cargar los datos en paralelo en una nueva tabla de base de datos.

Para ver un tutorial, consulte [Carga de datos de Azure Blob Storage en SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

## <a name="recommendations"></a>Recomendaciones
Muchos de nuestros asociados tienen soluciones de carga. Para más información, consulte una lista de los [asociados de soluciones][solution partners]. 

Si los datos provienen de un origen no relacional y quiere cargarlos en Almacenamiento de datos SQL,debe transformarlos en filas y columnas antes de cargarlos. Los datos transformados no necesitan almacenarse en una base de datos; se puede almacenar en archivos de texto.

Cree estadísticas de los datos recién cargados. Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas.  Para obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos.  Para más información, vea las [estadísticas][Statistics].

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias sobre desarrollo, vea la [información general sobre desarrollo][development overview].

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample databases]: ./sql-data-warehouse-load-sample-databases.md
[Migration overview]: ./sql-data-warehouse-overview-migrate.md
[solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
