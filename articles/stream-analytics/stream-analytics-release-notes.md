---
title: "Notas de la versión de Stream Analytics | Microsoft Docs"
description: "Notas de la versión de Análisis de transmisiones"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/03/2017
ms.author: samacha
ms.openlocfilehash: 3251cd47bb917912d63330345dbf392e724448ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-release-notes"></a>Notas de la versión de Análisis de transmisiones

## <a name="notes-for-06142017-update-of-stream-analytics-tools-for-visual-studio"></a>Notas de la actualización del 14/06/2017 de las herramientas de Stream Analytics para Visual Studio
Esta actualización corresponde a Visual Studio Tools. Esta versión contiene las siguientes características nuevas:

| Título | Descripción |
| --- | --- |
| Compatibilidad con el editor de scripts de Java |Puede disfrutar de la experiencia del editor scripts de Java nativo después de crear las funciones de script de Java.|
| Visualización del mensaje de error en tiempo de ejecución de trabajos | Si hay errores en tiempo de ejecución durante la ejecución del trabajo, puede verlos en la pestaña Errores ajustando la ventana de tiempo de visualización. Muestra de manera predeterminada los mensajes de error de los últimos 30 minutos. |
| Compatibilidad de CSV y Avro con la entrada de pruebas locales | Además de JSON, ahora puede usar el formato de archivo CSV y Avro en la entrada de pruebas locales.|

## <a name="notes-for-05032017-update-of-stream-analytics"></a>Notas de la actualización de Stream Analytics del 03/05/2017
Esta actualización es para la versión d documentación de solución de problemas.

Se han publicado la [guía de solución de problemas](stream-analytics-troubleshooting-guide.md) y otros documentos. Revíselos. Agradecemos sus comentarios.

## <a name="notes-for-04242017-update-of-stream-analytics-tools-for-visual-studio"></a>Notas de la actualización del 24/04/2017 de las herramientas de Stream Analytics para Visual Studio
Esta actualización corresponde a Visual Studio Tools. Esta versión contiene las siguientes características nuevas:

| Título | Descripción |
| --- | --- |
| Visualización del resultado de la prueba local en Visual Studio | Para ver el resultado de la salida de la prueba local, basta con presionar ENTRAR en la ventana de la consola de salida o cerrarla. El resultado se muestra en formato de tabla en una ventana de Visual Studio. |
| Resultado local de salida en formato JSON | Al ejecutar una prueba local, el resultado de salida se genera como formatos de archivo CSV y JSON. |
| Vista previa de datos de entradas y salidas de Blob Storage o Table Storage | Al hacer doble clic en una entrada o salida de Blob Storage o Table Storage en la ventana del trabajo, pude obtener fácilmente la vista previa de los datos dentro de Visual Studio. |
| Visualización de los mensajes de error de las entradas y salidas | Si hay algunos errores en tiempo de ejecución relacionados con las entradas o salidas del trabajo, se muestran en el diagrama de trabajo, donde puede mantener el puntero sobre él para ver el mensaje de error detallado.|


## <a name="notes-for-02012017-release-of-stream-analytics"></a>Notas de la versión de Stream Analytics del 01/02/2017
Esta versión contiene la siguiente actualización:

| Título | Descripción |
| --- | --- |
| Introducción a las funciones definidas por el usuario (UDF) de JavaScript |[Las funciones definidas por el usuario de Java](stream-analytics-javascript-user-defined-functions.md) ahora están disponibles para proporcionar flexibilidad adicional en la creación de consultas. |
| Introducción a las herramientas para Visual Studio y Stream Analytics |Las [herramientas para Visual Studio](stream-analytics-tools-for-visual-studio.md) ahora están disponibles para depuración y otras utilidades de mayor índole. |
| Introducción al registro de diagnósticos |El [registro de diagnósticos](stream-analytics-job-diagnostic-logs.md) ahora está disponible para opciones de solución de problemas adicionales. |
| Introducción a las funciones geoespaciales |Las [funciones geoespaciales](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx) ahora están disponibles con carácter general. |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 15/04/2016
Esta versión contiene la siguiente actualización:

