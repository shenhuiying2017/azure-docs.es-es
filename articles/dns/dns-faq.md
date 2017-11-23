---
title: "Preguntas más frecuentes sobre DNS de Azure | Microsoft Docs"
description: "Preguntas más frecuentes sobre DNS de Azure"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: a8cff450730d05970398989ceb3e8585aefd6771
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2017
---
# <a name="azure-dns-faq"></a>Preguntas más frecuentes sobre DNS de Azure

## <a name="about-azure-dns"></a>Más información sobre DNS de Azure

### <a name="what-is-azure-dns"></a>¿Qué es Azure DNS?

El sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP. DNS de Azure es un servicio de hospedaje para los dominios DNS, que permite resolver nombres mediante la infraestructura de Microsoft Azure. Al hospedar dominios en Azure, puede administrar los registros DNS con las mismas credenciales, API, herramientas y facturación que con los demás servicios de Azure.

Los dominios DNS de DNS de Azure se hospedan en la red global de servidores de nombres DNS de Azure. Utilizamos redes de difusión por proximidad, por lo que cada consulta de DNS la responde el servidor DNS disponible más cercano. Esto ofrece un rendimiento rápido y alta disponibilidad para el dominio.

El servicio DNS de Azure se basa en Azure Resource Manager. Como tal, se beneficia de características Resource Manager, como control de acceso basado en roles, registros de auditoría y bloqueo de recursos. Los dominios y registros pueden administrarse mediante Azure Portal, cmdlets de Azure PowerShell y la CLI de Azure multiplataforma. Las aplicaciones que requieren la administración automática de DNS pueden integrarse con el servicio a través de los SDK y API de REST.

### <a name="how-much-does-azure-dns-cost"></a>¿Cuánto cuesta DNS de Azure?

El modelo de facturación de DNS de Azure se basa en el número de zonas DNS hospedadas en DNS de Azure y el número de consultas de DNS recibidas. Los descuentos se proporcionan en función del uso.

