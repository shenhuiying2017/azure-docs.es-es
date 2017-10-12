---
title: "Escala distribuida geográficamente con entornos de Servicio de aplicaciones"
description: "Aprenda a escalar aplicaciones horizontalmente con distribución geográfica con el Administrador de tráfico y los entornos de Servicio de aplicaciones."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.openlocfilehash: 21f747239e565aba79a84c8e946a71e306b64968
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Escala distribuida geográficamente con entornos de Servicio de aplicaciones
## <a name="overview"></a>Información general
Aquellos escenarios de aplicaciones que requieren una escala muy elevada pueden superar la capacidad de recursos de proceso disponible para una sola implementación de una aplicación.  Algunos ejemplos de los escenarios que requieren una escala extremadamente alta son las aplicaciones de votación, los acontecimientos deportivos y los espectáculos televisados. Se pueden cumplir los requisitos de gran escala mediante el escalado horizontal de las aplicaciones, con la realización de varias implementaciones de la aplicación en una única región, así como entre regiones, para afrontar los requisitos extremos de carga.

Los entornos de Servicio de aplicaciones son una plataforma ideal para el escalado horizontal.  Una vez seleccionado una configuración de entorno de Servicio de aplicaciones que admita una tasa de solicitudes conocida, los desarrolladores pueden implementar más entornos de Servicio de aplicaciones a modo de "plantilla" para lograr una capacidad de carga máxima deseada.

Por ejemplo, imagine una aplicación que se ejecuta en una configuración de entorno de Servicio de aplicaciones y en la que se probó el procesamiento de 20.000 solicitudes por segundo (RPS).  Si la capacidad de carga máxima deseada es 100.000 RPS, se pueden crear y configurar cinco (5) entornos de Servicio de aplicaciones para asegurarse de que la aplicación pueda afrontar la carga máxima prevista.

Puesto que los clientes suelen acceder a las aplicaciones mediante un dominio personalizado (o mnemónico), los desarrolladores necesitan una forma de distribuir las solicitudes de aplicaciones a todas las instancias del entorno de Servicio de aplicaciones.  Una excelente manera de lograr esto es resolver el dominio personalizado con un [perfil de Traffic Manager de Azure][AzureTrafficManagerProfile].  El perfil de Traffic Manager puede configurarse para que apunte a todos los entornos de Servicio de aplicaciones individuales.  Traffic Manager controlará automáticamente la distribución de los clientes por todos los entornos de Servicio de aplicaciones en función de la configuración de equilibrio de carga en el perfil de Traffic Manager.  Este enfoque funciona independientemente de si todos los entornos de Servicio de aplicaciones están implementados en una sola región de Azure o en varias regiones de Azure del mundo.

Además, como los clientes acceden a las aplicaciones a través del dominio mnemónico, desconocen el número de entornos de Servicio de aplicaciones que ejecutan una aplicación.  Como resultado, los desarrolladores pueden agregar y quitar rápida y fácilmente entornos de Servicio de aplicaciones según la carga de tráfico observada.

En el siguiente diagrama conceptual, se muestra una aplicación escalada horizontalmente en tres entornos de Servicio de aplicaciones en una única región.

![Arquitectura conceptual][ConceptualArchitecture] 

El resto de este tema lo guía por los pasos necesarios para configurar una topología distribuida para la aplicación de ejemplo usando varios entornos de App Service.

## <a name="planning-the-topology"></a>Planeación de la topología
Antes de crear la superficie de una aplicación distribuida, resulta útil tener algunos datos con antelación.

