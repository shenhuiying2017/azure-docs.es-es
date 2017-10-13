---
title: "Guía de solución de problemas de Azure Stream Analytics | Microsoft Docs"
description: "Solución de problemas de los trabajos de Stream Analytics"
keywords: "guía de solución de problemas"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: dcff312e4a282b15e76ea32aadb1981a496a2446
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Guía de solución de problemas de Azure Stream Analytics

A primera vista, la solución de problemas de Azure Stream Analytics puede parecer una tarea compleja. Después de trabajar con muchos usuarios, hemos creado esta guía para ayudar a acelerar el proceso y eliminar las ideas preconcebidas sobre sus entradas, salidas, consultas y funciones.

## <a name="troubleshoot-your-stream-analytics-job"></a>Solución de problemas de los trabajos de Stream Analytics

Para obtener mejores resultados en la solución de problemas de los trabajos de Stream Analytics, siga estas instrucciones:

1.  Pruebe la conectividad:
    - Compruebe la conectividad a entradas y salidas con el botón **Probar conexión** para cada entrada y salida.

2.  Examine los datos de entrada:
    - Para comprobar si los datos de entrada pasan al Centro de eventos, use [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) (Explorador de Service Bus) para conectarse al centro de eventos de Azure (si se usa la entrada de centro de eventos).  
    - Use el botón [**Datos de ejemplo**](stream-analytics-sample-data-input.md) para cada entrada y descargue los datos de ejemplo de entrada.
    - Inspeccione los datos de ejemplo para entender la forma de los datos: el esquema y los [tipos de datos](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Pruebe la consulta:
    - En la pestaña **Consulta**, use el botón **Probar** para probar la consulta, y use los datos de ejemplo descargados para [probar la consulta](stream-analytics-test-query.md). Examine los errores e intente corregirlos.
    - Si usa [**Marca de tiempo por**](https://msdn.microsoft.com/library/azure/mt573293.aspx), asegúrese de que los eventos tengan marcas de tiempo mayores que la [hora de inicio del trabajo](stream-analytics-out-of-order-and-late-events.md).

4.  Depure la consulta:
    - Vuelva a compilar la consulta progresivamente a partir de una instrucción select simple para agregados más complejos mediante pasos. Para crear la lógica de consulta paso a paso, use cláusulas [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx).
    - Use [SELECT INTO](stream-analytics-select-into.md) para depurar los pasos de consulta.

5.  Elimine los errores comunes, como:
    - Una cláusula [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) en la consulta filtrada por todos los eventos, que impide que se genere alguna salida.
    - Cuando use funciones de ventana, espere la duración entera de la ventana para ver una salida de la consulta.
    - La marca de tiempo de los eventos es anterior a la hora de inicio del trabajo y, por tanto, los eventos se descartan.

6.  Use la ordenación de eventos:
    - Si todos los pasos anteriores han funcionado bien, vaya a la hoja **Configuración** y seleccione [**Ordenación de eventos**](stream-analytics-out-of-order-and-late-events.md). Compruebe que esta directiva está configurada de acuerdo con su escenario. La directiva *no* se aplica cuando usa el botón **Probar** para probar la consulta. El resultado es una diferencia entre probar en el explorador frente a ejecutar el trabajo de producción.

7.  Depure mediante métricas:
    - Si no obtiene ninguna salida tras la duración prevista (basada en la consulta), intente lo siguiente:
        - Examine las [**métricas de supervisión**](stream-analytics-monitoring.md) en la pestaña **Monitor**. Dado que los valores se agregan, las métricas se retrasan unos minutos.
            - Si Eventos de entrada > 0, el trabajo puede leer los datos de entrada. Si Eventos de entrada no es > 0, entonces:
                - Para ver si el origen de datos contiene datos válidos, compruébelo mediante el [Explorador de Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Esta comprobación se aplica si el trabajo usa un centro de eventos como entrada.
                - Compruebe si el formato de serialización de datos y la codificación de datos son los esperados.
                - Si el trabajo usa un centro de eventos, compruebe si el cuerpo del mensaje es *Null*.
            - Si Errores de conversión de datos > 0 y subiendo, puede cumplirse lo siguiente:
                - El trabajo podría no ser capaz de deserializar los eventos.
                - El esquema de eventos podría no coincidir con el esquema definido o esperado de los eventos en la consulta.
                - Los tipos de datos de algunos de los campos del evento podrían no coincidir con las expectativas.
            - Si Errores en tiempo de ejecución > 0, significa que el trabajo puede recibir los datos, pero genera errores al procesar la consulta.
                - Para encontrar los errores, vaya a los [registros de auditoría](../azure-resource-manager/resource-group-audit.md) y filtre por el estado *Error*.
            - Si Eventos de entrada > 0 y Eventos de salida = 0, significa que se cumple uno de los siguientes casos:
                - El procesamiento de consultas no ha generado ningún evento de salida.
                - Los eventos o sus campos pueden tener un formato incorrecto, por lo que no se genera ninguna salida después de procesar la consulta.
                - El trabajo no pudo insertar datos en el [receptor de salida](stream-analytics-select-into.md) por motivos de autenticación o conectividad.
        - En todos los casos de error mencionados anteriormente, los mensajes de registro de operaciones explican detalles adicionales (como lo que está sucediendo), salvo en casos en que la lógica de la consulta filtra todos los eventos. Si el procesamiento de varios eventos genera errores, Stream Analytics registra los tres primeros mensajes de error del mismo tipo al cabo de 10 minutos en los registros de operaciones. Luego, suprime los errores idénticos adicionales con un mensaje que dice "Los errores se producen demasiado rápido y se están suprimiendo".

8. Depure mediante auditorías y registros de diagnóstico:
    - Use [Registros de auditoría](../azure-resource-manager/resource-group-audit.md) y filtre para identificar y depurar errores.
    - Use [registros de diagnóstico de trabajo](stream-analytics-job-diagnostic-logs.md) para identificar y depurar errores.

9. Examine las salidas:
    - Cuando el estado del trabajo es *En ejecución* y, en función de la duración que esté estipulada en la consulta, puede ver la salida en el origen de datos del receptor.
    - Si no puede ver salidas que van a un tipo de salida específica, rediríjalas a un tipo de salida que sea menos compleja, como un blob de Azure. Mediante el Explorador de Storage, compruebe si se puede ver la salida. Además, compruebe si existen limitaciones en la salida que impidan que se reciban los datos.

10. Use el análisis de flujo de datos con las métricas de diagrama de trabajo:
    - Para analizar el flujo de datos e identificar los problemas, use el [diagrama de trabajo con métricas](stream-analytics-job-diagram-with-metrics.md).

11. Abra un caso de soporte técnico:
    - Por último, si todo lo demás no funciona, abra un caso de soporte técnico de Microsoft con el id. de suscripción que contiene el trabajo.

## <a name="get-help"></a>Obtener ayuda

Para obtener ayuda adicional, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
