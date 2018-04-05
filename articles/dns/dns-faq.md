---
title: Preguntas más frecuentes sobre DNS de Azure | Microsoft Docs
description: Preguntas más frecuentes sobre Azure DNS
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: f07f914ccf8ea6df216e3f571e38d7628b2d7fb6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="azure-dns-faq"></a>Preguntas más frecuentes sobre Azure DNS

## <a name="about-azure-dns"></a>Más información sobre Azure DNS

### <a name="what-is-azure-dns"></a>¿Qué es Azure DNS?

El sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP. DNS de Azure es un servicio de hospedaje para los dominios DNS, que permite resolver nombres mediante la infraestructura de Microsoft Azure. Al hospedar dominios en Azure, puede administrar los registros DNS con las mismas credenciales, API, herramientas y facturación que con los demás servicios de Azure.

Los dominios DNS de Azure DNS se hospedan en la red global de servidores de nombres DNS de Azure. Este utiliza redes de difusión por proximidad, con el fin de que cada consulta de DNS la responda el servidor DNS disponible más cercano disponible. Azure DNS ofrece no solo un rendimiento rápido, sino también una alta disponibilidad para su dominio.

El servicio DNS de Azure se basa en Azure Resource Manager. Como tal, se beneficia de características Resource Manager, como control de acceso basado en roles, registros de auditoría y bloqueo de recursos. Los dominios y registros pueden administrarse mediante Azure Portal, cmdlets de Azure PowerShell y la CLI de Azure multiplataforma. Las aplicaciones que requieren la administración automática de DNS pueden integrarse con el servicio a través de los SDK y API de REST.

### <a name="how-much-does-azure-dns-cost"></a>¿Cuánto cuesta DNS de Azure?

El modelo de facturación de DNS de Azure se basa en el número de zonas DNS hospedadas en DNS de Azure y el número de consultas de DNS recibidas. Los descuentos se proporcionan en función del uso.

