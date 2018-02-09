---
title: "Administración de Azure Data Lake Analytics mediante la interfaz de la línea de comandos (CLI) de Azure | Microsoft Docs"
description: "Aprenda a administrar cuentas de Análisis con Data Lake, orígenes de datos, usuarios y trabajos mediante la CLI de Azure"
services: data-lake-analytics
documentationcenter: 
author: SnehaGunda
manager: Kfile
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: sngun
ms.openlocfilehash: edaedaa517a672cd4bad5dc35527f4595ab4a85f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Administración del Análisis de Azure Data Lake mediante Interfaz de línea de comandos (CLI) de Azure

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Aprenda a administrar cuentas, orígenes de datos, usuarios y trabajos de Azure Data Lake Analytics con la CLI de Azure. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.


**Requisitos previos**

Antes de empezar este tutorial, debe contar con los recursos siguientes:

* Una suscripción de Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* CLI de Azure. Consulte [Instalación y configuración de la CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Descargue e instale la **versión preliminar** [herramientas de la CLI de Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para completar esta demostración.

* Para realizar la autenticación, use el comando `az login` y seleccione la suscripción que desea usar. Para más información acerca de cómo autenticarse con una cuenta profesional o educativa, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Ahora puede acceder a los comandos de Data Lake Analytics y Data Lake Store. Ejecute el comando siguiente para mostrar los comandos de Data Lake Store y Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Administrar cuentas

Antes de ejecutar un trabajo de Análisis de Data Lake, debe tener una cuenta de Análisis de Data Lake. A diferencia de HDInsight de Azure, no se paga por una cuenta de Análisis cuando no está ejecutando un trabajo. Solo se paga por el tiempo en que se ejecuta un trabajo.  Para obtener más información, consulte la página con [información general sobre Análisis con Azure Data Lake](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Creación de cuentas

Ejecute el comando siguiente para crear una cuenta de Data Lake. 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Actualización de cuentas

El siguiente comando actualiza las propiedades de una cuenta existente de Análisis de Data Lake

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Enumeración de cuentas

Enumerar cuentas de Análisis de Data Lake dentro de un grupo de recursos específico

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Obtención de los detalles de una cuenta

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Eliminación de una cuenta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Administración de orígenes de datos

Actualmente, Data Lake Analytics admite estos dos orígenes de datos:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Cuando crea una cuenta de Análisis, debe designar una cuenta de Almacén de Azure Data Lake para que sea la cuenta de almacenamiento predeterminada. La cuenta de almacenamiento predeterminada de Data Lake sirve para almacenar los registros de auditoría y de metadatos de trabajos. Una vez creada la cuenta de Análisis, puede agregar más cuentas de Data Lake Store y/o cuentas de Azure Storage. 

### <a name="find-the-default-data-lake-store-account"></a>Búsqueda de la cuenta predeterminada de Almacén de Data Lake

Ejecute el comando `az dla account show` para ver la cuenta predeterminada de Data Lake Store. El nombre de la cuenta predeterminada aparece bajo la propiedad defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Incorporación de cuentas de Blob Storage adicionales

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Solo se admiten nombres cortos de almacenamiento de blobs. No use el FQDN, por ejemplo "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Adición de más cuentas de Almacén de Data Lake

El comando siguiente actualiza la cuenta de Data Lake Analytics especificada con una cuenta de Data Lake Store adicional:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Actualizar el origen de datos existente

Para actualizar una clave de cuenta de almacenamiento de blobs existente:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Enumeración de orígenes de datos:

Para mostrar las cuentas de Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Para mostrar la cuenta de Blob Storage:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Origen de datos de lista de Análisis de Data Lake](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Eliminar orígenes de datos:
Para eliminar una cuenta de Almacén Data Lake:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Para eliminar una cuenta de Almacenamiento de blobs:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Trabajos de administración
Debe tener una cuenta de Análisis de Data Lake para poder crear un trabajo.  Para obtener más información, consulte [Administración de cuentas de Análisis de Data Lake](#manage-accounts).

### <a name="list-jobs"></a>Enumeración de trabajos

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Origen de datos de lista de Análisis de Data Lake](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obtención de detalles del trabajo

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Envío de trabajos

> [!NOTE]
> La prioridad predeterminada de un trabajo es 1000 y el grado predeterminado de paralelismo para un trabajo es 1.
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>Cancelación de trabajos
Use el comando de la lista para buscar el identificador del trabajo y, a continuación, use Cancelar para cancelar el trabajo.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="use-azure-resource-manager-groups"></a>Uso de grupos del Administrador de recursos de Azure
Las aplicaciones normalmente se componen de muchos elementos, por ejemplo, una aplicación web, una base de datos, un servidor de base de datos, un almacenamiento y servicios de terceros. Azure Resource Manager le permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como un Azure Resource Group. Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para realizar la implementación se usa una plantilla, que puede funcionar en distintos entornos, como producción, pruebas y ensayo. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). 

Un servicio Análisis de Data Lake puede incluir los siguientes componentes:

* Cuenta de Análisis de Azure Data Lake
* Cuenta predeterminada y necesaria de Almacén de Azure Data Lake
* Cuentas adicionales de Almacén de Azure Data Lake
* Cuentas adicionales de Azure Storage

Puede crear todos estos componentes en un grupo de Resource Manager para que sea más fáciles de administrar.

![Cuenta y almacenamiento de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

La cuenta de Análisis de Data Lake y las cuentas de almacenamiento dependientes deben ubicarse en el mismo centro de datos de Azure.
Sin embargo, el grupo de Resource Manager se puede ubicar en un centro de datos distinto.  

## <a name="see-also"></a>Otras referencias
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introducción a Data Lake Analytics mediante el portal de Azure](data-lake-analytics-get-started-portal.md)
* [Administración de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

