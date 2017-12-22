---
title: "Ejemplo de red perimetral de Azure: creación de una red perimetral simple con grupos de seguridad de red | Microsoft Docs"
description: "Creación de una red perimetral con grupos de seguridad de red"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Ejemplo 1: Crear una red perimetral simple mediante NSG con una plantilla de Azure Resource Manager
[Volver a la página de procedimientos recomendados de límites de seguridad][HOME]

> [!div class="op_single_selector"]
> * [Plantilla de Resource Manager](virtual-networks-dmz-nsg.md)
> * [Clásico: PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

En este ejemplo se crea una red perimetral primitiva con cuatro servidores Windows y grupos de seguridad de red. También se describe cada uno de las secciones de plantilla pertinentes para que comprenda mejor cada paso. Hay también una sección llamada "Escenario de tráfico" para proporcionar información detallada paso a paso de cómo pasa el tráfico a través de los niveles de defensa de la red perimetral. Por último, en la sección de referencias está el código de plantilla completo e instrucciones para crear este entorno para probar y experimentar con diferentes escenarios. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Red perimetral de entrada con grupo de seguridad de red][1]

## <a name="environment-description"></a>Descripción del entorno
En este ejemplo, una suscripción contiene los siguientes recursos:

* Un único grupo de recursos
* Una red virtual con dos subredes (FrontEnd y BackEnd)
* Un grupo de seguridad de red que se aplica a ambas subredes
* Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
* Dos servidores Windows que representan servidores back-end de aplicaciones ("AppVM01", "AppVM02")
* Un servidor Windows que representa un servidor DNS ("DNS01")
* Una dirección IP pública asociada al servidor de aplicaciones web

En la sección de referencias, hay un vínculo a una plantilla de Azure Resource Manager que se basa el entorno descrito en este ejemplo. Aunque la creación de las máquinas y redes virtuales la realiza la plantilla ejemplo, no se describe en detalle en este documento. 

**Para crear este entorno** (las instrucciones detalladas se encuentran en la sección de referencias de este documento), siga estos pasos:

1. Implemente la plantilla de Azure Resource Manager en: [Plantillas de inicio rápido de Azure][Template]
2. Instale la aplicación de ejemplo en: [Script de aplicación de ejemplo][SampleApp]

>[!NOTE]
>Para conectarse mediante RDP a los servidores back-end de esta instancia, el servidor IIS se usa como un equipo de inicio de sesión seguro. Primero, conéctese mediante RDP al servidor IIS y, luego, al servidor back-end. Asimismo, una IP pública se puede asociar con cada servidor NIC para facilitar la conexión RDP.
> 
>

Las secciones siguientes le acompañan por las líneas clave del script de la plantilla de Azure Resource Manager para ofrecerle una descripción detallada de los grupos de seguridad de red y cómo funcionan en este ejemplo.

## <a name="network-security-groups-nsg"></a>Grupos de seguridad de red (NSG)
En este ejemplo, se crea un grupo de seguridad de red y se cargan después seis reglas. 

>[!TIP]
>Por lo general, debe crear primero las reglas específicas de "Permitir" y, por último, las reglas de "Denegar" más genéricas. La prioridad asignada indica qué reglas se evalúan primero. Una vez que se encuentra el tráfico para aplicar a una regla específica, no se evalúa ninguna otra regla. Se pueden aplicar reglas de grupo de seguridad de red en cualquier dirección, entrante o saliente (desde la perspectiva de la subred).
>
>

De forma declarativa, se compilan las reglas siguientes para el tráfico entrante:

1. Se permite el tráfico DNS interno (puerto 53)
2. Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina virtual
3. Se permite el tráfico HTTP (puerto 80) desde Internet al servidor web (IIS01)
4. Se permite cualquier tráfico (todos los puertos) desde IIS01 a AppVM1.
5. Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (ambas subredes).
6. Se deniega todo el tráfico (todos los puertos) desde la subred front-end a la subred back-end.

