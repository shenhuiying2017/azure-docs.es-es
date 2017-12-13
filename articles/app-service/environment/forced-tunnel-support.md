---
title: "Configuración de Azure App Service Environment para que use tunelización forzada"
description: "Habilitación de la instancia de App Service Environment para que funcione cuando se realiza la tunelización forzada del tráfico saliente"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configuración de App Service Environment con tunelización forzada

Azure App Service Environment es una implementación de Azure App Service en una instancia del cliente de Azure Virtual Network. Muchos clientes configuran sus redes virtuales como extensiones de sus redes locales con redes privadas virtuales o con conexiones de Azure ExpressRoute. Debido a las directivas corporativas u otras restricciones de seguridad, configuran las rutas para enviar todo el tráfico saliente local para poder salir a internet. Se conoce como tunelización forzada al cambio del enrutamiento de la red virtual para que el tráfico saliente de esta fluya a través de la conexión ExpressRoute o VPN a un entorno local. 

La tunelización forzada puede ocasionar problemas en una instancia de App Service Environment. App Service Environment cuenta con varias dependencias externas, que se enumeran en el documento de [arquitectura de red de App Service Environment][network]. De forma predeterminada, App Service Environment requiere que todas las comunicaciones salientes pasen por la dirección VIP que se aprovisiona con él.

Las rutas constituyen un aspecto crucial del concepto de tunelización forzada y de cómo tratar con ella. En una instancia de Azure Virtual Network, el enrutamiento se realiza según la coincidencia de prefijo más larga (LPM). Si hay más de una ruta con la misma coincidencia LPM, se selecciona una ruta en función de su origen en el orden siguiente:

* Ruta definida por el usuario (UDR)
* Ruta BGP (cuando se utiliza ExpressRoute)
* Ruta del sistema

Para más información sobre el enrutamiento en una red virtual, lea [Rutas definidas por el usuario y reenvío IP][routes]. 

Si quiere que App Service Environment funcione en una red virtual con tunelización forzada, tiene dos opciones:

* Permitir que App Service Environment tenga acceso directo a Internet
* Cambiar el punto de conexión de salida de App Service Environment

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Permitir que App Service Environment tenga acceso directo a Internet

Para que App Service Environment funcione mientras la red virtual está configurada con una conexión ExpressRoute, puede:

* Configurar ExpressRoute para anunciar 0.0.0.0/0. De forma predeterminada, obliga a dirigir todo el tráfico saliente tráfico local.
* Crear un UDR. Aplíquelo a la subred que contiene la instancia de App Service Environment, con un prefijo de dirección de 0.0.0.0/0 y un tipo de próximo salto de Internet.

Si realiza estos dos cambios, el tráfico destinado a Internet que se origina en la subred de App Service Environment no se fuerza hacia la conexión ExpressRoute y App Service Environment funcionará.

> [!IMPORTANT]
> Las rutas definidas en una UDR tienen que ser lo suficientemente específicas para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute. En el ejemplo anterior se utiliza el intervalo de direcciones amplio 0.0.0.0/0. Puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.
>
> Las instancias de App Service Environment no son compatibles con las configuraciones de ExpressRoute que anuncian rutas entre la ruta de acceso de emparejamiento público y la ruta de acceso de emparejamiento privado. Las configuraciones de ExpressRoute con el emparejamiento público configurado reciben anuncios de ruta de Microsoft. Los anuncios contienen un gran conjunto de intervalos de direcciones IP de Microsoft Azure. Si los intervalos de direcciones se anuncian en la ruta de acceso de emparejamiento privado, se fuerza la tunelización de todos los paquetes de red salientes de la subred de App Service Environment a la infraestructura de red local de un cliente. Actualmente, este flujo de red no es compatible de forma predeterminada con las instancias de App Service Environment. Una solución a este problema es dejar de anunciar las rutas entre la ruta de acceso de emparejamiento público y la de emparejamiento privado. Otra solución consiste en permitir que App Service Environment funcione en una configuración de tunelización forzada.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>Cambio del punto de conexión de salida de App Service Environment ##

