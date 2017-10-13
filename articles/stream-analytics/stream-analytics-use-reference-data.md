---
title: "Uso de tablas de búsqueda y datos de referencia en Stream Analytics | Microsoft Docs"
description: "Uso de datos de referencia en una consulta de Análisis de transmisiones"
keywords: "tabla de búsqueda, datos de referencia"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 06103be5-553a-4da1-8a8d-3be9ca2aff54
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 438ec565f3c6e06ab7ec92cf1bbfbdde88f99b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Uso de datos de referencia o tablas de consulta en una transmisión de entrada de Análisis de transmisiones
Los datos de referencia (también denominados tabla de consulta) son un conjunto finito de datos estáticos o de cambio lento de naturaleza, que se usan para realizar una búsqueda o para relacionarlos con el flujo de datos. Para usar los datos de referencia en un trabajo de Azure Stream Analytics, por lo general usará una [instrucción JOIN de los datos de referencia](https://msdn.microsoft.com/library/azure/dn949258.aspx) en la consulta. Stream Analytics usa Azure Blob Storage como capa de almacenamiento para los datos de referencia y con Azure Data Factory los datos de referencia se pueden transformar o copiar en Azure Blob Storage para su uso como datos de referencia, desde [cualquier número de almacenes de datos locales y en la nube](../data-factory/copy-activity-overview.md). Los datos de referencia se modelan como una secuencia de blobs (que se define en la configuración de entrada) en orden ascendente por la fecha y hora que se especifique en el nombre del blob. **Solo** se admite que se agreguen al final de la secuencia con una fecha y hora **posteriores** a las especificadas en el último blob de la secuencia.

Stream Analytics tiene un **límite de 100 MB por blob**, pero los trabajos pueden procesar varios blobs de referencia mediante la propiedad **patrón de la ruta de acceso**.


## <a name="configuring-reference-data"></a>Configuración de datos de referencia
Para configurar los datos de referencia, tiene que crear primero una entrada que sea del tipo **Datos de referencia**. En la siguiente tabla se explica cada propiedad que necesitará proporcionar al crear la entrada de los datos de referencia con su descripción:


<table>
<tbody>
<tr>
<td>Nombre de propiedad</td>
<td>Description</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Nombre descriptivo que se usará en la consulta de trabajo para hacer referencia a esta entrada.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento donde se encuentran los blobs. Si está en la misma suscripción que su trabajo de Stream Analytics, puede seleccionarla en el menú desplegable.</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento. Se rellena automáticamente si la cuenta de almacenamiento está en la misma suscripción que el trabajo de Stream Analytics .</td>
</tr>
<tr>
<td>Contenedor de almacenamiento</td>
<td>Los contenedores proporcionan una agrupación lógica de los blobs almacenados en el servicio BLOB de Microsoft Azure. Cuando carga un blob al servicio BLOB, debe especificar un contenedor para ese blob.</td>
</tr>
<tr>
<td>Patrón de la ruta de acceso</td>
<td>La ruta de acceso para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede elegir especificar una o más instancias de las siguientes dos variables:<BR>{date}, {time}<BR>Ejemplo 1: products/{date}/{time}/product-list.csv<BR>Ejemplo 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si ha usado {date} dentro del patrón de ruta de acceso especificado, puede seleccionar el formato de fecha en el que se organizan los blobs en la lista desplegable de formatos admitidos.<BR>Ejemplo: AAAA/MM/DD, MM/DD/AAAA, etc.</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si ha usado {time} dentro del patrón de ruta de acceso especificado, puede seleccionar el formato de hora en el que se organizan los blobs en la lista desplegable de formatos admitidos.<BR>Ejemplo: HH, HH/mm o HH: mm</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Para asegurarse de que las consultas funcionen como se espera, Análisis de transmisiones debe saber cuál es el formato de serialización que usa para los flujos de datos entrantes. Para los datos de referencia, los formatos admitidos son CSV y JSON.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Generación de datos de referencia en una programación
Si los datos de referencia son un conjunto de datos que cambia con poca frecuencia, se pueden actualizar los datos de referencia especificando un patrón de ruta de acceso en la configuración de entrada con los tokens de sustitución de {date} y {time}. Stream Analytics selecciona las definiciones actualizadas de los datos de referencia según este patrón de ruta de acceso. Por ejemplo, un patrón de `sample/{date}/{time}/products.csv` con un formato de fecha de **"AAAA-MM-DD"** y un formato de hora de **"HH:mm"** indica a Stream Analytics que seleccione el blob actualizado `sample/2015-04-16/17-30/products.csv` a las 17:30 del 16 de abril de 2015 (zona horaria UTC).

> [!NOTE]
> En estos momentos, los trabajos de Análisis de transmisiones buscan la actualización de blobs solo cuando la hora de la máquina coincide con la hora codificada en el nombre del blob. Por ejemplo, el trabajo buscará `sample/2015-04-16/17-30/products.csv` en cuanto sea posible, pero no antes de las 17:30 del 16 de abril de 2015 (zona horaria UTC). No buscará *nunca* un blob con una hora codificada anterior a la última detectada.
> 
> Por ejemplo, una vez que el trabajo encuentre el blob `sample/2015-04-16/17-30/products.csv`, ignorará los archivos cuya fecha codificada sea anterior a las 17:30 del 16 de abril de 2015. Por tanto, si un blob `sample/2015-04-16/17-25/products.csv` se crea en el mismo contenedor posteriormente, el trabajo no lo usará.
> 
> Del mismo modo, si `sample/2015-04-16/17-30/products.csv` solo se crea a las 22:03 del 16 de abril de 2015, pero en el contenedor no hay ningún blob con una fecha anterior, el trabajo usará este archivo a partir de las 22:03 del 16 de abril de 2015, además de los datos de referencia anteriores hasta ese momento.
> 
> Una excepción se produce cuando el trabajo debe volver a procesar datos anteriores en el tiempo o cuando se inicia por primera vez. En momento de iniciarse, el trabajo busca el blob más reciente generado antes de la hora de inicio del trabajo especificada. Esto se hace para garantizar que haya un conjunto de datos de referencia **no vacío** al iniciarse el trabajo. Si no se encuentra ninguno, el trabajo muestra el diagnóstico siguiente: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) puede usarse para organizar la tarea de crear los blobs actualizados requeridos por Stream Analytics para actualizar las definiciones de datos de referencia. Factoría de datos es un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos. Factoría de datos admite la [conexión a un gran número de almacenes de datos locales y en la nube](../data-factory/copy-activity-overview.md) y el desplazamiento sencillo de los datos con la regularidad que se especifique. Para obtener más información e instrucciones paso a paso sobre cómo configurar una canalización de Factoría de datos para generar datos de referencia para Análisis de transmisiones que se actualiza según una programación predefinida, consulte este [ejemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Sugerencias sobre cómo actualizar los datos de referencia
1. Si sobrescribe blobs de datos de referencia, el Análisis de transmisiones no volverá a cargar el blob y, en algunos casos, puede impedir que el trabajo se ejecute. La mejor forma de cambiar datos de referencia consiste en agregar un nuevo blob con el mismo patrón de ruta de acceso y contenedor definidos en la entrada del trabajo, y usar una fecha y hora **posterior** a la especificada en el último blob de la secuencia.
2. Los blobs de datos de referencia **no** se ordenan por la hora de "Última modificación" del blob sino únicamente por la fecha y la hora que se especifiquen en el nombre del blob mediante las sustituciones de {date} y {time}.
3. En algunas ocasiones, un trabajo debe retroceder en el tiempo, por lo que los blobs de datos de referencia no se deben modificar ni eliminar.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
Ya conoce Análisis de transmisiones, un servicio administrado para el análisis del streaming de datos desde Internet de las cosas. Para obtener más información acerca de este servicio, consulte:

* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
