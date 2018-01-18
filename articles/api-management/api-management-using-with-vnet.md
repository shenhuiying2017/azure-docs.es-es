---
title: Usar Azure API Management con redes virtuales
description: "Obtenga información sobre cómo configurar una conexión a una red virtual en Azure API Management y acceder a servicios web con esta."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 81634b366f5b66444d1e5474b4ab517208b50375
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Usar Azure API Management con redes virtuales
Azure Virtual Network (VNET) le permiten colocar cualquier recurso de Azure en una red que se pueda enrutar distinta de Internet y a la que controla el acceso. Después, estas redes se pueden conectar a sus redes locales mediante diversas tecnologías de VPN. Para más información sobre Azure Virtual Network, vea: [Información general sobre Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Azure API Management se puede implementar dentro de la red virtual (VNET), por lo que puede tener acceso a los servicios back-end dentro de la red. El portal para desarrolladores y la puerta de enlace de API pueden configurarse para que sea accesible desde Internet o solo dentro de la red virtual.

> [!NOTE]
> Azure API Management admite redes virtuales clásicas y de Azure Resource Manager.
>

## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos que se describen en este artículo, debe tener:

+ Una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Una instancia de APIM. Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ La conectividad de VNET está disponible únicamente en los niveles Premium y Desarrollador. Cambie a uno de estos niveles siguiendo las instrucciones del tema sobre [actualización y escalado](upgrade-and-scale.md#upgrade-and-scale).

## <a name="enable-vpn"></a>Habilitar la conexión de VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Habilitación de la conectividad de VNET mediante Azure Portal

1. Acceda a la instancia de APIM de [Azure Portal](https://portal.azure.com/).
2. Seleccione **Virtual Network**.
3. Configure la instancia de API Management que se va a implementar dentro de la red virtual.

    ![Menú Red virtual de API Management][api-management-using-vnet-menu]
4. Seleccione el tipo de acceso que prefiera:
    
    * **Externo:** la puerta de enlace de API Management y el portal para desarrolladores son accesibles públicamente desde Internet con un equilibrador de carga externo. La puerta de enlace puede acceder a recursos dentro de la red virtual.
    
    ![Emparejamiento público][api-management-vnet-public]
    
    * **Interno:** la puerta de enlace de API Management y el portal para desarrolladores solo son accesibles desde la red virtual con un equilibrador de carga interno. La puerta de enlace puede acceder a recursos dentro de la red virtual.
    
    ![Emparejamiento privado][api-management-vnet-private]`

    Ahora verá una lista de todas las regiones donde se aprovisiona el servicio Administración de API. Seleccione una VNET y la subred de cada región. La lista se rellena con redes virtuales de Resource Manager y clásicas disponibles en las suscripciones de Azure que se configuran en la región que va a configurar.
    
    > [!NOTE]
    > **Punto de conexión de servicio** en el diagrama anterior incluye la puerta de enlace o proxy, el portal para desarrolladores, el portal para editores, GIT y el punto de conexión de administración directa.
    > **Punto de conexión de administración** en el diagrama anterior es el punto de conexión hospedado en el servicio para administrar la configuración mediante Azure Portal y Powershell.
    > Además, tenga en cuenta que, aunque el diagrama muestra las direcciones IP para sus diversos puntos de conexión, el servicio API Management **solo** responde en sus nombres de host configurados.
    
    > [!IMPORTANT]
    > Al implementar una instancia de Azure API Management en una VNET de Resource Manager, el servicio debe estar en una subred dedicada que no contiene ningún otro recurso excepto instancias de Azure API Management. Si se intenta implementar una instancia de Azure API Management en una subred de VNET de Resource Manager que contiene otros recursos, se producirá un error en la implementación.
    >

    ![Selección de una VPN][api-management-setup-vpn-select]

5. Haga clic en **Guardar** en la parte superior de la pantalla.

> [!NOTE]
> Tenga en cuenta que la dirección VIP de la instancia de API Management cambiará cada vez que se habilita o deshabilita VNET.  
> La dirección VIP también cambia cuando se mueve API Management de **externo** a **interno** o viceversa.
>

> [!IMPORTANT]
> Si quita API Management de una red virtual o cambia aquella en la que se implementa, la red virtual usada anteriormente puede permanecer bloqueada hasta 4 horas. Durante este periodo no será posible eliminar la red virtual ni implementar un nuevo recurso en ella.

## <a name="enable-vnet-powershell"></a>Habilitar una conexión de VNET con cmdlets de PowerShell
También puede habilitar la conectividad de VNET con los cmdlets de PowerShell

* **Crear un servicio de API Management dentro de una red virtual**: use el cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) para crear un servicio de Azure API Management dentro de una VNET.

* **Implementar un servicio existente de API Management dentro de una VNET**: use el cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) para mover un servicio existente de Azure API Management dentro de una red virtual.

## <a name="connect-vnet"></a>Conectar a un servicio web hospedado en una red virtual
Después de conectar el servicio API Management a la VNET, se accede a los servicios de back-end de la misma forma que a los servicios públicos. Solo tiene que escribir la dirección IP local o el nombre de host (si se ha configurado un servidor DNS para la VNET) del servicio web en el campo **Dirección URL de servicio web** al crear una API o editar una existente.

![Agregar una API desde VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"></a>Problemas comunes de configuración de red
A continuación se muestra una lista de problemas de errores de configuración comunes que pueden producirse al implementar el servicio de API Management en una red virtual.

* **Configuración del servidor DNS personalizado**: el servicio de API Management depende de varios servicios de Azure. Cuando API Management está hospedado en una red virtual con un servidor DNS personalizado, necesita resolver los nombres de host de esos servicios de Azure. Siga [estas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) instrucciones sobre la configuración de DNS personalizado. Vea la siguiente tabla de puertos y otros requisitos de red a efectos de referencia.

> [!IMPORTANT]
> Se recomienda que, si usa un servidor de DNS personalizado para la red virtual, lo configure **antes** de implementar en él un servicio de API Management. En caso contrario, debe actualizar el servicio API Management cada vez que cambie los servidores DNS (s) mediante la ejecución de la [operación Aplicar configuración de red](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates).

* **Puertos necesarios para API Management**: el tráfico entrante y saliente en la subred en la que se implementa API Management puede controlarse mediante el [grupo de seguridad de red][Network Security Group]. Si alguno de estos puertos no está disponible, es posible que API Management no funcione correctamente y sea inaccesible. Tener bloqueados uno o varios de estos puertos es otro problema común de una configuración incorrecta cuando se usa API Management con una red virtual.

Cuando la instancia del servicio de API Management se hospeda en una red virtual, se usan los puertos de la tabla siguiente.

| Puertos de origen/destino | Dirección | Protocolo de transporte | Origen/destino | Propósito (*) | Tipo de red virtual |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Entrada |TCP |INTERNET/VIRTUAL_NETWORK|Comunicación de cliente con Administración de API|Externo |
| * / 3443 |Entrada |TCP |INTERNET/VIRTUAL_NETWORK|Punto de conexión de administración para Azure Portal y Powershell |Interno |
| * / 80, 443 |Salida |TCP |VIRTUAL_NETWORK/INTERNET|Dependencia de Azure Storage, Azure Service Bus y Azure Active Directory (donde corresponda).|Externa e interna | 
| * / 1433 |Salida |TCP |VIRTUAL_NETWORK/INTERNET|**Acceso a los puntos de conexión de Azure SQL** |Externa e interna |
| * / 5671, 5672 |Salida |TCP |VIRTUAL_NETWORK/INTERNET|Dependencia de la directiva de registro en el centro de eventos y el agente de supervisión |Externa e interna |
| * / 445 |Salida |TCP |VIRTUAL_NETWORK/INTERNET|Dependencia del recurso compartido de archivos de Azure para Git |Externa e interna |
| * / 25028 |Salida |TCP |VIRTUAL_NETWORK/INTERNET|Conexión a la retransmisión de SMTP para enviar correos electrónicos |Externa e interna |
| * / 6381 - 6383 |Entrada y salida |TCP |VIRTUAL_NETWORK/VIRTUAL_NETWORK|Acceso a instancias de caché de Redis entre RoleInstances |Externa e interna |
| * / * | Entrada |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Equilibrador de carga de la infraestructura de Azure |Externa e interna |

>[!IMPORTANT]
> * Los puertos para los que el *Propósito* está en **negrita** son necesarios para que el servicio API Management se implemente correctamente. Sin embargo, si se bloquean los otros puertos, se producirá la degradación de la capacidad de usar y supervisar el servicio en ejecución.

* **Funcionalidad SSL**: para permitir la creación y validación de la cadena de certificados SSL, el servicio API Management necesita conectividad de red saliente a ocsp.msocsp.com, mscrl.microsoft.com y crl.microsoft.com. Esta dependencia no es obligatoria, si los certificados que cargue en API Management contienen la cadena completa de la raíz de la entidad de certificación.

* **Acceso de DNS**: se requiere acceso saliente en el puerto 53 para establecer la comunicación con los servidores DNS. Si existe un servidor DNS personalizado en el otro punto de conexión de una puerta de enlace de VPN, el servidor DNS debe estar accesible desde la subred que alberga la API Management.

* **Supervisión de métricas y estado**: conexión de red saliente a puntos de conexión de supervisión de Azure, que se resuelven en los siguientes dominios: global.metrics.nsatc.net, shoebox2.metrics.nsatc.net, prod3.metrics.nsatc.net.

* **Configuración de ExpressRoute**: una configuración de cliente común es definir su propia ruta predeterminada (0.0.0.0/0) que fuerza a que el tráfico saliente de Internet fluya a nivel local. El flujo de tráfico interrumpe invariablemente la conectividad con Azure API Management porque el tráfico saliente está bloqueado de forma local o porque se usa NAT para convertirlo en un conjunto de direcciones irreconocibles que no funcionan con varios puntos de conexión de Azure. La solución es definir una, o varias, rutas definidas por el usuario ([UDR][UDRs]) en la subred que contiene el servicio Azure API Management. Una ruta definida por el usuario define las rutas de subred específica que se respetarán en lugar de la ruta predeterminada.
  Si es posible, se recomienda usar la configuración siguiente:
 * La configuración de ExpressRoute anuncia 0.0.0.0/0 y, de manera predeterminada, fuerza la tunelización de todo el tráfico saliente a local.
 * El UDR aplicado a la subred que contiene Azure API Management define 0.0.0.0/0 con un tipo de próximo salto de Internet.
 El efecto combinado de estos pasos es que el UDR a nivel de subred tiene prioridad sobre la tunelización forzada de ExpressRoute, lo que garantiza el acceso saliente a Internet desde Azure API Management.

**Enrutamiento a través de aplicaciones virtuales de red**: las configuraciones que usan un enrutamiento definido por el usuario con una ruta predeterminada (0.0.0.0/0) para enrutar el tráfico destinado a Internet desde la subred API Management mediante una aplicación virtual de red que se ejecuta en Azure impedirán la comunicación completa entre API Management y los servicios requeridos. Esta configuración no es compatible. 

>[!WARNING]  
>Azure API Management no es compatible con las configuraciones de ExpressRoute que **anuncian incorrectamente rutas entre la ruta de acceso entre pares públicos y la ruta de acceso entre pares privados**. Las configuraciones de ExpressRoute con el emparejamiento público configurado recibirán anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure. Si estos intervalos de direcciones se anuncian incorrectamente en la ruta de acceso entre pares privados, el resultado final es que se forzará incorrectamente la tunelización de todos los paquetes de red salientes desde la subred de la instancia de Azure API Management a la infraestructura de red local del cliente. Este flujo de red interrumpe Azure API Management. La solución a este problema consiste en detener rutas anunciadas entre la ruta de acceso de interconexión pública y la ruta de acceso de interconexión privada.


## <a name="troubleshooting"> </a>Solución de problemas
* **Programa de instalación inicial**: cuando la implementación inicial del servicio API Management en una subred no se realiza correctamente, se recomienda implementar una máquina virtual en la misma subred. Siga con el escritorio remoto en la máquina virtual y compruebe que hay conectividad al menos con cada recurso que abarca su suscripción de Azure 
    * Azure Storage Blob
    * Azure SQL Database

 > [!IMPORTANT]
 > Una vez que valide la conectividad, asegúrese de quitar todos los recursos implementados en la subred, antes de implementar API Management en ella.

* **Actualizaciones incrementales**: al realizar cambios en la red, consulte [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), para comprobar que el servicio API Management no ha perdido el acceso a inguno de los recursos críticos de los que depende. El estado de conectividad debe actualizarse cada 15 minutos.

* **Vínculos de navegación de recursos**: cuando se implementan en la subred de red virtual del estilo de Resource Manager, API Management reserva la subred creando un vínculo de navegación de recursos. Si la subred ya contiene un recurso de un proveedor distinto, la implementación **producirá un error**. De forma similar, al eliminar un servicio API Management o moverlo a una subred diferente, se quitará ese vínculo de navegación de recursos. 

## <a name="routing"> </a> Enrutamiento
+ También se reservará una dirección IP pública (VIP) con equilibrio de carga para proporcionar acceso a todos los puntos de conexión del servicio.
+ Se usará una dirección IP de un intervalo de IP de subred (DIP) para el acceso a los recursos dentro de la red virtual y una dirección IP pública (VIP) para el acceso a los recursos fuera de la red virtual.
+ La dirección IP pública con equilibrio de carga puede encontrarse en la hoja Información general/nformación esencial en Azure Portal.

## <a name="limitations"></a>Limitaciones
* Una subred que contenga instancias de API Management no puede contener otros tipos de recursos de Azure.
* La subred y el servicio API Management tienen que estar en la misma suscripción.
* Una subred que contenga instancias de API Management no se puede mover a otras suscripciones.
* Para implementaciones de API Management de varias regiones configuradas en el modo de red virtual interna, los usuarios son responsables de administrar el equilibrio de carga a través de varias regiones, ya que son los propietarios del enrutamiento.


## <a name="related-content"></a>Contenido relacionado
* [Conexión de una red virtual a back-end mediante VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Conexión a una red virtual a partir de diferentes modelos de implementación](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Uso de API Inspector para hacer un seguimiento de las llamadas en Azure API Management](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md
