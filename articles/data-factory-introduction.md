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
	ms.date="03/09/2015" 
	ms.author="spelluru"/>

# Introducción al servicio Factoría de datos de Azure
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->

El servicio **Factoría de datos de Azure** es un servicio completamente administrado para crear servicios de almacenamiento de datos, procesamiento de datos y movimiento en canalizaciones de producción de datos confiable, escalable y simplificado. El servicio Factoría de datos le permite: 

- Crear flujos de trabajo de datos (canalizaciones) que unen, agregan y transforman datos locales, basados en la nube y almacenes de datos de Internet. 
- Transformar datos estructurados, semiestructurados y no estructurados de diversos orígenes de datos en información de confianza.
- Generar datos que pueden utilizarse fácilmente mediante el uso de inteligencia empresarial (BI), herramientas de análisis y otras aplicaciones. 
- Configurar procesamiento de datos complejos mediante scripting de JSON simple.
- Supervisar y administrar canalizaciones de un vistazo con una experiencia visual enriquecida que se ofrece a través del Portal de vista previa de Azure.  


<!--
This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
--> 

## Información general
Tradicionalmente, los proyectos de integración de datos han girado en torno a la creación de procesos de extracción, transformación y carga (ETL) que extraen datos de diversos orígenes de datos dentro de una organización, transforman los datos para ajustarse al esquema de destino de un almacén de datos empresarial (EDW) y cargan los datos en un EDW. Luego, se accede al EDW como el único origen de confianza para soluciones de análisis de BI. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

El panorama actual de los datos para las empresas continúa creciendo exponencialmente en volumen, variedad y complejidad. Es más diverso que nunca con datos locales y que nacen en la nube de diferentes formatos y velocidades.  El procesamiento de datos debe producirse en varias ubicaciones geográficas e incluye una combinación de software de código abierto, soluciones comerciales y servicios de procesamiento personalizados que son caros y difíciles de integrar y mantener.  La agilidad necesaria para adaptarse al panorama cambiante actual de Big Data es una oportunidad de combinar el EDW tradicional con las capacidades que se requieren en un sistema de producción de la información moderno.  

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]

El servicio **Factoría de datos de Azure** es la plataforma de composición para trabajar con los EDW tradicionales y el panorama cambiante de datos al permitir que las empresas aprovechen todos los datos que están a su disposición para tomar decisiones basadas en datos. Permite a las empresas acoplar esta diversidad al proporcionar una plataforma para componer el procesamiento, almacenamiento y movimiento de los datos en canalizaciones de producción de información y administrar los activos de datos de confianza.

El servicio Factoría de datos de Azure le permite:

- **Trabajar fácilmente con diversos sistemas de almacenamiento y procesamiento de los datos.**  
El servicio de Factoría de datos permite crear canalizaciones de producción de información que mueven y procesan los datos de forma local (por ejemplo, SQL Server) y orígenes de datos en la nube como Base de datos de SQL Azure, tabla de Azure y blobs. 
- **Transformar datos en información de confianza.**  
El servicio Factoría de datos es compatible con procesamiento Hive, Pig y C#, junto con las características de procesamiento clave como la administración automática de clúster de Hadoop (HDInsight), los reintentos para errores transitorios, las directivas de tiempo de espera configurables y las alertas.  
- **Supervisar las canalizaciones de datos en un solo lugar.** 
El servicio Factoría de datos proporciona una vista completa y confiable de los servicios de movimiento de datos, procesamiento y almacenamiento.  Le ayuda a evaluar el estado de la canalización de datos de un extremo a otro rápidamente, a identificar problemas y a tomar medidas correctivas si es necesario. Visualmente, puede realizar el seguimiento del linaje de datos y las relaciones entre los datos a través de cualquiera de los orígenes y consultar una contabilización histórica completa de ejecución del trabajo, estado del sistema y dependencias desde un solo panel de supervisión.
- **Obtener información detallada de los datos transformados**
El servicio Factoría de datos permite crear canalizaciones de datos que generan datos de confianza, que pueden utilizarse en inteligencia empresarial y herramientas de análisis y otras aplicaciones.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into 'Hubs'.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a "West US Azure Hub" which manages all of the Linked services and pipelines focused in the West US data center, or a "Sales EDW Hub" which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Modelo de aplicación
El siguiente diagrama ilustra el modelo de aplicaciones que admite el servicio de Factoría de datos de Azure.

![Application Model][image-data-factory-application-model]

Hay tres etapas de producción de información en una factoría de datos de Azure:

