---
title: Uso de Azure API Management con redes virtuales internas | Microsoft Docs
description: Aprenda a instalar y configurar Azure API Management en una red virtual interna.
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: df2ebb6ee8b1f108c751226188556ced907314e1
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Uso del servicio Azure API Management con una red virtual interna
Con Azure Virtual Network, Azure API Management puede administrar las API que no están accesibles desde Internet. Para establecer la conexión, hay una serie de tecnologías de VPN disponibles. API Management puede implementarse de dos modos en una red virtual:
* Externo
* Interno


Cuando API Management se implementa en el modo de red virtual interna, todos los puntos de conexión de servicio (puerta de enlace, portal del desarrollador, portal del editor, administración directa y Git) solamente están visibles en una red virtual en la que usted controla el acceso. Ninguno de los puntos de conexión de servicio está registrado en el servidor DNS público.

Si utiliza API Management en modo interno, puede conseguir los siguientes escenarios:
* Puede conseguir que terceras personas puedan obtener acceso de forma segura a las API hospedadas en el centro de datos privado desde fuera de este centro utilizando conexiones de sitio a sitio o conexiones de VPN de Azure ExpressRoute.
* Puede permitir escenarios de nube híbrida exponiendo las API basadas en la nube y las API locales a través de una puerta de enlace común.
* Puede administrar las API hospedadas en varias ubicaciones geográficas mediante un único punto de conexión de puerta de enlace. 


## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos que se describen en este artículo, debe tener:

+ **Una suscripción activa a Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Una instancia de Azure API Management**. Para más información, consulte [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Creación de una instancia de API Management en una red virtual interna
En las redes virtuales internas, el servicio API Management se hospeda detrás de un equilibrador de carga interno (ILB).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Habilitación de una conexión de red virtual mediante Azure Portal

1. Navegue a la instancia de Azure API Management en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Red virtual**.
3. Configure la instancia de API Management que se va a implementar dentro de la red virtual.

    ![Menú de configuración de una instancia de Azure API Management en una red virtual interna][api-management-using-internal-vnet-menu]

4. Seleccione **Guardar**.

Una vez finalizada correctamente la implementación, debería ver la dirección IP virtual interna del servicio en el panel.

![Panel de API Management con una red virtual interna configurada][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Habilitación de una conexión de red virtual mediante cmdlets de PowerShell
También puede habilitar la conectividad de la red virtual utilizando cmdlets de PowerShell.

* Creación de un servicio API Management dentro de una red virtual: utilice el cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) para crear un servicio Azure API Management dentro de una red virtual y configúrelo para que utilice el tipo de red virtual interna.

* Implementación de un servicio API Management existente en una red virtual: utilice el cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) para crear un servicio API Management existente en una red virtual y configúrelo para que utilice el tipo de red virtual interna.

## <a name="apim-dns-configuration"></a>Configuración de DNS
Cuando API Management está en modo de red virtual externa, el DNS está administrado por Azure. En el modo de red virtual interna, es usted quien tiene que administrar su propio enrutamiento.

> [!NOTE]
> El servicio API Management no escucha las solicitudes procedentes de direcciones IP. Solo responde a las solicitudes dirigidas al nombre de host establecido en los puntos de conexión de servicio. Estos puntos de conexión de servicio pueden ser la puerta de enlace, el portal del desarrollador, el portal del editor, el punto de conexión de administración directa y Git.

### <a name="access-on-default-host-names"></a>Acceso de nombres de host predeterminados
Cuando se crea un servicio de API Management (por ejemplo, "contoso"), los siguientes puntos de conexión de servicio se configuran de forma predeterminada:

   * Puerta de enlace o proxy: contoso.azure-api.net

   * Portal del desarrollador y portal del editor: contoso.portal.azure-api.net

   * Punto de conexión de administración directa: contoso.management.azure-api.net

   * Git: contoso.scm.azure-api.net

Para obtener acceso a estos puntos de conexión de servicio de API Management, puede crear una máquina virtual en una subred conectada a la red virtual en la que está implementado API Management. Suponiendo que la dirección IP virtual interna del servicio sea 10.0.0.5, puede asignar el archivo del host, %SystemDrive%\drivers\etc\hosts. del modo siguiente:

   * 10.0.0.5     contoso.azure-api.net

   * 10.0.0.5     contoso.portal.azure-api.net

   * 10.0.0.5     contoso.management.azure-api.net

   * 10.0.0.5     contoso.scm.azure-api.net

Así, podrá tener acceso a todos los puntos de conexión de servicio desde la máquina virtual que ha creado. Si utiliza un servidor DNS personalizado en una red virtual, también puede crear registros D de DNS y acceder a estos puntos de conexión desde cualquier lugar de la red virtual. 

### <a name="access-on-custom-domain-names"></a>Acceso de nombres de dominio personalizados

   1. Si no quiere acceder al servicio API Management con los nombres de host predeterminados, puede definir nombres de dominio personalizados para todos los puntos de conexión de servicio, tal y como se indica en la siguiente ilustración: 

   ![Configuración de un dominio personalizado para API Management][api-management-custom-domain-name]

   2. A continuación, puede crear registros en el servidor DNS para acceder a los puntos de conexión que solo están accesibles desde dentro de la red virtual.

## <a name="routing"> </a> Enrutamiento
+ Se reservará una dirección IP virtual privada con equilibrio de carga desde el intervalo de subred y se utilizará para tener acceso a los puntos de conexión del servicio API Management desde la red virtual.
+ También se reservará una dirección IP pública (VIP) con equilibrio de carga para proporcionar acceso al punto de conexión del servicio de administración solo a través del puerto 3443.
+ Se usará una dirección IP de un intervalo de IP de subred (DIP) para el acceso a los recursos dentro de la red virtual y una dirección IP pública (VIP) para el acceso a los recursos fuera de la red virtual.
+ Las direcciones IP privadas y públicas con equilibrio de carga pueden encontrarse en la hoja Información general/nformación esencial en Azure Portal.

## <a name="related-content"></a>Contenido relacionado
Para obtener más información, consulte los artículos siguientes:
* [Problemas comunes de configuración de red al establecer Azure API Management en una red virtual][Common network configuration problems]
* [Preguntas más frecuentes (P+F) acerca de Azure Virtual Network](../virtual-network/virtual-networks-faq.md)
* [Creating a record in DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx) (Creación de un registro en DNS)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

