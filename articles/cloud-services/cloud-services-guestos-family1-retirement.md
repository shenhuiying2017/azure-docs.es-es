---
title: Aviso de retirada de la familia 1 del SO invitado | Microsoft Docs
description: "Proporciona información acerca de cuándo se produjo la retirada de la familia 1 del SO invitado de Azure y cómo determinar si el usuario se ve afectado."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 3178a09dab1cb972a3460d54dc9908fb95cce68b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="guest-os-family-1-retirement-notice"></a>Aviso de retirada de la familia 1 del SO invitado
La retirada de la familia 1 del SO se anunció por primera vez el 1 de junio de 2013.

**2 de septiembre de 2014** La familia 1.x del sistema operativo invitado (SO invitado) de Azure, basada en el sistema operativo Windows Server 2008, se retiró oficialmente. Todos los intentos para implementar nuevos servicios o actualizar los ya existentes mediante la familia 1 generarán un mensaje de error que informa de que la familia 1 del SO invitado se ha retirado.

**3 de noviembre de 2014** El soporte extendido para la familia 1 del SO invitado finaliza y se retira completamente. Todos los servicios todavía en la familia 1 se verá afectados. Podemos detener dichos servicios en cualquier momento. No existen garantías de que los servicios continúen ejecutándose a menos que los actualice manualmente.

Si tiene más preguntas, visite los [foros de Cloud Services](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) o [póngase en contacto con el soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Cómo saber si se ve afectado
Si se observa cualquiera de las situaciones siguientes,sus servicios en la nube se ven afectados:

1. Se especifica de manera explícita el valor "osFamily = "1" en el archivo ServiceConfiguration.cscfg del servicio en la nube.
2. No se especifica ningún valor explícitamente para osFamily en el archivo ServiceConfiguration.cscfg del servicio en la nube. Actualmente, el sistema usa el valor predeterminado de "1" en este caso.
3. Azure Portal muestra el valor de la familia del sistema operativo invitado como "Windows Server 2008".

Para determinar la familia de SO que ejecuta cada servicio en la nube, puede ejecutar el siguiente script en Azure PowerShell, aunque antes debe [configurar Azure PowerShell](/powershell/azureps-cmdlets-docs). Para más información acerca del script, consulte [Azure Guest OS Family 1 End of Life: June 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx) (Final de la vida de la familia 1 del SO invitado de Azure: junio de 2014).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Los servicios en la nube se verán afectados por la retirada de la familia 1 del SO si la columna osFamily del resultado del script está vacía o contiene un "1".

## <a name="recommendations-if-you-are-affected"></a>Recomendaciones en caso de verse afectado
Se recomienda migrar los roles de los servicios en la nube a una de las familias del SO invitado compatibles:

**Familia 4.x del SO invitado** : Windows Server 2012 R2 *(recomendado)*

1. Asegúrese de que la aplicación use el SDK 2.1 o posterior con .NET Framework 4.0, 4.5 o 4.5.1.
2. Establezca el atributo osFamily en “4” en el archivo ServiceConfiguration.cscfg y vuelva a implementar el servicio en la nube.

**Familia 3.x del SO invitado** : Windows Server 2012

1. Asegúrese de que la aplicación use el SDK 1.8 o posterior con .NET Framework 4.0 o 4.5.
2. Establezca el atributo osFamily en “3” en el archivo ServiceConfiguration.cscfg y vuelva a implementar el servicio en la nube.

**Familia 2.x del SO invitado** : Windows Server 2008 R2

1. Asegúrese de que la aplicación use el SDK 1.3 o posterior con .NET Framework 3.5 o 4.0.
2. Establezca el atributo osFamily en "2" en el archivo ServiceConfiguration.cscfg y vuelva a implementar el servicio en la nube.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>El soporte extendido para la familia 1 del SO invitado finalizó el 3 de noviembre de 2014.
Los servicios en la nube de la familia 1 del SO invitado ya no son compatibles. Migre la familia 1 lo antes posible para evitar la interrupción del servicio.  

## <a name="next-steps"></a>Pasos siguientes
Revise las [versiones del SO invitado](cloud-services-guestos-update-matrix.md)más recientes.
