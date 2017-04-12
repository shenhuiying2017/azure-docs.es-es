---
title: "Creación de una red virtual (Azure Portal) | Microsoft Docs"
description: "Obtenga información sobre cómo crear una red virtual usando el portal de Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 988510350ceb97a15bb305edff397a0e3212a89d
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Creación de una red virtual mediante el Portal de Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure cuenta con dos modelos de implementación: Azure Resource Manager y el clásico. Microsoft recomienda crear recursos mediante el modelo de implementación de Resource Manager. Para más información acerca de las diferencias entre los dos modelos, lea el artículo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Descripción de los modelos de implementación de Azure).
 
En este artículo se describe cómo crear una red virtual con el modelo de implementación de Resource Manager mediante Azure Portal. También puede crear una red virtual mediante Resource Manager con otras herramientas o crear una red virtual a través del modelo de implementación clásica seleccionando una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Portal](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [Plantilla](virtual-networks-create-vnet-arm-template-click.md)
> * [Portal (clásico)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (clásico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI (clásico)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Crear una red virtual

Para crear una red virtual mediante Azure Portal, realice los siguientes pasos:

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Nuevo** > **Redes** > **Red virtual**, tal y como se muestra en la siguiente imagen:

    ![Nueva red virtual](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. En la hoja **Red virtual** que aparece, asegúrese de que *Resource Manager* está seleccionado y haga clic en **Crear**, tal y como se muestra en la siguiente imagen:

    ![Red virtual](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. En la hoja **Crear red virtual** que ha aparecido, escriba *TestVNet* en **Nombre**, *192.168.0.0/16* como **Espacio de direcciones**, *FrontEnd* en **Nombre de subred**, *192.168.1.0/24* en **Intervalo de direcciones de subred**, *TestRG* en **Grupo de recursos**, seleccione la **suscripción**, una **ubicación** y haga clic en el botón **Crear**, tal como se muestra en la siguiente imagen:

    ![Creación de una red virtual](./media/virtual-network-create-vnet-arm-pportal/3.png)

    Como alternativa, puede seleccionar un grupo de recursos existente. Para más información sobre los grupos de recursos, lea el artículo [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). También puede seleccionar una ubicación diferente. Para más información sobre ubicaciones y regiones de Azure, lea el artículo [Regiones de Azure](https://azure.microsoft.com/regions).

5. El portal solo permite crear una subred al crear una red virtual. En este escenario, se debe crear una segunda subred una vez creada la red virtual. Para crear la segunda subred, haga clic en **Todos los recursos** y, después, haga clic en **TestVNet** en la hoja **Todos los recursos**, como se muestra en la siguiente imagen:

    ![Red virtual creada](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. En la hoja **TestVNet** que aparece, haga clic en **Subred** y, a continuación, haga clic en **+Subred**, escriba *BackEnd* como **nombre**, *192.168.2.0/24* como **Intervalo de direcciones** en la hoja **Agregar subred** y, a continuación, haga clic en **Aceptar**, tal y como se muestra en la siguiente imagen:

    ![Subred agregada](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. Aparecen las dos subredes, tal como se muestra en la siguiente imagen:
    
    ![Lista de subredes en la red virtual](./media/virtual-network-create-vnet-arm-pportal/6.png)

En este artículo se explica cómo crear una red virtual con dos subredes para la realización de pruebas. Antes de crear una red virtual para su uso en producción, es recomendable leer los artículos [Información general sobre Virtual Network](virtual-networks-overview.md) y [Planeación y diseño de redes virtuales de Azure](virtual-network-vnet-plan-design-arm.md) para comprender perfectamente las redes virtuales y todas las configuraciones. 

## <a name="next-steps"></a>Pasos siguientes

Aprenda a conectar:

- Una máquina virtual (VM) a una red virtual, para lo cual debería leer los artículos [Creación de una máquina virtual Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [Creación de una máquina virtual Linux](../virtual-machines/linux/quick-create-portal.md). En lugar de crear una red virtual y la subred en los pasos de los artículos, puede seleccionar una red virtual y una subred a la que conectar una máquina virtual.
- La red virtual a otras redes virtuales. Para ello, lea el artículo [Conexión de redes virtuales](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- La red virtual a una red local mediante una red privada virtual (VPN) de sitio a sitio o un circuito ExpressRoute. Aprenda cómo hacerlo mediante los artículos [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Conexión de una red virtual a una red local mediante una VPN de sitio a sitio) y [Vinculación de una red virtual a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
