---
title: Mover datos a Azure SQL Database para Azure Machine Learning | Microsoft Docs
description: Crear tabla SQL y cargar datos en ella
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 2d0c36990ba897bef83601bd4295518618224733
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover datos a una base de datos de SQL de Azure para Aprendizaje automático de Azure
En este tema se describen las opciones para mover datos de archivos planos (formatos CSV o TSV) o de datos almacenados en un servidor de SQL Server local a Azure SQL Database. Estas tareas para mover datos a la nube forman parte del proceso de ciencia de datos en equipos.

Para ver un tema que describa las opciones para mover datos a un servidor de SQL Server local para Machine Learning, vea [Mover datos a un servidor SQL Server en una máquina virtual de Azure](move-sql-server-virtual-machine.md).

El **menú** siguiente redirige a temas en los que se describe cómo ingerir datos en entornos de destino en que se pueden almacenar y procesar datos durante el proceso de ciencia de datos en equipos (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

En la tabla siguiente se resumen las opciones para mover datos a una base de datos SQL de Azure.

| <b>ORIGEN</b> | <b>DESTINO: Base de datos SQL de Azure</b> |
| --- | --- |
| <b>Archivo plano (formatos CSV o TSV)</b> |<a href="#bulk-insert-sql-query">Consulta SQL de inserción masiva |
| <b>SQL Server local</b> |1. <a href="#export-flat-file">Exportación a un archivo plano<br> 2. <a href="#insert-tables-bcp">Asistente para migración de base de datos de SQL<br> 3. <a href="#db-migration">Copia de seguridad y restauración de una base de datos<br> 4. <a href="#adf">Factoría de datos de Azure |

## <a name="prereqs"></a>Requisitos previos
El procedimiento aquí descrito requiere disponer de:

* Una **suscripción de Azure**. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**. En este tutorial se usa una cuenta de Azure Storage para almacenar los datos. Si no dispone de una cuenta de almacenamiento de Azure, vea el artículo [Creación de una cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) . Tras crear la cuenta de almacenamiento, tendrá que obtener la clave de cuenta que se usa para tener acceso al almacenamiento. Consulte [Administración de las claves de acceso de almacenamiento](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Acceso a una **base de datos SQL de Azure**. Si debe configurar una instancia de Azure SQL Database, [Getting Started with Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) (Introducción a Microsoft Azure SQL Database) proporciona información sobre cómo aprovisionar una nueva instancia de Azure SQL Database.
* **Azure PowerShell** instalado y configurado de forma local. Para obtener instrucciones, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

**Datos**: los procesos de migración se demuestran con el [conjunto de datos de taxis de Nueva York](http://chriswhong.com/open-data/foil_nyc_taxi/). El conjunto de datos de taxis de Nueva York contiene información sobre los datos de carreras y las tarifas, y está disponible en Azure Blob Storage: [Datos de taxis de Nueva York](http://www.andresmh.com/nyctaxitrips/). En [Descripción del conjunto de datos de carreras de taxi de Nueva York](sql-walkthrough.md#dataset), se ofrece un ejemplo y una descripción de estos archivos.

Puede adaptar los procedimientos que se describen aquí para un conjunto de datos propios o seguir los pasos descritos para el uso del conjunto de datos de taxis de Nueva York. Para cargar el conjunto de datos de taxis de Nueva York en la base de datos de SQL Server local, siga el procedimiento descrito en [Importación masiva de datos en una base de datos de SQL Server](sql-walkthrough.md#dbload). Estas instrucciones corresponden a un servidor SQL Server en una máquina virtual de Azure, pero el procedimiento para realizar la carga en SQL Server local es el mismo.

## <a name="file-to-azure-sql-database"></a> Mover datos desde un origen de archivo plano a una base de datos SQL de Azure
Los datos de archivos planos (formatos CSV o TSV) se pueden mover a una base de datos de SQL de Azure mediante una consulta SQL de inserción masiva.

### <a name="bulk-insert-sql-query"></a> Consulta SQL de inserción masiva
Los pasos del procedimiento en el que se usa la consulta SQL de inserción masiva son similares a los descritos en las secciones en las que se explica el movimiento de datos desde un origen de archivo plano a un servidor SQL Server en una máquina virtual de Azure. Para detalles, vea [Consulta SQL de inserción masiva](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Mover datos desde un servidor SQL Server local a una base de datos SQL de Azure
Si los datos de origen están almacenados en un servidor SQL Server local, hay varias alternativas para mover los datos a una instancia de Azure SQL Database:

1. [Exportación a un archivo plano](#export-flat-file)
2. [Asistente para migración de Base de datos SQL](#insert-tables-bcp)
3. [Copia de seguridad y restauración de una base de datos](#db-migration)
4. [Factoría de datos de Azure](#adf)

Los pasos para las tres primeras opciones son muy similares a los de la sección [Mover datos a un servidor SQL Server en una máquina virtual de Azure](move-sql-server-virtual-machine.md) , en la que se explican los mismos procedimientos. En las instrucciones siguientes se proporcionan vínculos a las secciones correspondientes de ese tema.

### <a name="export-flat-file"></a>Exportación a un archivo plano
Los pasos para esta exportación a un archivo plano son similares a los que se explican en [Exportación a un archivo plano](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Asistente para migración de Base de datos SQL
Los pasos para usar el Asistente para migración de SQL Database son similares a los que se explican en [Asistente para migración de SQL Database](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Copia de seguridad y restauración de una base de datos
Los pasos para usar la copia de seguridad y restauración de la base de datos son similares a los que se explican en [Copia de seguridad y restauración de la base de datos](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Factoría de datos de Azure
El procedimiento para mover datos a una instancia de Azure SQL Database con Azure Data Factory (ADF) se explica en el tema [Mover datos desde un servidor SQL Server local a SQL Azure con Azure Data Factory](move-sql-azure-adf.md). En este tema se muestra cómo mover datos desde una base de datos local de SQL Server a una instancia de Azure SQL Database a través de Azure Blob Storage con Azure Data Factory (ADF).

Considere el uso de ADF cuando los datos deban migrarse continuamente en un escenario híbrido con acceso a recursos locales y de nube, y cuando los datos se transfieran o deban modificarse o tener lógica de negocios agregada mientras se migran. La ADF permite la programación y supervisión de trabajos mediante scripts JSON sencillos que administran el movimiento de datos de forma periódica. La ADF también tiene otras capacidades como la compatibilidad con operaciones complejas.
