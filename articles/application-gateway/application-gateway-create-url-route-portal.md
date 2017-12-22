---
title: Crear una regla basada en ruta de acceso para una puerta de enlace de aplicaciones mediante Azure Portal | Microsoft Docs
description: Aprenda a crear una regla basada en ruta de acceso para una puerta de enlace de aplicaciones mediante Azure Portal.
services: application-gateway
documentationcenter: na
author: davidmu1
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
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Crear una regla basada en ruta de acceso para una puerta de enlace de aplicaciones mediante Azure Portal

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI de Azure 2.0](application-gateway-create-url-route-cli.md)

Con el enrutamiento basado en ruta de dirección URL puede asociar rutas a partir de la ruta de dirección URL de solicitudes HTTP. Comprueba si hay una ruta a un grupo de servidores back-end configurada para la dirección URL indicada en la puerta de enlace de aplicaciones y, luego, envía el tráfico de red al grupo definido. Un uso habitual del enrutamiento basado en ruta de dirección URL es el equilibrio de carga de las solicitudes de diferentes tipos de contenido entre diferentes grupos de servidores back-end.

Las puertas de enlace de aplicaciones tienen dos tipos de reglas: básicas y basadas en ruta de dirección URL. El tipo de regla básico proporciona un servicio round robin para los grupos de servidores back-end. Las reglas basadas en ruta de acceso, además de la distribución round robin, también usan el patrón de ruta de acceso de la dirección URL de solicitud al elegir el grupo de servidores back-end adecuado.

## <a name="scenario"></a>Escenario

En el siguiente escenario se crea una regla basada en ruta de acceso en una puerta de enlace de aplicaciones existente.
En este escenario se presupone que ya ha seguido los pasos descritos en [Creación de una puerta de enlace de aplicaciones con el portal](application-gateway-create-gateway-portal.md).

![Ruta de dirección URL][scenario]

## <a name="createrule"></a>Creación de la regla basada en ruta de acceso

Una regla basada en ruta de acceso necesita su propio agente de escucha. Antes de crear la regla, compruebe que tiene un agente de escucha disponible para su uso.

### <a name="step-1"></a>Paso 1

Vaya a [Azure Portal](http://portal.azure.com) y seleccione una puerta de enlace de aplicaciones existente. Haga clic en **Reglas**.

![Introducción a Application Gateway][1]

### <a name="step-2"></a>Paso 2

Haga clic en el botón **Basada en ruta de acceso** para agregar una nueva regla basada en ruta de acceso.

### <a name="step-3"></a>Paso 3

La hoja **Add path-based rule** (Agregar regla basada en ruta de acceso) tiene dos secciones. La primera sección es donde se define el agente de escucha, el nombre de la regla y la configuración predeterminada de la ruta de acceso. La configuración predeterminada de la ruta de acceso se aplica a las rutas que no entran en la regla personalizada basada en la ruta de acceso. La segunda sección de la hoja **Add path-based rule** (Agregar regla basada en ruta de acceso) es donde se definen las propias reglas basadas en ruta de acceso.

**Configuración básica**

* **Nombre**: Nombre descriptivo para la regla accesible en el portal.
* **Agente de escucha**: Agente de escucha que se usa para la regla.
* **Grupo de back-end predeterminado**: Servidor back-end que se usará para la regla predeterminada.
* **Configuración de HTTP predeterminada**: Configuración HTTP que se usará para la regla predeterminada.

**Configuración de la regla basada en ruta de acceso**

* **Nombre**: Nombre descriptivo para la regla basada en ruta de acceso.
* **Rutas de acceso**: Ruta de acceso que buscará la regla al reenviar el tráfico.
* **Grupo de back-end**: Servidor back-end que se usará para la regla.
* **Configuración de HTTP**: Configuración HTTP que se usará para la regla.

> [!IMPORTANT]
> La opción **Rutas de acceso** es la lista de patrones de ruta de acceso con los que se buscan coincidencias. Cada patrón debe comenzar con una barra diagonal y solo se permiten los asteriscos al final. Ejemplos válidos: /xyz, /xyz*, y /xyz/*.  

![Agregar hoja de regla basada en ruta de acceso con información completada][2]

La incorporación de una regla basada en ruta de acceso a una puerta de enlace de aplicaciones existente es un proceso que se lleva a cabo fácilmente en Azure Portal. Después de crear una regla basada en ruta de acceso, puede editarla para incluir más reglas. 

![Agregar más reglas basadas en ruta de acceso][3]

Este paso configura una regla basada en la ruta de acceso. Es importante saber que las solicitudes no se vuelven a escribir. Según van llegando, la puerta de enlace de aplicaciones las inspecciona y, en función del patrón de dirección URL, las envía al grupo de servidores back-end adecuado.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar la descarga de SSL con Azure Application Gateway, vea [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante Azure Portal](application-gateway-ssl-portal.md).

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
