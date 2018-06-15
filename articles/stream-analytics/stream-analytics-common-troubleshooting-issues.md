---
title: Problemas comunes y solución en Azure Stream Analytics
description: En este artículo se describen varios problemas comunes de Azure Stream Analytics y los pasos necesarios para solucionar dichos problemas.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: e04d1072acee635235b0a5bd8465ca38c861017b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523530"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problemas comunes de Stream Analytics y pasos de solución de problemas

## <a name="troubleshoot-malformed-input-events"></a>Solución de problemas de eventos de entrada con formato incorrecto

 Los problemas de serialización surgen cuando el flujo de entrada del trabajo de Stream Analytics contiene mensajes con un formato incorrecto. Por ejemplo, que un mensaje tenga un formato incorrecto podría deberse a la falta de un paréntesis o una llave en un objeto JSON, o a un formato de marca de tiempo incorrecto en un campo de tiempo. 
 
 Cuando un trabajo de Stream Analytics recibe un mensaje con formato incorrecto desde una entrada, descarta el mensaje y lo notifica al usuario con una advertencia. Aparece un símbolo de advertencia en el icono **Entradas** de su trabajo de Stream Analytics (este signo de advertencia persiste mientras el estado del trabajo sea en ejecución):

![Icono de entradas](media/stream-analytics-malformed-events/inputs_tile.png)

Si desea más información, habilite los registros de diagnóstico para ver los detalles de la advertencia. En el caso de los eventos de entrada de formato incorrecto, los registros de ejecución contienen una entrada con un mensaje similar a: "Mensaje: no se pudieron deserializar los eventos de entrada de recurso <blob URI> como json". 

### <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

1. Vaya al icono de entradas y haga clic para ver las advertencias.

