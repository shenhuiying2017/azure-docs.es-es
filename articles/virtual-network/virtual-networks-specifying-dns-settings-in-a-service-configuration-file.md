---
title: "Especificación de la configuración DNS en un archivo de configuración de servicio | Microsoft Docs"
description: "Especificación de la configuración de DNS personalizada mediante un archivo de configuración de servicio para una red virtual"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
ms.openlocfilehash: 0fba2ea06827aff29a7a092933edb8120d668b29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Especificar la configuración DNS en un archivo de configuración de servicio
## <a name="dns-elements"></a>Elementos DNS
Un archivo de configuración de servicio puede contener un elemento DnsServers con una lista de direcciones IPv4 de los servidores de sistema de nombres de dominio (DNS) que el servicio usará. La configuración en el archivo de configuración de servicio sobrescribirá la del archivo de configuración de red. Para obtener más información, consulte [Esquema de configuración del servicio de Azure (archivo .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> El atributo **name** en el elemento **DnsServer** solo se usa como nombre de referencia. No representa el nombre de host del servidor DNS. Cada valor del atributo **DnsServer** debe ser único en toda la suscripción de Microsoft Azure.
> 
> 

## <a name="see-also"></a>Otras referencias
[Esquema de configuración del servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuración de red virtual de Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configuración de una red virtual mediante un archivo de configuración de red](http://go.microsoft.com/fwlink/?LinkId=248094)

[Información acerca de la configuración de red virtual en el Portal de administración](http://go.microsoft.com/fwlink/?LinkId=248092)

