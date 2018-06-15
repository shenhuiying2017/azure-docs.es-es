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
ms.openlocfilehash: 9ab0dfac298ab7246935649010100c14dbe9c76a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921541"
---
1. En el portal, haga clic en **+Crear un recurso**.
2. En el cuadro de búsqueda, escriba **puerta de enlace de red local**, a continuación, presione **Entrar** para buscar. Se devolverá una lista de resultados. Haga clic en **Puerta de enlace de red local** y haga clic en el botón **Crear** para abrir la página **Crear puerta de enlace de red local**.

  ![Creación de la puerta de enlace de red local](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Create the local network gateway")

3. En la página **Crear puerta de enlace de red local**, especifique los valores de la puerta de enlace de red local.

  - **Nombre:** especifique el nombre del objeto de puerta de enlace de red local.
  - **Dirección IP:** es la dirección IP pública del dispositivo VPN al que desea que Azure se conecte. Especifique una dirección IP pública válida. La dirección IP no puede encontrarse detrás de NAT y Azure debe poder acceder a ella. Si no tiene la dirección IP en este momento, puede usar los valores que se muestran en el ejemplo, pero deberá volver y reemplazar la dirección IP del marcador de posición por la dirección IP pública de su dispositivo VPN. Si no lo hace, Azure no podrá conectarse.
  - **Espacio de direcciones** hace referencia a los intervalos de direcciones de la red que representa esta red local. Puede agregar varios intervalos de espacios de direcciones. Asegúrese de que los intervalos que especifique aquí no se superpongan con los de otras redes a las que quiera conectarse. Azure enrutará el intervalo de direcciones que especifique a la dirección IP del dispositivo VPN local. *Use sus propios valores aquí, y no los mostrados en el ejemplo, si quiere conectarse a su sitio local*.
  - **Configurar BGP:** solo se debe usar al configurar BGP. En caso contrario, no seleccione esta opción.
  - **Suscripción:** compruebe que se muestra la suscripción correcta.
  - **Grupo de recursos:** seleccione el grupo de recursos que desea utilizar. Puede crear un grupo de recursos nuevo o seleccionar uno ya creado.
  - **Ubicación:** seleccione la ubicación en la que se creará este objeto. Puede seleccionar la misma ubicación en la que reside la red, pero no es obligatorio.

4. Cuando haya terminado de especificar los valores, haga clic en el botón **Crear** en la parte inferior de la página para crear la puerta de enlace de red local.