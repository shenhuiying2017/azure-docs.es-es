---
title: Usar Azure API Management con redes virtuales
description: "Obtenga información sobre cómo configurar una conexión a una red virtual en Azure API Management y acceder a servicios web con esta."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7dfbf0e460dc8de13a4a71c1f925c93f6238df5d


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Usar Azure API Management con redes virtuales
Las redes virtuales de Azure (VNET) le permiten colocar cualquier recurso de Azure en una red que se pueda enrutar distinta de Internet y a la que controla el acceso. Después, estas redes se pueden conectar a sus redes locales mediante diversas tecnologías de VPN. Para más información sobre las redes virtuales de Azure, vea: [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md).

Azure API Management se puede conectar a una red virtual (VNET) para que pueda acceder a servicios de back-end en la red y para que el portal para desarrolladores y la puerta de enlace de la API sean accesibles en la red.

## <a name="enable-vpn"> </a>Habilitar la conexión de VNET
> La conectividad VNET solo está disponible en los niveles **Premium** y **Desarrollador**. Para cambiar de nivel, abra el servicio API Management en Azure Portal y, después, abra la pestaña **Escala y precios**. En la sección **Plan de tarifa**, seleccione el nivel Premium y haga clic en Guardar.
> 
> 

Para habilitar la conectividad de VNET, abra el servicio API Management en Azure Portal y, después, abra la página **Red virtual**.

![Menú Red virtual de API Management][api-management-using-vnet-menu]

Seleccione el tipo de acceso que prefiera:

* **Externo:** la puerta de enlace de API Management y el portal para desarrolladores son accesibles públicamente desde Internet con un equilibrador de carga externo. La puerta de enlace puede acceder a recursos dentro de la red virtual.

![Emparejamiento público][api-management-vnet-public]

* **Interno:** la puerta de enlace de API Management y el portal para desarrolladores solo son accesibles desde la red virtual con un equilibrador de carga interno. La puerta de enlace puede acceder a recursos dentro de la red virtual.

![Emparejamiento privado][api-management-vnet-private]

Ahora verá una lista de todas las regiones donde se aprovisiona el servicio Administración de API. Seleccione una VNET y la subred de cada región. La lista se rellena con redes virtuales de ARM y clásicas disponibles en sus suscripciones de Azure que se configuran en la región seleccionada.

![Selección de una VPN][api-management-setup-vpn-select]

Haga clic en **Guardar** en la parte superior de la pantalla. 

> No podrá realizar operaciones de administración en el servicio API Management mientras se está actualizando. La puerta de enlace de API Management y el portal para desarrolladores seguirán estando disponibles.
> Tenga en cuenta que es probable que la dirección VIP de la instancia de API Management cambie cada vez que se habilite a deshabilite VNET.
> 
> 

## <a name="enable-vnet-powershell"> </a>Habilitar una conexión de VNET con commandlets de PowerShell
También puede habilitar la conectividad de VNET con el commandlet de PowerShell [Set-AzureRmApiManagementVirtualNetworks](https://msdn.microsoft.com/library/mt619277.aspx).

## <a name="connect-vnet"> </a>Conectar a un servicio web hospedado en una red virtual
Después de conectar el servicio API Management a la VNET, se accede a los servicios de back-end de la misma forma que a los servicios públicos. Solo tiene que escribir la dirección IP local o el nombre de host (si se ha configurado un servidor DNS para la VNET) del servicio web en el campo **Dirección URL de servicio web** al crear una API o editar una existente.

![Agregar una API desde VPN][api-management-setup-vpn-add-api]

## <a name="custom-dns"> </a>Configuración del servidor DNS personalizado
Administración de API depende de varios servicios de Azure. Cuando API Management está hospedado en un VNET con un servidor DNS personalizado, necesita poder resolver los nombres de host de esos servicios de Azure. Siga [estas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) instrucciones sobre la configuración de DNS personalizado. Vea los diagramas en la tabla de puertos siguientes como referencia.

## <a name="ports-required"> </a>Puertos necesarios para API Management
> Si alguno de estos puertos no está disponible, es posible que API Management no funcione correctamente y sea inaccesible. Tener bloqueados uno o varios de estos puertos es el problema más común de una configuración incorrecta cuando se usa Administración de API con una red virtual.
> 
> 

Cuando la instancia del servicio de Administración de API se hospeda en una red virtual, se usan los puertos de la tabla siguiente.

| Puertos | Dirección | Protocolo de transporte | Propósito | Origen/destino | Tipo de acceso |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Entrada |TCP |Comunicación de cliente con Administración de API |INTERNET/VIRTUAL_NETWORK |Externo |
| 3443 |Entrada |TCP |Administración de puntos de conexión |INTERNET/VIRTUAL_NETWORK |Externa e interna |
| 80, 443 |Salida |TCP |Dependencia en Azure Storage y Azure Service Bus |VIRTUAL_NETWORK/INTERNET |Externa e interna |
| 1433 |Salida |TCP |Dependencia de Azure SQL |VIRTUAL_NETWORK/INTERNET |Externa e interna |
| 9350, 9351, 9352, 9353, 9354 |Salida |TCP |Dependencia de Service Bus |VIRTUAL_NETWORK/INTERNET |Externa e interna |
| 5671 |Salida |AMQP |Dependencia de directiva de registro en Event Hubs |VIRTUAL_NETWORK/INTERNET |Externa e interna |
| 6381, 6382, 6383 |Entrante o saliente |UDP |Dependencia de Redis Cache |VIRTUAL_NETWORK/VIRTUAL_NETWORK |Externa e interna |
| 445 |Salida |TCP |Dependencia del recurso compartido de archivos de Azure para Git |VIRTUAL_NETWORK/INTERNET |Externa e interna |

## <a name="limitations"> </a>Limitaciones
* Una subred que contenga instancias de API Management no puede contener otros tipos de recursos de Azure.
* La subred y el servicio API Management tienen que estar en la misma suscripción.
* Una subred que contenga instancias de API Management no se puede mover a otras suscripciones.
* Al usar una red virtual interna, solo estará disponible una dirección IP interna del intervalo indicado en [RFC 1918](https://tools.ietf.org/html/rfc1918), no se puede especificar una dirección IP pública.
* Para implementaciones de API Management de varias regiones con redes virtuales internas configuradas, los usuarios son responsables de administrar su propio equilibrio de carga, ya que son los propietarios del host DNS.

## <a name="related-content"> </a>Contenido relacionado
* [Crear una red virtual con una conexión VPN de sitio a sitio con Azure Portal][Crear una red virtual con una conexión VPN de sitio a sitio con Azure Portal]
* [Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure][Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure]

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Habilitación de conexiones VPN]: #enable-vpn
[Conexión a un servicio web detrás de VPN]: #connect-vpn
[Contenido relacionado]: #related-content


[Crear una red virtual con una conexión VPN de sitio a sitio con Azure Portal]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure]: api-management-howto-api-inspector.md



<!--HONumber=Nov16_HO3-->


