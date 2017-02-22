---
title: "Registros de diagnósticos de Azure Stream Analytics | Microsoft Docs"
description: "Obtenga información sobre cómo analizar registros de diagnósticos de trabajos de Stream Analytics en Microsoft Azure."
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: f0292efd50721ef58028df778052eb0ed6fcda84
ms.openlocfilehash: 724eba50b7428b0012e8f062e264ce057e2a5287


---
# <a name="job-diagnostic-logs"></a>Registros de diagnósticos de trabajos

## <a name="introduction"></a>Introducción
Stream Analytics expone dos tipos de registros: 
* [Registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), que están siempre habilitados y proporcionan información sobre operaciones realizadas en trabajos;
* [Registros de diagnósticos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), que los puede configurar el usuario y proporcionan una visión más completa de todo lo que ocurre con el trabajo, desde el momento en el que se crea, actualiza, mientras se ejecuta y hasta que se elimina;

## <a name="how-to-enable-diagnostic-logs"></a>Habilitación de registros de diagnósticos
Los registros de diagnósticos están **desactivados** de forma predeterminada. Para habilitarlos, siga estos pasos:

Inicie sesión en Azure Portal, navegue a la hoja de trabajos de streaming y use la hoja "Registros de diagnósticos" bajo "Supervisión".

![navegación por la hoja a los registros de diagnósticos](./media/stream-analytics-job-diagnostic-logs/image1.png)  

A continuación, haga clic en el vínculo “Activar diagnósticos”.

![activar los registros de diagnósticos](./media/stream-analytics-job-diagnostic-logs/image2.png)

En los diagnósticos abiertos, cambie el estado a "Activado".

![cambiar el estado de los registros de diagnósticos](./media/stream-analytics-job-diagnostic-logs/image3.png)

Configure el destino de archivo deseado (cuenta de almacenamiento, centro de eventos, Log Analytics) y seleccione las categorías de registros que desea recopilar (Ejecución o Creación). A continuación, guarde la nueva configuración de diagnósticos.

Una vez guardada, la configuración tardará unos 10 minutos en aplicarse y, después, los registros comenzarán a aparecer en el destino de archivo configurado que puede ver en la hoja “Registros de diagnóstico”:

![navegación por la hoja a los registros de diagnósticos](./media/stream-analytics-job-diagnostic-logs/image4.png)

En la página de [registros de diagnósticos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) hay disponible más información sobre la configuración de diagnósticos.

## <a name="diagnostic-logs-categories"></a>Categorías de registros de diagnósticos
Existen dos categorías de registros de diagnósticos que se capturan actualmente:

* **Creación:** capture los registros relacionados con operaciones de creación de trabajos: creación, incorporación y eliminación de entradas y salidas, incorporación y actualización de la consulta, inicio y detención del trabajo.
* **Ejecución:** capture lo que sucede durante la ejecución del trabajo.
    * Errores de conectividad;
    * Errores de procesamiento de datos;
        * Eventos que no cumplen con la definición de consulta (tipos y valores de campo no coincidentes, campos ausentes, etc.);
        * Errores de evaluación de expresión;
    * Etc.

## <a name="diagnostic-logs-schema"></a>Esquema de registros de diagnósticos
Todos los registros se almacenan en formato JSON y cada entrada tiene los siguientes campos de cadena comunes:

Nombre | Descripción
------- | -------
Twitter en tiempo | Marca de tiempo (en UTC) del registro.
resourceId | Identificador del recurso en el que tuvo lugar la operación, en mayúsculas. Incluye el identificador de suscripción, el grupo de recursos y el nombre de trabajo. Por ejemplo: `/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB`
categoría | Categoría del registro. Puede ser `Execution` o `Authoring`
operationName | Nombre de la operación que se registra. Por ejemplo: `Send Events: SQL Output write failure to mysqloutput`
status | Estado de la operación. Por ejemplo: `Failed, Succeeded`.
level | Nivel de registro. Por ejemplo: `Error, Warning, Informational`
propiedades | Detalles específicos de la entrada de registro; se serializa como cadena JSON; vea la información siguiente para más detalles.

### <a name="execution-logs-properties-schema"></a>Esquema de propiedades de registros de ejecución
Los registros de ejecución contienen información sobre eventos que se produjeron durante la ejecución del trabajo de Stream Analytics.
Según el tipo de eventos, las propiedades tendrán un esquema diferente. Actualmente existen los tipos siguientes:

### <a name="data-errors"></a>Errores de datos
Cualquier error de procesamiento de datos se encontrará en esta categoría de registros. Se producen principalmente durante la operación de lectura, serialización y escritura de datos. No se incluyen los errores de conectividad que se tratan como eventos genéricos.

Nombre | Descripción
------- | -------
Origen | Nombre de la entrada o salida del trabajo donde se produjo el error.
Message | Mensaje asociado al error.
Escriba | Tipo de error. Por ejemplo, `DataConversionError, CsvParserError, ServiceBusPropertyColumnMissingError`, etc.
Datos | Contiene datos útiles para localizar con exactitud el origen del error. Sujeto a truncamiento dependiendo del tamaño.

En función del valor de **operationName**, los errores de datos tendrán el siguiente esquema:
* **Serializar eventos** - Ocurre durante operaciones de lectura de eventos cuando los datos de la entrada no cumplen el esquema de la consulta:
    * Discordancia de tipos durante la (de)serialización de eventos: campo que causa el error.
    * No se puede leer un evento, serialización no válida: información sobre el lugar en los datos de entrada donde se produjo el error, como el nombre de blob para la entrada del blob, desplazamiento y una muestra de los datos.
* **Enviar eventos** - Operaciones de escritura: evento de streaming que causó el error.

### <a name="generic-events"></a>Eventos genéricos
Todo lo demás

Nombre | Descripción
-------- | --------
Error | (opcional) Información de error, normalmente información de excepción si está disponible.
Message| Mensaje de registro.
Escriba | Tipo de mensaje, se asigna a la categorización interna de errores; por ejemplo, JobValidationError, BlobOutputAdapterInitializationFailure etc.
Id. de correlación | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identifica de manera única la ejecución del trabajo. Todas las entradas de registro de ejecución generadas desde que se inició el trabajo hasta que se detiene tendrán el mismo “identificador de correlación”.



## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


