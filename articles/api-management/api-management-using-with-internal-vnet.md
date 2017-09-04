---
title: Uso de API Management con red virtual interna | Microsoft Docs
description: "Obtenga información acerca de cómo instalar y configurar Azure API Management en una red virtual interna."
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
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: a4c2bda1226ca05c775d011fba7bc59d4dab8998
ms.contentlocale: es-es
ms.lasthandoff: 08/31/2017

---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>Uso de Azure API Management con una red virtual interna
Con redes virtuales de Azure (VNET), API Management puede administrar las API no accesibles en Internet. Hay varias tecnologías VPN disponibles para realizar la conexión, y API Management se puede implementar en dos modos principales dentro de la red virtual:
* Externo
* Interno

## <a name="overview"> </a>Información general
Cuando se implementa API Management en un modo de red virtual interna, todos los puntos de conexión de servicio (puerta de enlace, portal para desarrolladores, portal para editores, administración directa y GIT) solo están visibles dentro de una red virtual a la que controla el acceso. Ninguno de los puntos de conexión de servicio se registra en el servidor DNS público.

Al usar API Management en modo interno, puede conseguir lo siguiente:
* Hacer que las API se hospeden de manera segura en su centro de datos privado accesible por terceros que estén fuera de él por medio de conexiones de sitio a sitio o VPN de ExpressRoute.
* Habilitar escenarios de nube híbrida mediante la exposición de las API basadas en la nube y las API locales a través de una puerta de enlace común.
* Administrar las API hospedadas en diversas ubicaciones geográficas mediante un único punto de conexión de puerta de enlace. 

## <a name="enable-vpn"> </a>Creación de API Management en una red virtual interna
El servicio API Management en red virtual interna se hospeda detrás de un equilibrador de carga interno. La dirección IP del equilibrador de carga está en el intervalo [RFC1918](http://www.faqs.org/rfcs/rfc1918.html).  

### <a name="enable-vnet-connection-using-azure-portal"></a>Habilitación de la conexión de red virtual mediante Azure Portal
Primero cree el servicio API Management siguiendo los pasos de [Creación de una instancia de API Management][Create API Management service]. A continuación, configure API Management para que se implemente dentro de una red virtual.

![Menú para configurar API Management en una red virtual interna][api-management-using-internal-vnet-menu]

Una vez finalizada correctamente la implementación, debería ver la dirección IP virtual interna de su servicio en el panel.

![Panel de API Management con red virtual interna configurada][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>Habilitación de una conexión de VNET con cmdlets de PowerShell
También puede habilitar la conectividad de VNET con los cmdlets de PowerShell.

* **Crear un servicio de API Management dentro de una red virtual**: use el cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) para crear un servicio de Azure API Management dentro de una VNET y configurarlo para usar el tipo de VNET interno.

* <seg>
  **Implementar un servicio existente de API Management dentro de una VNET**: use el cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) para mover un servicio existente de Azure API Management dentro de una red virtual y configurarlo para usar el tipo de VNET interno..</seg>

## <a name="apim-dns-configuration"></a>Configuración de DNS
Cuando se usa API Management en el modo de red virtual externa, DNS está administrado por Azure. Para el modo de red virtual interna, tiene que administrar su propio DNS.

> [!NOTE]
> El servicio API Management no escucha las solicitudes que llegan en direcciones IP. Solo responde a las solicitudes para el nombre de host configurado en sus puntos de conexión de servicio (entre los que están la puerta de enlace, el portal para desarrolladores, el portal para editores, el punto de conexión de administración directa y GIT).

### <a name="access-on-default-host-names"></a>Acceso en nombres de host predeterminados:
Cuando se crea un servicio de API Management en la nube pública de Azure, denominada por ejemplo "contoso", los siguientes punto de conexión de servicio se configuran de forma predeterminada.

>   Puerta de enlace o proxy: contoso.azure-api.net

> Portal para desarrolladores y portal para editores: contoso.portal.azure-api.net

> Punto de conexión de administración directa: contoso.management.azure-api.net

>   GIT: contoso.scm.azure-api.net

Para obtener acceso a estos puntos de conexión de servicio de API Management, puede crear una máquina virtual en una subred conectada a la red virtual en el que se implementa API Management. Suponiendo que la dirección IP virtual interna para el servicio sea 10.0.0.5, puede realizar la asignación de archivos de host (%SystemDrive%\drivers\etc\hosts) del modo siguiente:

> 10.0.0.5    contoso.azure-api.net

> 10.0.0.5    contoso.portal.azure-api.net

> 10.0.0.5    contoso.management.azure-api.net

> 10.0.0.5    contoso.scm.azure-api.net

Así podrá tener acceso a todos los puntos de conexión de servicio de la máquina virtual que creó. Si utiliza un servidor DNS personalizado en una red virtual, también puede crear registros D de DNS y tener acceso a estos puntos de conexión desde cualquier lugar de la red virtual. 

### <a name="access-on-custom-domain-names"></a>Acceso en nombres de dominio personalizado:
Si no desea tener acceso al servicio API Management con los nombres de host predeterminados, puede configurar los nombres de dominio personalizados para todos los puntos de conexión de servicio del modo siguiente:

![Configuración de dominio personalizado para API Management][api-management-custom-domain-name]

A continuación, puede crear registros D en el servidor DNS para tener acceso a estos puntos de conexión que solo son accesibles desde dentro de la red virtual.

## <a name="related-content"> </a>Contenido relacionado
* [Common Network configuration issues while setting up APIM in VNET][Common Network Configuration Issues] (Problemas comunes de configuración de red al configurar API Management en la red virtual)
* [P+F de Virtual Network](../virtual-network/virtual-networks-faq.md)
* [Creating A record in DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx) (Creación de registro D en DNS)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues

