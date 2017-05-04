---
title: "Introducción a Data Factory, un servicio de integración de datos | Microsoft Docs"
description: "Sepa lo que es Data Factory de Azure: un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos."
keywords: "integración de datos, integración de datos de nube, qué es data factory de azure"
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
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introducción al servicio Data Factory de Azure, un servicio de integración de datos en la nube
## <a name="what-is-azure-data-factory"></a>¿Qué es la factoría de datos de Azure?
En el mundo de los macrodatos, ¿cómo aprovechan las empresas los datos existentes? ¿Es posible enriquecer los datos generados en la nube mediante el uso de datos de referencia de orígenes de datos locales u otros orígenes de datos dispares? Por ello, es necesaria una plataforma para la agregación y procesamiento de datos procedentes de una gran variedad de orígenes. Azure Data Factory es un servicio de integración de datos basado en la nube que organiza y automatiza el **movimiento** y la **transformación** de datos. Puede crear soluciones de integración de datos que pueden ingerir datos de entrada de distintos almacenes, transformarlos y procesarlos, y publicar los datos resultantes en otros almacenes de datos. 

![Diagrama: Información general de Data Factory, un servicio de integración de datos](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figura 1.** Ingiera datos de varios orígenes, prepárelos, transfórmelos y analícelos, y después, y publique unos datos listos para usar que puedan consumirse.


## <a name="what-does-it-offer"></a>¿Qué ofrece? 
Tradicionalmente, los proyectos de integración de datos han girado en torno a la creación de procesos de extracción, transformación y carga (ETL) que extraían los datos a partir de diversos orígenes de datos de una organización, los transformaban para adaptarse al esquema de destino de un almacenamiento de datos empresarial (EDW) y los cargaban en este último como se indica en la siguiente imagen. Posteriormente, se accedía al EDW como única fuente para las soluciones de análisis de BI.

![ETL tradicional](media/data-factory-introduction/traditional-etl.png)
**ETL tradicional**

Hoy en día, el panorama de los datos para las empresas continúa creciendo exponencialmente en volumen, variedad y complejidad, como se muestra en la siguiente imagen. Es más diverso que nunca, con datos de origen local y datos procedentes de la nube en diferentes formatos y velocidades. El procesamiento de datos debe realizarse en diferentes ubicaciones geográficas e incluye una combinación de software de código abierto, soluciones comerciales y servicios de procesamiento personalizado que son caros y difíciles de integrar y mantener. La agilidad necesaria para adaptarse al cambiante panorama actual de los macrodatos constituye una oportunidad para combinar el EDW tradicional con las funcionalidades requeridas para un sistema de producción de la información moderno. Azure Data Factory es la plataforma de composición para combinar los EDW tradicionales y el panorama cambiante de los datos, y permitir que las empresas aprovechen todos los datos que están disponibles para la toma de decisiones basadas en datos.

![Nuevo panorama de macrodatos](media/data-factory-introduction/new-big-data-landscape.png)
**Nuevo panorama de macrodatos**

El servicio Azure Data Factory permite a las empresas aprovechar esta diversidad proporcionando una plataforma para **transformar los servicios de procesamiento de datos, almacenamiento y movimiento en canalizaciones de producción de información**, y administrar los recursos de datos de confianza.

El servicio Azure Data Factory le permite:
- **Trabajar con facilidad con diversos sistemas de procesamiento y almacenamiento de datos**. 

    Las empresas tienen datos de tipos dispares que se encuentran en diversos orígenes. El primer paso en la creación de un sistema de producción de información es conectarse a todos los orígenes necesarios de datos y procesamiento, como servicios SaaS, recursos web compartidos de archivos, FTP, servicios web y mover los datos según sea necesario a una ubicación centralizada para su posterior procesamiento.

    Sin la factoría de datos, las empresas deben crear componentes de movimiento de datos personalizados o escribir servicios personalizados para integrar estos orígenes de datos y procesamientos. Esto resulta costoso; además, es difícil integrar y mantener dichos sistemas y a menudo se carece de capacidades de supervisión y alerta de clase empresarial y de los controles que un servicio completamente administrado puede ofrecer.

    Con Data Factory, puede usar la actividad de copia en una canalización de datos para mover los datos desde almacenes de datos de orígenes locales y en la nube a un almacén de datos de centralización en la nube para su posterior análisis. Por ejemplo, puede recopilar datos de una instancia de Azure Data Lake Store y transformarlos posteriormente mediante un servicio de proceso de Azure Data Lake Analytics. O también puede recopilar los datos en Azure Blob Storage y transformarlos más adelante mediante el uso de un clúster de Hadoop de Azure HDInsight.
- **Transforme datos en información de confianza**. 

    Una vez que los datos están presentes en un almacén de datos centralizado en la nube, puede crear e implementar canalizaciones de datos para producir de forma confiable datos transformados según una programación sostenible y controlada para suministrar a los entornos de producción datos de confianza. La transformación de datos en Azure Data Factory se realiza mediante actividades de transformación como las de Hive, Pig, MapReduce, ejecución de lotes de Azure Machine Learning y mediante actividades personalizadas de C# que se ejecutan en un clúster de Hadoop de Azure HDInsight, máquinas virtuales de Azure Machine Learning o en grupos de máquinas virtuales de Azure Batch.
- **Supervise las canalizaciones de datos desde un solo lugar**.

    Con una diversa cartera de datos, es importante tener una vista completa y confiable de sus servicios de almacenamiento, procesamiento y movimiento de datos. Data Factory le ayuda a valorar rápidamente el estado de la canalización de datos de un extremo al otro, detectar problemas y aplicar medidas correctivas cuando se requieran. Realice un seguimiento visual de la ascendencia de los datos y las relaciones entre ellos a través de cualquiera de sus orígenes. Consulte una representación histórica completa de la ejecución de trabajos, el estado del sistema y las dependencias desde un solo panel de supervisión.

## <a name="how-does-it-work"></a>¿Cómo funciona? 
Hay tres fases de producción de información en Azure Data Factory:

![Tres fases de producción de información](media/data-factory-introduction/three-information-production-stages.png)

- **Conexión y recopilación**: en esta fase, se recopilan los datos de diversos orígenes de datos en un solo lugar.
- **Transformación y enriquecimiento**: en esta fase, se procesan o se transforman los datos recopilados.
- **Publicación**. En esta fase, los datos se publican para su uso por parte de las herramientas de BI, de análisis y otras aplicaciones.

## <a name="key-components"></a>Componentes claves
Una suscripción de Azure puede tener una o varias instancias de Azure Data Factory (o factorías de datos). Azure Data Factory se compone de cuatro componentes claves que funcionan de forma conjunta para proporcionar una plataforma en la que se pueden crear orquestaciones de movimiento y transformación de datos simples o complejos en el flujo de datos.

### <a name="activity"></a>Actividad
Las actividades definen las acciones que se van a realizar en los datos. Por ejemplo, puede utilizar una actividad de copia para copiar datos de un almacén de datos a otro. De igual forma, puede usar una actividad de Hive, que ejecuta una consulta de Hive en un clúster de Azure HDInsight para transformar o analizar los datos. Data Factory admite dos tipos de actividades: actividades de movimiento de datos y actividades de transformación de datos.

Cada actividad puede tener cero o más conjuntos de datos de entrada y generar uno o más conjuntos de datos de salida. 


### <a name="data-movement-activities"></a>Actividades de movimiento de datos
Copiar actividad en Data Factory realiza una copia de los datos de un almacén de datos de origen a uno receptor. Data Factory admite los siguientes almacenes de datos. Se pueden escribir datos desde cualquier origen en todos los tipos de receptores. Haga clic en un almacén de datos para obtener información sobre cómo copiar datos a un almacén como origen o destino.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Para más información, consulte el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Actividades de transformación de datos
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Para más información, consulte el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md).

