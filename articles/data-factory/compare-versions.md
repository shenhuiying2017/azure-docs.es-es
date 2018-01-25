---
title: "Comparación de las versiones 1 y 2 de Azure Data Factory | Microsoft Docs"
description: "En este artículo se comparan Azure Data Factory V1 con Azure Data Factory V2."
services: data-factory
documentationcenter: 
author: kromerm
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2018
ms.author: makromer
ms.openlocfilehash: 83065e6cacd784a3914cfac3ff2552a712688366
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="compare-azure-data-factory-v1-and-v2"></a>Comparación de Azure Data Factory V1 y V2
En este artículo se comparan las versiones 2 y 1 de Azure Data Factory. Para ver una introducción a la versión 1, consulte [Introducción a Azure Data Factory](v1/data-factory-introduction.md). Para ver una introducción a la versión 2, consulte [Introducción a Azure Data Factory](introduction.md).

## <a name="feature-comparison"></a>Comparación de características
En la tabla siguiente se comparan las características de las versiones 1 y 2. 

| Característica | versión 1 | versión 2 | 
| ------- | --------- | --------- | 
| Conjuntos de datos | Una vista de datos con nombre que hace referencia a los datos que desea usar en sus actividades como entrada y salida. Los conjuntos de datos identifican datos en distintos almacenes de datos, como tablas, archivos, carpetas y documentos. Por ejemplo, un conjunto de datos de Blob de Azure especifica el contenedor de blobs y la carpeta de Azure Blob Storage de la que la actividad debe leer los datos.<br/><br/>La **disponibilidad** define el modelo de segmentación de la ventana de procesamiento para el conjunto de datos (por ejemplo, cada hora, cada día, etc.). | Los conjuntos de datos son los mismos en la versión 2. Sin embargo, no es preciso definir programaciones de **disponibilidad** para los conjuntos de datos. Se puede definir un recurso de desencadenador que puede programar las canalizaciones a partir de un paradigma de programador de reloj. Para más información, consulte [Desencadenadores](concepts-pipeline-execution-triggers.md#triggers) y [Conjuntos de datos](concepts-datasets-linked-services.md). | 
| Servicios vinculados | Los servicios vinculados son muy similares a las cadenas de conexión, que definen la información de conexión necesaria para que Data Factory se conecte a recursos externos. | Los servicios vinculados son los mismos que en la versión 1 de Data Factory, salvo en que tienen una nueva propiedad **connectVia** para utilizar el entorno de proceso Integration Runtime de la versión 2 de Data Factory. Para más información, consulte [Integration Runtime en Azure Data Factory](concepts-integration-runtime.md) y [Propiedades del servicio vinculado](connector-azure-blob-storage.md#linked-service-properties). |
| Procesos | Una factoría de datos puede tener una o más canalizaciones. Una canalización es una agrupación lógica de actividades que realizan una tarea. Use startTime, endTime, isPaused para programar y ejecutar canalizaciones. | Las canalizaciones son grupos de actividades que se realizan en los datos. Sin embargo, la programación de actividades en la canalización se ha dividido en nuevos recursos de desencadenador. En la versión 2 de Data Factory, las canalizaciones se pueden considerar más como "unidades de flujo de trabajo" que se programan por separado a través de desencadenadores. <br/><br/>Las canalizaciones no tienen "ventanas" de ejecución temporal en la versión 2 de Data Factory. Los conceptos de startTime, endTime e isPaused, que están presentes en la versión 1 de Data Factory dejan de estarlo en la versión 2. Para más información, consulte [Ejecución y desencadenadores de canalización en Azure Data Factory](concepts-pipeline-execution-triggers.md) y [Canalizaciones y actividades en Azure Data Factory](concepts-pipelines-activities.md). |
| Actividades | Las actividades definen las acciones que se realizan en los datos en una canalización. Se admiten las actividades de movimiento de datos (actividad de copia) y transformación de datos (como, Hive, Pig y MapReduce). | En la versión 2 de Data Factory, las actividades siguen siendo acciones definidas en una canalización. La versión 2 incorpora nuevas [actividades de los flujos de control](concepts-pipelines-activities.md#control-activities). Dichas actividades se usan en un flujo de control (bucle y bifurcación). Las actividades de movimiento de datos y transformación de datos que se admitían en la versión 1 se admiten en la 2. En la versión 2, se pueden definir actividades de transformación sin usar conjuntos de datos. |
| Movimiento de datos híbridos y distribución de actividades | [Data Management Gateway](v1/data-factory-data-management-gateway.md), que ahora se denomina Integration Runtime, admitía el movimiento de datos entre un entorno local y la nube.| Data Management Gateway ahora se denomina Integration Runtime autohospedado. Proporciona la misma funcionalidad que en la versión 1. <br/><br/> En la versión 2, Azure-SSIS Integration Runtime también admite la implementación y ejecución de paquetes de SQL Server Integration Services (SSIS) en la nube. Para más información, consulte [Integration Runtime en Azure Data Factory](concepts-integration-runtime.md).|
| Parámetros | N/D | Los parámetros son pares de clave-valor de valores de configuración de solo lectura que se definen en las canalizaciones. Al ejecutar manualmente la canalización se pueden pasar argumentos para los parámetros. Si usa un desencadenador de programador, el desencadenador también puede pasar valores de los parámetros. Las actividades dentro de la canalización consumen los valores de parámetro.  |
| Expresiones | La versión 1 de Data Factory permite usar funciones y variables del sistema en las consultas de selección de datos y en las propiedades de actividad/conjunto de datos. | En la versión 2 se pueden usar expresiones en cualquier lugar en un valor de cadena JSON. Para más información, consulte [Expresiones y funciones de Azure Data Factory en V2](control-flow-expression-language-functions.md).|
| Ejecuciones de la canalización | N/D | Una instancia individual de una ejecución de una canalización. Por ejemplo, supongamos que tiene una canalización que se ejecuta las 8 a. m., 9 a. m. y 10 a. m. En este caso, habrá tres ejecuciones independientes de la canalización (ejecuciones de canalización). Cada canalización ejecutar tiene un identificador de canalización único. El identificador de ejecución de canalización es un GUID que define de forma exclusiva una ejecución de canalización concreta. Normalmente las instancias de ejecuciones de canalización se crean al pasar argumentos a parámetros que se definen en las canalizaciones. |
| Ejecuciones de actividad | N/D | Una instancia de una ejecución de una actividad en una canalización. | 
| Ejecuciones de desencadenador | N/D | Una instancia de una ejecución de un desencadenador. Para más información, consulte [Desencadenadores](concepts-pipeline-execution-triggers.md). |
| Scheduling | La programación se basa en las horas de inicio y finalización de la canalización y en la disponibilidad del conjunto de datos. | Desencadenador o ejecución del programador a través de un programador externo. Para obtener más información, consulte [Desencadenadores y ejecución de la canalización](concepts-pipeline-execution-triggers.md). |

Las secciones siguientes proporcionan más información acerca de las funcionalidades de la versión 2. 

## <a name="control-flow"></a>Flujo de control  
Para admitir distintos patrones y flujos de integración en el almacenamiento de datos actual, la versión 2 de Data Factory ha habilitado un nuevo modelo de canalización de datos flexible que no está asociado con los datos de serie temporal. Algunos flujos comunes que anteriormente no eran posibles ahora están habilitados. Dichos flujos se describen en las secciones siguientes.   

### <a name="chaining-activities"></a>Encadenamiento de actividades
En la versión 1, para encadenar dos actividades, la salida de una se tenía que configurar como la entrada de la otra. En la versión 2, se pueden encadenar las actividades de manera secuencial dentro de una canalización. Puede usar la propiedad **dependsOn** en una definición de actividad para encadenarla a una actividad ascendente. Para más información y un ejemplo, consulte [Canalizaciones y actividades en Azure Data Factory](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) y [Actividades de bifurcación y encadenamiento en una canalización de Data Factory](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Actividades de bifurcación
En la versión 2, las actividades de una canalización se pueden bifurcar. La [actividad de la condición IF](control-flow-if-condition-activity.md) proporciona la misma funcionalidad que una instrucción `if` en los lenguajes de programación. Evalúa un conjunto de actividades cuando la condición se evalúa como `true` y otro conjunto de actividades cuando la condición se evalúa como `false`. Para ver un ejemplo de bifurcación de actividades, consulte el tutorial [Actividades de bifurcación y encadenamiento en una canalización de Data Factory](tutorial-control-flow.md).

### <a name="parameters"></a>Parámetros 
Puede definir los parámetros en el nivel de canalización y pasar los argumentos al invocar la canalización a petición o desde un desencadenador. Las actividades pueden consumir los argumentos que se pasan a la canalización. Para más información, consulte [Ejecución y desencadenadores de canalización en Azure Data Factory](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Paso a estado personalizado
Los resultados de la actividad, incluido el estado, se pueden usar en una posterior actividad de la canalización. Por ejemplo, en la definición de JSON de una actividad, se puede acceder a la salida de la actividad anterior mediante la siguiente sintaxis: `@activity('NameofPreviousActivity').output.value`. Con esta característica se pueden crear flujos de trabajo en los que los valores pueden pasar de una actividad a otra.

### <a name="looping-containers"></a>Contenedores de bucle
La [actividad ForEach](control-flow-for-each-activity.md) define un flujo de control repetido en la canalización. Esta actividad iterar una colección y ejecuta las actividades especificadas en un bucle. La implementación del bucle de esta actividad es similar a la estructura del bucle ForEach de los lenguajes de programación. 

La actividad [Until](control-flow-until-activity.md) proporciona la misma funcionalidad que una estructura de bucle Do-Until en los lenguajes de programación. Ejecuta un conjunto de actividades en bucle hasta que la condición asociada a la actividad la evalúa como `true`. Puede especificar un valor de tiempo de espera para la actividad Until en Data Factory.  

### <a name="trigger-based-flows"></a>Flujos basados en el desencadenador
Las canalizaciones se pueden desencadenar a petición o por tiempo de reloj. El artículo [Ejecución y desencadenadores de canalización en Azure Data Factory](concepts-pipeline-execution-triggers.md) tiene información detallada acerca de los desencadenadores. 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Invocación de una canalización desde otra
La actividad [Execute Pipeline](control-flow-execute-pipeline-activity.md) permite que una canalización de Data Factory invoque otra.

### <a name="delta-flows"></a>Flujos delta
Un caso de uso importante en los patrones de extracción, transformación y carga de datos es la "carga diferencial", en la que se cargan solo los datos que han cambiado desde la última iteración de una canalización. Las nuevas funcionalidades de la versión 2, como la [actividad de búsqueda](control-flow-lookup-activity.md), la programación flexible y el flujo de control habilitan este caso de uso de forma natural. Para ver instrucciones detalladas, consulte [Carga de datos de forma incremental de Azure SQL Database a Azure Blob Storage](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Otras actividades del flujo de control
Estas son otras actividades del flujo de control compatibles con la versión 2 de Data Factory. 

Actividad de control | DESCRIPCIÓN
---------------- | -----------
[Actividad ForEach](control-flow-for-each-activity.md) | Define un flujo de control repetido en la canalización. Esta actividad se usa para iterar una colección y ejecuta las actividades especificadas en un bucle. La implementación del bucle de esta actividad es similar a la estructura de bucle ForEach de los lenguajes de programación.
[Actividad web](control-flow-web-activity.md) | Llama a un punto de conexión REST personalizado desde una canalización de Data Factory. Puede pasar conjuntos de datos y servicios vinculados que la actividad consumirá y a los que tendrá acceso. 
[Actividad de búsqueda](control-flow-lookup-activity.md) | Lee o busca el valor de un registro o nombre de tabla desde cualquier origen externo. Además, las actividades posteriores pueden hacer referencia a esta salida. 
[Actividad de obtención de metadatos](control-flow-get-metadata-activity.md) | Recupera los metadatos de todos los datos de Azure Data Factory. 
[Actividad de espera](control-flow-wait-activity.md) | Detiene la canalización durante un período especificado.

## <a name="deploy-ssis-packages-to-azure"></a>Implementación de paquetes SSIS en Azure 
Use Azure-SSIS si desea mover las cargas de trabajo de SSIS a la nube, crear factoría de datos mediante la versión 2 y aprovisionar una instancia de Azure-SSIS Integration Runtime.

Azure-SSIS Integration Runtime es un clúster totalmente administrado de máquinas virtuales de Azure (nodos) que se dedican a ejecutar los paquetes SSIS en la nube. Después de aprovisionar Azure-SSIS Integration Runtime, puede usar las mismas herramientas que para implementar paquetes SSIS en un entorno de SSIS local. 

Por ejemplo, puede usar SQL Server Data Tools o SQL Server Management Studio para implementar paquetes de SSIS en este runtime de Azure. Para obtener instrucciones detalladas, consulte el tutorial [Implementación de paquetes de SQL Server Integration Services en Azure](tutorial-deploy-ssis-packages-azure.md). 

## <a name="flexible-scheduling"></a>Programación flexible
En la versión 2 de Data Factory, no es preciso definir las programaciones de disponibilidad de los conjuntos de datos. Se puede definir un recurso de desencadenador que puede programar las canalizaciones a partir de un paradigma de programador de reloj. También puede pasar parámetros a canalizaciones desde un desencadenador para un modelo de programación o ejecución. 

Las canalizaciones no tienen "ventanas" de ejecución temporal en la versión 2 de Data Factory. Los conceptos de startTime, endTime e isPaused, de la versión 1 de Data Factory no existen en la versión 2. Para más información acerca de cómo crear y programar una canalización en la versión 2 de Data Factory, consulte [Ejecución y desencadenadores de canalización en Azure Data Factory](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Compatibilidad con más almacenes de datos
La versión 2 admite la copia de datos a o desde más almacenes de datos que la versión 1. En los siguientes artículos encontrará una lista de los almacenes de datos compatibles:

- [Versión 1: almacenes de datos compatibles](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Versión 2: almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Compatibilidad con clúster de Spark a petición
La versión 2 admite la creación de un clúster de Azure HDInsight Spark a petición. Para crear un clúster de Spark a petición, especifique el tipo de clúster como Spark en la definición a petición del servicio vinculado a HDInsight. Después, ya puede configurar la actividad de Spark en la canalización de este servicio vinculado. 

En tiempo de ejecución, cuando se ejecuta la actividad, el servicio Data Factory crea automáticamente el clúster de Spark. Para más información, consulte los siguientes artículos.

- [Actividad de Spark en la versión 2](transform-data-using-spark.md)
- [Servicio vinculado a petición de Azure HDInsight](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Actividades personalizadas
En la versión 1, el código de la actividad de DotNet (personalizada) se implementa mediante la creación de un proyecto de biblioteca de clases .NET con una clase que implementa el método Execute de la interfaz de IDotNetActivity. Por consiguiente, necesita escribir código personalizado en .NET Framework 4.5.2 y ejecutarlo en nodos de grupo de Azure Batch basados en Windows. 

En las actividades personalizadas de la versión 2, no es preciso implementar ninguna interfaz .NET. Puede ejecutar directamente comandos, scripts y su propio código personalizado compilado como ejecutable. 

Para más información, consulte [Diferencias entre la actividad personalizada de Azure Data Factory V2 y la actividad de DotNet (personalizada) de Azure Data Factory V1](transform-data-using-dotnet-custom-activity.md#difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1).

## <a name="sdks"></a>SDK
 Data Factory V2 proporciona un conjunto más completo de SDK que se pueden usar para crear, administrar y supervisar canalizaciones.

- **SDK de .NET**: el SDK de .NET se actualiza para la versión 2.

- **PowerShell**: los cmdlets de PowerShell se actualizan para la versión 2. Los cmdlets de la versión 2 tienen **DataFactoryV2** en su nombre, por ejemplo: Get-AzureRmDataFactoryV2. 

- **SDK de Python**: este SDK es una novedad de la versión 2.

- **API de REST**: la API de REST se actualiza para la versión 2. 

Los SDK que se actualizan para la versión 2 no son compatibles con los clientes de la versión 1. 

## <a name="authoring-experience"></a>Experiencia de creación

| &nbsp; | V2 | V1 |
| ------ | -- | -- | 
| Azure Portal | [Sí](quickstart-create-data-factory-portal.md) | [Sí](data-factory-build-your-first-pipeline-using-editor.md) |
| Azure PowerShell | [Sí](quickstart-create-data-factory-powershell.md) | [Sí](data-factory-build-your-first-pipeline-using-powershell.md) |
| .NET SDK | [Sí](quickstart-create-data-factory-dot-net.md) | [Sí](data-factory-build-your-first-pipeline-using-vs.md) |
| API DE REST | [Sí](quickstart-create-data-factory-rest-api.md) | [Sí](data-factory-build-your-first-pipeline-using-rest-api.md) |
| SDK de Python | [Sí](quickstart-create-data-factory-python.md) | Sin  |
| Plantilla de Resource Manager | [Sí](quickstart-create-data-factory-resource-manager-template.md) | [Sí](data-factory-build-your-first-pipeline-using-arm.md) | 


## <a name="monitoring-experience"></a>Experiencia de supervisión
En la versión 2, también puede supervisar factorías de datos mediante [Azure Monitor](monitor-using-azure-monitor.md). Los nuevos cmdlets de PowerShell admiten la supervisión de [instancias de Integration Runtime](monitor-integration-runtime.md). Tanto la versión 1 como la versión 2 admiten la supervisión visual a través de una aplicación de supervisión que se puede iniciar desde Azure Portal.


## <a name="next-steps"></a>pasos siguientes
Para aprender a crear una factoría de datos, siga las instrucciones detalladas de las siguientes guías de inicio rápido: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md) y [API de REST](quickstart-create-data-factory-rest-api.md). 
