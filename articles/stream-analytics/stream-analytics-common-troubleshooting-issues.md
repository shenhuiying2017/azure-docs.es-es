---
title: Solución de problemas de eventos de entrada con formato incorrecto en Azure Stream Analytics
description: ¿Cómo puedo saber qué evento de mis datos de entrada ocasiona problemas en un trabajo de Stream Analytics?
services: stream-analytics
author: jasonwhowell
manager: Kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: fcbb03b4d9aed797cf99c374661c743d39f81276
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problemas comunes de Stream Analytics y pasos de solución de problemas

## <a name="troubleshoot-for-malformed-input-events"></a>Solución de problemas de eventos de entrada con formato incorrecto

Cuando el flujo de entrada del trabajo de Stream Analytics contiene mensajes con un formato incorrecto, se producen problemas de serialización. Los mensajes con un formato incorrecto incluyen una serialización incorrecta, por ejemplo, faltan paréntesis en un objeto JSON o el formato de marca de hora es incorrecto. Cuando un trabajo de Stream Analytics recibe un mensaje con formato incorrecto, descarta el mensaje y lo notifica al usuario con una advertencia. Aparece un símbolo de advertencia en el icono **Entradas** de su trabajo de Stream Analytics (este signo de advertencia persiste mientras el estado del trabajo sea en ejecución):

![Icono de entradas](media/stream-analytics-malformed-events/inputs_tile.png)

Puede habilitar los registros de diagnóstico para ver los detalles de la advertencia. Para los eventos de entrada de formato incorrecto, los registros de ejecución contienen una entrada con un mensaje con el siguiente aspecto: "Mensaje: no se pudo deserializar los eventos de entrada de recurso <blob URI> como json)". 

### <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

1. Vaya al icono de entradas y haga clic para ver las advertencias.
2. El icono de detalles de entrada muestra un conjunto de advertencias con detalles sobre el problema. A continuación se muestra un ejemplo de un mensaje de advertencia con la partición, el desplazamiento y los números de secuencia donde hay datos JSON con un formato incorrecto. 

   ![Mensaje de advertencia con desplazamiento](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Para obtener los datos JSON con formato incorrecto, ejecute el código CheckMalformedEvents.cs, puede obtener la TI desde el [repositorio de ejemplos de GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lee el identificador de la partición y el desplazamiento e imprime los datos de este último. 

4. Una vez leídos los datos, puede analizar y corregir el formato de serialización. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Tratamiento de los registros duplicados al usar Azure SQL Database como salida para un trabajo de Stream Analytics

Al configurar Azure SQL Database como salida para un trabajo de Stream Analytics, inserta registros en bloque en la tabla de destino. En general, Azure Stream Analytics garantiza [al menos una entrega]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) al receptor de salida, aún se puede [lograr exactamente una entrega]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) a la salida de SQL cuando la tabla SQL tiene una restricción única definida. 

Una vez las restricciones de clave única configuradas en la tabla SQL, cuando se insertan registros duplicados en la tabla SQL, Azure Stream Analytics quita el registro duplicado al dividir los datos en lotes e insertarlos repetidamente hasta que solo se encuentra un registro duplicado. Si hay un número considerable de filas duplicadas en la canalización, la división e inserción repetitivas de datos y la omisión de los duplicados uno a uno es un proceso lento. Si ve varios mensajes de advertencia de infracción de clave en el registro de actividad en la última hora, es probable que la salida de SQL esté ralentizando todo el trabajo. Para resolver este problema, [configure el índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que provoca la infracción de clave al habilitar la opción IGNORE_DUP_KEY. Al habilitar esta opción, se permite que SQL omita los valores duplicados durante las inserciones en bloque y SQL Azure simplemente genera un mensaje de advertencia en lugar de un error. Azure Stream Analytics ya no producen errores de infracción de clave principal.

Tenga en cuenta las observaciones siguientes al configurar IGNORE_DUP_KEY para varios tipos de índices:

* No se puede establecer IGNORE_DUP_KEY en una clave principal o una restricción única que use ALTER INDEX, debe quitar y volver a crear el índice.  
* Puede establecer la opción IGNORE_DUP_KEY con ALTER INDEX para un índice único, que es diferente de la restricción PRIMARY KEY/UNIQUE y se crea con la definición de INDEX o CREATE INDEX.  
* IGNORE_DUP_KEY no se aplica a los índices de almacén de columna, ya que en ellos no se puede exigir exclusividad.  

## <a name="next-steps"></a>Pasos siguientes

* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

