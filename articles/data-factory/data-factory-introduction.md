<properties 
	pageTitle="Introducción a la Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo puede usar el servicio de la factoría de datos de Azure para componer el procesamiento de datos, el almacenamiento de datos y los servicios de movimiento de datos para crear canalizaciones que generen información de confianza." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Introducción al servicio Factoría de datos de Azure
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


El **factoría de datos de Azure** es un servicio totalmente administrado para crear servicios de movimiento de datos, procesamiento de datos y almacenamiento de datos en las canalizaciones de producción de datos optimizadas, escalables y confiables. El servicio Factoría de datos le permite:

- Crear flujos de trabajo de datos (canalizaciones) que unen, agregan y transforman datos locales, basados en la nube y almacenes de datos de Internet. 
- Transformar datos estructurados, semiestructurados y no estructurados de diversos orígenes de datos en información de confianza.
- Generar datos que pueden utilizarse fácilmente mediante el uso de inteligencia empresarial (BI), herramientas de análisis y otras aplicaciones. 
- Configurar procesamiento de datos complejos mediante scripting de JSON simple.
- Supervisar y administrar canalizaciones de un vistazo con una experiencia visual enriquecida que se ofrece a través del Portal de vista previa de Azure.  

El vídeo siguiente proporciona una breve descripción de servicio del generador de datos de Azure.


