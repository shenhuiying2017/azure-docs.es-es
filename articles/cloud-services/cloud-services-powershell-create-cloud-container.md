---
title: "Creación de un contenedor de servicios en la nube con PowerShell | Microsoft Docs"
description: "En este artículo se explica cómo crear un contenedor de servicios en la nube con PowerShell. El contenedor hospeda roles web y roles de trabajo."
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: 2023fa7b318f9f76ce1e1ea0a46110297be9a001
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Uso de un comando de Azure PowerShell para crear un contenedor vacío de servicios en la nube
En este artículo se explica cómo crear rápidamente un contenedor de servicios en la nube mediante cmdlets de Azure PowerShell. Siga estos pasos:

1. Instale el cmdlet de Microsoft Azure PowerShell desde la página de [descargas de Azure PowerShell](http://aka.ms/webpi-azps) .
2. Abra un símbolo del sistema de PowerShell.
3. Use [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) para iniciar sesión.

   > [!NOTE]
   > Para más instrucciones acerca de cómo instalar el cmdlet de Azure PowerShell y conectarse a la suscripción de Azure, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Use el cmdlet **New-AzureService** para crear un contenedor de servicios en la nube de Azure vacío.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Para invocar el cmdlet, siga este ejemplo:

   ```
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Para obtener más información sobre cómo crear el servicio en la nube de Azure, ejecute:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Pasos siguientes
* Para administrar la implementación de servicios en la nube, consulte los comandos [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) y [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Para más información, también puede consultar [Configuración de servicios en la nube](cloud-services-how-to-configure.md) .
* Para publicar el proyecto de servicio en la nube en Azure, consulte el código de ejemplo de **PublishCloudService.ps1** de [Entrega continua para Servicios en la nube de Azure](cloud-services-dotnet-continuous-delivery.md).
