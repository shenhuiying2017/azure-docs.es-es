---
title: "Creación o actualización de una instancia de Azure Application Gateway con el firewall de aplicaciones web | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones con el firewall de aplicaciones web mediante el portal.
services: application-gateway
documentationcenter: na
author: georgewallace
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
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 650f26d19615d27a94f3947aad7b7904b6c1fabc
ms.contentlocale: es-es
ms.lasthandoff: 08/04/2017

---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Creación de una puerta de enlace de aplicaciones con el firewall de aplicaciones web mediante el portal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI de Azure](application-gateway-web-application-firewall-cli.md)

Aprenda a crear una puerta de enlace de aplicaciones web habilitada para un firewall de aplicaciones web.

El firewall de aplicaciones web (WAF) de Azure Application Gateway protege las aplicaciones web de ataques web comunes, como inyección de código SQL, ataques de scripts entre sitios y secuestros de sesiones. La aplicación web protege de muchas de las vulnerabilidades web de OWASP Top 10.

## <a name="scenarios"></a>Escenarios

En este artículo, hay dos escenarios:

En el primer escenario, aprenderá a [crear una puerta de enlace de aplicaciones con el firewall de aplicaciones web](#create-an-application-gateway-with-web-application-firewall)

En el segundo escenario, aprenderá a [agregar el firewall de aplicaciones web a una puerta de enlace de aplicaciones existente](#add-web-application-firewall-to-an-existing-application-gateway).

![Escenario de ejemplo][scenario]

> [!NOTE]
> La configuración adicional de la puerta de enlace de aplicaciones, incluidos los sondeos personalizados sobre el estado, las direcciones del grupo de back-end y las reglas se realiza después de que se configura la puerta de enlace de aplicaciones, no durante la implementación inicial.

## <a name="before-you-begin"></a>Antes de empezar

Azure Application Gateway requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para que tenga varias subredes. Una vez que se implementa una puerta de enlace de aplicaciones en una subred adicional solo se pueden agregar a ella puertas de enlace de aplicaciones adicionales.

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> Adición del firewall de aplicaciones web a una puerta de enlace de aplicaciones existente

En este ejemplo se actualiza una puerta de enlace de aplicaciones existente para admitir el firewall de aplicaciones web en modo de prevención.

1. En el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en la puerta de enlace de aplicaciones existente en la hoja **Todos los recursos**. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir el nombre en **Filtrar por nombre...** para acceder fácilmente a la zona DNS.

   ![Creación de una puerta de enlace de aplicaciones][1]

1. Haga clic en **Firewall de aplicaciones web** y actualice la configuración de la puerta de enlace de aplicaciones. Cuando haya terminado, haga clic en **Guardar**

    La configuración para actualizar una puerta de enlace de aplicaciones existente para admitir el firewall de aplicaciones web es:

   | **Configuración** | **Valor** | **Detalles**
   |---|---|---|
   |**Actualizar al nivel WAF**| Activado | Esta opción establece el nivel de la puerta de enlace de aplicaciones al nivel WAFS.|
   |**Estado de Firewall**| habilitado | Esta configuración habilita el firewall en el WAF.|
   |**Modo de firewall** | Prevención | Esta configuración hace referencia a cómo el firewall de aplicaciones web se ocupa del tráfico malintencionado. El modo **Detección** solo registra los eventos, mientras que el modo **Prevención** registra los eventos y detiene el tráfico malintencionado.|
   |**Conjunto de reglas**|3.0|Esta configuración determina el [conjunto de reglas básico](application-gateway-web-application-firewall-overview.md#core-rule-sets) que se usa para proteger a los miembros del grupo de back-end.|
   |**Configurar reglas deshabilitadas**|Varía|Para evitar posibles falsos positivos, esta configuración le permite deshabilitar ciertas [reglas y grupos de reglas](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Al actualizar una puerta de enlace de aplicaciones existente para la SKU de WAF, cambia el tamaño de la SKU a **medio**. Este cambio se puede reconfigurar una vez finalizada la configuración.

    ![hoja que muestra configuración básica][2-1]

    > [!NOTE]
    > Para ver los registros del firewall de aplicaciones web, se deben habilitar los diagnósticos y se debe seleccionar ApplicationGatewayFirewallLog. Para las pruebas se puede elegir 1 en Número de instancias. Es importante saber que el SLA no cubre ningún número de instancias que esté por debajo de las dos instancias y, por consiguiente, no se recomienda. Las puertas de enlace pequeñas no están disponibles cuando se usa el firewall de aplicaciones web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>crear una puerta de enlace de aplicaciones con el firewall de aplicaciones web

En este escenario:

* Creará una puerta de enlace de aplicaciones de firewall de aplicaciones web con dos instancias.
* Creará una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
* Creará una subred denominada Appgatewaysubnet que usa 10.0.0.0/28 como bloque CIDR.
* Configurará un certificado para la descarga SSL.

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Si aún no tiene cuenta, puede registrarse para obtener [una evaluación gratuita durante un mes](https://azure.microsoft.com/free).
1. En el panel Favoritos del portal, haga clic en **Nuevo**.
1. En la hoja **Nuevo**, haga clic en **Redes**. En la hoja **Redes**, haga clic en **Application Gateway**, como se muestra en la siguiente imagen:
1. Navegue a Azure Portal, haga clic en **Nuevo** > **Redes** > **Application Gateway**

    ![Creación de una puerta de enlace de aplicaciones][1]

1. En la hoja **Datos básicos** que aparece, escriba los valores siguientes y haga clic en **Aceptar**:

   | **Configuración** | **Valor** | **Detalles**
   |---|---|---|
   |**Name**|AdatumAppGateway|Nombre de la puerta de enlace de aplicaciones.|
   |**Nivel**|WAF|Los valores disponibles son Estándar y WAF. Visite [Firewall de aplicaciones web](application-gateway-web-application-firewall-overview.md) para obtener más información sobre WAFS.|
   |**Tamaño de la SKU**|Mediano|Las opciones al elegir el nivel Estándar son pequeño, mediano y grande. Al elegir el nivel de WAF, las opciones solo son mediano y grande.|
   |**Recuento de instancias**|2|Número de instancias de la puerta de enlace de aplicaciones para alta disponibilidad. Los recuentos de instancias de 1 solo deben usarse para fines de pruebas.|
   |**Suscripción**|[Su suscripción]|Seleccione una suscripción donde se va a crear la puerta de enlace de la aplicación.|
   |**Grupos de recursos**|**Crear nuevo:** AdatumAppGatewayRG|Cree un grupo de recursos. El nombre del grupo de recursos debe ser único dentro de la suscripción seleccionada. Para más información sobre los grupos de recursos, lea el artículo [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Ubicación**|Oeste de EE. UU.||

   ![hoja que muestra configuración básica][2-2]

1. En la hoja **Configuración** que aparece en **Red virtual**, haga clic en **Elegir una red virtual**. Este paso abre la hoja **Elegir una red virtual**.  Haga clic en **Crear nuevo** para abrir la hoja **Crear red virtual**.

   ![Elegir una red virtual][2]

1. En la hoja **Crear red virtual**, escriba los valores siguientes y luego haga clic en **Aceptar**. Este paso cierra las hojas **Crear red virtual** y **Elegir una red virtual**. Además, en este paso se rellena el campo **Subred** en la hoja **Configuración** con la subred elegida.

   |**Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|Nombre de la puerta de enlace de aplicaciones.|
   |**Espacio de direcciones**|10.0.0.0/16| Este valor es el espacio de direcciones de la red virtual.|
   |**Nombre de subred**|AppGatewaySubnet|Nombre de la subred de la puerta de enlace de aplicaciones.|
   |**Intervalo de direcciones de subred**|10.0.0.0/28 | Esta subred permite más subredes en la red virtual para miembros del grupo de back-end.|

1. En la hoja **Configuración** de **Configuración de IP de front-end**, seleccione **Pública** como el **Tipo de dirección IP**.

1. En la hoja **Configuración** de **Dirección IP pública**, haga clic en **Elegir una dirección IP pública** para abrir la hoja **Elegir una dirección IP pública** y luego haga clic en **Crear nuevo**.

   ![Elegir una dirección IP pública][3]

1. En la hoja **Crear dirección IP pública**, acepte el valor predeterminado y haga clic en **Aceptar**. De esta forma, se cierran las hojas **Elegir dirección IP pública** y **Crear dirección IP pública** y se rellena el campo **Dirección IP pública** con la dirección IP pública elegida.

1. En la hoja **Configuración** en **Configuración de agente de escucha**, haga clic en **HTTP** en **Protocolo**. Para usar **https**, se requiere un certificado. La clave privada del certificado es necesaria, por lo que es preciso proporcionar un .pfx exportado del certificado y la contraseña del archivo.

1. Configure los valores específicos de **WAF** .

   |**Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |**Estado de Firewall**| habilitado| Esta opción activa o desactiva WAF.|
   |**Modo de firewall** | Prevención| Esta configuración determina las acciones que realiza WAF sobre el tráfico malintencionado. Si se elige **Detección** , solo se registra el tráfico.  Si se elige **Prevención** , el tráfico se registra y se detiene con una respuesta de error 403 no autorizado.|


1. Revise la página Resumen y haga clic en **Aceptar**.  La puerta de enlace de aplicaciones se pone en cola y se crea.

1. Una vez creada la puerta de enlace de aplicaciones, navegue hasta ella en el portal para continuar con su configuración.

    ![Vista de recursos de Application Gateway][10]

Estos pasos permiten crear una puerta de enlace de aplicaciones básica con la configuración predeterminada para el agente de escucha, el grupo de back-end, la configuración de http de back-end y las reglas. Esta configuración se puede modificar para adaptarse a la implementación una vez que el aprovisionamiento sea correcto

> [!NOTE]
> Las puertas de enlace de la aplicación creadas con la configuración de firewall de aplicación web básica se configuran con CRS 3.0 para protección.

## <a name="next-steps"></a>Pasos siguientes

A continuación, puede aprender a configurar un alias de dominio personalizado para la [dirección IP pública](../dns/dns-custom-domain.md#public-ip-address) con Azure DNS u otro proveedor DNS.

Aprenda a configurar el registro de diagnóstico para registrar los eventos que se detectan o impiden con el firewall de aplicaciones web en [Diagnósticos de Application Gateway](application-gateway-diagnostics.md).

Para aprender a crear sondeos de estado personalizado, visite [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Para aprender a configurar la descarga de SSL y eliminar la cara descripción de SSL de los servidores web, visite [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante Azure Resource Manager](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

