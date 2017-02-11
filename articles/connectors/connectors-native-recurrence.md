---
title: "Adición del desencadenador de periodicidad en Logic Apps | Microsoft Docs"
description: "Información general sobre el desencadenador de periodicidad y cómo utilizarlo con una aplicación lógica de Azure."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: fe558958c316c8dba42163e277ae01451f712e5a


---
# <a name="get-started-with-the-recurrence-trigger"></a>Introducción al desencadenador de periodicidad
Con el desencadenador de periodicidad, puede crear flujos de trabajo eficaces en la nube.

Por ejemplo, puede:

* Programar un flujo de trabajo para ejecutar un procedimiento almacenado de SQL cada día.
* Enviar por correo electrónico un resumen de todos los tweets de la última semana sobre un determinado hashtag.

Para empezar a usar la acción de periodicidad en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-a-recurrence-trigger"></a>Uso de un desencadenador de periodicidad
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md).

Esta es una secuencia de ejemplo de cómo configurar un desencadenador de periodicidad en una aplicación lógica:

1. Agregue el desencadenador de **periodicidad** como primer paso en una aplicación lógica.
2. Rellene los parámetros para el intervalo de periodicidad.

La aplicación lógica inicia ahora una ejecución después de cada intervalo de tiempo.

![Desencadenador HTTP](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>Detalles del desencadenador
El desencadenador de periodicidad tiene las siguientes propiedades que se pueden configurar.

Active una aplicación lógica después de un intervalo de tiempo especificado.
Un * significa que es un campo obligatorio.

| Nombre para mostrar | Nombre de propiedad | Description |
| --- | --- | --- |
| Frecuencia* |frequency |La unidad de tiempo: `Second`, `Minute`, `Hour`, `Day` o `Year`. |
| Intervalo* |interval |Intervalo de frecuencia definido para la periodicidad. |
| Zona horaria |timeZone |Si se especifica una hora de inicio sin una diferencia horaria con UTC, se usará esta zona horaria. |
| Hora de inicio |startTime |Hora de inicio en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). |

<br>

## <a name="next-steps"></a>Pasos siguientes
Ahora, pruebe la plataforma y [cree una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). Puede explorar los demás conectores disponibles en aplicaciones lógicas consultando nuestra [lista de API](apis-list.md).




<!--HONumber=Feb17_HO2-->


