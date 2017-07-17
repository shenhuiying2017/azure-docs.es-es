---
title: "Introducción a Data Factory, un servicio de integración de datos | Microsoft Docs"
description: "Sepa lo que es Azure Data Factory: un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos."
keywords: "integración de datos, integración de datos de nube, qué es azure data factory"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 537bdee67ed9648c3cba2099553d847399609705
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
<a id="introduction-to-azure-data-factory" class="xliff"></a>

# Introducción a la Azure Data Factory 
<a id="what-is-azure-data-factory" class="xliff"></a>

## ¿Qué es Azure Data Factory?
En el mundo de los macrodatos, ¿cómo aprovechan las empresas los datos existentes? ¿Es posible enriquecer los datos generados en la nube mediante el uso de datos de referencia de orígenes de datos locales u otros orígenes de datos dispares? Por ejemplo, una empresa de juegos recopila muchos registros generados por juegos en la nube. Quieren analizar estos registros para obtener información sobre las preferencias de los clientes, la demografía, el comportamiento de uso, etc. a fin de identificar oportunidades de venta cruzada e incremento de ventas, desarrollar nuevas y atractivas características para impulsar el crecimiento del negocio y proporcionar una experiencia mejor a los clientes. 

Para analizar estos registros, la empresa debe usar los datos de referencia, como información de clientes, información de juegos o información de campañas de marketing, que se encuentran en un almacén de datos local. Por lo tanto, desean ingerir datos de registro del almacén de datos en la nube y datos de referencia del almacén de datos local. A continuación, procesarán los datos mediante Hadoop en la nube (Azure HDInsight) y publicarán los datos de resultados en un almacén de datos en la nube, como Azure SQL Data Warehouse, o en un almacén de datos local, como SQL Server. Quieren que este flujo de trabajo se ejecute una vez a la semana. 

Lo que se necesita es una plataforma que permita a la empresa crear un flujo de trabajo que pueda ingerir datos de ambos almacenes, local y en la nube, transformar o procesar los datos mediante servicios de proceso existentes, como Hadoop, y publicar los resultados en un almacén de datos local o en la nube para su consumo por parte de las aplicaciones BI. 

