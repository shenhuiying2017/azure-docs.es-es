---
title: "Creación de una regla basada en ruta de acceso para Azure Application Gateway mediante Azure Portal | Microsoft Docs"
description: Aprenda a crear una regla basada en ruta de acceso para una puerta de enlace de aplicaciones mediante el portal.
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c184e94a04cfbdedcae70ed154aeb7dd134d1baf
ms.contentlocale: es-es
ms.lasthandoff: 08/04/2017

---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Creación de una regla basada en ruta de acceso para una puerta de enlace de aplicaciones mediante el portal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-url-route-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI de Azure 2.0](application-gateway-create-url-route-cli.md)

El enrutamiento basado en URL permite asociar rutas en función de la dirección URL de la solicitud HTTP. Comprueba si hay una ruta a un grupo de back-end configurada para la dirección URL indicada en Application Gateway y envía el tráfico de red al grupo de back-end definido. Un uso común del enrutamiento basado en URL es el equilibrio de carga de las solicitudes de diferentes tipos de contenido entre diferentes grupos de servidores de back-end.

El enrutamiento basado en URL introduce un nuevo tipo de regla en Application Gateway. Application Gateway tiene dos tipos de reglas: básicas y basadas en la ruta de acceso. El tipo de regla básica proporciona un servicio round robin para los grupos de back-end mientras que las reglas basadas en la ruta de acceso, además de la distribución round robin, también tienen en cuenta el patrón de la dirección URL de la solicitud durante la elección del grupo de back-end adecuado.

## <a name="scenario"></a>Escenario

El siguiente escenario pasa por la creación de una regla basada en ruta de acceso en una puerta de enlace de aplicaciones existente.
En este escenario se asume que ya ha seguido los pasos para [crear una puerta de enlace de aplicaciones](application-gateway-create-gateway-portal.md).

![ruta de dirección URL][scenario]

## <a name="createrule"></a>Creación de la regla basada en ruta de acceso

Una regla basada en ruta de acceso requiere su propio agente de escucha; antes de crear la regla compruebe tiene un agente de escucha disponible para utilizar.

### <a name="step-1"></a>Paso 1

Vaya a [Azure Portal](http://portal.azure.com) y seleccione una Application Gateway existente. Haga clic en **Reglas**

![Introducción a Application Gateway][1]

### <a name="step-2"></a>Paso 2

Haga clic en el botón **Basada en ruta de acceso** para agregar una nueva regla basada en ruta de acceso.

### <a name="step-3"></a>Paso 3

La hoja **Add path-based rule** (Agregar regla basada en ruta de acceso) tiene dos secciones. La primera sección es donde se define el agente de escucha, el nombre de la regla y la configuración de la ruta de acceso predeterminada. La configuración predeterminada de la ruta de acceso se aplica a las rutas que no entran en la regla personalizada basada en la ruta de acceso. La segunda sección de la hoja **Add path-based rule** (Agregar regla basada en ruta de acceso) es donde se definen las propias reglas basadas en ruta de acceso.

**Configuración básica**

* **Nombre**: este valor es un nombre descriptivo para la regla a la que se puede acceder en el portal.
* **Agente de escucha**: este valor es el agente de escucha que se utiliza para la regla.
* **Grupo de back-end predeterminado** : opción de configuración que define el back-end que se usará para la regla predeterminada.
* **Configuración HTTP predeterminada** : opción de configuración que define los ajustes de HTTP que se usarán para la regla predeterminada.

**Reglas basadas en ruta de acceso**

* **Nombre**: este valor es un nombre descriptivo para la regla basada en ruta de acceso.
* **Rutas**: esta opción de configuración define la ruta de acceso que buscará la regla al reenviar el tráfico.
* **Grupo de back-end** : opción de configuración que define el back-end que se usará para la regla.
* **Configuración HTTP** : opción de configuración que define los ajustes de HTTP que se usarán para la regla.

> [!IMPORTANT]
> Rutas de acceso: la lista de patrones de ruta de acceso con los que se buscan coincidencias. Cada uno de ellos debe comenzar con / y el único lugar donde se permite un carácter "\*" es al final. Algunos ejemplos válidos son /xyz, /xyz* o /xyz/*.  

![Agregar hoja de regla basada en ruta de acceso con información completada][2]

La incorporación de una regla basada en ruta de acceso a una puerta de enlace de aplicaciones existente es un proceso que se realiza fácilmente a través del portal. Una vez creada una regla de ruta de acceso, es posible modificarla para agregar reglas adicionales. 

![agregar reglas basadas en ruta de acceso adicionales][3]

Este paso configura una regla basada en la ruta de acceso. Es importante comprender que las solicitudes no se vuelven a escribir según van llegando; Application Gateway las inspecciona y, según el modelo de dirección URL, las envía al back-end adecuado.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar la descarga de SSL con Azure Application Gateway, consulte [Configuración de la descarga SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png