Con estas reglas enlazadas a cada subred, si una solicitud HTTP era entrante desde Internet al servidor web, se aplicarían ambas reglas, la 3 (permitir) y la 5 (denegar), pero como la regla 3 tiene mayor prioridad, solo se aplicaría esa y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al servidor web. Si ese mismo tráfico estaba intentando ponerse en contacto con el servidor DNS01, se aplicaría primero la regla 5 (denegar) y no se permitiría que el tráfico pase al servidor. La regla 6 (denegar) impide que la subred front-end se comunique con la subred back-end (excepto el tráfico permitido en las reglas 1 y 4); este conjunto de reglas protege la red back-end en el caso de que un atacante ponga en peligro la aplicación web en el front-end, y el atacante tendría acceso limitado a la red back-end "protegida" (solo a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para aplicar la directiva de seguridad en ambas direcciones, es necesario el enrutamiento definido por el usuario; esto se verá en el "Ejemplo 3" de la [página de procedimientos recomendados de límites de seguridad][HOME].

Cada regla se explica con más detalle a continuación:

1. Hay que crear instancias en un recurso de grupo de seguridad de red para contener las reglas:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. En este ejemplo, la primera regla permite el tráfico DNS entre todas las redes internas al servidor DNS en la subred back-end. La regla tiene algunos parámetros importantes:
  * Las reglas "destinationAddressPrefix" pueden usar un tipo especial de prefijo de dirección denominado "Etiqueta predeterminada". Estas etiquetas son identificadores que proporciona el sistema para incluir de manera más sencilla una categoría mayor de prefijos de dirección. Esta regla utiliza la etiqueta predeterminada "Internet" para indicar cualquier dirección fuera de la red virtual. Otras etiquetas de prefijo son VirtualNetwork y AzureLoadBalancer.
  * "Direction" indica en qué dirección del flujo de tráfico esta entrada surte efecto. La dirección se entiende desde la perspectiva de la subred o máquina virtual (depende de donde esté enlazado este NSG). Por lo tanto, si Direction es "Inbound" y el tráfico está entrando en la subred, la regla se aplicará y tráfico que sale de la subred no se vería afectado por esta regla.
  * "Priority" establece el orden de evaluación de un flujo de tráfico. Cuanto menor sea el número de prioridad, mayor será la prioridad de la regla. En cuanto se aplica una regla a un flujo de tráfico específico, no se procesa ninguna otra regla. Por tanto, si una regla con prioridad 1 permite el tráfico y una regla de prioridad 2 deniega el tráfico y ambas reglas se aplican al tráfico, se permitirá que el tráfico fluya (como la regla 1 tenía una prioridad más alta, entró en vigor y no se aplicó ninguna otra regla).
  * "Access" indica si se bloquea ("Deny") o se permite ("Allow") el tráfico al que afecta esta regla.

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. Esta regla permite que el tráfico RDP fluya desde Internet al puerto RDP en cualquier servidor de la subred enlazada. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Esta regla permite que el tráfico entrante de Internet llegue al servidor web. Esta regla no cambia el comportamiento de enrutamiento. La regla solo permite el paso del tráfico destinado a IIS01. Por lo tanto, si el tráfico de Internet tenía el servidor web como destino, esta regla lo permitiría y detendría el procesamiento de más reglas. (En la regla con prioridad 140, se bloquea todo el tráfico entrante de Internet restante). Si solo está procesando tráfico HTTP, esta regla se podría restringir aún más para permitir solo el puerto 80 de destino.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Esta regla permite que el tráfico pase desde el servidor IIS01 al servidor AppVM01, una regla posterior bloqueará todo el tráfico de front-end a back-end restante. Para mejorar esta regla, se debe agregar el puerto si se conoce. Por ejemplo, si el servidor IIS solo accede a SQL Server en AppVM01, el intervalo de puertos de destino debe cambiarse de "*" (cualquiera) a 1433 (puerto SQL), lo que deja una superficie de ataque entrante menor en AppVM01 en caso de que la aplicación web se vea comprometida.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Esta regla deniega el tráfico desde Internet a cualquiera de los servidores de la red. Con las reglas con prioridad 110 y 120, se permite solo el tráfico entrante de Internet al firewall y los puertos RDP en servidores, y se bloquea todo lo demás. Esta regla está configurada "a prueba de errores" para bloquear todos los flujos inesperados.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. La regla final deniega el tráfico desde la subred front-end a la subred back-end. Puesto que se trata de una regla solo entrante, se permite el tráfico inverso (desde el back-end al front-end).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Escenarios de tráfico
#### <a name="allowed-internet-to-web-server"></a>(*Permitido*) Internet a servidor web
1. Un usuario de Internet solicita una página HTTP de la dirección IP pública de la NIC asociada con la NIC IIS01.
2. La dirección IP pública pasa el tráfico a la red virtual hacia IIS01 (el servidor web)
3. La subred front-end comienza el procesamiento de las reglas de entrada:
  1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2. No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  3. Se aplica la regla 3 (Internet a IIS01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4. El tráfico llega a la dirección IP interna del servidor web IIS01 (10.0.1.5).
5. IIS01 escucha el tráfico web, recibe esta solicitud y comienza a procesarla.
6. IIS01 pide información al servidor SQL Server en AppVM01
7. No hay reglas de salida en la subred front-end, se permite el tráfico.
8. La subred back-end comienza el procesamiento de las reglas de entrada:
  1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2. No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  3. No se aplica la regla 3 (Internet a firewall) de grupo de seguridad de red, pasar a la regla siguiente.
  4. No se aplica la regla 4 (IIS01 a AppVM01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
9. AppVM01 recibe la consulta SQL y responde.
10. Como no hay ninguna regla de salida en la subred back-end, se permite la respuesta.
11. La subred front-end comienza el procesamiento de las reglas de entrada:
  1. No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
  2. La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
12. El servidor IIS recibe la respuesta SQL y completa la respuesta HTTP y la envía al solicitante.
13. Puesto que no hay reglas de salida en la subred front-end, se permite la respuesta y el usuario de Internet recibe la página web solicitada.

#### <a name="allowed-rdp-to-iis-server"></a>(*Permitido*) RDP al servidor IIS
1. Un administrador del servidor en Internet solicita una sesión RDP en IIS01 en la dirección IP pública de la NIC asociada con la NIC IIS01 (se puede encontrar esta dirección IP pública a través del Portal o PowerShell)
2. La dirección IP pública pasa el tráfico a la red virtual hacia IIS01 (el servidor web)
3. La subred front-end comienza el procesamiento de las reglas de entrada:
  1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2. Se aplica la regla 2 (RDP) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4. No hay reglas de salida, se aplican las reglas predeterminadas y se permite el tráfico de retorno.
5. La sesión RDP está habilitada.
6. IIS01 solicita el nombre de usuario y la contraseña.

>[!NOTE]
>Para conectarse mediante RDP a los servidores back-end de esta instancia, el servidor IIS se usa como un equipo de inicio de sesión seguro. Primero, conéctese mediante RDP al servidor IIS y, luego, al servidor back-end.
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Permitido*) Búsqueda de DNS del servidor web en el servidor DNS
1. El servidor web, IIS01, necesita una fuente de datos en www.data.gov, pero debe resolver la dirección.
2. La configuración de red de la red virtual incluye DNS01 (10.0.2.4 en la subred back-end) como servidor DNS principal, IIS01 envía la solicitud DNS a DNS01.
3. No hay reglas de salida en la subred front-end, se permite el tráfico.
4. La subred back-end comienza el procesamiento de las reglas de entrada:
  * Se aplica la regla 1 (DNS) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
5. El servidor DNS recibe la solicitud.
6. El servidor DNS no tiene la dirección en caché y solicita un servidor DNS raíz en Internet.
7. No hay reglas de salida en la subred back-end, se permite el tráfico.
8. El servidor DNS de Internet responde, puesto que esta sesión se inició internamente, se permite la respuesta.
9. El servidor DNS almacena en caché la respuesta y responde a la solicitud inicial a IIS01.
10. No hay reglas de salida en la subred back-end, se permite el tráfico.
11. La subred front-end comienza el procesamiento de las reglas de entrada:
  1. No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
  2. La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
12. IIS01 recibe la respuesta de DNS01.

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitido*) Archivo de acceso del servidor web en AppVM01
1. IIS01 solicita un archivo en AppVM01
2. No hay reglas de salida en la subred front-end, se permite el tráfico.
3. La subred back-end comienza el procesamiento de las reglas de entrada:
  1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2. No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  3. No se aplica la regla 3 (Internet a IIS01) de grupo de seguridad de red, pasar a la regla siguiente.
  4. No se aplica la regla 4 (IIS01 a AppVM01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4. AppVM01 recibe la solicitud y responde con el archivo (suponiendo que se autoriza el acceso).
5. Como no hay ninguna regla de salida en la subred back-end, se permite la respuesta.
6. La subred front-end comienza el procesamiento de las reglas de entrada:
  1. No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
  2. La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
7. El servidor IIS recibe el archivo.

#### <a name="denied-rdp-to-backend"></a>(*Denegado*) RDP a back-end
1. Un usuario de Internet trata de conectarse por RDP al servidor AppVM01.
2. Dado que no hay ninguna dirección IP pública asociada a esta NIC de servidores, este tráfico nunca escribiría la red virtual y no tendría acceso al servidor.
3. Sin embargo, si se habilitó una dirección IP pública por algún motivo, la regla de NSG 2 (RDP) permitiría este tráfico.

>[!NOTE]
>Para conectarse mediante RDP a los servidores back-end de esta instancia, el servidor IIS se usa como un equipo de inicio de sesión seguro. Primero, conéctese mediante RDP al servidor IIS y, luego, al servidor back-end.
>
>

#### <a name="denied-web-to-backend-server"></a>*(Denegado*) Web a servidor back-end
1. Un usuario de Internet trata de obtener acceso a un archivo en AppVM01.
2. Dado que no hay ninguna dirección IP pública asociada a esta NIC de servidores, este tráfico nunca escribiría la red virtual y no tendría acceso al servidor.
3. Si se habilitó una dirección IP pública por algún motivo, la regla de NSG 5 (RDP) bloquearía este tráfico.

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Denegado*) Búsqueda de DNS web en el servidor DNS
1. Un usuario de Internet trata de buscar un registro DNS interno en DNS01.
2. Dado que no hay ninguna dirección IP pública asociada a esta NIC de servidores, este tráfico nunca escribiría la red virtual y no tendría acceso al servidor.
3. Si se habilitó una dirección IP pública por algún motivo, la regla de NSG 5 (Internet a red virtual) bloquearía este tráfico. Nota: Esa regla 1 (DNS) no se aplicaría porque la dirección de origen de las solicitudes es Internet y la regla 1 solo se aplica a la red virtual local como el origen.

#### <a name="denied-sql-access-on-the-web-server"></a>(*Denegado*) Acceso a SQL en el servidor web
1. Un usuario de Internet solicita datos SQL de IIS01
2. Dado que no hay ninguna dirección IP pública asociada a esta NIC de servidores, este tráfico nunca escribiría la red virtual y no tendría acceso al servidor.
3. Si se habilitó la dirección IP pública por algún motivo, la subred front-end comenzaría el procesamiento de las reglas de entrada:
  1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2. No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  3. Se aplica la regla 3 (Internet a IIS01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4. El tráfico llega a dirección IP interna del IIS01 (10.0.1.5).
5. IIS01 no está escuchando en el puerto 1433, por lo que no hay respuesta a la solicitud.

## <a name="conclusion"></a>Conclusión
Este ejemplo representa una manera relativamente sencilla y directa de aislar la subred back-end del tráfico entrante.

Puede encontrar más ejemplos y una descripción general de los límites de seguridad de red [aquí][HOME].

## <a name="references"></a>Referencias
### <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure
En este ejemplo se usa una plantilla de Azure Resource Manager predefinida en un repositorio GitHub que mantiene Microsoft y está abierto a la comunidad. Esta plantilla puede implementarse directamente desde GitHub, o bien descargarse y modificarse para ajustarla a sus necesidades. 

La plantilla principal está en el archivo denominado "azuredeploy.json". Esta plantilla puede enviarse a través de PowerShell o la CLI (con el archivo asociado "azuredeploy.parameters.json") para implementar esta plantilla. Opino que la manera más fácil es usar el botón "Implementar en Azure" de la página de README.md de GitHub.

Para implementar la plantilla que crea este ejemplo de GitHub y Azure Portal, siga estos pasos:

1. En un explorador, acceda a la [plantilla][Template].
2. Haga clic en el botón "Implementar en Azure" (o el botón "Ver" para observar una representación gráfica de esta plantilla).
3. Escriba la cuenta de almacenamiento, el nombre de usuario y la contraseña en la hoja Parámetros; luego, haga clic en **Aceptar**.
5. Cree un grupo de recursos para esta implementación (puede usar uno existente, pero se recomienda una nueva contraseña para obtener los mejores resultados).
6. Si es necesario, cambie la configuración de Suscripción y Ubicación de la red virtual.
7. Haga clic en **Revisar los términos legales**, léalos y haga clic en **Comprar** para aceptar.
8. Haga clic en **Crear** para iniciar la implementación de esta plantilla.
9. Cuando se termine correctamente la implementación, navegue hasta el grupo de recursos creado para esta implementación para ver los recursos configurados dentro.

>[!NOTE]
>Esta plantilla habilita RDP solamente en el servidor IIS01 (busque la dirección IP pública de IIS01 en el Portal). Para conectarse mediante RDP a los servidores back-end de esta instancia, el servidor IIS se usa como un equipo de inicio de sesión seguro. Primero, conéctese mediante RDP al servidor IIS y, luego, al servidor back-end.
>
>

Para quitar esta implementación, elimine el grupo de recursos y también se eliminarán todos los recursos secundarios.

#### <a name="sample-application-scripts"></a>Scripts de aplicación de ejemplo
Cuando la plantilla se ejecute correctamente, configure el servidor web y el servidor de aplicaciones con una aplicación web sencilla para permitir las pruebas con esta configuración de red perimetral. Para instalar una aplicación de ejemplo para este y otros ejemplos de red perimetral, hay una en el siguiente vínculo: [Script de aplicación de ejemplo][SampleApp].

## <a name="next-steps"></a>Pasos siguientes

* Implementar este ejemplo
* Compilar la aplicación de ejemplo
* Probar los flujos de tráfico distintos a través de esta red perimetral

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Red perimetral de entrada con grupo de seguridad de red"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md