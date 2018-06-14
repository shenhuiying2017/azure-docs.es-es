---
title: 'Creación de una instancia VPN Gateway basada en rutas: Azure Portal | Microsoft Docs'
description: Creación rápida de una instancia de VPN Gateway basada en rutas mediante Azure Portal
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: 550f655f6eac5a114636978255578eb3753e0d4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918139"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Creación de una instancia de VPN Gateway basada en rutas mediante Azure Portal

En este artículo se explica cómo crear rápidamente una instancia de Azure VPN Gateway basada en rutas mediante Azure Portal.  Una instancia de VPN Gateway se usa al crear una conexión VPN a la red local. También puede utilizar una instancia de VPN Gateway para conectar redes virtuales. 

Los pasos que se describen en este artículo crearán una red virtual, una subred, una subred de puerta de enlace y una instancia de VPN Gateway basada en rutas (puerta de enlace de red virtual). Una vez completada la creación de la puerta de enlace, puede crear las conexiones. Estos pasos requieren una suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="vnet"></a>Creación de una red virtual

1. Desde un explorador, navegue al [Portal de Azure](http://portal.azure.com) e inicie sesión con su cuenta de Azure.
2. Haga clic en **Crear un recurso**. En el campo **Buscar en el Marketplace**, escriba "Red virtual". En la lista de resultados, busque **Virtual Network** y haga clic para abrir la página **Virtual Network**.
3. En la parte inferior de la página Virtual Network, en la lista **Seleccionar un modelo de implementación**, verifique que **Resource Manager** está seleccionado en la lista desplegable y haga clic en **Crear**. Se abre la página **Crear red virtual**.
4. En la página **Crear red virtual**, configure los valores de la red virtual. Al rellenar los campos, el signo de exclamación rojo se convierte en una marca de verificación verde cuando los caracteres escritos en el campo sean válidos. Use los valores siguientes:

  - **Nombre**: TestVNet1
  - **Espacio de direcciones**: 10.1.0.0/16
  - **Suscripción**: verifique que la suscripción que aparece en la lista es la que desea usar. Puede cambiar las suscripciones mediante la lista desplegable.
  - **Grupo de recursos:** TestRG1
  - **Ubicación**: Este de EE. UU.
  - **Subred**: Frontend
  - **Intervalo de direcciones**: 10.1.0.0/24

  ![Página Crear red virtual](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Página Crear red virtual")
5. Después de escribir los valores, seleccione **Pin to dashboard** (Anclar al panel) para que sea fácil de encontrar la red virtual en el panel y luego haga clic en **Crear**. Después de hacer clic en **Crear**, aparece un icono en el panel que refleja el progreso de la red virtual. El icono cambiará a medida que se vaya creando la red virtual.

## <a name="gwsubnet"></a>Incorporación de una subred de puerta de enlace

La subred de puerta de enlace contiene las direcciones IP reservadas que usan los servicios de puerta de enlace de la red virtual. Cree una subred de puerta de enlace.

1. En el portal, navegue a la red virtual para la que desea crear una puerta de enlace de red virtual.
2. En la página de la red virtual, haga clic en **Subredes** para expandir la página **VNet1 - Subredes**.
3. Haga clic en **+Subred de puerta de enlace** en la parte superior para abrir la página **Agregar subred**.

  ![Agregar la subred de la puerta de enlace](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Agregar la subred de la puerta de enlace")
4. El campo **Nombre** de la subred se rellena automáticamente con el valor requerido "GatewaySubnet". Ajuste los valores de **Intervalo de direcciones** rellenados automáticamente para que coincidan con los siguientes valores:

  **Intervalo de direcciones (bloque CIDR)**: 10.1.255.0/27

  ![Adición de la subred de la puerta de enlace](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Adición de la subred de la puerta de enlace")
5. Para crear la subred de puerta de enlace, haga clic en **Aceptar** en la parte inferior de la página.

## <a name="gwvalues"></a>Configuración de la puerta de enlace

1. En el lado izquierdo de la página del portal, haga clic en **+ Crear un recurso** y escriba "Virtual Network Gateway" en el cuadro de búsqueda. A continuación, presione **Entrar**. En **Resultados**, busque y haga clic en **Puerta de enlace de red virtual**.
2. En la parte inferior de la página "Puerta de enlace de red virtual", haga clic en **Crear** para abrir la página **Crear puerta de enlace de red virtual**.
3. En la página **Crear puerta de enlace de red virtual**, especifique los valores de la puerta de enlace de red virtual.

  - **Nombre**: Vnet1GW
  - **Tipo de puerta de enlace:** VPN 
  - **Tipo de VPN:**: basada en rutas
  - **SKU**: VpnGw1
  - **Ubicación**: Este de EE. UU.
  - **Red virtual**: haga clic en **Red virtual/Elegir una red virtual** para abrir la página **Elegir una red virtual**. Seleccione **VNet1**.

  ![Configuración de la puerta de enlace](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "Configure gateway settings")

## <a name="pip"></a>Creación de una dirección IP pública

Una instancia de VPN Gateway debe tener una dirección IP pública asignada de forma dinámica. Cuando crea una conexión a una instancia de VPN Gateway, esta es la dirección IP a la que se conecta el dispositivo local.

1. Seleccione **Primera configuración de IP - Crear configuración de IP de puerta de enlace** para solicitar una dirección IP pública.

  ![Primera configuración de IP](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "First IP configuration")
2. En la página **Elegir dirección IP pública** haga clic en **+Crear nuevo** para abrir la página **Crear dirección IP pública**.
3. Defina la configuración con los siguientes valores:

  - **Nombre**: **VNet1GWIP**
  - **SKU**: **Basic**

  ![Crear dirección IP pública](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "Crear PIP")
4. Para guardar los cambios, haga clic en **Aceptar** en la parte inferior de la página.

## <a name="creategw"></a>Creación de la puerta de enlace de VPN

1. Compruebe la configuración en la página **Crear puerta de enlace de red virtual**. Ajuste los valores si es necesario.

  ![Crear instancia de VPN Gateway](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "Create VPN gateway")
2. En la parte inferior de la página, haga clic en **Crear**.

Después de hacer clic en **Crear**, la configuración se validará y el icono **Implementado puerta de enlace de red virtual** aparecerá en el panel. Una instancia de VPN Gateway puede tardar hasta 45 minutos. Es posible que tenga que actualizar la página de portal para ver el estado completado.

## <a name="viewgw"></a>Visualización de VPN Gateway

1. Después de crea la puerta de enlace, vaya a VNet1 en el portal. La instancia de VPN Gateway aparece en la página Información general como un dispositivo conectado.

  ![Dispositivos conectados](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Connected devices")

2. En la lista de dispositivos, haga clic en **VNet1GW** para ver más información.

  ![Ver instancia de VPN Gateway](./media/create-routebased-vpn-gateway-portal/view-gateway.png "View VPN gateway")

## <a name="next-steps"></a>Pasos siguientes

Una vez creada la puerta de enlace, puede crear una conexión entre su red virtual y otra red virtual. O bien, cree una conexión entre su red virtual y una ubicación local.

> [!div class="nextstepaction"]
> [Creación de una conexión de sitio a sitio](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Creación de una conexión de punto a sitio](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Creación de una conexión a otra red virtual](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)