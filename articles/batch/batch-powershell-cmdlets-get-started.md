---
title: "Introducción a PowerShell para Azure Batch | Microsoft Docs"
description: "Una rápida introducción a los cmdlets de Azure PowerShell que puede usar para administrar recursos de Batch."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e33be6ed658e00250ea1e80cd7da4d348fb18296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Administración de recursos de Batch con cmdlets de PowerShell

Con los cmdlets de PowerShell de Lote de Azure puede realizar directamente y mediante scripts muchas de las mismas tareas que se llevan a cabo con las API de Lote, el Portal de Azure y la interfaz de la línea de comandos (CLI) de Azure. Esta es una breve introducción a los cmdlets que se pueden usar para administrar cuentas de Lote y trabajar con recursos de Lote tales como grupos, trabajos y tareas.

Para obtener una lista completa de los cmdlets de Lote y la sintaxis detallada de los cmdlets, consulte la [referencia de los cmdlets de Lote de Azure](/powershell/module/azurerm.batch/#batch).

Este artículo se basa en los cmdlets de Azure PowerShell versión 3.0.0. Le recomendamos que actualice su instancia de Azure PowerShell con frecuencia para aprovechar las mejoras y actualizaciones del servicio.

## <a name="prerequisites"></a>Requisitos previos
Realice las siguientes operaciones si desea utilizar Azure PowerShell para administrar los recursos de Batch.

* [Instalación y configuración de Azure PowerShell](/powershell/azure/overview)
* Ejecución del cmdlet **Login-AzureRmAccount** para conectarse a la suscripción (los cmdlets de Azure Batch se incluyen en el módulo Azure Resource Manager):
  
    `Login-AzureRmAccount`
* **Registro con el espacio de nombres del proveedor de Batch**. Esta operación solo debe realizarse **una vez por cada suscripción**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Administrar claves y cuentas por lotes
### <a name="create-a-batch-account"></a>Crear una cuenta de lote
**New-AzureRmBatchAccount** crea una cuenta de Batch en un grupo de recursos especificado. Si aún no dispone de un grupo de recursos, créelo ejecutando el cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Especifique una de las regiones de Azure en el parámetro **Ubicación**, como "centro de EE. UU.". Por ejemplo:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

A continuación, cree una cuenta de Batch en el grupo de recursos. Para ello, especifique su nombre en <*account_name*>, así como la ubicación y el nombre del grupo de recursos. La operación de crear la cuenta de Lote puede tardar un tiempo en completarse. Por ejemplo:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> El nombre de la cuenta de Lote debe ser único para la región de Azure en el grupo de recursos, contener entre 3 y 24 caracteres y usar solo letras minúsculas y números.
> 
> 

### <a name="get-account-access-keys"></a>Obtener claves de acceso de cuenta
**Get-AzureBatchAccountKeys** muestra las claves de acceso asociadas a una cuenta de Lote de Azure. Por ejemplo, ejecute lo siguiente para obtener las claves principales y secundarias de la cuenta que creó.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Generar una nueva clave de acceso
**New-AzureBatchAccountKey** genera una nueva clave de cuenta principal o secundaria para una cuenta de Lote de Azure. Por ejemplo, para generar una nueva clave principal para la cuenta de proceso por lotes, escriba:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Para generar una nueva clave secundaria, especifique "Secondary" para el parámetro **KeyType** . Deberá volver a generar las claves principales y secundarias por separado.
> 
> 

### <a name="delete-a-batch-account"></a>Eliminar una cuenta de lote
**Remove-AzureBatchAccount** elimina una cuenta de Lote. Por ejemplo:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Cuando se le pida, confirme que desea quitar la cuenta. La eliminación de cuenta puede tardar unos minutos en completarse.

## <a name="create-a-batchaccountcontext-object"></a>Creación de un objeto BatchAccountContext
Para realizar la autenticación mediante los cmdlets de PowerShell del servicio Lote al crear y administrar grupos, trabajos, tareas y otros recursos de este servicio, primero debe crear un objeto BatchAccountContext para almacenar el nombre y las claves de la cuenta:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Pase el objeto BatchAccountContext a los cmdlets que usan el parámetro **BatchContext** .

> [!NOTE]
> De forma predeterminada, la clave que se utiliza para la autenticación es la clave principal de la cuenta. Sin embargo, puede seleccionar otra clave explícitamente modificando la propiedad **KeyInUse** del objeto BatchAccountContext: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Creación y modificación de recursos de Lote
Utilice cmdlets, como **New-AzureBatchPool**, **New-AzureBatchJob** y **New-AzureBatchTask**, para crear recursos en una cuenta de Batch. Tiene a su disposición cmdlets **Get-** y **Set-** equivalentes para actualizar las propiedades de los recursos existentes y cmdlets **Remove-** para quitar recursos de una cuenta de Batch.

Cuando se utilizan muchos de estos cmdlets, además de pasar un objeto BatchContext, es preciso crear o pasar objetos que contienen la configuración detallada de los recursos, como se muestra en el ejemplo siguiente. Para obtener ejemplos adicionales, consulte la ayuda detallada de cada cmdlet.

### <a name="create-a-batch-pool"></a>Crear un grupo de Lote
Al crear o actualizar un grupo de Batch, seleccione una configuración de servicio en la nube o una configuración de máquina virtual para el sistema operativo en los nodos de proceso (consulte [Información general de las características de Batch para desarrolladores](batch-api-basics.md#pool)). Si se especifica la configuración de servicio en la nube, se crea la imagen de sus nodos de proceso con una de las [versiones del SO invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Si se especifica la configuración de máquina virtual, puede especificar alguna de las imágenes de máquina virtual de Linux o Windows que aparecen en [Azure Virtual Machines Marketplace][vm_marketplace], o proporcionar una imagen personalizada que haya preparado.

Al ejecutar **New-AzureBatchPool**, pase la configuración del sistema operativo en un objeto PSCloudServiceConfiguration o PSVirtualMachineConfiguration. Por ejemplo, el siguiente cmdlet crea un nuevo grupo de Lote con nodos de proceso de tamaño pequeño en la configuración del servicio en la nube, con una imagen de la última versión de sistema operativo de familia 3 (Windows Server 2012). En este caso, el parámetro **CloudServiceConfiguration** especifica la variable *$configuration* como objeto PSCloudServiceConfiguration. El parámetro **BatchContext** especifica una variable definida anteriormente *$context* como objeto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

El número destino de nodos de proceso en el nuevo grupo se determina mediante una fórmula de autoescalado. En este caso, la fórmula es simplemente **$TargetDedicated=4**, que indica que el número máximo de nodos de proceso en el grupo es 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consulta de grupos, trabajos, tareas y otros detalles
Utilice cmdlets, como **Get-AzureBatchPool**, **Get-AzureBatchJob** y **Get-AzureBatchTask**, para consultar las entidades creadas en una cuenta de Batch.

### <a name="query-for-data"></a>Consulta de datos
Por ejemplo, use **Get-AzureBatchPools** para encontrar sus grupos. De forma predeterminada, esto consulta todos los grupos de su cuenta, siempre que ya haya almacenado el objeto BatchAccountContext en *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Uso de un filtro OData
Puede proporcionar un filtro de OData mediante el parámetro **Filter** para buscar únicamente los objetos que le interesen. Por ejemplo, puede encontrar todos los grupos cuyos id. empiecen por "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Este método no es tan flexible como "Where-Object" en una canalización local. Sin embargo, la consulta se envía al servicio de lote directamente para que todos los filtros se apliquen en el servidor, lo cual ahorra ancho de banda de Internet.

### <a name="use-the-id-parameter"></a>Uso del parámetro Id
Una alternativa al filtro OData es el parámetro **Id**. Para consultar un grupo específico con el Id. "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

El parámetro **Id** solo admite búsquedas de identificadores completos. No se pueden usar caracteres comodín ni filtros de tipo OData.

### <a name="use-the-maxcount-parameter"></a>Uso del parámetro MaxCount
De forma predeterminada, cada cmdlet devuelve un máximo de 1000 objetos. Si se alcanza este límite, puede refinar el filtro para que devuelva menos objetos, o establecer explícitamente un máximo mediante el parámetro **MaxCount** . Por ejemplo:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para quitar el límite superior, establezca **MaxCount** en 0 o menos.

### <a name="use-the-powershell-pipeline"></a>Uso de la canalización de PowerShell
Los cmdlets de lote pueden aprovechar la canalización para enviar datos entre los cmdlets de PowerShell. El resultado es el mismo que si se especifica un parámetro, pero facilita el trabajo cuando se utilizan varias entidades.

Por ejemplo, busque y muestre todas las tareas de su cuenta:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Reinicie todos los nodos de proceso de un grupo:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Administración de paquetes de aplicación
Los paquetes de aplicación proporcionan una manera simplificada de implementar aplicaciones a los nodos de proceso de los grupos. Con los cmdlets de PowerShell incluidos en Batch, puede cargar y administrar paquetes de aplicación de la cuenta de Batch e implementar versiones del paquete en los nodos de proceso.

**Cree** una aplicación:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Agregue** un paquete de aplicación:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Especifique la **versión predeterminada** de la aplicación:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Muestre** los paquetes de la aplicación:

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Elimine** un paquete de aplicación:

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Elimine** una aplicación:

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Para poder eliminar una aplicación, primero debe eliminar todas las versiones de los paquetes. Si intenta eliminar una aplicación que contiene paquetes, aparecerá un error de tipo 'conflicto'.
> 
> 

### <a name="deploy-an-application-package"></a>Implementación de un paquete de aplicación
Al crear un grupo, puede especificar uno o varios paquetes de aplicación para implementarlos. Cuando se especifica un paquete en el momento de la creación de un grupo, se implementa en todos los nodos cuando cada nodo se incorpora al grupo. También se implementan paquetes cuando un nodo se reinicia o se restablece su imagen inicial.

Especifique la opción `-ApplicationPackageReference` al crear un grupo para implementar un paquete de aplicación en los nodos del grupo cuando se unen a este. Primero, cree un objeto **PSApplicationPackageReference** y configúrelo con el identificador de la aplicación y la versión del paquete que desee implementar en los nodos de proceso del grupo:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Ahora, cree el grupo y especifique el objeto de referencia del paquete como argumento en la opción `ApplicationPackageReferences`:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Puede encontrar más información sobre los paquetes de aplicación en [Implementación de aplicaciones en nodos de proceso con paquetes de aplicaciones de Batch](batch-application-packages.md).

> [!IMPORTANT]
> Para utilizar paquetes de aplicación, primero se debe [vincular una cuenta de Almacenamiento de Azure](#linked-storage-account-autostorage) a su cuenta de Lote.
> 
> 

### <a name="update-a-pools-application-packages"></a>Actualización de los paquetes de aplicación de un grupo
Para actualizar las aplicaciones asignadas a un grupo existente, primero debe crear un objeto PSApplicationPackageReference con las propiedades que desee (identificador de aplicación y versión de paquete):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

A continuación, debe obtener el grupo de Batch, eliminar los paquetes existentes, agregar la nueva referencia de paquete y actualizar el servicio Batch con la nueva configuración del grupo:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Ahora, las propiedades del grupo están actualizadas en el servicio Batch. Para que la implementación del nuevo paquete de aplicación en los nodos de proceso del grupo surta efecto, debe reiniciar estos nodos o restablecer su imagen inicial. Puede reiniciar todos los nodos de un grupo con este comando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> Puede implementar varios paquetes de aplicación en los nodos de proceso de un grupo. Si en lugar de reemplazar los paquetes que están instalados actualmente prefiere *agregar* un paquete de aplicación, omita la línea `$pool.ApplicationPackageReferences.Clear()` anterior.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Para conocer la sintaxis detallada de cmdlets y ejemplos de los mismos, consulte [Azure Batch Cmdlets](/powershell/module/azurerm.batch/#batch)(Cmdlets de Lote de Azure).
* Para más información sobre las aplicaciones y los paquetes de aplicación de Batch, consulte [Implementación de aplicaciones en nodos de proceso con paquetes de aplicaciones de Batch](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/