![Introducción a Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory es la plataforma para esta clase de escenarios. Se trata de un **servicio de integración de datos basado en la nube que le permite crear flujos de trabajo orientados a datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos**. Mediante Azure Data Factory, puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse para que los consuman las aplicaciones de inteligencia empresarial (BI).  

Es más una plataforma Extraer y cargar (EL) y luego Transformar y cargar (TL) que una plataforma tradicional Extraer, transformar y cargar (ETL). Las transformaciones que se realizan van a transformar/procesar los datos mediante servicios de proceso, en lugar de realizar transformaciones como las de agregar columnas derivadas, contar el número de filas, ordenar datos, etc. 

Actualmente, en Azure Data Factory, los datos que se consumen y generan en los flujos de trabajo son **datos fragmentados por tiempo** (por hora, día, semana, etc.). Por ejemplo, una canalización puede leer datos de entrada, procesar datos y generar datos de salida una vez al día. También puede ejecutar un flujo de trabajo una sola vez.  
  

<a id="how-does-it-work" class="xliff"></a>

## ¿Cómo funciona? 
Las canalizaciones (flujos de trabajo orientados a datos) en Azure Data Factory realizan normalmente los tres pasos siguientes:

![Tres fases de Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

<a id="connect-and-collect" class="xliff"></a>

### Conectar y recopilar
Las empresas tienen datos de varios tipos ubicados en orígenes dispares. El primer paso en la creación de un sistema de producción de información es conectarse a todos los orígenes necesarios de datos y procesamiento, como servicios SaaS, recursos web compartidos de archivos, FTP, servicios web y mover los datos según sea necesario a una ubicación centralizada para su posterior procesamiento.

Sin Data Factory, las empresas deben crear componentes de movimiento de datos personalizados o escribir servicios personalizados para integrar estos orígenes de datos y procesamientos. Tales sistemas, además de costosos, resultan difíciles de integrar y administrar, y a menudo carecen de las funcionalidades de supervisión y alerta de clase empresarial y de los controles que un servicio completamente administrado puede ofrecer.

Con Data Factory, puede usar la actividad de copia en una canalización de datos para mover los datos desde almacenes de datos de orígenes locales y en la nube a un almacén de datos de centralización en la nube para su posterior análisis. Por ejemplo, puede recopilar datos de una instancia de Azure Data Lake Store y transformarlos posteriormente mediante un servicio de proceso de Azure Data Lake Analytics. O también puede recopilar los datos en Azure Blob Storage y transformarlos más adelante mediante el uso de un clúster de Hadoop de Azure HDInsight.

<a id="transform-and-enrich" class="xliff"></a>

### Transformar y enriquecer
Una vez que los datos están presentes en un almacén de datos centralizado en la nube, querrá que los datos recopilados se procesen o transformen mediante servicios de proceso, como HDInsight Hadoop, Spark, Data Lake Analytics y Machine Learning. Querrá generar de manera confiable datos transformados según una programación controlada y fácil de mantener a fin de alimentar los entornos de producción con datos de confianza. 

<a id="publish" class="xliff"></a>

### Publicar 
Los datos transformados en la nube se entregarán a orígenes locales, como SQL Server, o se mantendrán en sus orígenes de almacenamiento en la nube para su consumo por parte de aplicaciones de inteligencia empresarial (BI), herramientas de análisis, etc.

<a id="key-components" class="xliff"></a>

## Componentes claves
Una suscripción de Azure puede tener una o varias instancias de Azure Data Factory (o factorías de datos). Azure Data Factory está compuesto por cuatro componentes principales que trabajan juntos para proporcionar una plataforma en la que pueda crear flujos de trabajo orientados a datos con pasos para moverlos y transformarlos. 

<a id="pipeline" class="xliff"></a>

### Canalización
Una factoría de datos puede tener una o más canalizaciones. Una canalización es un grupo de actividades. Juntas, las actividades de una canalización realizan una tarea. Por ejemplo, una canalización podría contener un grupo de actividades que ingiere datos de un blob de Azure y luego ejecutar una consulta de Hive en un clúster de HDInsight para particionar los datos. La ventaja de esto es que la canalización le permite administrar las actividades como un conjunto en lugar de tener que administrar cada una de ellas individualmente. Por ejemplo, puede implementar y programar la canalización completa, en lugar de las actividades de forma independiente. 

<a id="activity" class="xliff"></a>

### Actividad
Una canalización puede tener una o más actividades. Las actividades definen las acciones que se van a realizar en los datos. Por ejemplo, puede utilizar una actividad de copia para copiar datos de un almacén de datos a otro. De igual forma, puede usar una actividad de Hive, que ejecuta una consulta de Hive en un clúster de Azure HDInsight para transformar o analizar los datos. Data Factory admite dos tipos de actividades: actividades de movimiento de datos y actividades de transformación de datos.

<a id="data-movement-activities" class="xliff"></a>

### Actividades de movimiento de datos
Copiar actividad en Data Factory realiza una copia de los datos de un almacén de datos de origen a uno receptor. Data Factory admite los siguientes almacenes de datos. Se pueden escribir datos desde cualquier origen en todos los tipos de receptores. Haga clic en un almacén de datos para obtener información sobre cómo copiar datos a un almacén como origen o destino.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Para más información, consulte el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md).

<a id="data-transformation-activities" class="xliff"></a>

### Actividades de transformación de datos
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Para más información, consulte el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md).

<a id="custom-net-activities" class="xliff"></a>

### Actividades personalizadas de .NET
Si necesita mover datos a un almacén de datos, o desde este, que no sean compatibles con la actividad de copia, o bien transformar datos con su propia lógica, cree una **actividad de .NET personalizada**. Consulte el artículo [Uso de actividades personalizadas en una canalización de Azure Data Factory](data-factory-use-custom-activities.md)para obtener más información sobre la creación y el uso de una actividad personalizada.

<a id="datasets" class="xliff"></a>

### Conjuntos de datos
Cada actividad toma cero o más conjuntos de datos como entrada y genera uno o varios conjuntos de datos como salida. Los conjuntos de datos representan las estructuras de datos de los almacenes de datos que simplemente apuntan o hacen referencia a los datos que desea utilizar en sus actividades como entradas o salidas. Por ejemplo, un conjunto de datos del blob de Azure especifica el contenedor de blobs y la carpeta de Azure Blob Storage de los que la canalización debe leer los datos. O bien, un conjunto de datos de tabla de Azure SQL especifica la tabla en la que la actividad escribe los datos de salida. 

<a id="linked-services" class="xliff"></a>

