---
title: "Precios y facturación: Azure Logic Apps | Microsoft Docs"
description: "Descubra cómo funcionan los precios y la facturación en Azure Logic Apps."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.openlocfilehash: 63784c5e3af360b2f3f8cb330a9df8b27a85d859
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="logic-apps-pricing-model"></a>Modelo de precios de Logic Apps
Azure Logic Apps le permite escalar y ejecutar flujos de trabajo de integración en la nube.  A continuación se detallan los planes de facturación y precios de Logic Apps.
## <a name="consumption-pricing"></a>Precios de consumo
Las aplicaciones lógicas recién creadas usan un plan de consumo. Con el modelo de precios de consumo de Logic Apps, solo paga por lo que utiliza.  Las aplicaciones lógicas no se limitan cuando se utiliza un plan de consumo.
Se miden todas las acciones que se ejecutan durante la ejecución de una instancia de aplicación lógica.
### <a name="what-are-action-executions"></a>¿Cuáles son las ejecuciones de acción?
Cada paso de una definición de aplicación lógica es una acción, que incluye desencadenadores, pasos del flujo de control, como condiciones, ámbitos, bucles for each, bucles do until, llamadas a conectores y llamadas a acciones nativas.
Los desencadenadores son acciones especiales diseñadas para crear una nueva instancia de aplicación lógica cuando se produce un evento determinado.  Existen varios comportamientos diferentes para desencadenadores que podrían afectar al modo en que se mide la aplicación lógica.
* **Desencadenador de sondeo**: este desencadenador sondea continuamente un punto de conexión hasta que recibe un mensaje que satisface los criterios para crear una instancia de una aplicación lógica.  El intervalo de sondeo se puede configurar en el desencadenador en el Diseñador de aplicación lógica.  Cada solicitud de sondeo, incluso si no crea una instancia de una aplicación lógica, cuenta como una ejecución de acción.
* **Desencadenador de webhook** : este desencadenador espera a que un cliente envíe una solicitud en un punto de conexión determinado.  Cada solicitud enviada al punto de conexión de webhook se considera una ejecución de acción. El desencadenador de solicitud y de webhook HTTP son ambos desencadenadores de webhook.
* **Desencadenador de periodicidad**: este desencadenador crea una instancia de la aplicación lógica según el intervalo de periodicidad configurado en el desencadenador.  Por ejemplo, un desencadenador de periodicidad puede configurarse para que se ejecute cada tres días o incluso cada minuto.

Las ejecuciones de desencadenadores se pueden ver en la hoja de recursos de Logic Apps en la parte Historial de desencadenadores.

Todas las acciones que se ejecutaron, tanto si tuvieron éxito como si no, se miden como ejecuciones de acción.  Las acciones omitidas debido a condiciones que no se cumplieron o acciones que no se ejecutaron porque la aplicación lógica terminó antes de que se completara no se cuentan como ejecuciones de acción.

Las acciones ejecutadas dentro de bucles se cuentan por cada iteración del bucle.  Por ejemplo, una sola acción en un bucle for each que interacciona mediante una lista de 10 elementos se contará como el número de elementos de la lista (10) multiplicado por el número de acciones del bucle (1) más uno por la iniciación del bucle que, en este ejemplo, sería (10 * 1) + 1 = 11 ejecuciones de acción.
Si Logic Apps está deshabilitada, no puede tener nuevas instancias iniciadas y, por tanto, mientras las aplicaciones lógicas están deshabilitadas, no se cobrarán.  Tenga en cuenta que después de deshabilitar una aplicación lógica, las instancias pueden tardar un tiempo en pasar al modo inactivo antes de deshabilitarse completamente.
### <a name="integration-account-usage"></a>Uso de la cuenta de integración
En el uso basado en el consumo se incluye una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) a efectos de exploración, desarrollo y pruebas, que permite usar las características [B2B/EDI](logic-apps-enterprise-integration-b2b.md) y [procesamiento XML](logic-apps-enterprise-integration-xml.md) de Logic Apps sin costo adicional. Es posible crear un máximo de una cuenta por región y almacenar hasta 10 contratos y 25 mapas. Los esquemas, certificados y asociados no tienen ningún límite, y puede cargar tantos como necesite.

Además de incluir cuentas de integración con consumo, también puede crear cuentas de integración estándar sin estos límites y con el Acuerdo de Nivel de Servicio estándar de Logic Apps. Para obtener más información, consulte [Precios de Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="app-service-plans"></a>Planes del Servicio de aplicaciones
Las aplicaciones lógicas creadas anteriormente conforme al plan de App Service se seguirán comportando como antes. Según el plan elegido, se establecen limitaciones cuando se superan las ejecuciones diarias prescritas, pero se factura con el medidor de ejecuciones de acciones.
Los clientes EA con un plan de App Service en su suscripción, que no tiene que asociarse de manera explícita con la aplicación lógica, obtienen la ventaja de cantidades incluidas.  Por ejemplo, si tiene un plan de App Service estándar en su suscripción de EA y una aplicación lógica en la misma suscripción, no se cobra por 10000 ejecuciones de acciones al día (vea la siguiente tabla). 

Planes del Servicio de aplicaciones y sus ejecuciones de acción diarias permitidas:
|  | Libre, Compartido o Básico | Estándar | Premium |
| --- | --- | --- | --- |
| Ejecuciones de acción por día |200 |10.000 |50.000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Cambio en el precio desde un plan del Servicio de aplicaciones a la opción de pago según lo consumido
Para cambiar una aplicación lógica que tiene un plan de App Service asociado a un modelo de consumo, quite la referencia al plan de App Service en la definición de aplicación lógica.  Este cambio puede hacerse con una llamada a un cmdlet de PowerShell: `Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`
## <a name="pricing"></a>Precios
Para obtener información sobre precios, vea [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Pasos siguientes
* [Información general de Logic Apps][whatis]
* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

