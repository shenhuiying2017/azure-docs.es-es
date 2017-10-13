---
title: "Solución de problemas de Azure Stream Analytics con registros de diagnóstico |Microsoft Docs"
description: "Aprenda a analizar registros de diagnóstico de trabajos de Stream Analytics en Microsoft Azure."
keywords: 
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
ms.openlocfilehash: c9772df2c216d465ca6e90e69bce011969dd4f02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Solución de problemas de Azure Stream Analytics mediante registros de diagnóstico

En ocasiones, un trabajo de Azure Stream Analytics deja de procesarse inesperadamente. Es importante poder solucionar este tipo de evento. El evento podría deberse a un resultado de consulta inesperado, la conectividad a los dispositivos o una interrupción inesperada del servicio. Los registros de diagnóstico de Stream Analytics pueden ayudarle a identificar la causa de los problemas cuando se producen y a reducir el tiempo de recuperación.

## <a name="log-types"></a>Tipos de registro

Stream Analytics ofrece dos tipos de registros: 
* [Registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (siempre activados). Los registros de actividad proporcionan información sobre las operaciones realizadas en los trabajos.
* [Registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configurables). Los registros de diagnóstico proporcionan información más completa sobre todo lo que ocurre con un trabajo. Comienzan cuando se crea el trabajo y finalizan cuando se elimina el trabajo. Abarcan los eventos de cuando se actualiza el trabajo y mientras se está ejecutando.

> [!NOTE]
> Puede usar servicios como Azure Storage, Azure Event Hubs y Azure Log Analytics para analizar los datos no conformes. Se le cobra según el modelo de precios existente para esos servicios.
>

## <a name="turn-on-diagnostics-logs"></a>Activación de los registros de diagnóstico

Los registros de diagnóstico están **desactivados** de forma predeterminada. Para activar los registros de diagnóstico, siga estos pasos:

1.  Inicie sesión en el portal de Azure y vaya a la hoja Trabajo de streaming. En **Supervisión**, seleccione **Registros de diagnóstico**.

    ![Navegación en la hoja a los registros de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Seleccione **Activar diagnósticos**.

    ![Activación de los registros de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  En la página **Configuración de diagnóstico**, en **Estado**, seleccione **Activado**.

    ![Cambio de estado de los registros de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Configure el destino de archivo (cuenta de almacenamiento, centro de eventos, Log Analytics) que desee. A continuación, seleccione las categorías de registros que desea recopilar (Ejecución, Creación). 

5.  Guarde la nueva configuración de diagnóstico.

Tardará unos 10 minutos en surtir efecto. Luego, comienzan a aparecer los registros en el destino de archivo configurado (puede verlos en la página **Registros de diagnóstico**):

![Navegación en la hoja a los registros de diagnóstico: destino de archivo](./media/stream-analytics-job-diagnostic-logs/image4.png)

Para más información sobre la configuración de diagnósticos, consulte el artículo sobre la [recopilación y el uso de datos de diagnóstico provenientes de los recursos de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Categorías de registro de diagnóstico

Actualmente, se capturan dos categorías de registros de diagnóstico:

* **Creación**. Captura registros de eventos relacionados con operaciones de creación de trabajos: creación, adición y eliminación de entradas y salidas, adición y actualización de la consulta, inicio y detención del trabajo.
* **Ejecución**. Captura los eventos que se producen durante la ejecución del trabajo:
    * Errores de conectividad
    * Errores de procesamiento de datos, como por ejemplo:
        * Eventos que no se ajustan a la definición de consulta (tipos y valores de campo no coincidentes, campos ausentes, etc.)
        * Errores de evaluación de expresión
    * Otros eventos y errores

## <a name="diagnostics-logs-schema"></a>Esquema de registros de diagnóstico

Todos los registros se almacenan en formato JSON. Cada entrada tiene los siguientes campos de cadena comunes:

Nombre | Descripción
------- | -------
Twitter en tiempo | Marca de tiempo (en UTC) del registro.
resourceId | Identificador del recurso en el que tuvo lugar la operación, en mayúsculas. Incluye el identificador de suscripción, el grupo de recursos y el nombre del trabajo. Por ejemplo, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
categoría | La categoría del registro: **Ejecución** o **Creación**.
operationName | Nombre de la operación que se registra. Por ejemplo, **Eventos de envío: error de escritura de salida de SQL en mysqloutput**.
status | Estado de la operación. Por ejemplo, **Erróneo** o **Correcto**.
level | Nivel de registro. Por ejemplo, **Error**, **Advertencia** o **Información**.
propiedades | Detalle específico de entrada de registro, serializado como una cadena JSON. Para más información, consulte las siguientes secciones:

### <a name="execution-log-properties-schema"></a>Esquema de propiedades de registros de ejecución

Los registros de ejecución contienen información sobre eventos que se produjeron durante la ejecución del trabajo de Stream Analytics. El esquema de propiedades varía en función del tipo de evento. Actualmente, tenemos los siguientes tipos de registros de ejecución:

### <a name="data-errors"></a>Errores de datos

Cualquier error que se produce mientras el trabajo está procesando datos se puede incluir en esta categoría de registros. Estos registros se crean habitualmente durante las operaciones de lectura, serialización y escritura de datos. No incluyen errores de conectividad. Los errores de conectividad se tratan como eventos genéricos.

Nombre | Descripción
------- | -------
Origen | Nombre de la entrada o salida del trabajo donde se produjo el error.
Message | Mensaje asociado al error.
Tipo | Tipo de error. Por ejemplo, **DataConversionError**, **CsvParserError** o **ServiceBusPropertyColumnMissingError**.
Datos | Contiene datos útiles para localizar con exactitud el origen del error. Sujeto a truncamiento dependiendo del tamaño.

En función del valor de **operationName**, los errores de datos tendrán el siguiente esquema:
* **Eventos de serialización**. Los eventos de serialización se producen durante las operaciones de lectura de eventos. Tienen lugar cuando los datos en la entrada no satisfacen el esquema de consulta por una de las siguientes razones:
    * *Los tipos no coinciden durante la de(serialización) de eventos*: identifica el campo que provoca el error.
    * *No se puede leer un evento, la serialización no es válida*: muestra información sobre la ubicación de los datos de entrada donde se produjo el error. Incluye el nombre de blob de la entrada de blob, el desplazamiento y una muestra de los datos.
* **Eventos de envío**. Los eventos de envío se producen durante las operaciones de escritura. Identifican el evento de transmisión que produjo el error.

### <a name="generic-events"></a>Eventos genéricos

Los eventos genéricos incluyen todos los demás.

Nombre | Descripción
-------- | --------
Error | (opcional) Información de error. Normalmente, es información de excepciones, si está disponible.
Message| Mensaje de registro.
Tipo | Tipo de mensaje. Se asigna a la categorización interna de errores. Por ejemplo, **JobValidationError** o **BlobOutputAdapterInitializationFailure**.
Id. de correlación | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identifica de manera única la ejecución del trabajo. Todas las entradas de registros de ejecución desde el momento en que se inicia el trabajo hasta que se detiene tienen el mismo valor de **Id. de correlación**.

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es Stream Analytics?](stream-analytics-introduction.md)
* [Introducción a Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalado de trabajos de Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia de lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
