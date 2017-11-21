---
title: Movimiento de datos desde o hacia Azure Blob Storage con conectores SSIS | Microsoft Docs
description: Movimiento de datos desde o hacia Azure Blob Storage con conectores SSIS.
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 96a1b5fb-34d1-4b9b-8d99-2bb8289e0398
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 24237173876f2b292141d9373b346721a489bc56
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Movimiento de datos desde o hacia Azure Blob Storage con conectores SSIS
El [Feature Pack de SQL Server Integration Services para Azure](https://msdn.microsoft.com/library/mt146770.aspx) ofrece componentes para conectarse a Azure, transferir datos entre Azure y orígenes de datos locales y procesar datos almacenados en Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Una vez que los clientes movieron los datos locales a la nube, pueden tener acceso a ellos desde cualquier servicio de Azure para aprovechar al máximo el conjunto de tecnologías de Azure. Por ejemplo, se pueden usar en Azure Machine Learning o en un clúster de HDInsight.

Generalmente, este será el primer paso de los tutoriales de [SQL](sql-walkthrough.md) y [HDInsight](hive-walkthrough.md).

Si desea ver un análisis de los escenarios canónicos que usan SSIS para satisfacer las necesidades de negocio comunes en escenarios de integración de datos híbridos, visite el blog [Doing more with SQL Server Integration Services Feature Pack for Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) (Aprovechar más el Feature Pack de SQL Server Integration Services para Azure).

> [!NOTE]
> Para ver una introducción completa a Azure Blob Storage, consulte [Aspectos básicos de Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) y [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
Si desea realizar las tareas que describe este artículo, debe tener configurada una suscripción de Azure y una cuenta de Azure Storage. Debe saber cuál es el nombre de la cuenta de Azure storage y la clave de la cuenta para cargar o descargar datos.

* Para configurar una **suscripción a Azure**, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
* Para obtener instrucciones sobre cómo crear una **cuenta de almacenamiento** y cómo obtener información sobre la cuenta y la clave, consulte [Acerca de las cuentas de almacenamiento de Azure](../../storage/common/storage-create-storage-account.md).

Para usar los **conectores SSIS**, debe descargar:

* **SQL Server 2014 o 2016 Standard (o superior)**: la instalación incluye SQL Server Integration Services.
* **Feature Pack de Microsoft SQL Server 2014 o 2016 Integration Services para Azure**: se pueden descargar, respectivamente, en la página [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) y [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492).

> [!NOTE]
> SSIS se instala con SQL Server, pero no está incluido en la versión Express. Para obtener información sobre qué aplicaciones se incluyen en las distintas versiones de SQL Server, consulte [Ediciones de SQL Server](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Para obtener materiales de aprendizaje sobre SSIS, consulte [Aprendizaje práctico para SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Si desea obtener información sobre cómo trabajar con SISS para compilar paquetes de extracción, transformación y carga (ETL) sencillos, consulte [Tutorial de SSIS: Creación de un paquete ETL sencillo](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Descargar el conjunto de datos de taxis de la ciudad de Nueva York
El ejemplo aquí descrito usa un conjunto de datos disponible para todo público, el conjunto de datos [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) (Viajes en taxi en la ciudad de Nueva York). El conjunto de datos consta de alrededor de 173 millones de viajes en taxi que se realizaron en Nueva York durante el año 2013. Existen dos tipos de datos: datos de los detalles de las carreras y datos sobre las tarifas. Como existe un archivo correspondiente a cada mes, tenemos, en total, 24 archivos, cada uno de los cuales tiene un tamaño de 2 GB sin comprimir.

## <a name="upload-data-to-azure-blob-storage"></a>Carga de datos en el almacenamiento de blobs de Azure
Para mover los datos con el Feature Pack de SSIS desde un almacenamiento local a Blob Storage de Azure, usamos una instancia de la [**tarea de carga de blobs de Azure**](https://msdn.microsoft.com/library/mt146776.aspx), que aparece a continuación:

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

A continuación, se describen los parámetros que usa la tarea:

| Campo | Description |
| --- | --- |
| **AzureStorageConnection** |Especifica un Administrador de conexiones de Azure Storage existente o crea uno nuevo que hace referencia a una cuenta de Azure Storage que apunta adonde se almacenan los archivos de blob. |
| **BlobContainer** |Especifica el nombre del contenedor de blobs que mantiene los archivos cargados como blobs. |
| **BlobDirectory** |Especifica el directorio de blobs donde se almacena el archivo cargado como un blob en bloques. El directorio de blobs es una estructura jerárquica virtual. Si el blob ya existe, se reemplaza. |
| **LocalDirectory** |Especifica el directorio local que contiene los archivos que se cargarán. |
| **FileName** |Especifica un nombre de filtro para seleccionar archivos con el patrón de nombre especificado. Por ejemplo, MySheet\*.xls\* incluye archivos como MySheet001.xls y MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Especifica un filtro de intervalo de tiempo. Se incluirán los archivos modificados después de *TimeRangeFrom* y antes de *TimeRangeTo*. |

> [!NOTE]
> Es necesario corregir las credenciales de **AzureStorageConnection** y se debe salir de **BlobContainer** antes de intentar realizar la transferencia.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Cargar datos desde el almacenamiento de blobs de Azure
Para descargar datos desde Azure Blob Storage a un almacenamiento local con SSIS, use una instancia de [Azure Blob Upload Task](https://msdn.microsoft.com/library/mt146779.aspx) (Tarea de carga de Azure Blob).

## <a name="more-advanced-ssis-azure-scenarios"></a>Escenarios de SSIS-Azure más avanzados
El Feature Pack de SSIS permite administrar flujos más complejos al empaquetar juntas las tareas. Por ejemplo, los datos de blobs se podrían ingresar directamente en un clúster de HDInsight cuya salida se podría descargar nuevamente a un blob y, luego, a un almacenamiento local. SSIS puede ejecutar trabajos de Hive y Pig en un clúster de HDInsight, mediante el uso de conectores SSIS adicionales:

* Para ejecutar un script de Hive en un clúster de HDInsight de Azure con SSIS, use [Tarea de Hive para HDInsight de Azure](https://msdn.microsoft.com/library/mt146771.aspx).
* Para ejecutar un script de Pig en un clúster de HDInsight de Azure con SSIS, use [Tarea de Pig para HDInsight de Azure](https://msdn.microsoft.com/library/mt146781.aspx).

