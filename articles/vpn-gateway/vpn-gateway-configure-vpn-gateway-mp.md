---
title: "Configuración de VPN Gateway: Portal de Azure clásico | Microsoft Docs"
description: "Este artículo le lleva por la configuración de la puerta de enlace de VPN de red virtual y el cambio de un tipo de enrutamiento de VPN de puerta de enlace. Estos pasos corresponden al modelo de implementación clásica y el Portal de Azure clásico."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fbe59ba8-b11f-4d21-9bb1-225ec6c6d351
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 2ea4e6bb86b1ba6f7b501b193d0713d3901457af
ms.lasthandoff: 04/06/2017


---
# <a name="configure-a-vpn-gateway-in-the-classic-portal"></a>Configuración de VPN Gateway en el Portal de Azure clásico 
Si quiere crear una conexión segura entre locales segura entre Azure y su ubicación local, debe crear una puerta de enlace de red virtual. Una puerta de enlace de VPN es un tipo de puerta de enlace de red virtual. En el modelo de implementación clásica, una puerta de enlace de VPN puede ser uno de dos tipos de enrutamiento de VPN: estático o dinámico. El tipo de VPN que elija depende tanto del plan de diseño de la red como del dispositivo VPN local que quiera usar. Para obtener más información sobre dispositivos VPN, consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md).

**Información acerca de los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-overview"></a>Información general sobre la configuración
Los siguientes pasos lo llevan por la configuración de una puerta de enlace de VPN en el Portal de Azure clásico. Estos pasos se aplican a las puertas de enlace de redes virtuales que se crearon con el modelo de implementación clásica. Actualmente, no todos los valores de configuración de puertas de enlace están disponibles en el Portal de Azure. Cuando lo estén, crearemos un nuevo conjunto de instrucciones que se aplicarán al Portal de Azure.

### <a name="before-you-begin"></a>Antes de empezar
Antes de configurar la puerta de enlace, primero deberá crear la red virtual. Vea los pasos para crear una red virtual para la conectividad entre locales en [Creación de una red virtual con una conexión de sitio a sitio mediante el Portal de Azure clásico](vpn-gateway-site-to-site-create.md) o [Configuración de una conexión de punto a sitio a una red virtual mediante el portal clásico](vpn-gateway-point-to-site-create.md). A continuación, utilice los pasos siguientes para configurar la puerta de enlace de VPN y recopilar la información necesaria para configurar el dispositivo VPN. 