2. El icono de detalles de entrada muestra un conjunto de advertencias con detalles sobre el problema. A continuación se muestra un ejemplo de un mensaje de advertencia con la partición, el desplazamiento y los números de secuencia donde hay datos JSON con un formato incorrecto. 

   ![Mensaje de advertencia con desplazamiento](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Para obtener los datos JSON que tienen un formato incorrecto, ejecute el código CheckMalformedEvents.cs. Este ejemplo está disponible en el [repositorio de ejemplos de GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lee el identificador de la partición y el desplazamiento e imprime los datos de este último. 

4. Una vez leídos los datos, puede analizar y corregir el formato de serialización. 

## <a name="delayed-output"></a>Retraso en la salida

### <a name="first-output-is-delayed"></a>Se retrasa la primera salida
Cuando se inicia un trabajo de Stream Analytics, se leen los eventos de entrada, pero puede darse un retraso en la salida en determinadas circunstancias.

La presencia de valores de tiempo elevados en los elementos de consulta temporales pueden influir en el retraso de la salida. Para generar un resultado correcto a través de períodos de tiempo muy prolongados, el trabajo de streaming empieza por leer datos desde la hora más antigua posible (hasta siete días atrás) para rellenar la ventana de tiempo. Durante ese tiempo, no se produce ninguna salida hasta que se ponga al día la lectura de los eventos de entrada pendientes. Este problema puede surgir cuando el sistema actualiza los trabajos de streaming, reiniciando así el trabajo. Generalmente, estas actualizaciones se producen una vez cada dos meses. 

Por lo tanto, tenga precaución al diseñar la consulta de Stream Analytics. Si usa un período de tiempo muy prolongado (más de unas horas, hasta siete días) para los elementos temporales en la sintaxis de la consulta del trabajo, se puede producir en un retraso en la primera salida cuando se inicia o reinicia el trabajo.  

Una solución para este tipo de primer retraso de salida consiste en usar técnicas de ejecución en paralelo de consultas (creando particiones de datos) o agregar más unidades de streaming para mejorar el rendimiento hasta que el trabajo se ponga al día.  Para obtener más información, vea [Considerations when creating Stream Analytics jobs](stream-analytics-concepts-checkpoint-replay.md) (Consideraciones al crear trabajos de Stream Analytics).

Estos factores afectan a la vigencia de la primera salida que se genera:

1. Uso de agregados en ventanas (GROUP BY de ventanas de saltos de tamaño constante, de salto y deslizantes)
   - En el caso de los agregados de ventana de saltos de tamaño constante o de salto, los resultados se generan al final del período de tiempo. 
   - En las ventanas deslizantes, los resultados se generan cuando un evento entra en la ventana deslizante o sale de ella. 
   - Si tiene previsto usar un tamaño de ventana grande (> 1 hora), es mejor elegir la ventana de salto o deslizante para que se puedan ver los resultados con más frecuencia.

2. Uso de combinaciones temporales (JOIN con DATEDIFF)
   - Tan pronto como lleguen ambos lados de los eventos coincidentes, se generarán coincidencias.
   - Los datos que no tienen coincidencias (LEFT OUTER JOIN) se generan al final de la ventana DATEDIFF con respecto a cada evento en el lado izquierdo.

3. Uso de funciones analíticas temporales (ISFIRST, LAST y LAG con LIMIT DURATION)
   - En el caso de las funciones analíticas, el resultado se genera para cada evento, no hay ningún retraso.

### <a name="output-falls-behind"></a>La salida queda fuera
Durante el funcionamiento normal del trabajo, si detecta que la que salida del trabajo se queda fuera (latencia mayor y más larga), puede identificar las causas raíz mediante el examen de estos factores:
- Si está limitado el receptor inferior
- Si está limitado el receptor superior
- Si la lógica de procesamiento de la consulta es de proceso intensivo

Para ver los detalles, en Azure Portal, seleccione el trabajo de streaming y seleccione el **diagrama de trabajo**. Para cada entrada, hay una métrica de evento de trabajo pendiente por partición. Si la métrica de evento de trabajo pendiente sigue aumentando, es un indicador de que los recursos del sistema están limitados. Puede que se deba a la limitación del receptor de salida o una alta CPU. Para obtener más información sobre cómo usar el diagrama de trabajo, consulte [Depuración orientada a datos mediante el diagrama de trabajo](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Control de los registros duplicados en la salida de Azure SQL Database

Al configurar Azure SQL Database como salida para un trabajo de Stream Analytics, inserta registros en bloque en la tabla de destino. En general, Azure Stream Analytics garantiza [al menos una entrega]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) al receptor de salida, aún se puede [lograr exactamente una entrega]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) a la salida de SQL cuando la tabla SQL tiene una restricción única definida. 

Una vez configuradas las restricciones de clave únicas en la tabla SQL, si existen registros duplicados que se insertan en la tabla SQL, Azure Stream Analytics quita el registro duplicado. Divide los datos en lotes e inserta los lotes de forma recursiva hasta que se encuentra un único registro duplicado. Si el trabajo de streaming tiene un gran número de filas duplicadas, este proceso de división e inserción tiene que pasar por alto los duplicados uno por uno, lo cual es menos eficaz y consume muchos recursos. Si ve varios mensajes de advertencia de infracción de clave en el registro de actividad en la última hora, es probable que la salida de SQL esté ralentizando todo el trabajo. 

Para resolver este problema, [configure el índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que provoca la infracción de clave al habilitar la opción IGNORE_DUP_KEY. Al habilitar esta opción, se permite que SQL omita los valores duplicados durante las inserciones en bloque y SQL Azure simplemente genera un mensaje de advertencia en lugar de un error. Azure Stream Analytics ya no producen errores de infracción de clave principal.

Tenga en cuenta las observaciones siguientes al configurar IGNORE_DUP_KEY para varios tipos de índices:

* No se puede establecer IGNORE_DUP_KEY en una clave principal o una restricción única que use ALTER INDEX, debe quitar y volver a crear el índice.  
* Puede establecer la opción IGNORE_DUP_KEY con ALTER INDEX para un índice único, que es diferente de la restricción PRIMARY KEY/UNIQUE y se crea con la definición de INDEX o CREATE INDEX.  
* IGNORE_DUP_KEY no se aplica a los índices de almacén de columna, ya que en ellos no se puede exigir exclusividad.  

## <a name="next-steps"></a>Pasos siguientes
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
