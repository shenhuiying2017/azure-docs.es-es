---
title: "Creación de una puerta de enlace de aplicaciones mediante el portal | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones mediante el portal
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 2195eb4ce0e22c8c7c72ac0638ab927f13c4d454
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-application-gateway-by-using-the-portal"></a>Creación de una puerta de enlace de aplicaciones mediante el portal

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Plantilla de Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure](application-gateway-create-gateway-cli.md)

Obtenga información sobre cómo crear una puerta de enlace de aplicaciones con la descarga de SSL.

![Escenario de ejemplo][scenario]

Application Gateway es una aplicación virtual dedicada que cuenta con un controlador de entrega de aplicaciones (ADC) que se ofrece como servicio y que proporciona numerosas funcionalidades de equilibrio de carga de nivel 7.

En este escenario:

1. [Cree una puerta de enlace de aplicaciones mediana](#create-an-application-gateway) mediante la descarga de SSL con dos instancias en su subred propia.
1. [Agregue servidores al grupo de back-end](#add-servers-to-backend-pools).
1. [Elimine todos los recursos](#delete-all-resources). Incurrirá cargos por algunos de los recursos creados en este ejercicio mientras estén aprovisionados. Para minimizarlos, después de terminar el ejercicio, asegúrese de completar los pasos descritos en esta sección para eliminar los recursos que cree.



> [!IMPORTANT]
> La configuración adicional de la puerta de enlace de aplicaciones, incluidos los sondeos personalizados sobre el estado, las direcciones del grupo de back-end y las reglas se realiza después de que se configura la puerta de enlace de aplicaciones, no durante la implementación inicial.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Para crear una puerta de enlace de aplicaciones, complete estos pasos. La puerta de enlace de aplicaciones requiere su subred propia. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para que tenga varias subredes. Una vez que se implementa una puerta de enlace de aplicaciones en una subred adicional solo se pueden agregar a ella puertas de enlace de aplicaciones adicionales.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). Si aún no tiene cuenta, puede registrarse para obtener [una evaluación gratuita durante un mes](https://azure.microsoft.com/free).
1. En el panel Favoritos del portal, haga clic en **Nuevo**.
1. En la hoja **Nuevo**, haga clic en **Redes**. En la hoja **Redes**, haga clic en **Application Gateway**, como se muestra en la siguiente imagen:

    ![Creación de una puerta de enlace de aplicaciones][1]

1. En la hoja **Datos básicos** que aparece, escriba los valores siguientes y haga clic en **Aceptar**:

   | **Configuración** | **Valor** | **Detalles**
   |---|---|---|
   |**Name**|AdatumAppGateway|Nombre de la puerta de enlace de aplicaciones.|
   |**Nivel**|Estándar|Los valores disponibles son Estándar y WAF. Visite [Firewall de aplicaciones web](application-gateway-web-application-firewall-overview.md) para obtener más información sobre WAFS.|
   |**Tamaño de la SKU**|Mediano|Las opciones al elegir el nivel Estándar son pequeño, mediano y grande. Al elegir el nivel de WAF, las opciones solo son mediano y grande.|
   |**Recuento de instancias**|2|Número de instancias de la puerta de enlace de aplicaciones para alta disponibilidad. Los recuentos de instancias de 1 solo deben usarse para fines de pruebas.|
   |**Suscripción**|[Su suscripción]|Seleccione una suscripción donde se va a crear la puerta de enlace de la aplicación.|
   |**Grupos de recursos**|**Crear nuevo:** AdatumAppGatewayRG|Cree un grupo de recursos. El nombre del grupo de recursos debe ser único dentro de la suscripción seleccionada. Para más información sobre los grupos de recursos, lea el artículo [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Ubicación**|Oeste de EE. UU.||

1. En la hoja **Configuración** que aparece en **Red virtual**, haga clic en **Elegir una red virtual**. Se abrirá la hoja **Elegir una red virtual**.  Haga clic en **Crear nuevo** para abrir la hoja **Crear red virtual**.

 ![Elegir una red virtual][2]

1. En la hoja **Crear red virtual**, escriba los valores siguientes y luego haga clic en **Aceptar**. De esta forma, se cierran las hojas **Crear red virtual** y **Elegir una red virtual**. Además, se rellenará el campo **Subred** en la hoja **Configuración** con la subred elegida.

   |**Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|Nombre de la puerta de enlace de aplicaciones.|
   |**Espacio de direcciones**|10.0.0.0/16| Se trata del espacio de direcciones de la red virtual.|
   |**Nombre de subred**|AppGatewaySubnet|Nombre de la subred de la puerta de enlace de aplicaciones.|
   |**Intervalo de direcciones de subred**|10.0.0.0/28| Esta subred permite más subredes en la red virtual para miembros del grupo de back-end.|

1. En la hoja **Configuración** de **Configuración de IP de front-end**, seleccione **Pública** como el **Tipo de dirección IP**.

1. En la hoja **Configuración** de **Dirección IP pública**, haga clic en **Elegir una dirección IP pública** para abrir la hoja **Elegir una dirección IP pública** y luego haga clic en **Crear nuevo**.

 ![Elegir una dirección IP pública][3]

1. En la hoja **Crear dirección IP pública**, acepte el valor predeterminado y haga clic en **Aceptar**. De esta forma, se cerrarán las hojas **Elegir dirección IP pública** y **Crear dirección IP pública** y rellene el campo **Dirección IP pública** con la dirección IP pública elegida.

1. En la hoja **Configuración** en **Configuración de agente de escucha**, haga clic en **HTTPS** en **Protocolo**. Esto permite agregar campos adicionales. Haga clic en el icono de carpeta en el campo **Cargar certificado PFX** y seleccione el certificado .pfx apropiado. Escriba la información siguiente en los campos adicionales de **Configuración de agente de escucha**:

   |**Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |Nombre|Nombre del certificado|Este valor es un nombre descriptivo utilizado para hacer referencia al certificado.|
   |Password|Contraseña para .pfx| Se trata de la contraseña utilizada para la clave privada.|

1. Haga clic en **Aceptar** en la hoja **Configuración** para continuar.

1. Revise la configuración en la hoja **Resumen** y haga clic en **Aceptar** para iniciar la creación de la puerta de enlace de aplicaciones. Crear una puerta de enlace de aplicaciones es una tarea de ejecución prolongada y tardará tiempo en completarse.

## <a name="add-servers-to-backend-pools"></a>Adición de servidores a grupos de back-end

Cuando se crea la puerta de enlace de aplicaciones, los sistemas que hospedan la aplicación para el equilibrio de carga aún tienen que agregarse a dicha puerta de enlace. Las direcciones IP, los FQDN o las NIC de estos servidores se agregan a los grupos de direcciones de back-end.

### <a name="ip-address-or-fqdn"></a>Dirección IP o FQDN

1. Con la puerta de enlace de aplicaciones creada, en el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en la puerta de enlace de aplicaciones **AdatumAppGateway** en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir **AdatumAppGateway** en el cuadro **Filtrar por nombre…** para acceder fácilmente a la puerta de enlace de la aplicación.

1. Se muestra la puerta de enlace de aplicaciones que ha creado. Haga clic en **Grupos de back-end** y seleccione el grupo actual de back-end **appGatewayBackendPool** y, después, se abrirá la hoja **appGatewayBackendPool**.

   ![Grupos de back-end de Application Gateway][4]

1. Haga clic en **Agregar destino** para agregar direcciones IP de los valores de FQDN. Seleccione **Dirección IP o FQDN** como **Tipo** y escriba la dirección IP o el FQDN en el campo. Repita este paso para los miembros adicionales del grupo de back-end. Cuando termine, haga clic en **Guardar**.

### <a name="virtual-machine-and-nic"></a>Máquina virtual y NIC

También puede agregar NIC de máquinas virtuales como miembros del grupo de back-end. Solo las máquinas virtuales de la misma red virtual que la puerta de enlace de aplicaciones están disponibles en la lista desplegable.

1. Con la puerta de enlace de aplicaciones creada, en el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en la puerta de enlace de aplicaciones **AdatumAppGateway** en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir **AdatumAppGateway** en el cuadro **Filtrar por nombre…** para acceder fácilmente a la puerta de enlace de la aplicación.

1. Se muestra la puerta de enlace de aplicaciones que ha creado. Haga clic en **Grupos de back-end** y seleccione el grupo actual de back-end **appGatewayBackendPool** y, después, se abrirá la hoja **appGatewayBackendPool**.

   ![Grupos de back-end de Application Gateway][5]

1. Haga clic en **Agregar destino** para agregar direcciones IP de los valores de FQDN. Elija **Máquina virtual** como **Tipo** y seleccione la máquina virtual y la NIC que desea usar. Cuando termine, haga clic en **Guardar**.

   > [!NOTE]
   > Solo las máquinas virtuales de la misma red virtual que la puerta de enlace de aplicaciones están disponibles en el cuadro desplegable.

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, complete los pasos siguientes:

1. En el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en el grupo de recursos **AdatumAppGatewayRG** en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir **AdatumAppGatewayRG** en el cuadro **Filtrar por nombre…** para acceder fácilmente al grupo de recursos.
1. En la hoja **AdatumAppGatewayRG**, haga clic en el botón **Eliminar**.
1. El portal requiere que escriba el nombre del grupo de recursos para confirmar que desea eliminarlo. Haga clic en **Eliminar**, escriba AdatumAppGateway para el nombre de grupo de recursos y haga clic en **Eliminar**. Al eliminarse un grupo de recursos, se eliminan todos los recursos que contiene, por lo que siempre debe asegurarse de comprobar su contenido antes de eliminarlo. El portal elimina todos los recursos incluidos en el grupo de recursos y, después, el grupo de recursos en sí. Este proceso tarda varios minutos.

## <a name="next-steps"></a>Pasos siguientes

En este escenario se crea una puerta de enlace de aplicaciones predeterminada. Los siguientes pasos se usan para configurar la puerta de enlace de aplicaciones mediante la modificación de la configuración y el ajuste de las reglas de la puerta de enlace. Estos pasos se pueden encontrar si consulta los siguientes artículos:

Para aprender a crear sondeos de estado personalizado, visite [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Para aprender a configurar la descarga de SSL y eliminar la cara descripción de SSL de los servidores web, visite [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el Administrador de recursos de Azure](application-gateway-ssl-portal.md)

Aprenda a proteger aplicaciones con [Firewall de aplicaciones web de Application Gateway (versión preliminar)](application-gateway-webapplicationfirewall-overview.md), una característica de Application Gateway.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