- **Conectar y recopilar**. En esta etapa, se importan datos de diversos orígenes de datos en los centros de datos. Una canalización de una factoría de datos puede tener una o más actividades. Utilice una o varias actividades de **copia** en una canalización de datos para recopilar datos de los almacenes de datos de origen a un almacén de datos de destino con un centro de datos para su posterior procesamiento. Un clúster de HDInsight (proceso) y su almacenamiento de blobs de Azure asociado (almacenamiento) juntos forman un centro de datos, un centro de datos de HDInsight. Para utilizar un centro de datos de HDInsight, copie todos los datos de origen en un almacén de blobs de Azure asociado a HDInsight, para que los datos se procesen por el clúster de HDInsight. Una canalización se ejecuta en un recurso de proceso de un centro de datos, como un clúster de HDInsight.      
- **Transformar y enriquecer**. En esta etapa, se procesan los datos recopilados. Por ejemplo, una **actividad de HDInsight** en una canalización puede procesar los datos almacenados en el almacén de blobs de Azure asociado mediante la realización de transformaciones con scripts de Hive y Pig para generar información de confianza. Las canalizaciones pueden encadenarse (como se muestra en el diagrama) por lo que los conjuntos de datos de salida de una canalización puede ser conjuntos de datos de entrada para otra canalización en el mismo centro de datos o en otro centro de datos.  
- **Publicar**. En esta etapa, los datos se publican para su consumo por parte de herramientas de BI, herramientas de análisis y otras aplicaciones. Por ejemplo, una actividad de copia en la canalización puede copiar datos de salida desde el procesamiento realizado en la fase de transformación y enriquecimiento a un almacén de datos (por ejemplo: SQL Server local) sobre el cual se pueden crear soluciones de inteligencia empresarial.   

<!--

Las factorías de datos permiten a los desarrolladores crear canalizaciones que son grupos de actividades de movimiento y/o procesamiento de datos que aceptan uno o varios conjuntos de datos de entrada y producen uno o varios conjuntos de datos de salida. Las canalizaciones se pueden ejecutar una vez o en un intervalo flexible de programaciones (cada hora, diariamente, semanalmente, etc.). Un conjunto de datos es una vista de datos con nombre. Los datos que se describen pueden variar desde bytes simples, pasando por datos semiestructurados, como archivos CSV, hasta tablas o modelos.

Las canalizaciones que se componen de actividades de movimiento de datos (por ejemplo: Actividad de copia) se usan con frecuencia para importar/exportar datos de todos los orígenes de datos (bases de datos, archivos, servicios SaaS, etc.) que usa la organización en un centro de datos.
 
Una vez que los datos están en un **centro**, las **canalizaciones** hospedadas por los servicios de proceso del centro se usan para transformar los datos de una manera adecuada para su consumo (por parte de herramientas BI, aplicaciones, clientes, etc.).  
  
Por último, las **canalizaciones** pueden encadenarse (como se muestra en el diagrama) de forma que los **conjuntos de datos** de salida de una son los de entrada de otra.  Esto permite que los flujos de datos complejos se factoricen en **canalizaciones** que se ejecutan dentro de un centro de datos o que abarcan varios centros.  El uso de las **canalizaciones** de este modo ofrece a las organizaciones los bloques de creación para componer los mejores servicios locales, en la nube y de Software como servicio (SaaS), todo ello a través del prisma de una sola factoría de datos administrada fácilmente.
--> 

## Experiencia de creación

Puede crear factorías de datos mediante uno de los sistemas siguientes:

