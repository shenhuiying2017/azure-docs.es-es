---
title: "Actualización de Media Services después de revertir las claves de acceso de almacenamiento | Microsoft Docs"
description: "En este artículo se proporcionan instrucciones sobre cómo actualizar Servicios multimedia tras rotar las claves de acceso de almacenamiento."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: 304e72e0d2d4a7e95df513e6d5481def9eae3f68
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Actualización de Media Services después de revertir las claves de acceso de almacenamiento

Cuando crea una nueva cuenta de Azure Media Services (AMS), también se le pide que seleccione una cuenta de Azure Storage que se usa para almacenar el contenido multimedia. Puede agregar más de una cuenta de almacenamiento a su cuenta de Media Services. En este tema se explica cómo rotar las claves de almacenamiento. También se explica cómo agregar cuentas de almacenamiento a una cuenta multimedia. 

Para realizar las acciones descritas en este tema, debe usar las [API de ARM](https://docs.microsoft.com/rest/api/media/mediaservice) y [PowerShell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  Para más información, vea [How to manage Azure resources with PowerShell and Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md) (Administración de recursos de Azure con PowerShell y Resource Manager).

## <a name="overview"></a>Información general

Cuando se crea una nueva cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits, que se usan para autenticar el acceso a su cuenta de almacenamiento. Para aumentar la seguridad de sus conexiones de almacenamiento, se recomienda regenerar y rotar periódicamente su claves de acceso de almacenamiento. Se proporcionan dos claves de acceso (principal y secundaria) con el fin de permitirle mantener conexiones con la cuenta de almacenamiento mediante el uso de una clave de acceso mientras regenera la otra. Este procedimiento se conoce también como "rotación de claves de acceso".

Servicios multimedia depende de una clave de almacenamiento que se le ofrece. En concreto, los localizadores que se usan para transmitir o descargar sus activos dependen de la clave de acceso de almacenamiento. Cuando se crea una cuenta de AMS toma una dependencia en la clave de acceso de almacenamiento principal de forma predeterminada, pero como usuario puede actualizar la clave de almacenamiento que AMS tiene. Debe asegurarse de que Media Services conocen qué clave usar siguiendo los pasos descritos en este tema.  

>[!NOTE]
> Si tiene varias cuentas de almacenamiento, realizaría este procedimiento con cada una. El orden en que se rotan las claves de almacenamiento no es fijo. Puede rotar primero la clave secundaria y después la primaria, o viceversa.
>
> Antes de ejecutar los pasos que se describen en este tema en una cuenta de producción, asegúrese de probarlos en una cuenta de ensayo.
>

## <a name="steps-to-rotate-storage-keys"></a>Pasos para rotar claves de almacenamiento 
 
 1. Cambie la clave principal de la cuenta de almacenamiento mediante el cmdlet de PowerShell o en [Azure](https://portal.azure.com/) Portal.
 2. Llame al cmdlet Sync-AzureRmMediaServiceStorageKeys con los parámetros adecuados para forzar a la cuenta multimedia a seleccionar las claves de la cuenta de almacenamiento.
 
    En el ejemplo siguiente se muestra cómo sincronizar las claves para las cuentas de almacenamiento.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Espere una hora más o menos. Verifique que los escenarios de streaming estén funcionando.
 4. Cambie la clave secundaria de la cuenta de almacenamiento con el cmdlet de PowerShell o en Azure Portal.
 5. Llame al cmdlet Sync-AzureRmMediaServiceStorageKeys de PowerShell con los parámetros adecuados para forzar a la cuenta multimedia a seleccionar las nuevas claves de la cuenta de almacenamiento. 
 6. Espere una hora más o menos. Verifique que los escenarios de streaming estén funcionando.
 
### <a name="a-powershell-cmdlet-example"></a>Un ejemplos de cmdlet de PowerShell 

En el ejemplo siguiente se muestra cómo obtener la cuenta de almacenamiento y sincronizarla con la cuenta de AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Pasos para agregar cuentas de almacenamiento a la cuenta de AMS

En el tema siguiente se explica cómo agregar cuentas de almacenamiento a la cuenta de AMS: [Agregar varias cuentas de almacenamiento a una cuenta de Media Services](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimientos
Nos gustaría mencionar a las siguientes personas que han contribuido a crear este documento: Cenk Dingiloglu, Gada Milán y Seva Titov.
