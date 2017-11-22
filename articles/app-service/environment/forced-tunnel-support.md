---
title: "Configuración de Azure App Service Environment para que use tunelización forzada"
description: "Habilitación de App Service Environment para que funcione cuando el tráfico saliente se produce por tunelización forzada"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configuración de App Service Environment con tunelización forzada

App Service Environment (ASE) es una implementación de Azure App Service en Azure Virtual Network (VNet) de un cliente. Muchos clientes configuran sus redes virtuales para ser extensiones de sus redes locales con redes privadas virtuales o con conexiones ExpressRoute. Debido a las directivas corporativas u otras restricciones de seguridad, configuran las rutas para enviar todo el tráfico saliente local para poder salir a internet. Se conoce como tunelización forzada al cambio del enrutamiento de la red virtual para que el tráfico saliente de la misma fluya a través de la conexión ExpressRoute o VPN a un entorno local.  

La tunelización forzada puede causar problemas para un ASE. El ASE tiene varias dependencias externas, que se enumeran en este documento: [Arquitectura de red de ASE] [ network]. De forma predeterminada, el ASE requiere que todas las comunicaciones salientes pasen a través de la dirección VIP que se aprovisiona con el ASE.

Las rutas constituyen un aspecto crucial del concepto de tunelización forzada y de cómo tratar con ella. En una instancia de Azure Virtual Network, el enrutamiento se realiza basándose en la coincidencia de prefijo más larga (LPM).  Si hay más de una ruta con la misma coincidencia LPM, se selecciona una en función de su origen en el orden siguiente:

1. Ruta definida por el usuario
1. Ruta BGP (cuando se utiliza ExpressRoute)
1. Ruta del sistema

Para obtener más información sobre el enrutamiento en una red virtual, lea [Rutas definidas por el usuario y reenvío IP][routes]. 

Si desea que su ASE funcione en una red virtual de túnel forzado, tiene dos opciones:

1. Habilitar el ASE para que tenga acceso directo a internet
1. Cambiar el punto de conexión de salida para su ASE

## <a name="enable-your-ase-to-have-direct-internet-access"></a>Habilitar el ASE para que tenga acceso directo a internet

Para que el ASE funcione mientras la red virtual se configura con ExpressRoute, puede:

* Configurar ExpressRoute para anunciar 0.0.0.0/0. De forma predeterminada, obliga a dirigir todo el tráfico saliente tráfico local.
* Crear un UDR. Aplicarlo a la subred que contiene el ASE, con un prefijo de dirección de 0.0.0.0/0 y un tipo de próximo salto de Internet.

Si realiza estos dos cambios, el tráfico destinado a Internet, que se origina en la subred ASE, no se forzará hacia ExpressRoute y el ASE funciona.

> [!IMPORTANT]
> Las rutas definidas en una UDR tienen que ser lo suficientemente específicas para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute. En el ejemplo anterior se utiliza el intervalo de direcciones amplio 0.0.0.0/0. Puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.
>
> Las instancias de ASE no son compatibles con las configuraciones de ExpressRoute que anuncian rutas entre la ruta de acceso de emparejamiento público y la ruta de acceso de emparejamiento privado. Las configuraciones de ExpressRoute con el emparejamiento público configurado reciben anuncios de ruta de Microsoft. Los anuncios contienen un gran conjunto de intervalos de direcciones IP de Microsoft Azure. Si estos intervalos de direcciones se anuncian en la ruta de acceso de emparejamiento privado, la tunelización de todos los paquetes de red salientes se debe realizar desde la subred de ASE a la infraestructura de red local de un cliente. De forma predeterminada, actualmente este flujo de red no es compatible con ASE. Una solución a este problema es dejar de anunciar las rutas entre la ruta de acceso de emparejamiento público y la de emparejamiento privado.  La otra solución consiste en habilitar el ASE para trabajar en una configuración de túnel forzado.

## <a name="change-the-egress-endpoint-for-your-ase"></a>Cambiar el punto de conexión de salida para su ASE ##