Si ya tiene una puerta de enlace de VPN y desea cambiar el tipo de enrutamiento, consulte [Cambio del tipo de enrutamiento de puerta de enlace de VPN](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Creación de una puerta de enlace de VPN
1. En la página **Redes** del [Portal de Azure clásico](https://manage.windowsazure.com), compruebe que la columna de estado de la red virtual muestra **Creado**.
2. En la columna **Nombre** , haga clic en el nombre de la red virtual.
3. En la página **Panel** , observe que esta red virtual aún no tiene configurada ninguna puerta de enlace. Verá que este estado cambia a medida que avance por los pasos para configurar la puerta de enlace.

![Puerta de enlace no creada](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)

A continuación, en la parte inferior de la página, haga clic en **Crear puerta de enlace**. Puede seleccionar *Enrutamiento estático* o *Enrutamiento dinámico*. El tipo de enrutamiento de VPN que seleccione depende de algunos factores. Por ejemplo, del dispositivo VPN que será compatible y de si será necesario admitir conexiones de punto a sitio. Consulte [Acerca de los dispositivos VPN para la conectividad de redes virtuales](vpn-gateway-about-vpn-devices.md) para comprobar el tipo de enrutamiento de VPN que necesita. Una vez creada la puerta de enlace, no se puede cambiar entre los tipos de enrutamiento VPN de puerta de enlace sin eliminar y volver a crear la puerta de enlace. Cuando el sistema le pida confirmación de que desea crear la puerta de enlace, haga clic en **SÍ**.

![Tipo de enrutamiento VPN de puerta de enlace](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Una vez creada la puerta de enlace, verá que el gráfico de la puerta de enlace de la página cambia a amarillo e indica *Creando puerta de enlace*. La puerta de enlace puede tardar hasta 45 minutos en crearse. Espere a que la puerta de enlace se haya completado antes de avanzar con otras opciones de configuración.

![Creación de una puerta de enlace](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Cuando la puerta de enlace cambia a *Conectando*, puede recopilar la información que necesitará para el dispositivo VPN.

![Conexión de una puerta de enlace](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="site-to-site-connections"></a>Conexiones de sitio a sitio

### <a name="step-1-gather-information-for-your-vpn-device-configuration"></a>Paso 1. Recopilación de información para la configuración del dispositivo VPN
Si va a crear una conexión de sitio a sitio, una vez que se cree la puerta de enlace, recopile la información de la configuración del dispositivo VPN. Esta información se encuentra en la página **Panel** de la red virtual:

1. **Dirección IP de puerta de enlace:** la dirección IP se encuentra en la página **Panel**. No podrá verla hasta después de que ha terminado de crear la puerta de enlace.
2. **Clave compartida:** haga clic en el botón **Administrar clave**, en la parte inferior de la pantalla. Haga clic en el icono situado junto a la clave para copiarlo en el Portapapeles y, a continuación, pegue y guarde la clave. Este botón solo funciona cuando hay un solo túnel VPN S2S. Si tiene varios túneles de VPN S2S, utilice la API *Get Virtual Network Gateway Shared Key* o el cmdlet de PowerShell.

![Administrar claves](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)

### <a name="step-2--configure-your-vpn-device"></a>Paso 2:  Configurar el dispositivo VPN
Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. Aunque no se proporcionan todos los pasos de configuración para todos los dispositivos VPN, se puede encontrar información en los siguientes vínculos útiles:

- Para obtener más información sobre dispositivos VPN compatibles, vea [Dispositivos VPN](vpn-gateway-about-vpn-devices.md). 
- Para obtener vínculos a los valores de configuración de dispositivo, vea [Dispositivos VPN validados](vpn-gateway-about-vpn-devices.md#devicetable). Estos vínculos se proporcionan dentro de lo posible. Siempre es mejor ponerse en contacto con el fabricante del dispositivo para obtener la información de configuración más reciente.
- Para obtener información sobre cómo modificar los ejemplos de configuración de dispositivo, vea [Edición de ejemplos](vpn-gateway-about-vpn-devices.md#editing).
- Para los parámetros de IPsec/IKE, vea [Parámetros](vpn-gateway-about-vpn-devices.md#ipsec).
- Antes de configurar el dispositivo VPN, compruebe si hay [problemas conocidos de compatibilidad de dispositivos](vpn-gateway-about-vpn-devices.md#known) para el dispositivo VPN que desea usar.

Al configurar el dispositivo VPN, necesitará los elementos siguientes:

- La dirección IP pública de la puerta de enlace de red virtual. Para encontrarla, vaya a la hoja **Introducción** de la red virtual.
- Una clave compartida. Se trata de la misma clave compartida que se especifica al crear la conexión VPN de sitio a sitio. En nuestros ejemplos, se utiliza una clave compartida muy básica. Debe generar una clave más compleja para su uso.

Una vez configurado el dispositivo VPN, puede ver la información de conexión actualizada en la página Panel para la red virtual.

### <a name="step-3-verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Paso 3: Verificación de los intervalos de red local y la dirección IP de puerta de enlace de VPN
#### <a name="verify-your-vpn-gateway-ip-address"></a>Verificación de la dirección IP de la puerta de enlace de VPN
Para que la puerta de enlace se conecte correctamente, la dirección IP del dispositivo VPN debe configurarse correctamente para la red local que especificó para la configuración entre entornos. Normalmente, esto se configura durante el proceso de configuración de sitio a sitio. Sin embargo, si anteriormente usó esta red local con un dispositivo diferente o ha cambiado la dirección IP de esta red local, edite la configuración para especificar la dirección IP de la puerta de enlace correcta.

1. Para comprobar la dirección IP de la puerta de enlace, haga clic en **Redes** en el panel izquierdo del portal y seleccione **Redes locales** en la parte superior de la página. Verá la dirección de la puerta de enlace de VPN para cada red local que ha creado. Para modificar la dirección IP, seleccione la red virtual y haga clic en **Editar** en la parte inferior de la página.
2. En la página **Especificación de detalles de la red local** , modifique la dirección IP y, a continuación, haga clic en la flecha siguiente en la parte inferior de la página.
3. En la página **Especificación del espacio de direcciones** , haga clic en la marca de verificación de la parte inferior derecha para guardar la configuración.

#### <a name="verify-the-address-ranges-for-your-local-networks"></a>Verificación de los intervalos de direcciones de sus redes locales
Para que el tráfico correcto fluya por la puerta de enlace hasta la ubicación local, debe comprobar que se especifique cada intervalo de direcciones IP. Cada intervalo debe mostrarse en la configuración de **Redes locales** de Azure. Según la configuración de red de su ubicación local, esta tarea puede ser bastante pesada. A continuación, el tráfico vinculado a una dirección IP que está dentro de los intervalos especificados se enviará a través de la puerta de enlace de VPN de la red virtual. Los intervalos que indique no tienen que ser intervalos privados, aunque deberá comprobar que la configuración local puede recibir el tráfico entrante.

Para agregar o editar los intervalos de una red local, siga estos pasos:

1. Para editar los intervalos de direcciones IP de una red local, haga clic en **Redes** en el panel izquierdo del portal y seleccione **Redes locales** en la parte superior de la página. En el portal, la manera más fácil de ver los intervalos que ha enumerado es en la página **Editar** . Para ver los intervalos, seleccione la red virtual y haga clic en **Editar** en la parte inferior de la página.
2. No realice ningún cambio en la página **Especificación de detalles de la red local** . Haga clic en la flecha siguiente de la parte inferior de la página.
3. En la página **Especificación del espacio de direcciones** , realice los cambios del espacio de direcciones de red. A continuación, haga clic en la marca de verificación para guardar la configuración.

## <a name="how-to-view-gateway-traffic"></a>Visualización del tráfico de la puerta de enlace
Puede ver la puerta de enlace y el tráfico de la puerta de enlace desde la página **Panel** de la red virtual.

En la página **Panel** puede ver lo siguiente:

* La cantidad de datos que fluyen a través de la puerta de enlace, tanto de entrada como de salida.
* Los nombres de los servidores DNS especificados para la red virtual.
* La conexión entre la puerta de enlace y el dispositivo VPN.
* La clave compartida que se utiliza para configurar la conexión de la puerta de enlace para el dispositivo VPN.

## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Cambio del tipo de enrutamiento de puerta de enlace de VPN
Puesto que algunas configuraciones de conectividad solo están disponibles para ciertos tipos de enrutamiento de puerta de enlace, puede que necesite cambiar el tipo de enrutamiento VPN de puerta de enlace de una puerta de enlace VPN existente. Por ejemplo, puede desear agregar conectividad punto a sitio a una conexión de sitio a sitio ya existente que tiene una puerta de enlace estática. Las conexiones de punto a sitio requieren una puerta de enlace dinámica. Esto significa que para configurar una conexión P2S, tendrá que cambiar el tipo de enrutamiento de VPN de puerta de enlace de estático a dinámico.

Si necesita cambiar un tipo de enrutamiento VPN de puerta de enlace, deberá eliminar la puerta de enlace existente y, a continuación, volver a crearla con el nuevo tipo de enrutamiento. No es necesario eliminar toda la red virtual para cambiar el tipo de enrutamiento de la puerta de enlace.

Antes de cambiar el tipo de enrutamiento de VPN de puerta de enlace, asegúrese de comprobar que el dispositivo VPN sea compatible con el tipo de enrutamiento que quiere usar. Para descargar nuevos ejemplos de configuración de enrutamiento y comprobar los requisitos del dispositivo VPN, consulte [Acerca de los dispositivos VPN para la conectividad de redes virtuales](vpn-gateway-about-vpn-devices.md).

> [!IMPORTANT]
> Cuando se elimina una puerta de enlace de VPN de red virtual, se libera la VIP asignada a la puerta de enlace. Cuando vuelva a crear la puerta de enlace, se la asignará una nueva VIP.
> 
> 

1. **Elimine la puerta de enlace VPN existente.**
   
    En la página **Panel** de la red virtual, desplácese a la parte inferior de la página y haga clic en **Eliminar puerta de enlace**. Espere la notificación de que se ha eliminado la puerta de enlace. Cuando reciba una notificación en la pantalla de que se ha eliminado la puerta de enlace, podrá crear una nueva puerta de enlace.
2. **Cree una puerta de enlace de VPN.**
   
    Utilice el procedimiento que aparece en la parte superior de la página para crear una nueva puerta de enlace: [Creación de una puerta de enlace de VPN](#create-a-vpn-gateway).

## <a name="next-steps"></a>Pasos siguientes
Puede agregar máquinas virtuales a la red virtual. Vea [Creación de una máquina virtual personalizada](../virtual-machines/windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Si quiere configurar una conexión VPN de punto a sitio, vea [Configuración de una conexión VPN de punto a sitio](vpn-gateway-point-to-site-create.md).