Para más información, vea la [página de precios de DNS de Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>¿Qué es el Acuerdo de Nivel de Servicio de DNS de Azure?

Azure garantiza que las solicitudes de DNS válidas recibirán respuesta de al menos un servidor de nombres de Azure DNS como mínimo un 99,99 % del tiempo.

Para más información, vea la [página del SLA de DNS de Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>¿Qué es una "zona DNS"? ¿Es lo mismo que un dominio DNS? 

Un dominio es un nombre exclusivo dentro del sistema de nombres de dominio, como por ejemplo “contoso.com”.

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Por ejemplo, puede que el dominio "contoso.com" contenga varios registros DNS, como "mail.contoso.com" (para un servidor de correo) y "www.contoso.com" (para un sitio web). Estos registros se hospedarían en la zona DNS "contoso.com".

Un nombre de dominio es *solo un nombre*, donde una zona DNS es un recurso de datos que contiene los registros DNS de un nombre de dominio. DNS de Azure le permite hospedar una zona DNS y administrar los registros DNS de un dominio en Azure. También proporciona servidores de nombres DNS para responder a consultas de DNS de Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>¿Tengo que adquirir un nombre de dominio DNS para usar DNS de Azure? 

No necesariamente.

No hay que adquirir un dominio para hospedar una zona DNS en DNS de Azure. Se puede crear una zona DNS en cualquier momento sin necesidad de poseer el nombre del dominio. Las consultas de DNS de esta zona solo se resolverán si se dirigen a los servidores de nombres de DNS de Azure asignados a la zona.

Si desea vincular la zona DNS a la jerarquía DNS global, debe adquirir el nombre de dominio. Dicha vinculación permite que las consultas de DNS de cualquier parte del mundo encuentren su zona DNS y que se respondan con sus registros DNS.

## <a name="azure-dns-features"></a>Características de DNS de Azure

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>¿DNS de Azure admite la conmutación por error de puntos de conexión o el enrutamiento basados en DNS?

Azure Traffic Manager proporciona las características de conmutación por error de puntos de conexión y el enrutamiento de tráfico basados en DNS. Azure Traffic Manager es un servicio de Azure independiente que puede utilizarse junto con Azure DNS. Para más información, vea la [Introducción a Traffic Manager](../traffic-manager/traffic-manager-overview.md).

DNS de Azure solo admite el hospedaje de dominios DNS "estáticos", donde cada consulta de DNS de un registro DNS determinado siempre recibe la misma respuesta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>¿DNS de Azure admite el registro de nombres de dominio?

Nº DNS de Azure actualmente no admite la adquisición de nombres de dominio. Si desea adquirir dominios, debe usar un registrador de nombres de dominio de un tercero. El registrador suele cobrar una tarifa anual reducida. Los dominios se pueden hospedar en DNS de Azure para la administración de registros de DNS. Consulte [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md) para más información.

Compra de dominio es una característica de la que se realiza seguimiento en el trabajo pendiente de Azure. Puede usar el sitio de comentarios para [registrar su compatibilidad con esta característica](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>¿DNS de Azure admite DNSSEC?

Nº DNS de Azure no es compatible actualmente con DNSSEC.

DNSSEC es una característica de la que se realiza seguimiento en el trabajo pendiente de Azure DNS. Puede usar el sitio de comentarios para [registrar su compatibilidad con esta característica](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>¿DNS de Azure admite las transferencias de zona (AXFR/IXFR)?

Nº DNS de Azure no es compatible actualmente con las transferencias de zona. Las zonas DNS se pueden [importar en DNS de Azure mediante la CLI de Azure](dns-import-export.md). Los registros DNS pueden administrarse después a través del [Portal de administración de DNS de Azure](dns-operations-recordsets-portal.md), la [API de REST](https://docs.microsoft.com/powershell/module/azurerm.dns), el [SDK](dns-sdk.md), los [cmdlets de PowerShell](dns-operations-recordsets.md) o la [herramienta CLI](dns-operations-recordsets-cli.md).

Transferencia de zona es una característica de la que se realiza seguimiento en el trabajo pendiente de Azure DNS. Puede usar el sitio de comentarios para [registrar su compatibilidad con esta característica](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>¿DNS de Azure admite los redireccionamientos de dirección URL?

Nº Los servicios de redireccionamiento de dirección URL realmente no son un servicio DNS; funcionan a nivel de HTTP, en lugar de a nivel de DNS. Algunos proveedores DNS incluyen un servicio de redireccionamiento de dirección URL como parte de su oferta general. Esto solo se admite actualmente en DNS de Azure.

Redireccionamiento de URL es una característica de la que se realiza seguimiento en el trabajo pendiente de Azure DNS. Puede usar el sitio de comentarios para [registrar su compatibilidad con esta característica](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Uso de DNS de Azure

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>¿Puedo hospedar de forma conjunta un dominio con DNS de Azure y otro proveedor DNS?

Sí. DNS de Azure es compatible con dominios de hospedaje conjunto con otros servicios DNS.

Para ello, debe modificar los registros NS del dominio (que controlan qué proveedores reciben consultas de DNS del dominio) para apuntar a los servidores de nombres de ambos proveedores. Dicha modificación se puede realizar en tres lugares: en Azure DNS, en el otro proveedor y en la zona principal (suele configurarse mediante el registrador de nombres de dominio). Para más información sobre la delegación DNS, vea [Delegación de dominios DNS](dns-domain-delegation.md).

Además, debe garantizar que los registros DNS del dominio están sincronizados entre ambos servidores DNS. DNS de Azure no es compatible actualmente con las transferencias de zonas DNS. Los registros DNS se deben sincronizar mediante el [Portal de administración de Azure DNS](dns-operations-recordsets-portal.md), [la API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), el [SDK](dns-sdk.md), los [cmdlets de PowerShell](dns-operations-recordsets.md) o la [herramienta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>¿Tengo que delegar mi dominio en los cuatro servidores de nombres de Azure DNS?

Sí. Azure DNS asigna cuatro servidores de nombres a cada zona DNS para aislar los errores y aumentar la resistencia. Para poder optar al Acuerdo de Nivel de Servicio de Azure DNS, es preciso delegar el dominio en los cuatro servidores de nombres.

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

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>¿Se resuelven los servidores de nombres de Azure DNS en IPv6? 

Sí. Servidores de nombres de Azure DNS son de doble pila (tienen direcciones IPv4 e IPv6). Para encontrar la dirección IPv6 para los servidores de nombres de Azure DNS asignados a la zona DNS, puede usar una herramienta como nslookup (por ejemplo, `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>¿Cómo puedo configurar un nombre de dominio internacional (IDN) en Azure DNS?

Los nombres de dominio internacionales (IDN) funcionan mediante la codificación de cada nombre DNS con "[punycode](https://en.wikipedia.org/wiki/Punycode)". Las consultas de DNS se realizan con estos nombres codificados mediante punycode.

Puede configurar los nombres de dominio internacionales (IDN) en Azure DNS convirtiendo primero el nombre de la zona o el nombre del conjunto de registros a punycode. Azure DNS no admite actualmente la conversión integrada hacia o desde punycode.

## <a name="private-dns"></a>DNS privado

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>¿Azure DNS admite dominios "privados"?
La compatibilidad con dominios "privados" se implementa mediante la característica de zonas privadas.  Esta característica está actualmente en versión preliminar pública.  Las zonas privadas se administran con las mismas herramientas que las zonas de Azure DNS con conexión a Internet, pero solo se pueden resolver desde dentro de las redes virtuales especificadas.  Para más información, consulte la [introducción](private-dns-overview.md).

En este momento no se admiten zonas privadas en Azure Portal. 

Para obtener información acerca de otras opciones de DNS internas de Azure, consulte [Resolución de nombres para las máquinas virtuales e instancias de rol](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>¿En qué se diferencian una red virtual de registro y una red virtual de resolución en el contexto de las zonas privadas? 
Las redes virtuales se pueden vincular a una zona DNS privada de dos maneras: como red virtual de registro o como red virtual de resolución. En cualquier caso, las máquinas virtuales de la red virtual podrán resolverse correctamente con registros de la zona privada. Sin embargo, si especifica una red virtual como red virtual de registro, Azure registrará automáticamente (registro dinámico) registros DNS en la zona para las máquinas virtuales de la red virtual. Además, cuando se elimina una máquina virtual de una red virtual de registro, Azure también quita automáticamente el registro DNS correspondiente de la zona privada vinculada. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>¿Funcionará Azure DNS Private Zones entre regiones de Azure?
Sí. Las zonas privadas se admiten para la resolución de DNS entre redes virtuales en regiones de Azure, incluso sin emparejar explícitamente las redes virtuales, siempre que las redes virtuales se especifican como redes virtuales de resolución en la zona privada. Los clientes pueden necesitar que las redes virtuales se emparejen para que el tráfico TCP/HTTP fluya de una región a otra. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Para las zonas privadas, ¿se requiere que las redes virtuales tengan conectividad con Internet?
Nº Las zonas privadas funcionan en conjunción con las redes virtuales y permiten a los clientes administrar dominios de máquinas virtuales u otros recursos tanto en redes virtuales como entre ellas. Para la resolución de nombres no se requiere conectividad con Internet. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>¿Se puede usar la misma zona privada para varias redes virtuales para la resolución? 
Sí. Los clientes pueden asociar hasta 10 redes virtuales de resolución con una única zona privada.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>¿Se puede agregar una red virtual que pertenece a otra suscripción como red virtual de resolución a una zona privada? 
Sí, siempre que el usuario tenga permiso de escritura tanto en las redes virtuales como en la zona de DNS privado. Tenga en cuenta que el permiso de escritura se puede asignar a varios roles de RBAC. Por ejemplo, el rol de RBAC Colaborador de la red virtual clásica tiene permisos de escritura en las redes virtuales. Para más información acerca de los roles de RBAC, consulte [Introducción al control de acceso basado en roles en Azure Portal](../active-directory/role-based-access-control-what-is.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>¿Se eliminarán automáticamente los registros de DNS de una red virtual registrada automáticamente cuando el cliente elimine las máquinas virtuales?
Sí. Si elimina una máquina virtual en una red virtual de registro, eliminaremos automáticamente los registros de DNS de la zona, ya que esta es una red virtual de registro. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>¿Se puede eliminar manualmente un registro de máquina virtual realizado automáticamente en una zona privada (de una red virtual de registro)? 
Nº Actualmente, los clientes no pueden ver ni editar los registros de DNS de una máquina virtual que se registran automáticamente en una zona privada de una red virtual de registro. No obstante, en la zona dichos registros de DNS registrados automáticamente se pueden reemplazar por un registro de DNS creado manualmente. Consulte la siguiente pregunta, ya que en ella se trata este tema.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>¿Qué ocurre cuando se intenta crear manualmente un nuevo registro de DNS en una zona privada que tiene el mismo nombre de host que una máquina virtual existente (registrada automáticamente) en una red virtual de registro? 
Si intenta crear manualmente un nuevo registro de DNS en una zona privada que tenga el mismo nombre de host que una máquina virtual (registrada automáticamente) existente de una red virtual de registro, se permitirá que el nuevo registro de DNS sobrescriba el registro de máquina virtual registrado automáticamente. Además, si posteriormente intenta eliminar de la zona este registro de DNS creado manualmente, la operación se realizará correctamente y el registro automático volverá a producirse (el registro se DNS se volverá a crear automáticamente en la zona) siempre que la máquina virtual aún exista y tenga una dirección IP privada asociada. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>¿Qué ocurre si el vínculo de una red virtual de registro se anula de una zona privada? ¿Se quitaran de la zona también los registros de máquina virtual registrados automáticamente de la red virtual?
Sí. Si anula la vinculación de una red virtual de registro (actualiza la zona DNS para quitar de la red virtual asociada de registro asociada) de una zona privada, Azure quitará los registros de todas las máquinas virtuales registradas automáticamente de la zona. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>¿Qué pasa cuando se elimina una red virtual de registro (o de resolución) vinculada a una zona privada? ¿Tenemos que actualizar manualmente la zona privada para anular el vínculo de la red virtual de registro (o resolución) de la zona?
Sí. Cuando se elimina una red virtual de registro (o de resolución) sin haber anulado antes su vinculación de la zona privada, Azure permitirán realizar la operación de eliminación correctamente, pero no se anula la vinculación automáticamente entre la red virtual y la zona privada, en caso de haberla. Es preciso anular la vinculación manualmente de la zona privada. Por esta razón, se aconseja anular la vinculación entre la red virtual y su zona privada antes de eliminarla.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>¿Funcionaría la resolución de DNS con el valor de FQDN predeterminado (internal.cloudapp.net) aunque una zona privada (por ejemplo: contoso.local) esté vinculada a una red virtual? 
Sí. La característica de zonas privados no sustituye a las resoluciones DNS predeterminadas que usan la zona internal.cloudapp.net viene con Azure, y se ofrece como una funcionalidad o mejora adicional. En ambos casos (si se usa la zona internal.cloudapp.net que viene con Azure o la zona privada propia) se recomienda usar el FQDN de la zona con la que se desea realizar la resolución. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>¿Se cambiaría el sufijo DNS en las máquinas virtuales de una red virtual vinculada por el de la zona privada? 
Nº Actualmente, el sufijo DNS de las máquinas virtuales en la red virtual vinculada permanecerá como sufijo predeterminado proporcionado por Azure ("*. internal.cloudapp.net"). Sin embargo, en las máquinas virtuales dicho sufijo DNS se puede cambiar manualmente por el de la zona privada. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>¿Existen limitaciones para las zonas de privada durante esta versión preliminar?
Sí. Durante la versión preliminar pública, existen las siguientes limitaciones:
* Una red virtual de registro por zona privada.
* Hasta diez redes virtuales de resolución por zona privada.
* Una red virtual dada solo se puede vincular a una zona privada como red virtual de registro.
* Una red virtual determinada puede vincularse con un máximo de diez zonas privada como red virtual de resolución.
* Si se especifica una red virtual de registro, los registros DNS de las máquinas virtuales de dicha red que se registren en la zona privada no se podrán ver o recuperar desde Powershell, la CLI o las API, pero los registros de máquina virtual se registrarán y resolverán correctamente.
* El DNS inverso solo funciona en el espacio de direcciones IP privadas de la red virtual de registro.
* El DNS inverso de una dirección IP privada que no esté registrada en la zona privada (por ejemplo, la IP privada de una máquina virtual de una red virtual vinculada como red virtual de resolución a una zona privada) devolverá "internal.cloudapp.net" como sufijo de DNS; sin embargo, este sufijo no se podrá resolver.   
* La red virtual debe estar vacía (es decir, no hay máquinas virtuales con una NIC conectada) cuando se realiza la vinculación (es decir, la primera vez) con una zona privada como red virtual de registro o de resolución. Sin embargo, la red virtual podría no estar vacía para una futura vinculación como red virtual de registro o resolución con otras zonas privadas. 
* Actualmente, no se admite el reenvío condicional, por ejemplo, para habilitar la resolución entre redes de Azure y locales. Puede encontrar documentación acerca de la forma en que los clientes pueden poner en funcionamiento este escenario con otros mecanismos en [Resolución de nombres para las máquinas virtuales e instancias de rol](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>¿Existen cuotas o límites de zonas o registros en las zonas privadas?
No hay límites en cuanto al número de zonas que se permiten por suscripción ni al número de conjuntos de registros por zona en las zonas privadas. Tanto las zonas públicas como las privadas cuentan para los límites globales de DNS tal como se documenta [aquí](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>¿Admite Azure Portal las zonas privadas?
Las zonas privadas que ya se hayan creado con mecanismos ajenos a Azure Portal (API/PowerShell/CLI/SDK) se verán en él, pero los clientes no podrán crear zonas privadas nuevas ni administrar asociaciones con redes virtuales. Además, en el caso de las redes virtuales asociadas como redes virtuales de registro, los registros de máquina virtual registrados automáticamente no se verán desde Azure Portal. 

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca de Azure DNS](dns-overview.md)
<br>
[Más información acerca de cómo usar Azure DNS para dominios privados](private-dns-overview.md)
<br>
[Más información acerca de zonas y registros DNS](dns-zones-records.md)
<br>
[Introducción a DNS de Azure](dns-getstarted-portal.md)