En esta sección se describe cómo habilitar una instancia de App Service Environment en una configuración de tunelización forzada mediante la modificación del punto de conexión de salida que se usa en App Service Environment. Si se fuerza la tunelización del tráfico saliente de App Service Environment a una red local, debe permitir que el tráfico se origine en direcciones IP distintas a la dirección IP privada de App Service Environment.

Una instancia de App Service Environment no solo tiene dependencias externas, sino que también escucha el tráfico entrante y responde a dicho tráfico. Las respuestas no se pueden enviar desde otra dirección, dado que se interrumpe TCP. Se necesitan tres pasos para cambiar el punto de conexión de salida de App Service Environment:

1. Establecer una tabla de rutas para asegurarse de que el tráfico de administración entrante pueda regresar desde la misma dirección IP

2. Agregar las direcciones IP que se vayan a usar para la salida al firewall de App Service Environment

3. Establecer las rutas al tráfico saliente desde App Service Environment a un túnel

   ![Flujo de red en túnel forzado][1]

Puede configurar App Service Environment con diferentes direcciones de salida después de que ya esté configurado y operativo, o se puede configurar durante la implementación.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>Cambio de la dirección de salida después de que App Service Environment está operativo ###
1. Obtenga las direcciones IP que desea utilizar como direcciones IP de salida para App Service Environment. Si va a realizar una tunelización forzada, estas direcciones proceden de los dispositivos NAT o de las direcciones IP de puerta de enlace. Si quiere enrutar el tráfico de salida de App Service Environment a través de una NVA, la dirección de salida sería la dirección IP pública de la NVA.

2. Establezca las direcciones de salida en la información de configuración de App Service Environment. Vaya a resource.azure.com y, luego, a Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. A continuación, puede ver el archivo JSON que describe su instancia de App Service Environment. Asegúrese de que, en la parte superior, indica **lectura/escritura**. Seleccione **Editar**. Desplácese hasta la parte inferior y cambie el valor **userWhitelistedIpRanges** de **null** a algo parecido a lo siguiente. Use las direcciones que desea establecer como el intervalo de direcciones de salida. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Seleccione **PUT** en la parte superior. Esta opción desencadena una operación de escalado en App Service Environment y ajusta el firewall.
 
3. Cree o edite una tabla de rutas y rellene las reglas para permitir el acceso a o desde las direcciones de administración que se asignan a la ubicación de su instancia de App Service Environment. Para encontrar las direcciones de administración, consulte [Direcciones de administración de App Service Environment][management].

4. Ajuste las rutas que se aplican a la subred de App Service Environment con una tabla de rutas o con rutas de BGP. 

Si App Service Environment deja de responder desde el portal, hay un problema con los cambios. El problema podría ser que la lista de direcciones de salida esté incompleta o que el tráfico se haya perdido o bloqueado. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Creación de una nueva instancia de App Service Environment con una dirección de salida diferente ###

Si la red virtual ya está configurada para forzar la tunelización de todo el tráfico, debe realizar pasos adicionales para crear su instancia de App Service Environment de modo que pueda mostrarse correctamente. Durante la creación de App Service Environment, deberá habilitar el uso de otro punto de conexión de salida. Para ello, debe crear la instancia de App Service Environment con una plantilla que especifique las direcciones de salida permitidas.

1. Obtenga las direcciones IP que se usarán como direcciones de salida para App Service Environment.

2. Cree previamente la subred que se va a usar con App Service Environment. La necesitará para que pueda establecer rutas y también porque lo requiere la plantilla.

3. Cree una tabla de rutas con las direcciones IP de administración que se asignan a la ubicación de App Service Environment. Asígnela a su instancia de App Service Environment.

4. Siga las instrucciones que se indican en [Creación de una instancia de App Service Environment con una plantilla][template]. Descargue la plantilla adecuada.

5. Edite la sección "resources" del archivo azuredeploy.json. Incluya una línea para **userWhitelistedIpRanges** con sus valores, como:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Si esta sección está configurada correctamente, App Service Environment debe iniciarse sin problemas. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
