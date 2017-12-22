---
title: Hospedaje de varios sitios con Azure Application Gateway | Microsoft Docs
description: "En esta página se proporcionan instrucciones para configurar una puerta de enlace de aplicaciones de Azure para hospedar varias aplicaciones web en la misma puerta de enlace con Azure Portal."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 28a7fcb3e08a9c4b6a27e9fbc8d3ebae309adc62
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configuración de una puerta de enlace de aplicaciones existente para hospedar varias aplicaciones web

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-multisite-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

El hospedaje de varios sitios permite implementar más de una aplicación web en la misma puerta de enlace de aplicaciones. Se basa en la presencia de un encabezado host en la solicitud HTTP entrante para determinar el agente de escucha que recibe el tráfico. Luego, dicho agente dirige el tráfico al grupo de back-end adecuado, el configurado en la definición de las reglas de la puerta de enlace. En las aplicaciones web con SSL habilitado, la puerta de enlace de aplicaciones depende de la extensión de la indicación de nombre de servidor (SNI) para elegir el agente de escucha correcto para el tráfico web.En las aplicaciones web con SSL habilitado, Application Gateway depende de la extensión de la indicación de nombre de servidor (SNI) para elegir el agente de escucha correcto para el tráfico web. Un uso común del hospedaje de varios sitios es el equilibrio de carga de las solicitudes de diferentes dominios web entre diferentes grupos de servidores de back-end. De forma similar, varios subdominios del mismo dominio raíz también se pueden hospedar en la misma puerta de enlace de aplicaciones.

## <a name="scenario"></a>Escenario

En el siguiente ejemplo, la puerta de enlace de aplicaciones sirve el tráfico a contoso.com y a fabrikam.com con dos grupos de servidores back-end: el grupo de servidores de contoso y el grupo de servidores de fabrikam. Se puede usar una configuración similar para hospedar subdominios como app.contoso.com y blog.contoso.com.

![escenario multisitio][multisite]

## <a name="before-you-begin"></a>Antes de empezar

Este escenario agrega compatibilidad multisitio a una puerta de enlace de aplicaciones existente. Para completar este escenario debe estar disponible para su configuración una puerta de enlace de aplicaciones. Visite [Creación de una puerta de enlace de aplicaciones mediante el portal](application-gateway-create-gateway-portal.md) para aprender a crear una puerta de enlace de aplicaciones básica en el portal.

A continuación se muestran los pasos necesarios para actualizar una puerta de enlace de aplicaciones:

1. Cree grupos de back-end para cada sitio.
2. Cree un agente de escucha para cada puerta de enlace de aplicaciones del sitio que lo admitirá.
3. Cree reglas para asignar cada agente de escucha al back-end apropiado.

## <a name="requirements"></a>Requisitos

* **Grupo de servidores back-end** : lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública. También puede utilizarse el FQDN.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha** : tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL). En el caso de las puertas de enlace de aplicaciones con sitios múltiples habilitados, también se agregan el nombre de host y los indicadores de SNI.
* **Regla:** enlaza el agente de escucha y el grupo de servidores back-end, y define a qué grupo de servidores back-end se debe redireccionar el tráfico que llegue a un agente de escucha concreto. Las reglas se procesan en el orden en que aparecen y el tráfico se dirige a través de la primera regla que coincida independientemente de la especificidad. Por ejemplo, si tiene una regla que usa un agente de escucha básico y una regla que usa un agente de escucha multisitio en el mismo puerto, la regla con el agente de escucha multisitio debe aparecer antes que la regla con el agente de escucha básico para que la regla multisitio funcione según lo previsto. 
* **Certificados:** cada agente de escucha requiere un certificado único, en este ejemplo se crean dos agentes de escucha para varios sitios. Deben crearse dos certificados .pfx y las contraseñas para ellos.

## <a name="create-back-end-pools-for-each-site"></a>Creación de grupos de back-end para cada sitio

