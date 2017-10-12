---
title: "Creación o actualización de una puerta de enlace de aplicaciones con un firewall de aplicaciones web | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones con un firewall de aplicaciones web en el portal.
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>Creación de una puerta de enlace de aplicaciones con un firewall de aplicaciones web en el portal

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI de Azure](application-gateway-web-application-firewall-cli.md)

Aprenda a crear una puerta de enlace de aplicaciones web habilitada para un firewall de aplicaciones web (WAF).

El WAF de Azure Application Gateway protege las aplicaciones web de ataques web comunes, como inyección de código SQL, ataques de scripts entre sitios y secuestros de sesiones. El WAF sirve de protección frente a muchas de las vulnerabilidades web de OWASP Top 10.

## <a name="scenarios"></a>Escenarios

En este artículo se presentan dos escenarios. En el primer escenario, aprenderá a [crear una puerta de enlace de aplicaciones con un WAF](#create-an-application-gateway-with-web-application-firewall). En el segundo escenario, aprenderá a [agregar un WAF a una puerta de enlace de aplicaciones existente](#add-web-application-firewall-to-an-existing-application-gateway).

![Escenario de ejemplo][scenario]

> [!NOTE]
> Puede agregar sondeos de estado personalizados, direcciones del grupo de back-end y otras reglas a la puerta de enlace de aplicaciones. Estas aplicaciones se configuran después de la puerta de enlace de aplicaciones y no durante la implementación inicial.

## <a name="before-you-begin"></a>Antes de empezar

 Las puertas de enlace de aplicaciones necesitan su propia subred. Cuando cree una red virtual, asegúrese de dejar suficiente espacio de direcciones para tener varias subredes. Una vez que se implementa una puerta de enlace de aplicaciones en una subred, solo se puedan agregar otras puertas de enlace de aplicaciones a dicha subred.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Incorporación de un firewall de aplicaciones web a una puerta de enlace de aplicaciones existente

En este ejemplo, se actualiza una puerta de enlace de aplicaciones existente para que admita un WAF en el modo **Prevención**.

1. En el panel **Favoritos** de Azure Portal, seleccione **Todos los recursos**. En la hoja **Todos los recursos**, seleccione la puerta de enlace de aplicaciones existente. Si la suscripción seleccionada ya contiene varios recursos, puede escribir el nombre en **Filtrar por nombre** para acceder fácilmente a la zona DNS.

   ![Selección de una puerta de enlace de aplicaciones existente][1]

2. Seleccione **Firewall de aplicaciones web** y actualice la configuración de la puerta de enlace de aplicaciones. Cuando la actualización finalice, seleccione **Guardar**. 

3. Utilice la siguiente configuración para actualizar una puerta de enlace de aplicaciones existente de forma que admita el uso de un WAF:

   | **Configuración** | **Valor** | **Detalles**
   |---|---|---|
   |**Actualizar al nivel WAF**| Activado | Esta opción establece el nivel de la puerta de enlace de aplicaciones en el nivel WAF.|
   |**Estado de Firewall**| habilitado | Esta configuración habilita el firewall en el WAF.|
   |**Modo de firewall** | Prevención | Esta opción determina cómo va a administrar el WAF el tráfico malintencionado. El modo **Detección** solo registra los eventos. El modo **Prevención** registra los eventos y detiene el tráfico malintencionado.|
   |**Conjunto de reglas**|3.0|Esta opción determina el [conjunto de reglas básico](application-gateway-web-application-firewall-overview.md#core-rule-sets) que se usa para proteger a los miembros del grupo de back-end.|
   |**Configurar reglas deshabilitadas**|Varía|Para evitar posibles falsos positivos, puede utilizar esta opción para deshabilitar algunas [reglas y grupos de reglas](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Cuando se actualiza una puerta de enlace de aplicaciones existente a la SKU de WAF, el tamaño de la SKU cambia a **medio**. Una vez que finalice la configuración, podrá configurar de nuevo esta opción.

    ![Configuración básica][2-1]

    > [!NOTE]
    > Para consultar los logs de WAF, habilite el diagnóstico y seleccione **ApplicationGatewayFirewallLog**. Solo debe elegir **1** como número de instancias para realizar pruebas. No es recomendable utilizar un número de instancias inferior a **2**, ya que no está cubierto por el SLA. Las puertas de enlace pequeñas no están disponibles cuando se usa un WAF.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Creación de una puerta de enlace de aplicaciones con un firewall de aplicaciones web

En este escenario:

* Creará una puerta de enlace de aplicaciones de WAF de tamaño medio con dos instancias.
* Creará una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
* Creará una subred denominada Appgatewaysubnet que usa 10.0.0.0/28 como bloque CIDR.
* Configurará un certificado para la descarga SSL.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). Si aún no tiene una cuenta, puede registrarse para disfrutar de [una evaluación gratuita de un mes](https://azure.microsoft.com/free).

2. En el panel **Favoritos** del portal, seleccione **Nuevo**.

3. En la hoja **Nuevo**, seleccione **Redes**. En la hoja **Redes**, seleccione **Puerta de enlace de aplicaciones**, tal y como se muestra en la siguiente imagen:

    ![Creación de puertas de enlace de aplicaciones][1]

4. En la hoja **Datos básicos** que aparece, escriba los valores siguientes y seleccione **Aceptar**:

   | **Configuración** | **Valor** | **Detalles**
   |---|---|---|
   |**Name**|AdatumAppGateway|Nombre de la puerta de enlace de aplicaciones.|
   |**Nivel**|WAF|Los valores disponibles son Estándar y WAF. Para más información sobre los WAF, consulte [Firewall de aplicaciones web](application-gateway-web-application-firewall-overview.md).|
   |**Tamaño de la SKU**|Mediano|Las opciones del nivel estándar son **Pequeño**, **Medio** y **Grande**. Las opciones del nivel WAF son exclusivamente **Medio** y **Grande**.|
   |**Recuento de instancias**|2|Número de instancias de la puerta de enlace de aplicaciones para alta disponibilidad. Solo debe elegir 1 como número de instancias para realizar pruebas.|
   |**Suscripción**|[Su suscripción]|Seleccione la suscripción que va a utilizar para crear la puerta de enlace de aplicaciones.|
   |**Grupos de recursos**|**Crear nuevo:** AdatumAppGatewayRG|Cree un grupo de recursos. El nombre del grupo de recursos debe ser único dentro de la suscripción seleccionada. Para más información sobre los grupos de recursos, lea el artículo [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Ubicación**|Oeste de EE. UU.||

   ![Opciones de configuración básicas][2-2]

5. En la hoja **Configuración** que aparece en **Red virtual**, seleccione **Elegir una red virtual**. En la hoja **Elegir red virtual**, haga clic en **Crear nuevo**.

   ![Opción de red virtual][2]

6. En la hoja **Crear red virtual**, escriba los valores siguientes y seleccione **Aceptar**. El campo **Subred** de la hoja **Configuración** se rellena con la subred elegida.

   |**Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|Nombre de la puerta de enlace de aplicaciones.|
   |**Espacio de direcciones**|10.0.0.0/16| Este valor es el espacio de direcciones de la red virtual.|
   |**Nombre de subred**|AppGatewaySubnet|Nombre de la subred de la puerta de enlace de aplicaciones.|
   |**Intervalo de direcciones de subred**|10.0.0.0/28 | Esta subred permite más subredes en la red virtual para los miembros del grupo de back-end.|

7. En la hoja **Configuración**, en **Configuración de IP de front-end**, seleccione **Pública** como **Tipo de dirección IP**.

8. En la hoja **Configuración**, en **Dirección IP pública**, seleccione **Elegir una dirección IP pública**. En la hoja **Elegir dirección IP pública**, seleccione **Crear nuevo**.

   ![Opción de dirección IP pública][3]

9. En la hoja **Crear dirección IP pública**, acepte el valor predeterminado y seleccione **Aceptar**. El campo **Dirección IP pública** se rellena con la dirección IP pública elegida.

10. En la hoja **Configuración**, en **Configuración de agente de escucha**, haga clic en la opción **HTTP** de **Protocolo**. Para usar **HTTPS**, se requiere un certificado. Se necesita la clave privada del certificado. Proporcione una exportación .pfx del certificado y escriba la contraseña del archivo.

11. Configure los valores específicos de **WAF**.

   |**Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |**Estado de Firewall**| habilitado| Esta opción activa o desactiva el WAF.|
   |**Modo de firewall** | Prevención| Esta configuración determina las acciones que realiza el WAF sobre el tráfico malintencionado. El modo **Detección** solamente registra el tráfico. El modo **Prevención** registra y detiene el tráfico con una respuesta de tipo 403-No autorizado.|


12. Consulte la página **Resumen** y seleccione **Aceptar**. La puerta de enlace de aplicaciones se pone en cola y se crea.

13. Una vez creada la puerta de enlace de aplicaciones, navegue hasta ella en el portal para continuar con la configuración de la puerta de enlace de aplicaciones.

    ![Vista de los recursos de la puerta de enlace de aplicaciones][10]

Este procedimiento permite crear una puerta de enlace de aplicaciones básica con la configuración predeterminada para el agente de escucha, el grupo de back-end, la configuración HTTP de back-end y las reglas. Una vez que la operación de aprovisionamiento finalice correctamente, podrá modificar esta configuración para adaptarla a su implementación.

> [!NOTE]
> Las puertas de enlace de aplicaciones creadas con la configuración básica de WAF se configuran con CRS 3.0 como medida de protección.

## <a name="next-steps"></a>Pasos siguientes

Si desea configurar un alias de dominio personalizado para la [dirección IP pública](../dns/dns-custom-domain.md#public-ip-address), puede utilizar Azure DNS u otro proveedor de DNS.

Si desea configurar un registro de diagnóstico para incluir todos los eventos que se detecten o se impidan con un WAF, consulte este artículo sobre [diagnósticos de Application Gateway](application-gateway-diagnostics.md).

Si desea crear sondeos de estado personalizados, consulte este artículo sobre la [creación de un sondeo de estado personalizado](application-gateway-create-probe-portal.md).

Si desea configurar la descarga SSL y eliminar la costosa suscripción a SSL de los servidores web, consulte este artículo sobre la [configuración de la descarga SSL](application-gateway-ssl-portal.md).

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