* **Dominio personalizado de la aplicación:** ¿cuál es el nombre del dominio personalizado que los clientes van a usar para acceder a la aplicación?  Para la aplicación de ejemplo, el nombre de dominio personalizado es *www.scalableasedemo.com*
* **Dominio del Administrador de tráfico:** se debe elegir un nombre de dominio al crear un [perfil de Traffic Manager de Azure][AzureTrafficManagerProfile].  Este nombre se combinará con el sufijo *trafficmanager.net* para registrar una entrada de dominio que es administrada por el Administrador de tráfico.  Para la aplicación de ejemplo, el nombre elegido es *scalable-ase-demo*.  Como resultado, el nombre de dominio completo administrado por el Administrador de tráfico es *scalable-ase-demo.trafficmanager.net*.
* **Estrategia para escalar la superficie de la aplicación**: ¿se va a distribuir la superficie de la aplicación entre varias instancias de App Service Environment de una sola región?  ¿Varias regiones?  ¿Una combinación de ambos enfoques?  La decisión debería basarse en las expectativas de dónde se vaya a originar el tráfico del cliente, así como también en la medida en que el resto de la infraestructura de back-end de apoyo de una aplicación pueda escalarse.  Por ejemplo, con una aplicación totalmente sin estado, se puede escalar una aplicación de forma masiva mediante una combinación de varias instancias de App Service Environment por región de Azure, multiplicados por más instancias de App Service Environment implementadas en varias regiones de Azure.  Con más de 15 regiones de Azure públicas entre las que elegir, los clientes pueden realmente crear una superficie de aplicación de gran escala en todo el mundo.  Para la aplicación de ejemplo usada en este artículo, se crearon tres entornos de Servicio de aplicaciones en una sola región de Azure (Centro y Sur de EE. UU.).
* **Convención de nomenclatura para las instancias de App Service Environment:** cada instancia de App Service Environment requiere un nombre único.  Si existen más de uno o dos entornos de Servicio de aplicaciones, resulta útil disponer de una convención de nomenclatura para ayudar a identificar cada uno de ellos.  Para la aplicación de ejemplo, se usó una convención de nomenclatura sencilla.  Los nombres de las tres instancias de App Service Environment son *fe1ase*, *fe2ase* y *fe3ase*.
* **Convención de nomenclatura para las aplicaciones:** como se van a  implementar varias instancias de la aplicación, se necesita un nombre para cada instancia de la aplicación implementada.  Una característica poco conocida pero muy cómoda de los entornos de Servicio de aplicaciones es que se puede usar el mismo nombre de aplicación en varios entornos de Servicio de aplicaciones.  Dado que cada entorno de Servicio de aplicaciones tiene un sufijo de dominio único, los desarrolladores pueden reutilizar el mismo nombre de aplicación en cada entorno.  Por ejemplo, un desarrollador podría asignar los siguientes nombres a las aplicaciones:*myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net, etc*.  No obstante, para la aplicación de ejemplo, cada instancia tiene un nombre único.  Los nombres de las instancias de aplicación usados son *webfrontend1*, *webfrontend2* y *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configuración de un perfil del Administrador de tráfico
Una vez que se implementan varias instancias de una aplicación en varias instancias de App Service Environment, las instancias de aplicación individuales se pueden registrar en Traffic Manager.  Para la aplicación de ejemplo, se necesita un perfil del Administrador de tráfico para *scalable-ase-demo.trafficmanager.net* que pueda enrutar a los clientes a cualquiera de las siguientes instancias de la aplicación implementadas:

* **webfrontend1.fe1ase.p.azurewebsites.net:** una instancia de la aplicación de ejemplo que se implementa en la primera instancia de App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.net:** una instancia de la aplicación de ejemplo que se implementa en la segunda instancia de App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.net:** una instancia de la aplicación de ejemplo que se implementa en la tercera instancia de App Service Environment.

La forma más sencilla de registrar varios puntos de conexión de Azure App Service, todos los cuales se ejecutan en la **misma** región de Azure, es usando la [compatibilidad de Azure Resource Manager con Traffic Manager de Azure][ARMTrafficManager].  

El primer paso consiste en crear un perfil del Administrador de tráfico de Azure.  El código siguiente muestra cómo se creó el perfil para la aplicación de ejemplo:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Observe la forma en que el parámetro *RelativeDnsName* se ha establecido en *scalable-ase-demo*.  Así es cómo se crea el nombre de dominio *scalable-ase-demo.trafficmanager.net* y se asocia con un perfil del Administrador de tráfico.

El parámetro *TrafficRoutingMethod* define la directiva de equilibrio de carga que el Administrador de tráfico usará para determinar cómo distribuir la carga de los clientes entre todos los extremos disponibles.  En este ejemplo, se eligió el método *Weighted* .  Como resultado, las solicitudes de clientes se distribuirán entre todos los extremos de la aplicación registrados en función de las ponderaciones relativas asociadas con cada extremo. 