### Servicios vinculados
Los servicios vinculados son muy similares a las cadenas de conexión que definen la información de conexión necesaria para que Data Factory se conecte a recursos externos. Considérelos de esta forma: un servicio vinculado define la conexión al origen de datos y un conjunto de datos representa la estructura de los datos. Por ejemplo, un servicio vinculado de Azure Storage especifica la cadena de conexión para conectarse a la cuenta de Azure Storage. Además, un conjunto de datos de Azure Blob especifica el contenedor de blobs y la carpeta que contiene los datos.   

Los servicios vinculados se utilizan con dos fines en Data Factory:

* Para representar un **almacén de datos** que incluye, entre otros, una instancia de SQL Server local, una base de datos de Oracle, un recurso compartido de archivos o una cuenta de Azure Blob Storage. Para ver una lista de los almacenes de datos compatibles, consulte la sección [Actividades de movimiento de datos](#data-movement-activities) .
* Para representar un **recurso de proceso** que puede hospedar la ejecución de una actividad. Por ejemplo, la actividad HDInsightHive se ejecuta en un clúster de Hadoop para HDInsight. Consulte la sección sobre [actividades de transformación de datos](#data-transformation-activities) para ver una lista de los entornos de procesos admitidos.

<a id="relationship-between-data-factory-entities" class="xliff"></a>

### Relación entre las entidades de Data Factory
![Diagrama: Data Factory, un servicio de integración de datos en la nube - conceptos clave](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figura 2.** Relaciones entre conjuntos de datos, actividades, canalizaciones y servicios vinculados

<a id="supported-regions" class="xliff"></a>

## Regiones admitidas
Actualmente, se pueden crear factorías de datos en las regiones de **oeste de EE. UU.**, **este de EE.UU.** y **Europa del Norte**. Sin embargo, una factoría de datos puede acceder a almacenes de datos y a servicios de proceso en otras regiones de Azure para mover datos entre los almacenes de datos o para procesar datos mediante servicios de proceso.

Azure Data Factory no almacena ningún dato. Permite crear flujos de trabajo controlados por datos para orquestar el movimiento de los datos entre los [almacenes de datos admitidos](#data-movement-activities) y organizar el procesamiento de los datos mediante [servicios de proceso](#data-transformation-activities) en otras regiones o en entornos locales. También permite [supervisar y administrar flujos de trabajo](data-factory-monitor-manage-pipelines.md) mediante mecanismos de programación y la interfaz de usuario.

Aunque Data Factory solamente está disponible en las regiones de **oeste de EE. UU.**, **este de EE.UU.** y **Europa del Norte**, el servicio que atiende el movimiento de datos en Data Factory está disponible [globalmente](data-factory-data-movement-activities.md#global) en varias regiones. Si un almacén de datos se encuentra detrás de un firewall, será una [puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md) instalada en el entorno local la que mueva los datos en su lugar.

Por ejemplo, supongamos que sus entornos de proceso, tales como clúster de Azure HDInsight y Azure Machine Learning, se ejecutan fuera de la región de Europa Occidental. Puede crear y usar una instancia de Azure Data Factory en Europa del Norte y usarla para programar trabajos en los entornos de proceso en Europa Occidental. Data Factory tarda unos milisegundos en desencadenar el trabajo en su entorno de proceso, pero el tiempo para ejecutar el trabajo en el entorno de proceso no cambia.

<a id="get-started-with-creating-a-pipeline" class="xliff"></a>

## Introducción a la creación de una canalización
Puede usar una de esas herramientas o una de estas API para crear canalizaciones de datos en Azure Data Factory: 

- Azure Portal
- Visual Studio
- PowerShell
- API de .NET
- API de REST
- Plantilla de Azure Resource Manager. 

Para aprender a compilar factorías de datos con canalizaciones de datos, siga las instrucciones paso a paso en los siguientes tutoriales:

| Tutorial | Descripción |
| --- | --- |
| [Movimiento de datos entre dos almacenes de datos en la nube](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |En este tutorial, va a crear una factoría de datos con una canalización que **mueve datos** de Blob Storage a SQL Database. |
| [Transformar datos usando el clúster de Hadoop](data-factory-build-your-first-pipeline.md) |En este tutorial, va a crear su primera instancia de Azure Data Factory con una canalización de datos que **procesa los datos** ejecutando el script de Hive en un clúster de Azure HDInsight (Hadoop). |
| [Movimiento de datos entre un almacén de datos local y un almacén de datos en la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) |En este tutorial, va a compilar una factoría de datos con una canalización que **mueve los datos** de una base de datos de SQL Server **local** a un blob de Azure. Como parte del tutorial, instalará y configurará la puerta de enlace de administración de datos en su máquina. |