Si necesita mover datos a un almacén de datos, o desde este, que no sean compatibles con la actividad de copia, o bien transformar datos con su propia lógica, cree una **actividad de .NET personalizada**. Consulte el artículo [Uso de actividades personalizadas en una canalización de Data Factory de Azure](data-factory-use-custom-activities.md)para obtener más información sobre la creación y el uso de una actividad personalizada.

### <a name="pipeline"></a>Canalización
Una canalización es un grupo de actividades. Juntas, las actividades de una canalización realizan una tarea. Por ejemplo, una canalización puede contener un grupo de actividades que recopila datos de un blob de Azure y, a continuación, ejecuta una consulta de Hive en un clúster de HDInsight para particionar los datos de registro. La ventaja de esto es que la canalización le permite administrar las actividades como un conjunto en lugar de tener que administrar cada una de ellas individualmente. Por ejemplo, puede implementar y programar la canalización completa, en lugar de las actividades de forma independiente.

### <a name="datasets"></a>Conjuntos de datos
Los conjuntos de datos representan las estructuras de datos de los almacenes de datos que simplemente apuntan o hacen referencia a los datos que desea utilizar en sus actividades como entradas o salidas. Por ejemplo, un conjunto de datos del blob de Azure especifica el contenedor de blobs y la carpeta de Azure Blob Storage de los que la canalización debe leer los datos. 

