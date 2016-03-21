<properties
   pageTitle="Configurar una red virtual y una puerta de enlace para ExpressRoute | Microsoft Azure"
   description="Este artículo le guiará en la configuración de una red virtual (VNet) de ExpressRoute mediante el modelo de implementación clásica."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/08/2016"
   ms.author="cherylmc"/>

# Configuración de una red virtual para ExpressRoute en el Portal clásico

Los pasos de este artículo le guiarán en la configuración de una red virtual y una puerta de enlace para su uso con ExpressRoute mediante el modelo de implementación clásica y el portal clásico.

Si busca instrucciones para el modelo de implementación de Resource Manager, puede utilizar los siguientes artículos que le guiarán a través de cómo [crear una red virtual mediante PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) y [agregar una puerta de enlace de VPN a una red virtual de Resource Manager para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Para configurar una red virtual y la puerta de enlace

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com).

2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.

3. En la página **Detalles de la red virtual** escriba la siguiente información.

	- **Nombre**: nombre de la red virtual. Usará este nombre de red virtual al implementar las máquinas virtuales y las instancias PaaS, por lo que probablemente no querrá que este nombre sea muy complicado.
	- **Ubicación**: la ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales). Por ejemplo, si desea que las máquinas virtuales que implementa en la red virtual se encuentren físicamente en el Este de EE.UU., seleccione esa ubicación. No se puede cambiar la región asociada con la red virtual después de crearla.

4. En la página **Servidores DNS y conectividad VPN**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha.

	- **Servidores DNS**: escriba el nombre y la dirección IP del servidor DNS o seleccione un servidor DNS previamente registrado de la lista desplegable. Este valor no crea un servidor DNS; permite especificar los servidores DNS que desea usar para la resolución de nombres para esta red virtual.
	- **Configurar VPN de sitio a sitio**: seleccione la casilla **Configurar una VPN de sitio a sitio**.
	- **Seleccione ExpressRoute**: active la casilla **Usar ExpressRoute**. Esta opción solo aparece si seleccionó ***Configurar una VPN de sitio a sitio*** en el paso anterior.
	- **Red local**: una red local representa la ubicación física local. Puede seleccionar una red local que haya creado previamente o puede crear una nueva.

	Si selecciona una red local existente, omita el paso 5.

5. Si va a crear una nueva red local, verá la página **Conectividad de sitio a sitio**. Si seleccionó una red local que creó anteriormente, esta página no aparecerá en el asistente y puede pasar a la siguiente sección. Para configurar la red local, escriba la siguiente información y, después, haga clic en la flecha siguiente.

	- **Nombre**: el nombre que quiere para el sitio de red local.
	- **Espacio de direcciones**: incluidas la dirección IP de inicio y el CIDR (recuento de direcciones). Puede especificar cualquier intervalo de direcciones, siempre que no se solape con el intervalo de direcciones de la red virtual.
	- **Agregar espacio de direcciones**: este ajuste no es relevante para ExpressRoute. **Nota:** se requiere crear un sitio de red local para ExpressRoute. Los prefijos de dirección especificados para el sitio de red local se ignorarán. Los prefijos de dirección anunciados a Microsoft a través del circuito ExpressRoute se usarán para el enrutamiento.

6. En la página **Espacios de direcciones de la red virtual**, escriba la información siguiente y, después, haga clic en la marca de verificación de la parte inferior derecha para configurar la red.

	- **Espacio de direcciones**: incluidas la dirección IP de inicio y el recuento de direcciones. Compruebe que los espacios de direcciones especificados no se solapan con los espacios de direcciones que existen en la red local.
	- **Agregar subred**: incluidas Dirección IP de inicio y Recuento de direcciones. No se necesitan subredes adicionales, pero es posible que quiera crear una subred independiente para las máquinas virtuales que tengan direcciones IP dinámicas (DIPS). O bien, puede querer que las máquinas virtuales se encuentren en una subred independiente de las demás instancias PaaS.
	- **Agregar subred de puerta de enlace**: haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace solo se usa para la puerta de enlace de red virtual y es obligatoria para esta configuración. ***Importante:*** el prefijo de subred de puerta de enlace de ExpressRoute debe ser /28 o menor. (/27, /26 etc.)

7. Haga clic en la marca de verificación de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando termine, verá **Creado** listado en el **Estado** de la página **Redes** del Portal de Azure clásico.

8. En la página **Redes**, haga clic en la red virtual recién creada y luego en **Panel**.
9. En la parte inferior de la página del Panel, haga clic en **CREAR PUERTA DE ENLACE** y, después, haga clic en **Sí**.

10. Cuando comience a crearse la puerta de enlace, verá un mensaje que le informa de que la puerta de enlace se inició. La puerta de enlace puede tardar hasta 15 minutos en crearse.

11. Vincule la red a un circuito. Siga las instrucciones del artículo [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-classic.md).

## Pasos siguientes

- Si quiere agregar máquinas virtuales a la red virtual, consulte las [rutas de aprendizaje de máquinas virtuales](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Si quiere obtener más información sobre ExpressRoute, consulte [Descripción general técnica sobre ExpressRoute](expressroute-introduction.md).


 

<!---HONumber=AcomDC_0309_2016-->