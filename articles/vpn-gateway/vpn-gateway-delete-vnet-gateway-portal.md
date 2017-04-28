---
title: "Eliminación de una puerta de enlace de red virtual: Azure Portal y Azure Resource Manager | Microsoft Docs"
description: "Elimine una puerta de enlace de red virtual mediante PowerShell en el modelo de implementación de Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b4949db62ccd31cf6ce3d19df5459367cac99b59
ms.lasthandoff: 03/31/2017


---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Eliminación de una puerta de enlace de red virtual mediante el portal
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clásico: PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Hay un par de enfoques que puede adoptar cuando desee eliminar una puerta de enlace de red virtual correspondiente a una configuración de puerta de enlace VPN.

- Si quiere eliminar todo el contenido y volver a empezar, como en el caso de un entorno de prueba, puede eliminar el grupo de recursos. Cuando se elimina un grupo de recursos, se quitan todos los recursos de él. Este método solo se recomienda si no desea conservar los recursos del grupo en cuestión. Con este enfoque no podrá eliminar de forma selectiva solo unos pocos recursos.

- Si desea mantener algunos de los recursos en el grupo de recursos, será algo más complicado eliminar una puerta de enlace de red virtual. Antes de poder eliminar la puerta de enlace de red virtual, primero debe quitar todos los recursos que dependen de la puerta de enlace. Los pasos que seguirá dependerán del tipo de conexiones que creó y de los recursos dependientes de cada conexión.

##<a name="deletegw"></a>Eliminación de una puerta de enlace de red virtual

Para eliminar una puerta de enlace de red virtual, primero debe eliminar cada recurso que pertenece a la puerta de enlace de red virtual. Los recursos deben eliminarse en un orden determinado debido a las dependencias.

###<a name="step-1-navigate-to-the-virtual-network-gateway"></a>Paso 1: Navegación a la puerta de enlace de red virtual

En [Azure Portal](https://portal.azure.com), en **Todos los recursos**, haga clic en la puerta de enlace de red virtual que quiere eliminar. El gráfico de una puerta de enlace es el siguiente:

![Búsqueda de la puerta de enlace de red virtual](./media/vpn-gateway-delete-vnet-gateway-portal/gw.png)

###<a name="step-2-delete-connections"></a>Paso 2: Eliminación de conexiones

1. En la hoja de la puerta de enlace de red virtual, haga clic en **Conexiones** para ver todas las conexiones a la puerta de enlace.
2. Haga clic en los puntos suspensivos **...** en la fila del nombre de la conexión y seleccione **Eliminar** en la lista desplegable.
3. Haga clic en **Sí** para confirmar que quiere eliminar la conexión. Si tiene varias conexiones, elimine cada conexión.

###<a name="step-3-delete-the-local-network-gateways"></a>Paso 3: Eliminación de las puertas de enlace de red locales
1. En **Todos los recursos**, busque las puertas de enlace de red local asociadas a cada conexión. El gráfico de una puerta de enlace de red local es el siguiente:

    ![Búsqueda de una puerta de enlace de red local](./media/vpn-gateway-delete-vnet-gateway-portal/lng.png)
2. En la hoja **Información general** de la puerta de enlace de red local, haga clic en **Eliminar**.

###<a name="step-4-delete-the-virtual-network-gateway"></a>Paso 4: Eliminación de la puerta de enlace de red virtual
1. En **Todos los recursos**, busque la puerta de enlace de red virtual que quiere eliminar.
2. En la hoja **Información general**, haga clic en **Eliminar** para eliminar la puerta de enlace.

>[!NOTE]
> Si tiene una configuración P2S en esta red virtual además de la configuración S2S, eliminando la puerta de enlace de red virtual se desconectarán automáticamente todos los clientes P2S sin previo aviso.
>
>

###<a name="step-5-delete-the-public-ip-address-for-the-gateway"></a>Paso 5: Eliminación de una dirección IP pública para la puerta de enlace

1. En **Todos los recursos**, busque la dirección IP pública asignada a la puerta de enlace. Si la puerta de enlace de red virtual era activa-activa, verá dos direcciones IP públicas. El gráfico de una dirección IP pública es el siguiente:

    ![Dirección IP pública](./media/vpn-gateway-delete-vnet-gateway-portal/pip.png)

2. En la página **Introducción general** de la dirección IP pública, haga clic en **Eliminar** y después en **Sí** para confirmar.

###<a name="step-6-delete-the-gateway-subnet"></a>Paso 6: Eliminación de la subred de puerta de enlace

1. En **Todos los recursos**, busque la red virtual. 
2. En la hoja **Subredes**, haga clic en el elemento **GatewaySubnet** y después en **Eliminar**. 
3. Haga clic en **Sí** para confirmar que desea eliminar la subred de la puerta de enlace.

##<a name="deleterg"></a>Eliminación de una puerta de enlace VPN mediante la eliminación del grupo de recursos

Si no desea mantener ninguno de los recursos del grupo, sino que desea empezar de nuevo, puede eliminar dicho grupo al completo. Se trata de una forma rápida de quitarlos todos. Los siguientes pasos se aplican solo al modelo de implementación de Resource Manager.

1. En **Todos los recursos**, busque el grupo de recursos y haga clic para abrir la hoja.
2. Hacer clic en **Eliminar**. En la hoja Eliminar, revise los recursos afectados. Asegúrese de que desea eliminar todos estos recursos. Si no es así, siga los pasos de [Eliminación de una puerta de enlace VPN](#deletegw) en la parte superior de este artículo.
3. Para continuar, escriba el nombre del grupo de recursos que quiere eliminar y, después, haga clic en **Eliminar**.
