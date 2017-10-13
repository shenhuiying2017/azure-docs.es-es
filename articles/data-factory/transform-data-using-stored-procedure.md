---
title: "Transformación de datos mediante la actividad de procedimiento almacenado en Azure Data Factory | Microsoft Docs"
description: "Explica cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en Azure SQL Database o Azure Data Warehouse desde una canalización de Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: shengc
ms.openlocfilehash: f13f8aa0ca8686c0582bed77d047c9e6b39f7aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformación de datos mediante la actividad de procedimiento almacenado de SQL Server en Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-stored-proc-activity.md)
> * [Versión 2: Versión preliminar](transform-data-using-stored-procedure.md)


Las actividades de transformación en una [canalización](concepts-pipelines-activities.md) de Data Factory transforman y procesan sus datos sin procesar para convertirlos en predicciones y perspectivas. La actividad de procedimiento almacenado es una de las actividades de transformación que admite Data Factory. Este artículo se basa en el artículo sobre [transformación de datos](transform-data.md), que presenta información general de la transformación de datos y las actividades de transformación admitidas en Data Factory.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de procedimiento almacenado en V1](v1/data-factory-stored-proc-activity.md).
> 
> Si no está familiarizado con Azure Data Factory, vea [Introduction to Azure Data Factory](introduction.md) (Introducción a Azure Data Factory) y siga el [tutorial de transformación de datos](tutorial-transform-data-spark-powershell.md) antes de leer este artículo. 

Puede usar la actividad de procedimiento almacenado para invocar un procedimiento almacenado en uno de los siguientes almacenes de datos de la empresa o en una máquina virtual (VM) de Azure: 

- Base de datos SQL de Azure
- Almacenamiento de datos SQL de Azure
- Base de datos de SQL Server.  Si se usa SQL Server, se debe instalar el entorno de ejecución de integración autohospedado en el mismo equipo que hospeda la base de datos o en un equipo independiente que tenga acceso a la base de datos. El entorno de ejecución de integración autohospedado es un componente que conecta orígenes de datos locales o en la máquina virtual de Azure con servicios en la nube de forma segura y administrada. Consulte el artículo sobre el [entorno de ejecución de integración autohospedado](create-self-hosted-integration-runtime.md) para más información.

> [!IMPORTANT]
> Al copiar datos en Azure SQL Database o SQL Server, se puede configurar **SqlSink** en la actividad de copia para invocar un procedimiento almacenado mediante la propiedad **sqlWriterStoredProcedureName**. Para obtener más información sobre la propiedad, vea los artículos sobre los conectores siguientes: [Azure SQL Database](connector-azure-sql-database.md) y [SQL Server](connector-sql-server.md). No se admite la invocación de un procedimiento almacenado al copiar datos en Azure SQL Data Warehouse mediante una actividad de copia. Sin embargo, puede usar la actividad de procedimiento almacenado para invocar un procedimiento almacenado en un SQL Data Warehouse. 
>
> Al copiar datos de Azure SQL Database, SQL Server o Azure SQL Data Warehouse, se puede configurar **SqlSource** en la actividad de copia para invocar un procedimiento almacenado de lectura de datos mediante la propiedad **sqlReaderStoredProcedureName**. Para más información, consulte los artículos sobre los conectores siguientes: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md) y [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalles de la sintaxis
Este es el formato JSON para definir una actividad de procedimiento almacenado:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

En la tabla siguiente se describen estas propiedades JSON:

| Propiedad                  | Descripción                              | Obligatorio |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nombre de la actividad                     | Sí      |
| Descripción               | Texto que describe para qué se usa la actividad. | No       |
| type                      | Para la actividad de procedimiento almacenado, el tipo de actividad es SqlServerStoredProcedure | Sí      |
| linkedServiceName         | Referencia a Azure SQL Database, Azure SQL Data Warehouse o SQL Server registrado como un servicio vinculado en Data Factory. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos). | Sí      |
| storedProcedureName       | Especifique el nombre del procedimiento almacenado de Azure SQL Database, Azure SQL Data Warehouse o la base de datos de SQL Server que se representa mediante el servicio vinculado que usa la tabla de salida. | Sí      |
| storedProcedureParameters | Especifique valores para los parámetros del procedimiento almacenado. Use `"param1": { "value": "param1Value","type":"param1Type" }` para pasar valores de parámetros y su tipo nativo compatible con el origen de datos. Si necesita pasar NULL para un parámetro, use *"param1": {"value": null}* (todo en minúsculas). | No       |

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad de Hive](transform-data-using-hadoop-hive.md)
* [Actividad de Pig](transform-data-using-hadoop-pig.md)
* [Actividad MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad personalizada de .NET](transform-data-using-dotnet-custom-activity.md)
* [Actividad de ejecución de Batch de Machine Learning](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)