En esta sección se describe cómo habilitar un ASE para que funcione en una configuración de túnel forzado cambiando el punto de conexión de salida que usa el ASE. Si el tráfico saliente desde el ASE se dirige través de un túnel forzado a una red local, debe permitir el tráfico al origen desde las direcciones IP que no sean la dirección VIP del ASE.

Un ASE no solo tiene dependencias externas, sino que también debe escuchar el tráfico entrante para administrar el ASE. El ASE debe ser capaz de responder a este tipo de tráfico y las respuestas no se pueden enviar desde otra dirección, ya que eso interrumpe el protocolo TCP.  Por lo tanto, hay tres pasos necesarios para cambiar el punto de conexión de salida para el ASE.

1. Establecer una tabla de rutas para asegurarse de que el tráfico entrante de administración puede volver atrás desde la misma dirección IP
1. Agregar sus direcciones IP que se usarán para la salida al firewall ASE
1. Establecer las rutas para el tráfico saliente desde el ASE a un túnel

![Flujo de red en túnel forzado][1]

Puede configurar el ASE con direcciones de salida diferentes una vez que ya esté funcionando y operativo, o se puede establecer durante su implementación.  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>Cambio de la dirección de salida después de que el ASE esté operativo ###
1. Obtenga las direcciones IP que desea utilizar como de salida para su ASE. Si va a realizar la tunelización forzada, se trataría de sus direcciones IP de puerta de enlace o NAT.  Si desea redirigir el tráfico de salida de ASE a través de una NVA, la dirección de salida sería la dirección IP pública de la NVA.
2. Establezca las direcciones de salida en la información de configuración del ASE. Vaya a resource.azure.com y desplácese hasta: Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>; a continuación, puede ver el json que describe su ASE.  Asegúrese de que, en la parte superior, indica lectura/escritura.  Haga clic en Editar, desplácese hacia abajo hasta la parte inferior y cambie userWhitelistedIpRanges de  

       "userWhitelistedIpRanges": null 
      
  a algo similar a lo siguiente. Use las direcciones que desea establecer como el intervalo de direcciones de salida. 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  Haga clic en COLOCAR en la parte superior. Se desencadena una operación de escalado de su ASE y se ajusta el firewall.
   
3. Cree o edite una tabla de rutas y rellene las reglas para permitir el acceso a o desde las direcciones de administración que se asignan a la ubicación de su ASE.  Las direcciones de administración están aquí: [Direcciones de administración de App Service Environment][management] 

4. Ajuste las rutas que se aplican a la subred ASE con una tabla de ruta o con rutas BGP.  

Si el ASE deja de responder desde el portal, hay un problema con los cambios.  Puede ser que la lista de direcciones de salida esté incompleta, el tráfico se haya perdido o se haya bloqueado el tráfico.  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>Creación de un nuevo ASE con una dirección de salida diferente  ###

En caso de que la red virtual ya esté configurada para que todo el tráfico se dirija a través de túnel forzado, debe realizar algunos pasos adicionales para crear su ASE de forma que pueda salir correctamente. Esto significa que necesita habilitar el uso del otro punto de conexión de salida durante la creación del ASE.  Para ello, debe crear el ASE con una plantilla que especifique las direcciones de salida permitidas.

1. Obtenga las direcciones IP que se usarán como direcciones de salida para su ASE.
1. Cree previamente la subred que el ASE vaya a usar. Esto es necesario para permitirle definir rutas y también porque lo requiere la plantilla.  
1. Cree una tabla de rutas con las direcciones IP de administración que se asignan a la ubicación de su ASE y asígnesela a este
1. Siga las instrucciones que aparecen aquí, [Creación de un ASE con una plantilla][template] y despliegue la plantilla adecuada
1. Edite la sección "resources" (recursos) del archivo azuredeploy.json. Incluya una línea para **userWhitelistedIpRanges** con sus valores, como:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Si está configurado correctamente, el ASE debe iniciarse sin ningún problema.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