### <a name="linked-services"></a>Servicios vinculados
Los servicios vinculados son muy similares a las cadenas de conexión que definen la información de conexión necesaria para que Data Factory se conecte a recursos externos. Considérelos de esta forma: el conjunto de datos representa la estructura de los datos y el servicio vinculado define la conexión al origen de datos.  Los servicios vinculados se utilizan con dos fines en Factoría de datos:

* Para representar un **almacén de datos** que incluye, entre otros, una instancia de SQL Server local, una base de datos de Oracle, un recurso compartido de archivos o una cuenta de Azure Blob Storage. Para ver una lista de los almacenes de datos compatibles, consulte la sección [Actividades de movimiento de datos](#data-movement-activities) .
* Para representar un **recurso de proceso** que puede hospedar la ejecución de una actividad. Por ejemplo, la actividad HDInsightHive se ejecuta en un clúster de Hadoop para HDInsight. Consulte la sección sobre [actividades de transformación de datos](#data-transformation-activities) para ver una lista de los entornos de procesos admitidos.

### <a name="relationship-between-data-factory-entities"></a>Relación entre las entidades de Data Factory
![Diagrama: Data Factory, un servicio de integración de datos en la nube - conceptos clave](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figura 2.** Relaciones entre conjuntos de datos, actividades, canalizaciones y servicios vinculados

## <a name="supported-regions"></a>Regiones admitidas
Actualmente, se pueden crear factorías de datos en las regiones de **oeste de EE. UU.**, **este de EE.UU.** y **Europa del Norte**. Sin embargo, una factoría de datos puede acceder a almacenes de datos y a servicios de proceso en otras regiones de Azure para mover datos entre los almacenes de datos o para procesar datos mediante servicios de proceso.

Data Factory de Azure no almacena ningún dato. Permite crear flujos controlados por datos para organizar el movimiento de los datos entre los [almacenes de datos admitidos](data-factory-data-movement-activities.md#supported-data-stores-and-formats) y organizar el procesamiento de los datos mediante [servicios de proceso](data-factory-compute-linked-services.md) en otras regiones o en entornos locales. También permite [supervisar y administrar flujos de trabajo](data-factory-monitor-manage-pipelines.md) mediante mecanismos de programación y la interfaz de usuario.

Aunque Data Factory solamente está disponible en las regiones de **oeste de EE. UU.**, **este de EE.UU.** y **Europa del Norte**, el servicio que atiende el movimiento de datos en Data Factory está disponible [globalmente](data-factory-data-movement-activities.md#global) en varias regiones. Si un almacén de datos se encuentra detrás de un firewall, los datos se moverán mediante una [puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md), siempre que esté instalada en el entorno de local.

Por ejemplo, supongamos que sus entornos de proceso, tales como clúster de Azure HDInsight y Azure Machine Learning, se ejecutan fuera de la región de Europa Occidental. Puede crear y usar una instancia de Azure Data Factory en Europa del Norte y usarla para programar trabajos en los entornos de proceso en Europa Occidental. Data Factory tarda unos milisegundos en desencadenar el trabajo en su entorno de proceso, pero el tiempo para ejecutar el trabajo en el entorno de proceso no cambia.

En el futuro, pretendemos tener Data Factory de Azure en todas las ubicaciones geográficas admitidas por Azure.

## <a name="next-steps"></a>Pasos siguientes
Para aprender a compilar factorías de datos con canalizaciones de datos, siga las instrucciones paso a paso en los siguientes tutoriales:

| Tutorial | Descripción |
| --- | --- |
| [Movimiento de datos entre dos almacenes de datos en la nube](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |En este tutorial, va a crear una factoría de datos con una canalización que **mueve datos** de Blob Storage a SQL Database. |
| [Transformar datos usando el clúster de Hadoop](data-factory-build-your-first-pipeline.md) |En este tutorial, va a crear su primera instancia de Azure Data Factory con una canalización de datos que **procesa los datos** ejecutando el script de Hive en un clúster de Azure HDInsight (Hadoop). |
| [Movimiento de datos entre un almacén de datos local y un almacén de datos en la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) |En este tutorial, va a compilar una factoría de datos con una canalización que **mueve los datos** de una base de datos de SQL Server **local** a un blob de Azure. Como parte del tutorial, instalará y configurará la puerta de enlace de administración de datos en su máquina. |

