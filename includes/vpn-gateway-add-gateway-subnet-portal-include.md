---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 158400645423d2e9fe92768786b570e917907d98
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921524"
---
1. En el portal, navegue a la red virtual para la que desea crear una puerta de enlace de red virtual.
2. En la sección **Configuración** de la página de redes virtuales, haga clic en **Subredes** para expandir la página Subredes.
3. En la página **Subredes**, haga clic en **+Subred de puerta de enlace** en la parte superior para abrir la página **Agregar subred**.

  ![Agregar la subred de la puerta de enlace](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Agregar la subred de la puerta de enlace")
4. El **nombre** de la subred se rellena automáticamente con el valor "GatewaySubnet". El valor de Subred de puerta de enlace es necesario para que Azure reconozca que se trata de la subred de puerta de enlace. Modifique los valores de **Intervalo de direcciones** rellenados automáticamente para ajustarlos a sus requisitos de configuración.

  ![Adición de la subred de la puerta de enlace](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Adición de la subred de la puerta de enlace")
5. Para crear la subred, haga clic en **Aceptar** en la parte inferior de la página.