- [Vídeo: Introducción a generador de datos de Azure](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## Información general
Tradicionalmente, los proyectos de integración de datos han girado en torno a la creación de procesos de extracción, transformación y carga (ETL) que extraen datos de diversos orígenes de datos dentro de una organización, transforman los datos para ajustarse al esquema de destino de un almacén de datos empresarial (EDW) y cargan los datos en un EDW. Luego, se accede al EDW como el único origen de confianza para soluciones de análisis de BI.

![ETL tradicional][image-data-factory-introduction-traditional-ETL]

El panorama actual de los datos para las empresas continúa creciendo exponencialmente en volumen, variedad y complejidad. Es más diverso que nunca con datos locales y que nacen en la nube de diferentes formatos y velocidades. El procesamiento de datos debe producirse en varias ubicaciones geográficas e incluye una combinación de software de código abierto, soluciones comerciales y servicios de procesamiento personalizados que son caros y difíciles de integrar y mantener. La agilidad necesaria para adaptarse al panorama cambiante actual de Big Data es una oportunidad de combinar el EDW tradicional con las capacidades que se requieren en un sistema de producción de la información moderno.

![Procesamiento diversos hoy horizontal][image-data-factory-introduction-todays-diverse-processing-landspace]

El **factoría de datos de Azure** service es la plataforma de composición para trabajo en EDWs tradicionales y el panorama cambiante de datos para permitir que las empresas aprovechen todos los datos que está a su disposición para tomar decisiones basadas en datos. Permite a las empresas acoplar esta diversidad al proporcionar una plataforma para componer el procesamiento, almacenamiento y movimiento de los datos en canalizaciones de producción de información y administrar los activos de datos de confianza.

El servicio Factoría de datos de Azure le permite:

- **Trabajar fácilmente con los sistemas de almacenamiento y procesamiento de datos diversos.** El servicio de Factoría de datos permite crear canalizaciones de producción de información que mueven y procesan los datos de forma local (por ejemplo, SQL Server) y orígenes de datos en la nube como Base de datos de SQL Azure, tabla de Azure y blobs. 
- **Transformar datos en información de confianza.** El servicio Factoría de datos es compatible con procesamiento Hive, Pig y C#, junto con las características de procesamiento clave como la administración automática de clúster de Hadoop (HDInsight), los reintentos para errores transitorios, las directivas de tiempo de espera configurables y las alertas.  
- **Supervisar las canalizaciones de datos en un solo lugar.** El servicio Factoría de datos proporciona una vista completa y confiable de los servicios de movimiento de datos, procesamiento y almacenamiento. Le ayuda a evaluar el estado de la canalización de datos de un extremo a otro rápidamente, a identificar problemas y a tomar medidas correctivas si es necesario. Visualmente, puede realizar el seguimiento del linaje de datos y las relaciones entre los datos a través de cualquiera de los orígenes y consultar una contabilización histórica completa de ejecución del trabajo, estado del sistema y dependencias desde un solo panel de supervisión.
- **Obtener la información necesaria de datos transformados** el generador de datos servicio le permite crear canalizaciones de datos que generan datos de confianza, que pueden ser utilizados por herramientas analíticas y de inteligencia de negocios y otras aplicaciones.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Modelo de aplicación
El siguiente diagrama ilustra el modelo de aplicaciones que admite el servicio de Factoría de datos de Azure.

![Modelo de aplicación][image-data-factory-application-model]

Hay tres etapas de producción de información en una factoría de datos de Azure:

- **Conectar & recopilar**. En esta etapa, se importan datos de diversos orígenes de datos en los centros de datos. Una canalización de una factoría de datos puede tener una o más actividades. Utilice uno o varios **copia** las actividades en una canalización de datos para recopilar datos de origen de los almacenes de datos a un almacén de datos de destino con en un centro de datos para su posterior procesamiento. Un clúster de HDInsight (proceso) y su almacenamiento de blobs de Azure asociado (almacenamiento) juntos forman un centro de datos, un centro de datos de HDInsight. Para utilizar un centro de datos de HDInsight, copie todos los datos de origen en un almacén de blobs de Azure asociado a HDInsight, para que los datos se procesen por el clúster de HDInsight. Una canalización se ejecuta en un recurso de proceso de un centro de datos, como un clúster de HDInsight.      
- **Transformar y enriquecer**. En esta etapa, se procesan los datos recopilados. Por ejemplo, un **actividad HDInsight** en una canalización, puede procesar los datos almacenados en el almacén de blobs de Azure asociada mediante la realización de transformaciones mediante secuencias de comandos de Hive y Pig para generar información de confianza. Las canalizaciones pueden encadenarse (como se muestra en el diagrama) por lo que los conjuntos de datos de salida de una canalización puede ser conjuntos de datos de entrada para otra canalización en el mismo centro de datos o en otro centro de datos.  
- **Publicar**. En esta etapa, los datos se publican para su consumo por parte de herramientas de BI, herramientas de análisis y otras aplicaciones. Por ejemplo, una actividad de copia en la canalización puede copiar datos de salida desde el procesamiento realizado en la fase de transformación y enriquecimiento a un almacén de datos (por ejemplo: SQL Server local) sobre el cual se pueden crear soluciones de inteligencia empresarial.   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##Pasos siguientes
1. [Comenzar con el generador de datos][datafactory-getstarted]. Este artículo ofrece un completo tutorial que le muestra cómo crear una factoría de datos de Azure de ejemplo que copia datos desde un blob de Azure hasta una base de datos SQL de Azure.
2. [Tutorial: mover y procesar los archivos de registro mediante el generador de datos][adf-tutorial]. Este artículo proporciona un **-to-end tutorial** que muestra cómo implementar un **escenario real** mediante el generador de datos de Azure para transformar los datos de archivos de registro en perspectivas.

## Otras referencias
- [Factoría de datos - terminología][adf-terminology]. En este artículo se presenta la terminología utilizada en la creación de generadores de datos mediante el servicio de generador de datos de Azure 
- [Factoría de datos - preguntas más frecuentes][adf-faq]. Este artículo proporciona una lista de las preguntas y respuestas más frecuentes.
- [Escenarios habituales para usar el generador de datos de Azure][adf-common-scenarios]. En este artículo se describen algunos escenarios habituales para usar el servicio de Factoría de datos de Azure. 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png

<!---HONumber=GIT-SubDir--> 