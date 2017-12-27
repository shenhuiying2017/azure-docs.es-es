---
title: "Solución de problemas de rutas mediante Azure Portal | Microsoft Docs"
description: "Obtenga información acerca de cómo solucionar problemas de las rutas en el modelo de implementación de Azure Resource Manager utilizando Azure Portal."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: d9b951ad4c54a3714ba7e857d5198c351215cbac
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>Solución de problemas de rutas mediante Azure Portal
> [!div class="op_single_selector"]
> * 
            [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
>
>

Si experimenta problemas de conectividad de red tanto de entrada como de salida en Azure Virtual Machine (VM), es posible que las rutas estén afectando el flujo de tráfico de la máquina virtual. Este artículo proporciona información general sobre las funcionalidades de diagnóstico para las rutas para ayudarle en la solución del problema.

Las tablas de rutas están asociadas con subredes y son eficaces en todas las interfaces de red (NIC) en cada subred específica. Los siguientes tipos de rutas pueden aplicarse a cada interfaz de red:

* **Rutas del sistema:** de forma predeterminada, cada subred creada en Azure Virtual Network (VNet) tiene tablas de rutas del sistema que permiten el tráfico de red virtual local, el tráfico local a través de las puertas de enlace VPN y el tráfico de Internet. Las rutas del sistema existen también para las redes virtuales emparejadas.
* **Rutas BGP:** propagadas a las interfaces de red a través de ExpressRoute o de conexiones VPN de sitio a sitio. Para más información acerca del enrutamiento BGP consulte los artículos sobre [BGP con VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md) e [Introducción técnica de ExpressRoute](../expressroute/expressroute-introduction.md).
* **Rutas definidas por el usuario (UDR):** si usa dispositivos de red virtual o si hay tráfico de tunelización forzada a una red local a través de una VPN de sitio a sitio, es posible que tenga rutas definidas por el usuario (UDR) asociadas a la tabla de rutas de subred. Si no está familiarizado con las UDR, consulte el artículo sobre [rutas definidas por el usuario](virtual-networks-udr-overview.md#user-defined) .

Con las diversas rutas que se pueden aplicar a una interfaz de red, puede resultar difícil determinar qué rutas agregadas son eficaces. Para ayudarle a solucionar problemas de conectividad de red de máquina virtual, puede ver todas las rutas eficaces para una interfaz de red en el modelo de implementación de Azure Resource Manager.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Uso de rutas eficaces para solucionar problemas de flujo de tráfico de máquinas virtuales
Este artículo usa el siguiente escenario como ejemplo para ilustrar cómo solucionar problemas de las rutas eficaces para una interfaz de red:

Una máquina virtual (*VM1*) conectada a la red virtual (*VNet1*, prefijo: 10.9.0.0/16) no se puede conectar a una máquina virtual (VM3) en una red virtual recién emparejada (*VNet3*, prefijo 10.10.0.0/16). No hay ninguna ruta UDR o BGP aplicada a la interfaz de red VM1-NIC1 conectada a la máquina virtual, se aplican solo rutas del sistema.

En este artículo se explica cómo determinar la causa del error de conexión, utilizando la funcionalidad de rutas eficaces en el modelo de implementación de Azure Resource Management.
Aunque este ejemplo utiliza solo rutas de sistema, los pasos siguientes pueden utilizarse para determinar errores de conexión entrante y saliente a través de cualquier tipo de puerto.

> [!NOTE]
> Si la máquina virtual tiene más de una NIC conectada, compruebe las rutas eficaces para cada una de las NIC con el fin de diagnosticar problemas de conectividad de red, tanto de entrada como de salida, de una máquina virtual.
>
>

### <a name="view-effective-routes-for-a-virtual-machine"></a>Visualización de las rutas eficaces para una máquina virtual
Para ver las rutas agregadas que se aplican a una máquina virtual, complete los pasos siguientes:

1. Inicie sesión en Azure Portal en https://portal.azure.com. La cuenta debe tener asignada la operación *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* para la interfaz de red. Para más información sobre cómo asignar operaciones a las cuentas, consulte [Creación de roles personalizados para el control de acceso basado en rol de Azure](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Haga clic en **Más servicios** y, luego, haga clic en **Máquinas virtuales** en la lista que aparece.
3. En la lista seleccione una máquina virtual para solucionar problemas, después de hacerlo se abrirá una hoja de máquina virtual con opciones.
4. Haga clic en **Diagnosticar y solucionar problemas** y, a continuación, seleccione un problema común. En este ejemplo está seleccionado **No puedo conectar con mi VM Windows** .

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. Bajo el problema aparecen una serie de pasos, tal como se muestra en la siguiente imagen:

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Haga clic en *effective routes* (rutas eficaces) en la lista de pasos recomendados.
6. Aparece la hoja **Effective routes** (Rutas eficaces), como se muestra en la siguiente imagen:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Si la máquina virtual tiene solo una NIC, está seleccionada de forma predeterminada. Si tiene más de una NIC, seleccione la NIC para la que desea ver las rutas eficaces.

   > [!NOTE]
   > Si la máquina virtual asociada con la NIC no está en estado de ejecución, no se mostrará ninguna ruta eficaz. Solo las primeras 200 rutas eficaces se muestran en el portal. Para obtener la lista completa, haga clic en **Descargar**. Puede filtrar aún más los resultados en el archivo .csv descargado.
   >
   >

    Tenga en cuenta lo siguiente en el resultado:

   * **Origen**: indica el tipo de ruta. Las rutas del sistema se muestran como *Predeterminado*, los UDR se muestran como *Usuario* y las rutas de puerta de enlace (estáticas o BGP) se muestran como *VPNGateway*.
   * **Estado**: indica el estado de la ruta eficaz. Los valores posibles son *Activo* o *No válido*.
   * **AddressPrefixes**: especifica el prefijo de dirección de la ruta eficaz en la notación CIDR.
   * **nextHopType**: indica el próximo salto de la ruta dada. Los valores posibles son *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering* o *Null*. Un valor de *Null* para **nextHopType** en un UDR puede indicar una ruta no válida. Por ejemplo, si **nextHopType** es *VirtualAppliance* y la máquina virtual de dispositivo de red virtual no está en estado aprovisionado/en ejecución. Si **nextHopType** es *VPNGateway* y no hay ninguna puerta de enlace aprovisionada/en ejecución en la red virtual específica, la ruta puede no ser válida.
7. No hay ninguna ruta para la red virtual *WestUS-VNET3* (prefijo 10.10.0.0/16) desde *WestUS-VNet1* (prefijo 10.9.0.0/16) en la imagen del paso anterior. En la siguiente imagen, el vínculo de emparejamiento está en estado *Desconectado* :

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    El vínculo bidireccional para el emparejamiento se interrumpe, lo que explica por qué VM1 no pudo conectarse a VM3 en la red virtual *WestUS-VNet3* .
8. En la siguiente imagen se muestran las rutas después de establecer el vínculo de emparejamiento bidireccional:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Para informarse sobre más escenarios de solución de problemas para la evaluación de ruta y de tunelización forzada consulte la sección [Consideraciones](virtual-network-routes-troubleshoot-portal.md#considerations) de este artículo.

### <a name="view-effective-routes-for-a-network-interface"></a>Visualización de las rutas eficaces para una interfaz de red
Si el flujo de tráfico de red se ve afectado para una interfaz de red (NIC) determinada, puede ver una lista completa de rutas eficaces directamente en una NIC. Para ver las rutas agregadas que se aplican a una NIC, complete los pasos siguientes:

1. Inicie sesión en Azure Portal en https://portal.azure.com.
2. Haga clic en **Más servicios** y, a continuación, haga clic en **Interfaces de red**.
3. En la lista busque el nombre de una NIC o selecciónelo en la lista que aparece. En este ejemplo está seleccionada **VM1 NIC1** .
4. Seleccione **Rutas eficaces** en la hoja **Interfaz de red**, como se muestra en la siguiente imagen:

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    Observe que el **Ámbito** se establece de forma predeterminada en la interfaz de red seleccionada.

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>Visualización de las rutas eficaces para una tabla de rutas
Para modificar rutas definidas por el usuario (UDR) en una tabla de rutas, puede revisar el impacto de las rutas que se han agregado en una máquina virtual específica. Una tabla de rutas se puede asociar con cualquier número de subredes. Puede ver una lista completa de las rutas eficaces para todas las NIC a las que se aplica una tabla de rutas determinada, sin tener que cambiar el contexto de la hoja de tabla de rutas determinada.

En este ejemplo, se ha especificado una UDR (*UDRoute*) en una tabla de rutas (*UDRouteTable*). Esta ruta envía todo el tráfico de Internet de *Subnet1* en la red virtual *WestUS-VNet1* a través de un dispositivo de red virtual (NVA), en *Subnet2* de la misma red virtual. La ruta se muestra en la imagen siguiente:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Para ver las rutas agregadas que se aplican a una tabla de rutas, complete los pasos siguientes:

1. Inicie sesión en Azure Portal en https://portal.azure.com.
2. Haga clic en **Más servicios** y, a continuación, haga clic en **Tablas de rutas**.
3. Busque en la lista la tabla de rutas para la que desea ver las rutas agregadas y selecciónela. En este ejemplo, **UDRouteTable** está seleccionada. Aparece una hoja de la tabla de rutas seleccionada, tal como se muestra en la siguiente imagen:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. Seleccione **Rutas eficaces** en la hoja **Tabla de rutas**. El **Ámbito** se establece en la tabla de rutas seleccionada.
5. Una tabla de rutas se puede aplicar a varias subredes. Seleccione en la lista la **Subred** que desea revisar. En este ejemplo está seleccionada **Subnet1** .
6. Seleccione una **Interfaz de red**. Se muestran todas las NIC conectadas a la subred seleccionada. En este ejemplo está seleccionada **VM1 NIC1** .

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > Si la NIC no está asociada a una máquina virtual en ejecución, no se muestra ninguna ruta eficaz.
   >
   >

## <a name="considerations"></a>Consideraciones
Algunos aspectos que tener en cuenta al revisar la lista de rutas devuelven:

* El enrutamiento se basa en coincidencia de prefijo más larga (LPM) entre las rutas UDR, BGP y del sistema. Si hay más de una ruta con la misma coincidencia LPM, se selecciona una en función de su origen en el orden siguiente:

  * Ruta definida por el usuario
  * Ruta BGP
  * Ruta del sistema (predeterminado)

    En el caso de las rutas eficaces, solo puede ver aquellas que tienen una coincidencia LPM basada en todas las rutas disponibles. Al mostrar cómo las rutas se evalúan realmente para una NIC determinada, resulta mucho más fácil solucionar problemas en rutas específicas que pueden afectar a la conectividad (tanto de entrada como de salida) de la máquina virtual.
* Si tiene UDR que están enviando tráfico a un dispositivo de red virtual (NVA), con *VirtualAppliance* como **nextHopType**, asegúrese de que el reenvío IP está habilitado en el NVA que recibe el tráfico o se perderán los paquetes.
* Si está habilitada la tunelización forzada, todo el tráfico saliente de Internet se enrutará a la red local. Una conexión RDP o SSH desde Internet a su máquina virtual puede no funcionar con esta configuración, dependiendo de cómo la red local controle este tráfico.
  Puede habilitar la tunelización forzada:
  * Si utiliza VPN de sitio a sitio, estableciendo una ruta definida por el usuario (UDR) con nextHopType como VPN Gateway
  * Si se anuncia una ruta predeterminada a través de BGP
* Para que el tráfico de emparejamiento de red virtual funcione correctamente, es necesario que exista una ruta de sistema con **nextHopType** *VNetPeering* para el intervalo de prefijos de red virtual. Si dicha una ruta no existe y el vínculo de emparejamiento de red virtual parece correcto:
  * Espere unos segundos y vuelva a intentar si es un vínculo de emparejamiento recién establecido. En ocasiones la propagación de las rutas a todas las interfaces de red en una subred lleva algo más de tiempo.
  * Las reglas del grupo de seguridad de red (NSG) pueden estar afectando a los flujos de tráfico. Para más información, consulte el artículo sobre [Solución de problemas de los grupos de seguridad de red](virtual-network-nsg-troubleshoot-portal.md) .
