---
title: Solución de problemas con los receptores de Event Hubs en Azure Stream Analytics
description: En este artículo se describe cómo usar varios grupos de consumidores para las entradas de Event Hubs en los trabajos de Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312963"
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Solución de problemas con los receptores de Event Hubs en Azure Stream Analytics

Puede usar Azure Event Hubs en Azure Stream Analytics para ingerir o extraer datos de un trabajo. Una práctica recomendada para usar Event Hubs es usar varios grupos de consumidor para garantizar la escalabilidad del trabajo. Una razón es que el número de lectores en el trabajo de Stream Analytics para una entrada específica afecta a la cantidad de lectores en un grupo de consumidores único. El número exacto de destinatarios se basa en los detalles de implementación interna para la lógica de la topología de escalado horizontal. El número de destinatarios no se expone externamente. El número de lectores puede cambiar a la hora de inicio del trabajo o durante las actualizaciones del trabajo.

El error que se muestra cuando el número de receptores supera el máximo: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Cuando el número de lectores cambia durante una actualización de trabajo, se escriben advertencias transitorias en registros de auditoría. Los trabajos de Stream Analytics se recuperan automáticamente de estos problemas transitorios.

## <a name="add-a-consumer-group-in-event-hubs"></a>Agregar un grupo de consumidores a Event Hubs
Para agregar un nuevo grupo de consumidores a la instancia de Event Hubs, siga estos pasos:

1. Inicie sesión en el Portal de Azure.

2. Busque la instancia de Event Hubs.

3. Seleccione **Event Hubs** en el encabezado **Entidades**.

4. Seleccione un centro de eventos según el nombre.

5. En la página **Instancia de Event Hubs**, en el encabezado **Entidades**, seleccione **Grupos de consumidores**. Aparecerá un grupo de consumidores con nombre **$Default**.

6. Seleccione **+ Grupo de consumidores** para agregar un nuevo grupo de consumidores. 

   ![Agregar un grupo de consumidores a Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Cuando se crea la entrada en el trabajo de Stream Analytics para que apunte al centro de eventos, es necesario especificar el grupo de consumidores ahí. $Default se utiliza cuando no se especifica ninguno. Una vez que cree un nuevo grupo de consumidores, edite la entrada de Event Hubs en el trabajo de Stream Analytics y especifique el nombre del nuevo grupo de consumidores.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>El número de lectores por partición supera el límite de Event Hubs de cinco

Si la sintaxis de la consulta de streaming hace referencia varias veces al mismo recurso de entrada de Event Hubs, el motor de trabajo puede utilizar varios lectores por cada consulta de ese mismo grupo de consumidores. Cuando hay demasiadas referencias al mismo grupo de consumidores, el trabajo puede superar el límite establecido de cinco y se produce un error. En tales circunstancias, puede dividir aún más mediante el uso de múltiples entradas en varios grupos de consumidores; para ello, use la solución que se describe en la siguiente sección. 

Los escenarios en los que el número de lectores por partición supera el límite de Event Hubs de cinco incluyen los siguientes:

* Varias instrucciones SELECT: si usa varias instrucciones SELECT que hacen referencia a la **misma** entrada de centro de eventos, cada instrucción SELECT provoca que se cree un nuevo destinatario.
* UNION: cuando se utiliza UNION, es posible tener varias entradas que hacen referencia al **mismo** centro de eventos y grupo de consumidores.
* SELF JOIN: cuando se usa una operación SELF JOIN, es posible hacer referencia al **mismo** centro de eventos varias veces.

## <a name="solution"></a>Solución

Las siguientes prácticas recomendadas pueden ayudar a reducir los escenarios en los que el número de lectores por partición supera el límite de Event Hubs de cinco.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>División de la consulta en varios pasos mediante una cláusula WITH

La cláusula WITH especifica un conjunto de resultados con nombre temporal al que se puede hacer referencia mediante una cláusula FROM en la consulta. Defina la cláusula WITH en el ámbito de ejecución de una única instrucción SELECT.

Por ejemplo, en lugar de esta consulta:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Utilice esta consulta:

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Asegúrese de que las entradas se enlazan a grupos de consumidores diferentes

Para las consultas en las que hay conectadas tres o más entradas al mismo grupo de consumidores de Event Hubs, cree grupos de consumidores independientes. Esto requiere la creación de entradas de Stream Analytics adicionales.


## <a name="next-steps"></a>Pasos siguientes
* [Escalado de trabajos de Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia de lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
