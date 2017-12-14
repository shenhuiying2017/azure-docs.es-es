---
title: "Administración de Azure Data Lake Analytics mediante la interfaz de la línea de comandos (CLI) de Azure | Microsoft Docs"
description: "Aprenda a administrar cuentas de Análisis con Data Lake, orígenes de datos, usuarios y trabajos mediante la CLI de Azure"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: 142a5baf0ffb0425856520fadccee27f3fc0517f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Administración del Análisis de Azure Data Lake mediante Interfaz de línea de comandos (CLI) de Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Aprenda a administrar cuentas, orígenes de datos, usuarios y trabajos de Azure Data Lake Analytics con la CLI de Azure. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.


**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI**. Consulte [Instalación y configuración de la CLI de Azure](../cli-install-nodejs.md).
  * Descargue e instale la **versión preliminar** [herramientas de la CLI de Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para completar esta demostración.
* **Autenticación**, mediante el comando siguiente:
  
        azure login
    Para más información acerca de cómo autenticarse con una cuenta profesional o educativa, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](/cli/azure/authenticate-azure-cli).
* **Cambiar al modo de Administrador de recursos de Azure**con el siguiente comando:
  
        azure config mode arm

**Para mostrar los comandos de Almacén Data Lake y Análisis de Data Lake:**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>Administrar cuentas
Antes de ejecutar un trabajo de Análisis de Data Lake, debe tener una cuenta de Análisis de Data Lake. A diferencia de HDInsight de Azure, no se paga por una cuenta de Análisis cuando no está ejecutando un trabajo.  Solo se paga por el tiempo en que se ejecuta un trabajo.  Para obtener más información, consulte la página con [información general sobre Análisis con Azure Data Lake](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Creación de cuentas
      azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


### <a name="update-accounts"></a>Actualización de cuentas
El siguiente comando actualiza las propiedades de una cuenta existente de Análisis de Data Lake

    azure datalake analytics account set "<Data Lake Analytics Account Name>"


### <a name="list-accounts"></a>Enumeración de cuentas
Enumeración de cuentas de Análisis de Data Lake 

    azure datalake analytics account list

Enumerar cuentas de Análisis de Data Lake dentro de un grupo de recursos específico

    azure datalake analytics account list -g "<Azure Resource Group Name>"

Obtener detalles de una cuenta específica de Análisis de Data Lake

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

### <a name="delete-data-lake-analytics-accounts"></a>Eliminación de cuentas de Análisis de Data Lake
      azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Administración de orígenes de datos de cuenta
Actualmente, Análisis de Data Lake admite los siguientes orígenes de datos:

* [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Cuando crea una cuenta de Análisis, debe designar una cuenta de Almacén de Azure Data Lake para que sea la cuenta de almacenamiento predeterminada. La cuenta predeterminada de almacenamiento de ADL sirve para almacenar los metadatos de trabajos y los registros de auditoría de trabajos. Una vez creada la cuenta de Análisis, puede agregar más cuentas de Data Lake Store y/o cuentas de Azure Storage. 

### <a name="find-the-default-adl-storage-account"></a>Búsqueda de la cuenta de almacenamiento de ADL predeterminada
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

El valor aparece en properties:datalakeStoreAccount:name.

### <a name="add-additional-azure-blob-storage-accounts"></a>Adición de más cuentas de almacenamiento de blobs de Azure
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

> [!NOTE]
> Solo se admiten nombres cortos de almacenamiento de blobs.  No use el FQDN, por ejemplo "myblob.blob.core.windows.net".
> 
> 

### <a name="add-additional-data-lake-store-accounts"></a>Adición de más cuentas de Almacén de Data Lake
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] es un modificador opcional para indicar si el Data Lake que se va a agregar es la cuenta de Data Lake predeterminada. 

### <a name="update-existing-data-source"></a>Actualizar el origen de datos existente
Para establecer una cuenta de Almacén Data Lake existente como predeterminada:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d

Para actualizar una clave de cuenta de almacenamiento de blobs existente:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### <a name="list-data-sources"></a>Enumeración de orígenes de datos:
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

![Origen de datos de lista de Análisis de Data Lake](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Eliminar orígenes de datos:
Para eliminar una cuenta de Almacén Data Lake:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Para eliminar una cuenta de Almacenamiento de blobs:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## <a name="manage-jobs"></a>Trabajos de administración
Debe tener una cuenta de Análisis de Data Lake para poder crear un trabajo.  Para obtener más información, consulte [Administración de cuentas de Análisis de Data Lake](#manage-accounts).

### <a name="list-jobs"></a>Enumeración de trabajos
      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Origen de datos de lista de Análisis de Data Lake](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obtención de detalles del trabajo
      azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"

### <a name="submit-jobs"></a>Envío de trabajos
> [!NOTE]
> La prioridad predeterminada de un trabajo es 1000 y el grado predeterminado de paralelismo para un trabajo es 1.
> 
> 

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### <a name="cancel-jobs"></a>Cancelación de trabajos
Use el comando de la lista para buscar el identificador del trabajo y, a continuación, use Cancelar para cancelar el trabajo.

      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
      azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## <a name="manage-catalog"></a>Administrar catálogo
El catálogo de U-SQL se usa para estructurar datos y código, para que puedan compartirse mediante scripts de U-SQL. El catálogo permite el mayor rendimiento posible con los datos en Azure Data Lake. Para obtener más información, consulte [Uso del catálogo de U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-catalog-items"></a>Enumeración de elementos del catálogo
    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table

Entre los tipos, se incluyen base de datos, esquema, ensamblado, origen de datos externo, tabla, función con valores de tabla o estadísticas de tabla.

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-arm-groups"></a>Usar grupos ARM
Las aplicaciones normalmente se componen de muchos componentes,por ejemplo una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como Grupo de recursos de Azures Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). 

Un servicio Análisis de Data Lake puede incluir los siguientes componentes:

* Cuenta de Análisis de Azure Data Lake
* Cuenta predeterminada y necesaria de Almacén de Azure Data Lake
* Cuentas adicionales de Almacén de Azure Data Lake
* Cuentas adicionales de Azure Storage

Puede crear todos estos componentes en un grupo de ARM para que sean más fáciles de administrar.

![Cuenta y almacenamiento de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

La cuenta de Análisis de Data Lake y las cuentas de almacenamiento dependientes deben ubicarse en el mismo centro de datos de Azure.
Sin embargo, el grupo de ARM puede encontrarse en otro centro de datos.  

## <a name="see-also"></a>Otras referencias
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* 
            [Introducción a Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md)
* 
            [Administración de Análisis de Azure Data Lake mediante Azure Portal](data-lake-analytics-manage-use-portal.md)
* 
            [Supervisión y solución de problemas de trabajos de Data Lake Analytics mediante Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

