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
ms.openlocfilehash: 5b2aa7fedbc203c50796a0e0c8d9cdb3895ae6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198152"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Paso 1: Navegación a la puerta de enlace de red virtual

1. En [Azure Portal](https://portal.azure.com), vaya a **Todos los recursos**. 
2. Para abrir la página de la puerta de enlace de la red virtual, vaya a la puerta de enlace de la red virtual que quiera eliminar y haga clic en ella.

### <a name="step-2-delete-connections"></a>Paso 2: Eliminación de conexiones

1. En la página de la puerta de enlace de red virtual, haga clic en **Conexiones** para ver todas las conexiones a la puerta de enlace.
2. Haga clic en los puntos suspensivos **"..."** en la fila del nombre de la conexión y seleccione **Eliminar** en la lista desplegable.
3. Haga clic en **Sí** para confirmar que quiere eliminar la conexión. Si tiene varias conexiones, elimine cada conexión.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Paso 3: Eliminación de la puerta de enlace de red virtual

Tenga en cuenta que si tiene una configuración P2S en esta red virtual además de la configuración S2S, al eliminar la puerta de enlace de la red virtual se desconectarán automáticamente todos los clientes P2S sin previo aviso.

1. En la página de la puerta de enlace de la red virtual, haga clic en **Introducción**.
2. En la página **Información general**, haga clic en **Eliminar** para eliminar la puerta de enlace.