- **Portal de vista previa de Azure**. Las hojas de Factoría de datos en el Portal de vista previa de Azure proporcionan una interfaz de usuario enriquecida para crear factorías de datos y servicios vinculados. El **Editor de Factoría de datos**, que también forma parte del portal, le permite crear fácilmente servicios vinculados, tablas, conjuntos de datos y canalizaciones mediante la especificación de definiciones de JSON para estos artefactos. Consulte [Editor de Factoría de datos][data-factory-editor] para obtener información general del editor e [Introducción a la Factoría de datos][datafactory-getstarted] para consultar un ejemplo del uso del portal o editor para crear e implementar una fábrica de datos.   
- **Azure PowerShell**. Si es un usuario de PowerShell y prefiere usar PowerShell en lugar de la interfaz de usuario del Portal, puede usar los cmdlets de Factoría de datos de Azure que forman parte de Azure PowerShell para crear e implementar factorías de datos. Consulte [Creación y supervisión de Factoría de datos de Azure con Azure PowerShell][create-data-factory-using-powershell] para consultar un ejemplo sencillo y [Tutorial: Desplazamiento y procesamiento de archivos de registro mediante Factoría de datos][adf-tutorial] para consultar un ejemplo avanzado del uso de cmdlets de PowerShell para crear e implementar una factoría de datos. Consulte el contenido de [Referencia de cmdlets de Factoría de datos][adf-powershell-reference] en MSDN Library para obtener información completa de los cmdlets de Factoría de datos.  
- **Biblioteca de clases .NET**. Se pueden crear factorías de datos mediante programación con el SDK de .NET de Factoría de datos. Consulte [Creación, supervisión y administración de las factorías de datos mediante el SDK de .NET][create-factory-using-dotnet-sdk] para un tutorial para crear un generador de datos con el SDK. NET. Consulte [Referencia de biblioteca de clases de Factoría de datos][msdn-class-library-reference] para una amplia documentación sobre el SDK de .NET de Factoría de datos.  
- **API de REST**. También puede utilizar la API de REST expuesta por el servicio Factoría de datos de Azure para crear e implementar factorías de datos. Consulte [Referencia de API de REST de Factoría de datos][msdn-rest-api-reference] para una amplia documentación sobre la API de REST de Factoría de datos. 


##Terminología
Esta sección presenta la terminología relacionada con la Factoría de datos de Azure.

### Factoría de datos
Una **factoría de datos de Azure** tiene una o más canalizaciones que procesan los datos en almacenes de datos vinculados (almacenamiento de Azure, Base de datos de SQL de Azure, SQL Server local, etc.) mediante el uso de los servicios de proceso vinculados como HDInsight de Azure. Una factoría de datos de Azure no contiene datos en ella misma; en su lugar, los datos se almacenan en los almacenes de datos mencionados anteriormente.  

### Servicios vinculados
Un **servicio vinculado** es un servicio que está vinculado a una factoría de datos de Azure. Un servicio vinculado puede ser uno de los siguientes:

- Un servicio de **almacenamiento de datos**, como el almacenamiento de Azure, una base de datos SQL de Azure y una base de datos SQL Server local. Un almacén de datos es un contenedor de conjuntos de datos de entrada y salida.    
- Un servicio de **proceso** como HDInsight de Azure y el Aprendizaje automático de Azure. Un servicio de proceso procesa los datos de entrada y genera los datos de salida.  

### Conjunto de datos
Un **conjunto de datos** es una vista de datos con nombre. Los datos que se describen pueden variar desde bytes simples, pasando por datos semiestructurados, como archivos CSV, hasta tablas relacionales o incluso modelos. Una **tabla** de Factoría de datos es un conjunto de datos que tiene un esquema y es rectangular. Después de crear un servicio vinculado en un almacén de datos que hace referencia a un almacén de datos, puede definir conjuntos de datos que representan datos de entrada y salida que se almacenan en el almacén de datos. 


###Canalización
Una **canalización** en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. Por ejemplo, una **actividad de copia** copia los datos desde un almacenamiento de origen hasta un almacenamiento de destino y las **actividades de HDInsight** utilizan un clúster de HDInsight de Azure para procesar datos mediante consultas de Hive o scripts de Pig. Una factoría de datos puede tener una o más canalizaciones. 

Los pasos típicos para crear una instancia de la Factoría de datos de Azure son:

1. Creación de una **factoría de datos**.
2. Creación de un **servicio vinculado** para cada almacén de datos o servicio de proceso.
3. Creación de **conjuntos de datos** de entrada y salida.
4. Creación de una **canalización**. 

###Actividad
Un paso del procesamiento de datos en una canalización que toma uno o más conjuntos de datos de entrada y produce uno o más conjuntos de datos de salida.  Las actividades se ejecutan en un entorno de ejecución (por ejemplo: clúster de HDInsight de Azure) y leen o escriben datos en un almacén de datos asociado o vinculado con la factoría de datos de Azure. 

El servicio de Factoría de datos de Azure admite las siguientes actividades en una canalización: 

