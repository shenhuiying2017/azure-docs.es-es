---
title: "Creación de una puerta de enlace de aplicaciones con Azure Portal | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones mediante el portal
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 17d09ce98c40717d1db0927f791a7c97ea7835e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-the-portal"></a>Creación de una puerta de enlace de aplicaciones con el portal

[Application Gateway](application-gateway-introduction.md) es una aplicación virtual dedicada que cuenta con un controlador de entrega de aplicaciones (ADC) que se ofrece como servicio y que proporciona numerosas funcionalidades de equilibrio de carga de nivel 7 para una aplicación. Este artículo le guía por los pasos para crear una puerta de enlace de aplicaciones mediante Azure Portal y agregar un servidor existente como miembro back-end.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal en: [http://portal.azure.com](http://portal.azure.com).

## <a name="create-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Para crear una puerta de enlace de aplicaciones, complete estos pasos. La puerta de enlace de aplicaciones requiere su subred propia. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para que tenga varias subredes. Tras implementar la puerta de enlace de aplicaciones en una subred, solo pueden agregarse a ella otras puertas de enlace de aplicaciones.

1. En el panel Favoritos del portal, haga clic en **Nuevo**.
1. En la hoja **Nuevo**, haga clic en **Redes**. En la hoja **Redes**, haga clic en **Application Gateway**, como se muestra en la siguiente imagen:

    ![Creación de una puerta de enlace de aplicaciones][1]

1. En la hoja **Datos básicos** que aparece, escriba los valores siguientes y haga clic en **Aceptar**:

   | **Configuración** | **Valor** | **Detalles**|
   |---|---|---|
   |**Name**|AdatumAppGateway|Nombre de la puerta de enlace de aplicaciones.|
   |**Nivel**|Estándar|Los valores disponibles son Estándar y WAF. Visite [Firewall de aplicaciones web](application-gateway-web-application-firewall-overview.md) para obtener más información sobre WAFS.|
   |**Tamaño de la SKU**|Mediano|Las opciones al elegir el nivel Estándar son pequeño, mediano y grande. Al elegir el nivel de WAF, las opciones solo son mediano y grande.|
   |**Recuento de instancias**|2|Número de instancias de la puerta de enlace de aplicaciones para alta disponibilidad. Los recuentos de instancias de 1 solo deben usarse para fines de pruebas.|
   |**Suscripción**|[Su suscripción]|Seleccione una suscripción donde se va a crear la puerta de enlace de la aplicación.|
   |**Grupos de recursos**|**Crear nuevo:** AdatumAppGatewayRG|Cree un grupo de recursos. El nombre del grupo de recursos debe ser único dentro de la suscripción seleccionada. Para más información sobre los grupos de recursos, lea el artículo [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Ubicación**|Oeste de EE. UU.||

1. En la hoja **Configuración** que aparece en **Red virtual**, haga clic en **Elegir una red virtual**. Se abre la hoja **Elegir red virtual**.  Haga clic en **Crear nuevo** para abrir la hoja **Crear red virtual**.

   ![Elegir una red virtual][2]

1. En la hoja **Crear red virtual**, escriba los valores siguientes y luego haga clic en **Aceptar**. Las hojas **Crear red virtual** y **Elegir una red virtual** se cierran. Además, en este paso se rellena el campo **Subred** en la hoja **Configuración** con la subred elegida.

   | **Configuración** | **Valor** | **Detalles**|
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|Nombre de la puerta de enlace de aplicaciones.|
   |**Espacio de direcciones**|10.0.0.0/16|Se trata del espacio de direcciones de la red virtual.|
   |**Nombre de subred**|AppGatewaySubnet|Nombre de la subred de la puerta de enlace de aplicaciones.|
   |**Intervalo de direcciones de subred**|10.0.0.0/28|Esta subred permite más subredes en la red virtual para miembros del grupo de back-end.|

1. En la hoja **Configuración** de **Configuración de IP de front-end**, seleccione **Pública** como el **Tipo de dirección IP**.

1. En la hoja **Configuración** de **Dirección IP pública**, haga clic en **Elegir una dirección IP pública**. La hoja **Elegir una dirección IP pública** se abre y luego debe hacer clic en **Crear nueva**.

   ![Elegir una dirección IP pública][3]

1. En la hoja **Crear dirección IP pública**, acepte el valor predeterminado y haga clic en **Aceptar**. La hoja se cierra y se rellena la **dirección IP pública** con la dirección IP pública elegida.

1. En la hoja **Configuración** en **Configuración de agente de escucha**, haga clic en **HTTP** en **Protocolo**. En el campo **Puerto**, escriba el puerto que se usará.

2. Haga clic en **Aceptar** en la hoja **Configuración** para continuar.

1. Revise la configuración en la hoja **Resumen** y haga clic en **Aceptar** para iniciar la creación de la puerta de enlace de aplicaciones. La creación de una puerta de enlace de aplicaciones es una tarea de ejecución prolongada y tarda en completarse.

## <a name="add-servers-to-backend-pools"></a>Adición de servidores a grupos de back-end

Cuando crea la puerta de enlace de aplicaciones, los sistemas que hospedan la aplicación para el equilibrio de carga aún tienen que agregarse a dicha puerta de enlace. Las direcciones IP, los FQDN o las NIC de estos servidores se agregan a los grupos de direcciones de back-end.

### <a name="ip-address-or-fqdn"></a>Dirección IP o FQDN

1. Con la puerta de enlace de aplicaciones creada, en el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en la puerta de enlace de aplicaciones **AdatumAppGateway** en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir **AdatumAppGateway** en el cuadro **Filtrar por nombre…** para acceder fácilmente a la puerta de enlace de la aplicación.

1. Se muestra la puerta de enlace de aplicaciones que ha creado. Haga clic en **Grupos de back-end** y seleccione el grupo actual de back-end **appGatewayBackendPool**; se abre la hoja **appGatewayBackendPool**.

   ![Grupos de back-end de Application Gateway][4]

1. Haga clic en **Agregar destino** para agregar direcciones IP de los valores de FQDN. Seleccione **Dirección IP o FQDN** como **Tipo** y escriba la dirección IP o el FQDN en el campo. Repita este paso para los miembros adicionales del grupo de back-end. Cuando termine, haga clic en **Guardar**.

### <a name="virtual-machine-and-nic"></a>Máquina virtual y NIC

También puede agregar NIC de máquinas virtuales como miembros del grupo de back-end. Solo las máquinas virtuales de la misma red virtual que la puerta de enlace de aplicaciones están disponibles en la lista desplegable.

1. Con la puerta de enlace de aplicaciones creada, en el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en la puerta de enlace de aplicaciones **AdatumAppGateway** en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir **AdatumAppGateway** en el cuadro **Filtrar por nombre…** para acceder fácilmente a la puerta de enlace de la aplicación.

1. Se muestra la puerta de enlace de aplicaciones que ha creado. Haga clic en **Grupos de back-end** y seleccione el grupo actual de back-end **appGatewayBackendPool**; se abre la hoja **appGatewayBackendPool**.

   ![Grupos de back-end de Application Gateway][5]

1. Haga clic en **Agregar destino** para agregar direcciones IP de los valores de FQDN. Elija **Máquina virtual** como **Tipo** y seleccione la máquina virtual y la NIC que desea usar. Cuando termine, haga clic en **Guardar**.

   > [!NOTE]
   > Solo las máquinas virtuales de la misma red virtual que la puerta de enlace de aplicaciones están disponibles en el cuadro desplegable.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, elimine el grupo de recursos, la puerta de enlace de aplicaciones y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la hoja del enlace de aplicaciones y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este escenario, implementó una puerta de enlace de aplicaciones y agregó un servidor al grupo de back-end. Los siguientes pasos se usan para configurar la puerta de enlace de aplicaciones mediante la modificación de la configuración y el ajuste de las reglas de la puerta de enlace. Estos pasos se pueden encontrar si consulta los siguientes artículos:

Para aprender a crear sondeos de estado personalizado, visite [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Para aprender a configurar la descarga de SSL y eliminar la cara descripción de SSL de los servidores web, visite [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante Azure Resource Manager](application-gateway-ssl-portal.md)

Aprenda a proteger aplicaciones con [Firewall de aplicaciones web de Application Gateway (versión preliminar)](application-gateway-webapplicationfirewall-overview.md), una característica de Application Gateway.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
