---
title: Configuración de la conectividad de nube híbrida con Azure y Azure Stack | Microsoft Docs
description: Aprenda a configurar la conectividad de nube híbrida con Azure y Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 048e2636aabe406728c8fe1b93ef861f13346256
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutorial: Configuración de la conectividad de nube híbrida con Azure y Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede acceder a recursos con seguridad en Azure global y Azure Stack mediante el patrón de conectividad híbrida. 

En este tutorial, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Mantener datos localmente de cara a los requisitos de seguridad o normativos, pero tendrá acceso a los recursos de Azure global.
> - Mantener un sistema heredado mientras se usan recursos e implementaciones de aplicaciones escaladas en la nube en Azure global.

## <a name="prerequisites"></a>requisitos previos

Se requieren algunos componentes para crear una implementación de conectividad híbrida, que pueden tardar algún tiempo en prepararse. 

Un asociado de hardware u OEM de Azure puede implementar una instancia de Azure Stack de producción y todos los usuarios pueden implementar un ASDK. Además, un operador de Azure Stack debe implementar la instancia de App Service, crear planes y ofertas, crear una suscripción de inquilino y agregar la imagen de Windows Server 2016. 

Si ya tiene algunos de estos componentes, asegúrese de que cumplen los requisitos antes de comenzar.