Se necesita un grupo de back-end para cada sitio que va a admitir esa puerta de enlace de aplicaciones; en este caso se crearán dos, uno para contoso11.com y otro para fabrikam11.com.

### <a name="step-1"></a>Paso 1

Vaya a una puerta de enlace de aplicaciones existente en Azure Portal (https://portal.azure.com). Seleccione **Grupos de back-end** y haga clic en **Agregar**.

![adición de grupos de back-end][7]

### <a name="step-2"></a>Paso 2

Rellene la información para el grupo de back-end **grupo1**, agregue las direcciones IP o nombres completos para los servidores back-end y haga clic en **Aceptar**

![configuración del grupo de back-end grupo1][8]

### <a name="step-3"></a>Paso 3

En la hoja de grupos de back-end, haga clic en **Agregar** para agregar el grupo de back-end adicional **pool2**, agregando la direcciones IP o nombres completos para los servidores back-end y haga clic en **Aceptar**.

![configuración del grupo de back-end grupo2][9]

## <a name="create-listeners-for-each-back-end"></a>Creación de agentes de escucha para cada back-end

Application Gateway se basa en los encabezados de host HTTP 1.1 para hospedar más de un sitio web en la misma dirección IP pública y en el mismo puerto. El agente de escucha básico creado en el portal no contiene esta propiedad.

### <a name="step-1"></a>Paso 1

Haga clic en **Agentes de escucha** en la puerta de enlace de aplicaciones y haga clic en **Multisitio** para agregar el primer agente de escucha.

![hoja de información general de los agentes de escucha][1]

### <a name="step-2"></a>Paso 2

Rellene la información del agente de escucha. En este ejemplo se configura la terminación SSL y se crea un nuevo puerto de front-end. Cargue el certificado .pfx que se usará para la terminación SSL. La única diferencia de esta hoja en comparación con la hoja del agente de escucha básica estándar es el nombre de host.

![hoja de propiedades del agente de escucha][2]

### <a name="step-3"></a>Paso 3

Haga clic en **Multisitio** y cree otro agente de escucha, tal como se describe en el paso anterior para el segundo sitio. Asegúrese de utilizar un certificado diferente para el segundo agente de escucha. La única diferencia de esta hoja en comparación con la hoja del agente de escucha básica estándar es el nombre de host. Rellene la información del agente de escucha y haga clic en **Aceptar**.

![hoja de propiedades del agente de escucha][3]

> [!NOTE]
> La creación de agentes de escucha en Azure Portal para la puerta de enlace de aplicaciones es una tarea de larga duración; puede tardar algún tiempo en crear los dos agentes de escucha en este escenario. Cuando finalice, los agentes de escucha se muestran en el portal, tal como se muestra en la siguiente imagen:

![información general del agente de escucha][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>Creación de reglas para asignar agentes de escucha a grupos de back-end

### <a name="step-1"></a>Paso 1

Vaya a una puerta de enlace de aplicaciones existente en Azure Portal (https://portal.azure.com). Seleccione **Reglas** y elija la regla predeterminada existente **Regla1** y haga clic en **Editar**.

### <a name="step-2"></a>Paso 2

Rellene la hoja de reglas, tal como se muestra en la imagen siguiente. Elija el primer agente de escucha y el primer grupo y haga clic en **Guardar** cuando haya terminado.

![edición de una regla existente][6]

### <a name="step-3"></a>Paso 3

Haga clic en **Regla básica** para crear la segunda regla. Rellene el formulario con el segundo agente de escucha y el segundo grupo de back-end, y haga clic en **Aceptar** para guardarlo.

![adición de una hoja de reglas básicas][10]

Este escenario completa la configuración de una puerta de enlace de aplicaciones existentes con compatibilidad multisitio a través de Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a proteger sitios web con [Firewall de aplicaciones web de Application Gateway (versión preliminar)](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png