Con el perfil creado, se agrega cada instancia de la aplicación al perfil como un punto de conexión nativo de Azure.  El código siguiente recupera una referencia a cada aplicación web front-end y, luego, agrega cada aplicación como un punto de conexión del Administrador de tráfico por medio del parámetro *TargetResourceId* .

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Observe que existe una llamada a *Add-AzureTrafficManagerEndpointConfig* para cada instancia de aplicación individual.  El parámetro *TargetResourceId* de cada comando de Powershell hace referencia a una de las tres instancias de la aplicación implementada.  El perfil de Traffic Manager repartirá la carga entre los tres puntos de conexión registrados en el perfil.

Los tres extremos usan el mismo valor (10) para el parámetro *Weight* .  Como resultado, el Administrador de tráfico distribuye las solicitudes de los clientes entre las tres instancias de la aplicación de forma relativamente uniforme. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Hacer que el dominio personalizado de la aplicación apunte al dominio del Administrador de tráfico
El último paso necesario es hacer que el dominio personalizado de la aplicación apunte al dominio del Administrador de tráfico.  En el caso de la aplicación de ejemplo, esto significa que debe apuntar a *www.scalableasedemo.com* en *scalable-ase-demo.trafficmanager.net*.  Este paso se debe completar con el registrador de dominios que administra el dominio personalizado.  

Usando las herramientas de administración de dominios del registrador, se debe crear un registro CNAME que apunte al dominio personalizado en el dominio del Administrador de tráfico.  En la figura siguiente se muestra un ejemplo de esta configuración de CNAME:

![CNAME para el dominio personalizado][CNAMEforCustomDomain] 

Aunque no se trata en este tema, recuerde que cada instancia de aplicación individual debe tener el dominio personalizado registrado con ella también.  De lo contrario, si una solicitud llega a una instancia de la aplicación y la aplicación no tiene el dominio personalizado registrado con la aplicación, la solicitud producirá un error.  

En este ejemplo, el dominio personalizado es *www.scalableasedemo.com*y cada instancia de la aplicación tiene el dominio personalizado asociado con él.

![Dominio personalizado][CustomDomain] 

Como resumen del registro de un dominio personalizado con las aplicaciones de Azure App Service, consulte el siguiente artículo sobre el [registro de dominios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Prueba de la topología distribuida
El resultado final de la configuración del Administrador de tráfico y de DNS es que las solicitudes de *www.scalableasedemo.com* fluirán en la siguiente secuencia:

1. Un dispositivo o explorador realizará una búsqueda DNS de *www.scalableasedemo.com*
2. La entrada CNAME en el registrador de dominios hace que la búsqueda DNS se redirija al Administrador de tráfico de Azure.
3. Se realiza una búsqueda DNS de *scalable-ase-demo.trafficmanager.net* en uno de los servidores DNS del Administrador de tráfico de Azure.
4. Según la directiva de equilibrio de carga (el parámetro *TrafficRoutingMethod* usado antes al crear el perfil del Administrador de tráfico), el Administrador de tráfico seleccionará uno de los extremos configurados y devolverá el FQDN de ese extremo al explorador o dispositivo.
5. Dado que el FQDN del extremo es la dirección URL de una instancia de la aplicación que se ejecuta en un entorno de Servicio de aplicaciones, el explorador o el dispositivo pedirá a un servidor DNS de Microsoft Azure que resuelva el FQDN en una dirección IP. 
6. El explorador o el dispositivo enviará la solicitud HTTP/S a la dirección IP.  
7. La solicitud llegará a una de las instancias de la aplicación que se ejecutan en uno de los entornos de Servicio de aplicaciones.

En la imagen de consola siguiente, se muestra una búsqueda DNS para el dominio personalizado de la aplicación de ejemplo que se ha resuelto correctamente en una instancia de la aplicación que se ejecuta en uno de los tres entornos de Servicio de aplicaciones (en este caso, el segundo de los tres):

![Búsqueda DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Información y vínculos adicionales
Documentación sobre [compatibilidad de Azure Resource Manager con Traffic Manager de Azure][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