- La **actividad de copia** copia los datos desde un almacén de datos a otro almacén de datos. Consulte [Copia de datos con la Factoría de datos de Azure][copy-data-with-adf] para obtener más información acerca de los almacenes de datos que admite la actividad de copia. 
- La **Actividad de HDInsight** procesa datos mediante la ejecución de scripts de Hive o Pig o programas MapReduce en un clúster de HDInsight. Consulte [Uso de Pig y Hive con Factoría de datos][use-pig-hive] e [Invocación de programas MapReduce desde Factoría de datos][run-map-reduce] para obtener más información. 
- La **actividad de puntuación por lotes de Aprendizaje automático de Azure** invoca la API de puntuación por lotes de Aprendizaje automático de Azure. Consulte [Creación de canalizaciones predictivas mediante la Factoría de datos de Azure y el Aprendizaje automático de Azure][azure-ml-adf] para obtener más información. 
- **Actividad de procedimiento almacenado** invoca un procedimiento almacenado en una base de datos de SQL Azure. Consulte la [actividad de procedimiento almacenado][msdn-stored-procedure-activity] en MSDN Library para obtener información detallada.   

###Segmento
Una tabla de una factoría de datos de Azure se compone de segmentos. El ancho de un segmento se determina por la programación: cada hora o diariamente. Cuando la programación es "cada hora", se produce un segmento cada hora con la hora de inicio y la hora de finalización de una canalización. Por ejemplo, si la fecha y hora de inicio de la canalización es 03/03/2015 06:00:00 (6 a.m.) y la fecha y hora de finalización es 03/03/2015 09:00:00 (9 a.m. en el mismo día), se generan tres segmentos de datos, un segmento para cada intervalo de 1 hora: 6-7 a.m., 7-8 a.m. y 8-9 a.m.    

Los sectores proporcionan la capacidad de trabajar con un subconjunto de datos globales para una ventana de tiempo específico (por ejemplo, el segmento que se genera para la duración (hora): de 1:00 p.m. a 2:00 p.m.). 

### Ejecución de la actividad en un segmento
La **ejecución** o la ejecución de una actividad es una unidad de procesamiento de un segmento. Podría haber una o varias ejecuciones de un segmento en caso de reintentos o si vuelve a ejecutar el segmento en caso de error. Un segmento se identifica mediante su hora de inicio.

###Data Management Gateway
Microsoft **Data Management Gateway** es un software que conecta orígenes de datos locales a servicios en la nube para su consumo. Debe tener al menos una puerta de enlace instalada en su entorno corporativo y registrarla con el portal de la Factoría de datos de Azure antes de agregar orígenes de datos locales como servicios vinculados.
 
###Centro de datos
Un **centro de datos** de Azure es un contenedor de los servicios de almacenamiento y proceso de datos. Por ejemplo, un clúster de Hadoop con HDFS como almacenamiento y Hive o Pig como proceso (procesamiento) es un centro de datos. De forma similar, un almacenamiento de datos empresariales (EDW) se puede modelar como un centro de datos (base de datos como almacenamiento, procedimientos almacenados o herramienta ETL como servicios de proceso).  Las canalizaciones utilizan almacenes de datos y se ejecutan en recursos de proceso en un centro de datos. En este momento solo se admite el centro de HDInsight.

El centro de datos permite la división de una factoría de datos en agrupaciones específicas lógicas o de dominio, como el "centro de Azure del oeste de EE. UU." que administra todos los servicios vinculados (almacenes de datos y proceso) y las canalizaciones centradas en el centro de datos del oeste de EE. UU., o el "centro de EDW de ventas" que administra todos los servicios vinculados y las canalizaciones relacionadas con el llenado y el procesamiento de datos para el almacén de datos empresariales de ventas.

Una característica importante de los centros es que una canalización se ejecuta en un único centro. Esto significa que al definir una canalización, todos los servicios vinculados a los que hacen referencia las tablas o las actividades dentro de esa canalización deben tener el mismo nombre de centro que la propia canalización. Si no se especifica la propiedad HubName para un servicio vinculado, el servicio vinculado se coloca en el centro "predeterminado".

##Pasos siguientes

1. [Introducción a la Factoría de datos][datafactory-getstarted]. Este artículo ofrece un completo tutorial que le muestra cómo crear una factoría de datos de Azure de ejemplo que copia datos desde un blob de Azure hasta una base de datos SQL de Azure.
2. [Tutorial: Desplazamiento y procesamiento de archivos de registro mediante la Factoría de datos][adf-tutorial] En este artículo se proporciona un **tutorial completo** en el que se muestra cómo implementar un **escenario real** mediante la factoría de datos de Azure para transformar los datos de archivos de registro en información.
3. [Factoría de datos de Azure - Preguntas más frecuentes][adf-faq]. Este artículo proporciona una lista de las preguntas y respuestas más frecuentes. 
3. [Escenarios habituales para usar Factoría de datos de Azure][adf-common-scenarios]. En este artículo se describen algunos escenarios habituales para usar el servicio de Factoría de datos de Azure.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md

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

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=49-->