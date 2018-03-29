---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3e02242822283ec8597a7be217228cb95e748761
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
1. En el portal, navegue a la red virtual para la que desea crear una puerta de enlace de red virtual.
2. En la sección **Configuración** de la página de redes virtuales, haga clic en **Subredes** para expandir la página Subredes.
3. En la página **Subredes**, haga clic en **+Subred de puerta de enlace** en la parte superior para abrir la página **Agregar subred**.

  ![Agregar la subred de la puerta de enlace](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsub.png "Agregar la subred de la puerta de enlace")
4. El **nombre** de la subred se rellena automáticamente con el valor "GatewaySubnet". El valor de Subred de puerta de enlace es necesario para que Azure reconozca que se trata de la subred de puerta de enlace. Modifique los valores de **Intervalo de direcciones** rellenados automáticamente para ajustarlos a sus requisitos de configuración.

  ![Adición de la subred de la puerta de enlace](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addsub2.png "Adición de la subred de la puerta de enlace")
5. Para crear la subred, haga clic en **Aceptar** en la parte inferior de la página.