Para más información, vea la [página de precios de DNS de Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>¿Qué es el Acuerdo de Nivel de Servicio de DNS de Azure?

Garantizamos que las solicitudes de DNS recibirán una respuesta de al menos un servidor de nombres de DNS de Azure durante el 99,99 % del tiempo, como mínimo.

Para más información, vea la [página del SLA de DNS de Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>¿Qué es una "zona DNS"? ¿Es lo mismo que un dominio DNS? 

Un dominio es un nombre exclusivo dentro del sistema de nombres de dominio, como por ejemplo “contoso.com”.

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Por ejemplo, puede que el dominio "contoso.com" contenga varios registros DNS, como "mail.contoso.com" (para un servidor de correo) y "www.contoso.com" (para un sitio web). Se hospedarían en la zona DNS "contoso.com".

Un nombre de dominio es *solo un nombre*, donde una zona DNS es un recurso de datos que contiene los registros DNS de un nombre de dominio. DNS de Azure le permite hospedar una zona DNS y administrar los registros DNS de un dominio en Azure. También proporciona servidores de nombres DNS para responder a consultas de DNS de Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>¿Tengo que adquirir un nombre de dominio DNS para usar DNS de Azure? 

No necesariamente.

No hay que adquirir un dominio para hospedar una zona DNS en DNS de Azure. Se puede crear una zona DNS en cualquier momento sin necesidad de poseer el nombre del dominio. Las consultas de DNS de esta zona solo se resolverán si se dirigen a los servidores de nombres de DNS de Azure asignados a la zona.

Necesita adquirir el nombre del dominio si desea vincular la zona DNS a la jerarquía DNS global, lo que permite formular consultas de DNS desde cualquier parte del mundo para encontrar su zona DNS y recibir respuestas con los registros DNS.

## <a name="azure-dns-features"></a>Características de DNS de Azure

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>¿DNS de Azure admite la conmutación por error de puntos de conexión o el enrutamiento basados en DNS?

Azure Traffic Manager proporciona las características de conmutación por error de puntos de conexión y el enrutamiento de tráfico basados en DNS. Se trata de un servicio de Azure independiente que puede utilizarse junto con el DNS de Azure. Para más información, vea la [Introducción a Traffic Manager](../traffic-manager/traffic-manager-overview.md).

DNS de Azure solo admite el hospedaje de dominios DNS "estáticos", donde cada consulta de DNS de un registro DNS determinado siempre recibe la misma respuesta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>¿DNS de Azure admite el registro de nombres de dominio?

No. DNS de Azure actualmente no admite la adquisición de nombres de dominio. Si desea adquirir dominios, debe usar un registrador de nombres de dominio de un tercero. El registrador suele cobrar una tarifa anual reducida. Los dominios se pueden hospedar en DNS de Azure para la administración de registros de DNS. Consulte [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md) para más información.

Se trata de una característica sujeta a seguimiento en el trabajo pendiente. Puede usar el sitio de comentarios para [registrar la compatibilidad para esta característica](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-private-domains"></a>¿DNS de Azure admite dominios "privados"?
La compatibilidad con dominios "privados" se implementa mediante zonas DNS privadas.  Esta característica está disponible actualmente en forma de versión preliminar.  Las zonas de DNS privado se administran con las mismas herramientas que las zonas de Azure DNS con conexión a Internet, pero solo se pueden resolver desde dentro de las redes virtuales especificadas.  Para más información, consulte la [introducción](private-dns-overview.md).

Para obtener información acerca de otras opciones de DNS internas de Azure, consulte [Resolución de nombres para las máquinas virtuales e instancias de rol](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="does-azure-dns-support-dnssec"></a>¿DNS de Azure admite DNSSEC?

No. DNS de Azure no es compatible actualmente con DNSSEC.

Se trata de una característica sujeta a seguimiento en el trabajo pendiente. Puede usar el sitio de comentarios para [registrar la compatibilidad para esta característica](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>¿DNS de Azure admite las transferencias de zona (AXFR/IXFR)?

No. DNS de Azure no es compatible actualmente con las transferencias de zona. Las zonas DNS se pueden [importar en DNS de Azure mediante la CLI de Azure](dns-import-export.md). Los registros DNS pueden administrarse después a través del [Portal de administración de DNS de Azure](dns-operations-recordsets-portal.md), la [API de REST](https://docs.microsoft.com/powershell/module/azurerm.dns), el [SDK](dns-sdk.md), los [cmdlets de PowerShell](dns-operations-recordsets.md) o la [herramienta CLI](dns-operations-recordsets-cli.md).

Se trata de una característica sujeta a seguimiento en el trabajo pendiente. Puede usar el sitio de comentarios para [registrar la compatibilidad para esta característica](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>¿DNS de Azure admite los redireccionamientos de dirección URL?

No. Los servicios de redireccionamiento de dirección URL realmente no son un servicio DNS; funcionan a nivel de HTTP, en lugar de a nivel de DNS. Algunos proveedores DNS incluyen un servicio de redireccionamiento de dirección URL como parte de su oferta general. Esto solo se admite actualmente en DNS de Azure.

Esta característica está sujeta a un seguimiento en el trabajo pendiente. Puede usar el sitio de comentarios para [registrar la compatibilidad para esta característica](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Uso de DNS de Azure

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>¿Puedo hospedar de forma conjunta un dominio con DNS de Azure y otro proveedor DNS?

Sí. DNS de Azure es compatible con dominios de hospedaje conjunto con otros servicios DNS.

Para ello, debe modificar los registros NS del dominio (que controlan qué proveedores reciben consultas de DNS del dominio) para apuntar a los servidores de nombres de ambos proveedores. Estos registros NS deben modificarse en tres lugares: en DNS de Azure, en el otro proveedor y en la zona principal (suele configurarse a través del registrador de nombres de dominio). Para más información sobre la delegación DNS, vea [Delegación de dominios DNS](dns-domain-delegation.md).

Además, debe garantizar que los registros DNS del dominio están sincronizados entre ambos servidores DNS. DNS de Azure no es compatible actualmente con las transferencias de zonas DNS. Debe sincronizar los registros DNS mediante el [Portal de administración de DNS de Azure](dns-operations-recordsets-portal.md), la [API de REST](https://docs.microsoft.com/powershell/module/azurerm.dns), el [SDK](dns-sdk.md), los [cmdlets de PowerShell](dns-operations-recordsets.md) o la [herramienta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>¿Tengo que delegar mi dominio en los cuatro servidores de nombres de Azure DNS?

Sí. Azure DNS asigna cuatro servidores de nombres a cada zona DNS, a fin de poder aislar los errores y aumentar la resistencia. Para poder beneficiarse del SLA de Azure DNS, necesita delegar el dominio en los cuatro servidores de nombres.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>¿Cuáles son los límites de uso de Azure DNS?

Se aplican los límites predeterminados siguientes cuando se usa DNS de Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>¿Puedo mover una zona de Azure DNS entre los grupos de recursos o entre las suscripciones?

Sí. Las zonas DNS se pueden mover entre grupos de recursos o entre suscripciones.

No hay ningún impacto en las consultas de DNS al mover una zona DNS. Los servidores de nombres asignados a la zona siguen siendo los mismos, y las consultas de DNS se procesan de la forma habitual.

Para obtener más información e instrucciones sobre cómo mover zonas DNS, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>¿Cuánto tardan en surtir efecto los cambios de DNS?

Las zonas DNS y los registros DNS nuevos suelen reflejarse en los servidores de nombres de Azure DNS con rapidez, en cuestión de segundos.

Los cambios en los registros DNS existentes pueden tardar un poco más, pero deberían reflejarse en los servidores de nombres de Azure DNS en un período de 60 segundos. En este caso, el almacenamiento en caché DNS de Azure DNS (por parte de clientes DNS y resoluciones recursivas DNS) también puede influir en el tiempo que un cambio de DNS tarda en ser efectivo. Esta duración de memoria caché puede controlarse con la propiedad de período de vida (TTL) de cada conjunto de registros.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>¿Cómo puedo proteger mis zonas DNS contra una eliminación accidental?

Azure DNS se administra con Azure Resource Manager y con las ventajas de las características de Access Control que Azure Resource Manager ofrece. El control de acceso basado en rol se puede usar para controlar qué usuarios tienen acceso de lectura o escritura a conjuntos de registros y zonas DNS. Se pueden aplicar bloqueos de recursos para prevenir la modificación o eliminación accidental de conjuntos de registros y zonas DNS.

Para más información, vea [Protección de zonas y registros DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>¿Cómo puedo configurar registros de SPF en Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6-"></a>¿Resuelven los servidores de nombres de Azure DNS a través de IPv6? 

Sí. Servidores de nombres de Azure DNS son de doble pila (tienen direcciones IPv4 e IPv6). Para encontrar la dirección IPv6 para los servidores de nombres de Azure DNS asignados a la zona DNS, puede usar una herramienta como nslookup (por ejemplo, `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>¿Cómo puedo configurar un nombre de dominio internacional (IDN) en Azure DNS?

Los nombres de dominio internacionales (IDN) funcionan mediante la codificación de cada nombre DNS con "[punycode](https://en.wikipedia.org/wiki/Punycode)". Las consultas de DNS se realizan con estos nombres codificados mediante punycode.

Puede configurar los nombres de dominio internacionales (IDN) en Azure DNS convirtiendo primero el nombre de la zona o el nombre del conjunto de registros a punycode. Azure DNS no admite actualmente la conversión integrada hacia o desde punycode.

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre Azure DNS](dns-overview.md)
<br>
[Más información acerca del uso de Azure DNS para dominios privados](private-dns-overview.md)
<br>
[Más información sobre zonas y registros DNS](dns-zones-records.md)
<br>
[Introducción a Azure DNS](dns-getstarted-portal.md)