| Título | Description |
| --- | --- |
| Disponibilidad general para las salidas de Power BI |[salidas de Power BI](stream-analytics-power-bi-dashboard.md) están ahora disponibles. Se ha quitado la expiración de la autorización en 90 días para Power BI. Para más información sobre los escenarios donde es necesario renovar la autorización, consulte la sección [Renovación de la autorización](stream-analytics-power-bi-dashboard.md#renew-authorization) de Creación de un panel de Power BI. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 03/03/2016
Esta versión contiene la siguiente actualización:

| Título | Description |
| --- | --- |
| Nuevos elementos de lenguaje de consulta de Análisis de transmisiones |Ahora los elementos de lenguaje de consulta de Análisis de transmisiones tienen la página [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN"), la página [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN") y la página [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 12/10/2015
Esta versión contiene la siguiente actualización:

| Título | Description |
| --- | --- |
| Actualización de la versión de API de REST |La versión de la API de REST se actualizó a la 2015-10-01. Encontrará detalles sobre MSDN en [Referencia de la API de REST de la administración de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx) e [Integración de Machine Learning en Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Integración de Aprendizaje automático de Azure |Esta versión incluye compatibilidad con funciones definidas por el usuario de Azure Machine Learning. Consulte el [tutorial](stream-analytics-machine-learning-integration-tutorial.md) para obtener más información, así como el [anuncio general del blog](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 12/11/2015
Esta versión contiene la siguiente actualización:

| Título | Description |
| --- | --- |
| Nuevo comportamiento de SELECT |SELECT en el Análisis de transmisiones se ha ampliado para permitir * como descriptor de acceso de propiedad de un registro anidado. Para más información, consulte [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Tipos de datos complejos"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 22/10/2015
Esta versión contiene las siguientes actualizaciones:

| Título | Description |
| --- | --- |
| Características del lenguaje de consulta adicionales |Stream Analytics ha ampliado el lenguaje de consulta mediante la inclusión de las siguientes características: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx) y [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Se han quitado las limitaciones de agregados |En esta versión se quita la limitación de 15 agregados en una consulta. Actualmente no hay límites en el número de agregados por consulta. |
| Se ha agregado la característica GROUP BY System.Timestamp |La función [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) ahora permite window_type o [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| Se ha agregado OFFSET para las ventanas de saltos de tamaño constante y las ventanas de salto |De forma predeterminada, las ventanas de [saltos de tamaño constante](https://msdn.microsoft.com/library/azure/dn835055.aspx) y de [salto](https://msdn.microsoft.com/library/azure/dn835041.aspx) se alinean con la hora cero (1/1/0001 12:00:00 A.M. UTC). El nuevo parámetro (opcional) 'offsetsize' permite especificar una diferencia (o alineación) personalizada. |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 29/09/2015
Esta versión contiene las siguientes actualizaciones:

| Título | Description |
| --- | --- |
| Vista previa pública de Conjunto de aplicaciones de IoT de Azure |Análisis de transmisiones se incluye en la vista previa pública de Conjunto de aplicaciones de IoT de Azure. |
| Integración de Azure Portal |Además de su presencia continua en Azure Portal, Stream Analytics ahora se integra en [Azure Portal](https://azure.microsoft.com/overview/preview-portal/). Tenga en cuenta que la funcionalidad de Stream Analytics en la versión preliminar del portal actualmente es un subconjunto de la funcionalidad ofrecida en Azure Portal, sin soporte para pruebas de consultas en el explorador, configuración de salida de Power BI ni exploración o creación de nuevos recursos de entrada y salida en las suscripciones a las que tenga acceso. |
| Compatibilidad para salida de Cosmos DB |Ahora se pueden enviar trabajos de Stream Analytics a [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). |
| Compatibilidad para entrada de Centro de IoT |Los trabajos de Análisis de transmisiones ahora pueden introducir datos de los Centros de IoT. |
| TIMESTAMP BY para eventos heterogéneos |Ahora puede usar [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) con expresiones para especificar diferentes campos de marca de tiempo para cada caso cuando un único flujo de datos contiene varios tipos de eventos con marcas de tiempo en distintos campos. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 10/09/2015
Esta versión contiene las siguientes actualizaciones:

| Título | Description |
| --- | --- |
| Soporte para grupos de PowerBI |Para habilitar el uso compartido de datos con otros usuarios de Power BI, los trabajos de Análisis de transmisiones ahora pueden escribir en [grupos de PowerBI](stream-analytics-define-outputs.md#power-bi) dentro de su cuenta de Power BI. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 20/08/2015
Esta versión contiene las siguientes actualizaciones:

| Título | Description |
| --- | --- |
| Función LAST agregada |La función [LAST](http://msdn.microsoft.com/library/mt421186.aspx) ahora está disponible en los trabajos de Análisis de transmisiones, lo que le permite recuperar el evento más reciente en una secuencia de eventos en un período de tiempo determinado. |
| Nuevas funciones de matriz |Ahora están disponibles las funciones de matriz [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) y [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx). |
| Nuevas funciones de registro |Ahora están disponibles las funciones de registro [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) y [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx). |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 30/07/2015
Esta versión contiene las siguientes actualizaciones:

| Título | Description |
| --- | --- |
| Identificador de organización de BI energía desacoplado del identificador de Azure |Esta característica habilita la [salida de Power BI](stream-analytics-power-bi-dashboard.md) para trabajos de ASA en cualquier tipo de cuenta de Azure (Live ID o ID de organización). Además, puede tener un identificador de organización para su cuenta de Azure y usar otros distinto para autorizar la salida de Power BI. |
| Compatibilidad con la salida de Colas del Bus de servicio |Las [colas de Service Bus](stream-analytics-define-outputs.md#service-bus-queues) ahora están disponibles en los trabajos de Stream Analytics. |
| Compatibilidad con la salida de Temas de Service Bus |Los [temas de Service Bus](stream-analytics-define-outputs.md#service-bus-topics) ahora están disponibles en los trabajos de Stream Analytics. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 09/07/2015
Esta versión contiene las siguientes actualizaciones:

| Título | Description |
| --- | --- |
| Particiones de salida de blob personalizadas |Las salidas de almacenamiento de blobs ahora le dan la opción de especificar tanto la frecuencia en la que los blobs de salida son escritos, como la estructura y el formato de la estructura de la carpeta con la ruta de acceso a los datos de salida. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 03/05/2015
Esta versión contiene las siguientes actualizaciones:

| Título | Description |
| --- | --- |
| Se ha incrementado el valor máximo del período de tolerancia de fuera de servicio |El tamaño máximo del período de tolerancia de fuera de servicio es ahora de 59:59 (MM:SS) |
| Formato de salida JSON: separación por líneas o matriz |Cuando envíe elementos al Almacenamiento de blobs o al Centro de eventos, tiene la opción de hacer la salida como una matriz de objetos JSON o separando los objetos JSON con una nueva línea. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Notas de la versión de Análisis de transmisiones del 16/04/2015
| Título | Description |
| --- | --- |
| Retraso en la configuración de la cuenta de almacenamiento de Azure |Al crear un trabajo de Stream Analytics por primera vez en una región, se le pide que cree una nueva cuenta de almacenamiento o que especifique una cuenta existente para la supervisión de trabajos de Stream Analytics en esa región. Debido a la latencia en la configuración de la supervisión, al crear otro trabajo de Stream Analytics en la misma región, transcurridos 30 minutos, se le solicita que especifique una segunda cuenta de almacenamiento en lugar de mostrar la configurada recientemente en la lista desplegable de supervisión de cuentas de almacenamiento. Para evitar la creación de una cuenta de almacenamiento innecesaria, espere 30 minutos después de crear un trabajo en una región por primera vez antes de aprovisionar trabajos adicionales en dicha región. |
| Actualización del trabajo |En este momento, Análisis de transmisiones no admite modificaciones dinámicas en la definición o la configuración de un trabajo en ejecución. Para cambiar la entrada, la salida, la consulta, la escala o la configuración de un trabajo en ejecución, primero debe detener el trabajo. |
| Tipos de datos deducidos del origen de entrada |Si no se utiliza una instrucción CREATE TABLE, el tipo de entrada se deriva del formato de entrada; por ejemplo, todos los campos de CSV son cadenas. Los campos deben convertirse explícitamente al tipo adecuado mediante la función CAST para evitar errores de coincidencia de tipos. |
| Los campos que faltan generan valores null |Hacer referencia a un campo que no está presente en el origen de entrada da como resultado valores null en el evento de salida. |
| Las instrucciones WITH deben preceder a las instrucciones SELECT |En una consulta, las instrucciones SELECT deben seguir a subconsultas definidas en instrucciones WITH. |
| Problema de memoria agotada |Los trabajos de Análisis de transmisiones con una gran tolerancia para eventos sin orden o consultas complejas que mantienen una gran cantidad de estados pueden hacer que el trabajo se quede sin memoria, lo que da lugar a que este se reinicie. Las operaciones de inicio y detención son visibles en los registros de operaciones del trabajo. Para evitar este comportamiento, escale horizontalmente el resultado de la consulta entre varias particiones. En una versión futura esta limitación se aborda de forma que provoque la degradación del rendimiento de los trabajos y no que se reinicien. |
| Las entradas de blobs grandes sin marca de tiempo de carga pueden causar un problema de falta de memoria |El consumo de grandes archivos desde el almacenamiento de blobs puede hacer que los trabajos de Análisis de transmisiones se bloquee si no se especifica un campo de marca de tiempo a través de marca de tiempo. Para evitar este problema, mantenga el tamaño de cada blob por debajo de 10 MB. |
| Limitación del volumen de eventos de Base de datos SQL |Cuando se utiliza la Base de datos SQL como destino de la salida, unos volúmenes de datos de salida muy elevados pueden hacer que se agote el tiempo de espera del trabajo de Análisis de transmisiones. Para resolver este problema, reduzca el volumen de salida con agregados u operadores de filtro o elija el almacenamiento de blobs de Azure o los centros de eventos como destino de la salida. |
| Los conjuntos de datos PowerBI solo pueden contener una tabla |PowerBI no admite más de una tabla en un conjunto de datos determinado. |

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
