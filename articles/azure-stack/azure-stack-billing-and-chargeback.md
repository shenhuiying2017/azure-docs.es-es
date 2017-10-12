---
title: "Facturación y contracargo del cliente en Azure Stack | Microsoft Docs"
description: "Averigüe cómo recuperar la información de la utilización de recursos de Azure Stack."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Utilización y facturación en Azure Stack

La utilización representa la cantidad de recursos consumidos por un usuario. Azure Stack recopila información de la utilización de cada usuario y la usa para facturársela. Este artículo describe cómo se factura a los usuarios de Azure Stack la utilización de recursos y cómo se obtiene acceso a la información de facturación para el análisis, el contracargo, etc.

Azure Stack contiene la infraestructura necesaria para recopilar y agregar los datos de utilización de todos los recursos, así como para enviar estos datos a Azure Commerce. Se puede tener acceso a estos datos y exportarlos a un sistema de facturación con un adaptador de facturación, o bien exportarlos a una herramienta de inteligencia empresarial como Microsoft Power BI. Una vez exportada, esta información de facturación se usa para el análisis o se transfiere a un sistema de contracargo.

![Modelo conceptual de un adaptador de facturación que conecta Azure Stack a una aplicación de facturación](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>Canalización de uso

Todos los proveedores de recursos de Azure Stack emiten datos de uso en función de la utilización de los recursos. El servicio de uso agrega periódicamente (cada hora o cada día) estos datos de uso y los almacena en la base de datos de uso. Tanto los operadores de Azure Stack como los usuarios pueden acceder localmente a los datos de uso almacenados mediante el uso de las API de uso. 

Si ha [registrado una instancia de Azure Stack en Azure](azure-stack-register.md), Usage Bridge se configura para enviar los datos de uso a Azure Commerce. Una vez que los datos estén disponibles en Azure, puede acceder a ellos a través del portal de facturación o mediante las API de Azure Usage. En el tema [Usage data reporting](azure-stack-usage-reporting.md) (Informe de datos de uso) obtendrá más información acerca de los datos de uso que se notifican a Azure. 

La siguiente imagen muestra los principales componentes de la canalización de uso:

![Canalización de uso](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>¿Qué información de utilización se puede encontrar y cómo?

Los proveedores de recursos de Azure Stack como, por ejemplo, Compute, Storage y Network, generan datos de utilización a intervalos de horas para cada suscripción. Los datos de uso contienen información acerca del recurso que se usa, como el nombre del recurso, la suscripción usada, la cantidad usada, etc. Para obtener información sobre los recursos de identificadores de medidores, consulte el artículo de [preguntas más frecuentes de API de utilización](azure-stack-usage-related-faq.md). 

Después de que se hayan recopilado los datos de utilización, se [notifican a Azure](azure-stack-usage-reporting.md) para que genere una factura, que se puede ver en el Portal de facturación de Azure. 

> [!NOTE]
> El informe de datos de uso no es necesario para Azure Stack Development Kit ni para los usuarios del sistema integrado de Azure Stack cuya licencia esté dentro del modelo de capacidad. Para más información acerca de las licencias de Azure Stack, consulte la hoja de datos de [Packaging and pricing](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) (Empaquetado y precios).

El Portal de facturación de Azure muestra los datos de utilización solo de los recursos facturables. Además de los recursos facturables, Azure Stack captura los datos de utilización de un conjunto más amplio de recursos, a los que se pueden obtener acceso en el entorno de Azure Stack a través de las API de REST o PowerShell. Los operadores de Azure Stack pueden recuperar los datos de uso de las suscripciones de todos los usuarios, mientras que un usuario solo puede obtener sus detalles de uso.

## <a name="retrieve-usage-information"></a>Recuperar información de utilización

Para generar los datos de uso, es preciso tener recursos en ejecución y que utilicen activamente el sistema, por ejemplo, una máquina virtual activa, una cuenta de almacenamiento que contiene algunos datos, etc. Si no está seguro de si tiene algún recurso que se ejecute en Azure Stack Marketplace, implemente una máquina virtual (VM) y compruebe la hoja de supervisión de la máquina virtual para asegurarse de que se está ejecutando. Use los siguientes cmdlets de PowerShell para ver los datos de utilización:

1. [Instale PowerShell para Azure Stack.](azure-stack-powershell-install.md)
2. [Configure el entorno de PowerShell del usuario de Azure Stack](user/azure-stack-powershell-configure-user.md) o del [operador de Azure Stack](azure-stack-powershell-configure-admin.md) 

3. Para recuperar los datos de utilización, use el cmdlet [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) de PowerShell:

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>Pasos siguientes

[Report Azure Stack usage data to Azure](azure-stack-usage-reporting.md) (Notificar los datos de utilización de Azure Stack a Azure)

[Provider Resource Usage API](azure-stack-provider-resource-api.md) (API de utilización de recursos de proveedor)

[Tenant Resource Usage API](azure-stack-tenant-resource-usage-api.md) (API de utilización de recursos de inquilino)

[Usage-related FAQ](azure-stack-usage-related-faq.md) (P+F relacionadas con la utilización)