En este tema también se da por supuesto que tiene algunos conocimientos de Azure y Azure Stack. Si desea más información antes de continuar, asegúrese de empezar con estos temas:
 - [Introducción a Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Conceptos clave de Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
 - Cree una [aplicación web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) en Azure. Tome nota de la dirección URL de la nueva aplicación web, ya que se utiliza más adelante.

### <a name="azure-stack"></a>Azure Stack
 - Use su instancia de Azure Stack de producción o implemente el Kit de desarrollo de Azure Stack desde el siguiente vínculo:
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - La instalación suele tardar unas horas, de modo que planéela en consecuencia.
 - Implemente servicios PaaS de [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) para Azure Stack. 
 - Cree [planes u ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dentro del entorno de Azure Stack. 
 - [Cree una suscripción de inquilino](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro del entorno de Azure Stack. 


### <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que se cumplen los criterios siguientes:

 - Compruebe que tiene una dirección IPv4 pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
 - Asegúrese de que todos los recursos se implementan en la misma región o ubicación.

#### <a name="example-values"></a>Valores de ejemplo

Los ejemplos de este artículo utilizan los valores siguientes. Puede usar estos valores para crear un entorno de prueba o consultarlos para comprender mejor los ejemplos de este artículo. Para más información sobre la configuración de VPN Gateway en general, consulte [Acerca de la configuración de VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Especificaciones de conexión:
 - **Tipo de VPN:**: basada en rutas
 - **Tipo de conexión:**: de sitio a sitio (IPsec)
 - **Tipo de puerta de enlace:** VPN
 - **Nombre de la conexión de Azure**: Azure-Gateway-AzureStack-S2SGateway (el portal rellenará automáticamente este valor)
 - **Nombre de la conexión de Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (el portal rellenará automáticamente este valor)
 - **Clave compartida**: cualquiera compatible con el hardware de VPN, con valores coincidentes en ambos lados de la conexión.
 - **Suscripción**: cualquier suscripción preferida.
 - **Grupo de recursos**: infraestructura de prueba.

| Conexión de Azure/Azure Stack | NOMBRE | Subred | Dirección IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Red virtual de Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Red virtual de Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Puerta de enlace de red virtual de Azure | Puerta de enlace de Azure |  |  |
| Puerta de enlace de red virtual de Azure Stack | Puerta de enlace de AzureStack |  |  |
| IP pública de Azure | GatewayPublicIP de Azure |  | Se determina durante la creación |
| Dirección IP pública de Azure Stack | AzureStack-GatewayPublicIP |  | Se determina durante la creación |
| Puerta de enlace de red local de Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valor de dirección IP pública de Azure Stack |
| Puerta de enlace de red local de Azure Stack | S2SGateway de Azure<br>10.100.102.0/23 |  | Valor de dirección IP pública de Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Creación de una red virtual en Azure global y Azure Stack

> [!Note]  
> Debe asegurarse de que no hay ninguna superposición de direcciones IP en los espacios de direcciones de red virtual de Azure o Azure Stack. 

Puede crear una red virtual con el modelo de implementación de Resource Manager mediante Azure Portal. Si va a utilizar estos pasos como tutorial, use los [valores de ejemplo](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values). Si no va a hacerlo, asegúrese de reemplazar los valores por los suyos. 

1. Desde un explorador, navegue al [Portal de Azure](http://portal.azure.com/) e inicie sesión con su cuenta de Azure.
2. Haga clic en **Crear un recurso**. En el campo **Buscar en el Marketplace**, escriba `virtual network`. En la lista de resultados, busque **Virtual Network** y haga clic para abrir la página **Virtual Network**.
3. En la parte inferior de la página Virtual Network, en la lista **Seleccionar un modelo de implementación**, seleccione **Resource Manager** y, luego, haga clic en **Crear**. Se abre la página "Crear red virtual".
4. En la página **Crear red virtual**, configure los valores de la red virtual. Al rellenar los campos, el signo de exclamación rojo se convierte en una marca de verificación verde cuando los caracteres escritos en el campo sean válidos.
5. Repita estos pasos desde el **portal del inquilino** de Azure Stack.

## <a name="add-a-gateway-subnet"></a>Adición de una subred de puerta de enlace

Antes de conectar la red virtual a una puerta de enlace, es preciso crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. Los servicios de puerta de enlace usan las direcciones IP especificadas en la subred de puerta de enlace.

En el [portal](http://portal.azure.com/), navegue a la red virtual de Resource Manager para la que desea crear una puerta de enlace de red virtual.

1. En la sección **Configuración** de la página de redes virtuales, seleccione **Subredes** para expandir la página **Subredes**.
2. En la página **Subredes**, haga clic en **+Subred de puerta de enlace** para abrir la página **Agregar subred**.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. El **nombre** de la subred se rellena automáticamente con el valor "GatewaySubnet". Este valor es necesario para que Azure reconozca que se trata de subred de puerta de enlace. Ajuste los valores de **Intervalo de direcciones** que se rellenan automáticamente para que coincidan con sus requisitos de configuración y seleccione **Aceptar** en la parte inferior de la página para crear la subred.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Creación de una puerta de enlace de red virtual en Azure y Azure Stack.

1. En el lado izquierdo de la página del portal, seleccione + y escriba "Puerta de enlace de red virtual" en el cuadro de búsqueda. En **Resultados**, busque y seleccione **Puerta de enlace de red virtual**.
2. En la parte inferior de la página **Puerta de enlace de red virtual**, seleccione **Crear**. Se abre la página **Crear puerta de enlace de red virtual**.
3. En la página **Crear puerta de enlace de red virtual**, especifique los valores de la puerta de enlace de red virtual, como se detalla en los valores del ejemplo y en los valores adicionales que se detallan a continuación.
    - **SKU**: básica.
    - **Virtual Network**: seleccione la red virtual que creó anteriormente. Se seleccionará automáticamente la subred de puerta de enlace que creó anteriormente. 
    - **Primera configuración de IP**: se trata de la dirección IP pública de la puerta de enlace. 
        - Haga clic en **Crear configuración de IP de puerta de enlace** para abrir la página **Elegir dirección IP pública**.
        - Haga clic en **+Crear nueva** para abrir la página **Crear dirección IP pública**.
        - Escriba un nombre para la dirección IP pública. Deje la SKU como **Básica** y, luego, seleccione **Aceptar** en la parte inferior de esta página para guardar los cambios.

    > [!Note]  
    > Actualmente, VPN Gateway solo admite la asignación de direcciones IP públicas dinámicas. Sin embargo, esto no significa que la dirección IP cambia después de que se ha asignado a una puerta de enlace VPN. La única vez que la dirección IP pública cambia es cuando la puerta de enlace se elimina y se vuelve a crear. No cambia cuando se cambia el tamaño, se restablece o se realizan actualizaciones u otras operaciones de mantenimiento interno de una puerta de enlace VPN.

4. Compruebe la configuración. 
5. Haga clic en **Crear** para empezar a crear la puerta de enlace de VPN. Se validará la configuración y verá el icono "Implementación de la puerta de enlace de red virtual" en el panel. La creación de una puerta de enlace puede tardar hasta 45 minutos. Es posible que tenga que actualizar la página de portal para ver el estado completado.

    Una vez creada la puerta de enlace, puede ver la dirección IP que se le ha asignado consultando la red virtual en el portal. La puerta de enlace aparece como un dispositivo conectado. Para más información, puede seleccionar el dispositivo conectado (la puerta de enlace de red virtual).
6. Repita estos pasos en la implementación de Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Creación de la puerta de enlace de red virtual local Azure y Azure Stack

La puerta de enlace de red local suele hacer referencia a la ubicación local. Asigne al sitio un nombre al que Azure o Azure Stack puedan hacer referencia y, luego, especifique la dirección IP del dispositivo VPN local con la que creará una conexión. Especifique también los prefijos de dirección IP que se enrutarán a través de la puerta de enlace VPN al dispositivo VPN. Los prefijos de dirección que especifique son los prefijos que se encuentran en la red local. Si la red local cambia o necesita cambiar la dirección IP pública del dispositivo VPN, puede actualizar fácilmente los valores más adelante.

1. En el portal, seleccione **+Crear un recurso**.
2. En el cuadro de búsqueda, escriba **Puerta de enlace de red local** y, luego, presione **Entrar** para realizar la búsqueda. Se devolverá una lista de resultados. Haga clic en **Puerta de enlace de red local** y seleccione el botón **Crear** para abrir la página **Crear puerta de enlace de red local**.
3. En la página **Crear puerta de enlace de red local**, especifique los valores de la puerta de enlace de red local, como se detalla en nuestros valores de ejemplo y en los valores adicionales que se detallan a continuación.
    - **Dirección IP:** es la dirección IP pública del dispositivo VPN al que desea que Azure o Azure Stack se conecten. Especifique una dirección IP pública válida. La dirección IP no puede encontrarse detrás de NAT y Azure debe poder acceder a ella. Si no tiene la dirección IP en este momento, puede usar los valores que se muestran en el ejemplo, pero deberá volver y reemplazar la dirección IP del marcador de posición por la dirección IP pública de su dispositivo VPN. Si no lo hace, Azure no podrá conectarse.
    - **Espacio de direcciones** hace referencia a los intervalos de direcciones de la red que representa esta red local. Puede agregar varios intervalos de espacios de direcciones. Asegúrese de que los intervalos que especifique aquí no se superpongan con los de otras redes a las que quiera conectarse. Azure enrutará el intervalo de direcciones que especifique a la dirección IP del dispositivo VPN local. Use aquí sus propios valores, y no los mostrados en el ejemplo, si quiere conectarse a su sitio local.
    - **Configurar BGP:**: solo se debe usar al configurar BGP. En caso contrario, no seleccione esta opción.
    - **Suscripción:**: compruebe que se muestra la suscripción correcta.
    - **Grupo de recursos:**: seleccione el grupo de recursos que desea utilizar. Puede crear un grupo de recursos nuevo o seleccionar uno ya creado.
    - **Ubicación:**: seleccione la ubicación en la que se creará este objeto. Puede seleccionar la misma ubicación en la que reside la red, pero no es obligatorio.
4. Cuando haya terminado de especificar los valores, seleccione el botón **Crear** en la parte inferior de la página para crear la puerta de enlace de red local.
5. Repita estos pasos en la implementación de Azure Stack.

## <a name="configure-your-connection"></a>Configuración de la conexión

Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. En este paso, configurará el dispositivo VPN, que se conoce como conexión. Al configurar la conexión, necesitará lo siguiente:
    - Una clave compartida. Se trata de la misma clave compartida que se especifica al crear la conexión VPN de sitio a sitio. En estos ejemplos se utiliza una clave compartida básica. Se recomienda que genere y utilice una clave más compleja.
    - La dirección IP pública de la puerta de enlace de red virtual. Puede ver la dirección IP pública mediante Azure Portal, PowerShell o la CLI. Para buscar la dirección IP pública de la puerta de enlace de VPN mediante Azure Portal, vaya a las puertas de enlace de red virtual y seleccione el nombre de su puerta de enlace.
Creación de la conexión VPN de sitio a sitio entre la puerta de enlace de la red virtual y el dispositivo VPN local.
1. En el portal, seleccione **+Crear un recurso**.
2. En el cuadro de búsqueda, escriba **Conexiones** y, luego, presione **Entrar** para realizar la búsqueda. Se devolverá una lista de resultados. Haga clic en **Conexiones** y, luego, seleccione el botón Crear para abrir la página **Crear conexiones**.
3. En la página **Crear conexiones**, configure los valores de la conexión.
     - Aspectos básicos:
        1. **Tipo de conexión**: seleccione Sitio a sitio (IPsec).
        2. **Grupo de recursos**: (seleccione el grupo de recursos de prueba).
     - Configuración:
        1. **Puerta de enlace de red virtual**: seleccione la puerta de enlace de red virtual que creó anteriormente.
        2. **Puerta de enlace de red local**: seleccione la puerta de enlace de red local que creó anteriormente. 
        3. **Nombre de la conexión**: este valor se rellena automáticamente con los valores de las dos puertas de enlace. 
        4. **Clave compartida**: este valor debe ser el mismo que el que usa para el dispositivo VPN local. En el ejemplo se usa "abc123", pero puede (y debería) utilizar algo más complejo. Lo importante es que el valor que especifique aquí debe ser el mismo que el que se especificó al configurar el dispositivo VPN.
    - Los restantes valores de **Suscripción**, **Grupo de recursos** y **Ubicación** son fijos.
4. Haga clic en **Aceptar** para crear la conexión. El mensaje Creando la conexión aparecerá de forma intermitente en la pantalla.
5. La conexión se puede ver en la página **Conexiones de la puerta de enlace de red virtual. El estado pasará de **Desconocido** a **Conectando** y luego a **Correcto**.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
