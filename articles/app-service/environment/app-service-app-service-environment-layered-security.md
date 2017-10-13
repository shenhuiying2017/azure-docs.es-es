---
title: Arquitectura de seguridad por capas con entornos del Servicio de aplicaciones
description: "Implementación de una arquitectura de seguridad por capas con entornos del Servicio de aplicaciones"
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.openlocfilehash: 6c1f62b5e10a625911feea17ae6835e027709790
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementación de una arquitectura de seguridad por capas con entornos del Servicio de aplicaciones
## <a name="overview"></a>Información general
Como los entornos del Servicio de aplicaciones proporcionan un entorno de tiempo de ejecución aislado que está implementado en una red virtual, los desarrolladores pueden crear una arquitectura de seguridad por capas que proporcione diferentes niveles de acceso a la red para cada capa de aplicación física.

Un objetivo común es ocultar los back-ends de API al acceso general desde Internet y permitir que solo las aplicaciones web ascendentes puedan llamar a las API.  Los [Grupos de seguridad de red (NSG)][NetworkSecurityGroups] pueden usarse para restringir el acceso público a aplicaciones API en subredes que contengan los entornos de App Service.

El diagrama a continuación muestra una arquitectura de ejemplo con una aplicación basada en WebAPI, implementada en un entorno del Servicio de aplicaciones.  Tres instancias de aplicación web independiente, implementadas en tres entornos del Servicio de aplicaciones independientes, realizan llamadas de back-end a la misma aplicación WebAPI.

![Arquitectura conceptual][ConceptualArchitecture] 

El signo más de color verde indica que el grupo de seguridad de red en la subred que contiene "apiase" permite llamadas entrantes desde las aplicaciones web ascendentes, así como llamadas de sí mismo.  Sin embargo el mismo grupo de seguridad de red deniega explícitamente el acceso al tráfico general de entrada desde Internet. 

El resto de este tema le guía por los pasos necesarios para configurar el grupo de seguridad de red en la subred que contiene "apiase".

## <a name="determining-the-network-behavior"></a>Determinación del comportamiento de la red
Para saber qué reglas de seguridad de red son necesarias, tiene que determinar a qué clientes de red se les permitirá ponerse en contacto con el entorno del Servicio de aplicaciones que contiene la aplicación de API, y a qué clientes se bloqueará.

Puesto que los [Grupos de seguridad de red (NSG)][NetworkSecurityGroups] están aplicados a las subredes y los entornos de App Service están implementados en las subredes, las reglas contenidas en un NSG se aplican a **todas** las aplicaciones que se ejecutan en un entorno de App Service.  En la arquitectura de ejemplo de este artículo, una vez que un grupo de seguridad de red se aplica a la subred que contiene "apiase", todas las aplicaciones que se ejecuten en el entorno del Servicio de aplicaciones "apiase" estarán protegidas por el mismo conjunto de reglas de seguridad. 

* **Determinar la dirección IP saliente de los autores de llamadas que preceden en la cadena:** ¿cuál es la dirección o direcciones IP de los autores de llamadas que preceden en la cadena?  Se tiene que garantizar de forma explícita el acceso de estas direcciones en el NSG.  Dado que las llamadas entre los entornos del Servicio de aplicaciones se consideran llamadas de "Internet", se tiene que permitir el acceso en el NSG de la dirección IP saliente asignada a cada uno de los tres entornos del Servicio de aplicaciones ascendentes para la subred "apiase".   Para obtener más información acerca de cómo determinar la dirección IP saliente para aplicaciones que se ejecutan en un entorno de App Service, consulte el artículo de información general sobre la [arquitectura de red][NetworkArchitecture].
* **¿Tiene la aplicación de API de back-end que llamarse a sí misma?**  Un punto sutil que a veces se pasa por alto es el escenario en el que la aplicación back-end tiene que llamarse a sí misma.  Si una aplicación de API de back-end en un entorno del Servicio de aplicaciones tiene que llamarse a sí misma, esta llamada se trata también como una llamada de "Internet".  En la arquitectura de ejemplo, esto requiere que se permita también el acceso desde la dirección IP saliente del entorno del Servicio de aplicaciones "apiase".

## <a name="setting-up-the-network-security-group"></a>Creación del grupo de seguridad de red
Una vez que se conozca el conjunto de direcciones IP salientes, el siguiente paso es crear un grupo de seguridad de red.  Se pueden crear grupos de seguridad de red para las redes virtuales basadas en Resource Manager, así como para las redes virtuales clásicas.  Los ejemplos siguientes muestran la creación y configuración de un grupo de seguridad de red en una red virtual clásica mediante Powershell.

Para la arquitectura del ejemplo, los entornos se encuentran en "South Central US", por lo que se crea un NSG vacío en esa región:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

En primer lugar, se agrega una regla de permiso explícito para la infraestructura de administración de Azure como se indica en el artículo sobre [tráfico de entrada][InboundTraffic] para entornos de App Service.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

A continuación, se agregan dos reglas para permitir las llamadas HTTP y HTTPS desde el primer entorno del Servicio de aplicaciones ascendente ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Repetir para el segundo y tercer entorno del Servicio de aplicaciones ascendente ("fe2ase" y "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Por último, conceder acceso a la dirección IP saliente del entorno del Servicio de aplicaciones de la API de back-end para que puede devolverse la llamada a sí misma.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

No es necesario configurar ninguna otra regla de seguridad de red porque cada NSG tiene un conjunto de reglas predeterminadas que bloquean el acceso de entrada desde Internet de forma predeterminada.

La lista completa de las reglas en el grupo de seguridad de red se muestran a continuación.  Observe cómo la última regla, que aparece resaltada, bloquea el acceso de entrada de todos los autores de llamadas que no sean aquellos a los que se concedió acceso explícitamente.

![Configuración NSG][NSGConfiguration] 

El último paso es aplicar el NSG a la subred que contiene el entorno del Servicio de aplicaciones "apiase".  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Con el NSG aplicado a la subred, solo los tres entornos del Servicio de aplicaciones y el entorno del Servicio de aplicaciones que contiene el back-end de API tienen permitido llamar en el entorno de "apiase".

## <a name="additional-links-and-information"></a>Información y vínculos adicionales
Información acerca de los [grupos de seguridad de red](../../virtual-network/virtual-networks-nsg.md). 

Descripción de las [direcciones IP saliente][NetworkArchitecture] y entornos de App Service.

[Puertos de red][InboundTraffic] usados en un entorno de App Service

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
