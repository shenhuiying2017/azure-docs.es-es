<properties title="Introduction to Azure Data Factory" pageTitle="Introducción a la Factoría de datos de Azure" description="Obtenga información acerca de cómo puede usar el servicio de la factoría de datos de Azure para componer el procesamiento de datos, el almacenamiento de datos y los servicios de movimiento de datos para crear canalizaciones que generen información de confianza." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Introducción al servicio Factoría de datos de Azure
El servicio **Factoría de datos de Azure** es un servicio completamente administrado para la composición de servicios de almacenamiento, procesamiento y movimiento de datos en canalizaciones de producción de datos mejoradas, escalables y fiables.  Los desarrolladores pueden usar la factoría de datos para transformar datos estructurados, semiestructurados y sin estructurar de orígenes locales y en la nube en información de confianza. Los desarrolladores crean flujos de trabajo orientados a datos (canalizaciones) que combinan, agregan y transforman datos que proceden de sus servicios locales, basados en la nube y de Internet, y configuran procesamientos de datos complejos a través de scripting JSON simple. El servicio Factoría de datos de Azure permite supervisar y administrar estas canalizaciones de un vistazo con una experiencia visual enriquecida que se ofrece a través del Portal de vista previa de Azure. La información generada por las canalizaciones puede usarse fácilmente mediante BI y herramientas de análisis, así como otras aplicaciones para controlar de forma confiable decisiones y perspectivas empresariales clave.

Este artículo proporciona información general sobre el servicio Factoría de datos de Azure, el valor que proporciona y los escenarios que admite. 

##Información general sobre la Factoría de datos de Azure
Tradicionalmente, los proyectos de integración de datos han girado en torno a la creación de procesos de extracción, transformación y carga (ETL) que extraen datos de diversos orígenes de datos dentro de una organización, transforman los datos para ajustarse al esquema de destino de un almacén de datos empresarial (EDW) y cargan los datos en un EDW tal como se muestra en la siguiente imagen. Luego, se accede al EDW como el único origen de confianza para soluciones de análisis de BI. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

El panorama actual de los datos para las empresas continúa creciendo exponencialmente en volumen, variedad y complejidad, como se muestra en la siguiente imagen.  Es más diverso que nunca con datos locales y que nacen en la nube de diferentes formatos y velocidades.  El procesamiento de datos debe producirse en varias ubicaciones geográficas e incluye una combinación de software de código abierto, soluciones comerciales y servicios de procesamiento personalizados que son caros y difíciles de integrar y mantener.  La agilidad necesaria para adaptarse al panorama cambiante actual de Big Data es una oportunidad de combinar el EDW tradicional con las capacidades que se requieren en un sistema de producción de la información moderno.  El servicio Factoría de datos de Azure es la plataforma de composición para trabajar con los EDW tradicionales y el panorama cambiante de datos al permitir que las empresas aprovechen todos los datos que están a su disposición para tomar decisiones basadas en datos.

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]


El servicio **Factoría de datos de Azure** permite a las empresas acoplar esta diversidad al proporcionar una plataforma para **componer el procesamiento, almacenamiento y movimiento de los datos en canalizaciones de producción de información y administrar los activos de datos de confianza**.

El servicio Factoría de datos de Azure le permite:

- **Trabajar fácilmente con diversos sistemas de almacenamiento y procesamiento de los datos.** 
La factoría de datos le permite usar los datos donde están almacenados y componer servicios para su almacenamiento, procesamiento y movimiento.  Por ejemplo, puede componer canalizaciones de producción de información para procesar los datos locales como SQL Server, junto con los datos en la nube como Base de datos de SQL de Azure, Blobs y Tablas.  
- **Transformar datos en información de confianza.** 
Combinar y dar forma a datos complejos puede no ser fácil de conseguir a la primera y cambiar los modelos de datos puede ser costoso y laborioso.  Mediante la factoría de datos puede centrarse en análisis de transformación mientras el servicio se encarga de la estructura.  La factoría de datos es compatible con procesamiento Hive, Pig y C#, junto con las características de procesamiento clave como la administración automática de clúster de Hadoop (HDInsight), los reintentos para errores transitorios, las directivas de tiempo de espera configurables y las alertas.  
- **Supervisar las canalizaciones de datos en un solo lugar.** 
Con una cartera de datos diversa, es importante tener una vista completa y confiable de los servicios de movimiento, procesamiento y almacenamiento de los datos.  La factoría de datos le ayuda a evaluar rápidamente el estado completo de las canalizaciones de datos, a analizar los problemas y a tomar las acciones correctivas necesarias. Realice un seguimiento visual del linaje de los datos y de las relaciones entre sus datos en cualquiera de sus orígenes. Vea un historial completo de las ejecuciones de los trabajos, del estado del sistema y de las dependencias desde un único panel de supervisión.
- **Obtener información detallada de los datos transformados**
Adáptese a las preguntas en constante cambio a las que su organización necesita responder y manténgase al día cuando la producción de datos esté preparada.  Mejore su capacidad de impulsar mejor su información empresarial mediante la producción de información oportuna y de confianza para el consumo. Use canalizaciones de datos para entregar datos transformados desde la nube hasta los orígenes locales como SQL Server, o manténgalos en sus orígenes de almacenamiento en la nube para su consumo por BI y herramientas de análisis y otras aplicaciones.

Hoy en día, para aprovechar las ventajas de la factoría de datos, los desarrolladores interactúan directamente con canalizaciones de datos, servicios de almacenamiento y servicios de proceso individuales.  A medida que evolucione el servicio Factoría de datos, presentaremos servicios adicionales de almacenamiento y procesamiento, y nuevos mecanismos de agrupar servicios de proceso y almacenamiento y canalizaciones de datos en "centros".  Aquí describimos los centros en nuestra introducción, dado que este concepto incipiente aparece en todo el servicio como precursor de futuras versiones.

Un centro de factorías de datos de Azure es un contenedor de servicios de almacenamiento y proceso (ambos conocidos como servicios vinculado), así como de las canalizaciones de datos que se utilizan y se ejecutan en esos recursos. El contenedor de centros permite que la factoría de datos se divida en agrupaciones específicas lógicas o de dominio.  Por ejemplo, una empresa puede tener un "centro de Azure del oeste de EE. UU." que administra todos los servicios vinculados y las canalizaciones centradas en el centro de datos del oeste de EE. UU., o un "centro de EDW de ventas" que administra todos los servicios vinculados y las canalizaciones asociadas con el llenado y el procesamiento de datos para el EDW de ventas.  Una característica importante de los centros es que una canalización se ejecuta en un único centro. Esto significa que al definir una canalización, todos los servicios vinculados a los que hacen referencia las tablas o las actividades dentro de esa canalización deben tener el mismo nombre de centro que la propia canalización.

Los centros le ayudarán a encapsular el almacenamiento y los procesos de forma que las canalizaciones puedan hacer referencia únicamente a los servicios y las tablas específicos que utilizan. Luego el centro puede utilizar directivas para decidir dónde ejecutar una canalización. Esto tendrá varias consecuencias importantes. Una es que proporcionará un escalado vertical más fácil dado que se pueden agregar más servicios vinculados a un centro y se puede equilibrar la carga de las canalizaciones entre estos nuevos servicios vinculados. Otra es que se volverán a usar las definiciones de canalización en diferentes centros.

## Escenarios
Hay una amplia variedad de escenarios de canalizaciones de datos que abarcan sistemas locales, sistemas en la nube, sistemas de SaaS, así como la producción de datos de transmisión por secuencias y basada en lotes.  En esta sección se describen algunos escenarios de ejemplo que admite actualmente la Fábrica de datos de Azure, y seguirán creciendo como escenarios de centros.

###Escenario 1: Orígenes de datos para el centro de datos
![Source the Data Hub][image-data-factory-introduction-secenario1-source-datahub]

Las empresas tienen datos de tipos dispares ubicados en orígenes dispares.  El primer paso en la creación de un sistema de producción de la información es conectarse a todos los orígenes de datos necesarios de datos y procesamiento, como servicios SaaS, recursos compartidos de archivo, FTP, servicios web, y mover los datos según sea necesario en los sucesivos procesamientos.

Sin la factoría de datos, las empresas deben crear componentes de movimiento de datos personalizados o escribir servicios personalizados para integrar estos orígenes de datos y procesamientos.  Esto resulta costoso; además, es difícil integrar y mantener dichos sistemas y a menudo se carece de capacidades de supervisión y alerta de clase empresarial y de los controles que un servicio completamente administrado puede ofrecer.
  
Con la Factoría de datos de Azure, los servicios de almacenamiento y procesamiento de datos se recopilan en un contenedor de centros que facilita y optimiza las actividades de cálculo y almacenamiento, permite la administración unificada del consumo de los recursos y proporciona servicios para el movimiento de los datos cuando es necesario.

###Escenario 2: Operacionalización de la producción de información

Los escenarios de operacionalización son el siguiente paso lógico después de los escenarios de orígenes de datos. Una vez que los datos están presentes en un centro, puede crear y operacionalizar canalizaciones de datos para producir de forma confiable datos transformados según una programación sostenible y controlada para suministrar entornos de producción con datos de confianza.  La transformación de datos en la Factoría de datos de Azure tiene lugar a través de un procesamiento Hive, Pig y C# personalizado que se ejecuta en Hadoop (HDInsight de Azure).  Estas transformaciones pueden utilizarse para limpiar datos, enmascarar campos de datos críticos y realizar otras operaciones en los datos en una gran variedad de formas complejas.  Por lo general, la operacionalización se consigue con infraestructuras y servicios personlizados complejos y difíciles de mantener, y plantea una serie de desafíos para la implementación, la administración, el escalado, la solución de problemas y el control de versiones de dicha solución.
  
Con la factoría de datos como un servicio completamente administrado, los usuarios pueden operacionalizar estas canalizaciones definiéndolas con programaciones periódicas puntuales o complejas, y la orquestación se controla directamente mediante el servicio Factoría de datos.  Con los centros, la administración de clúster de todos los datos y el procesamiento dentro de un centro se controlan en nombre del usuario, por lo que los usuarios pueden centrarse en el análisis de transformación en lugar de en la administración de la infraestructura.  La factoría de datos de Azure elimina los desafíos de trabajar con servicios personalizados frágiles y permite a las empresas producir información de confianza de forma confiable y reproducible.


###Escenario 3:  Integración de la producción de información con la detección de datos
Los enfoques y tecnologías tradicionales de BI si bien proporcionan un "origen autorizado de la verdad", casi siempre tienen efectos secundarios graves: un trabajo pendiente constante de solicitudes debido a un proceso de solicitud de cambio controlado cuidadosamente.  Para adaptarse rápidamente a las cuestiones empresariales cambiantes, las empresas precisan de una gran flexibilidad para conectar sus sistemas de producción de la información con sus sistemas de consumo de la información.  La factoría de datos de Azure ayuda a abordar el desafío de conectar estos sistemas con canalizaciones de datos optimizadas para la producción de información y el desafío del consumo de la información al permitir que estén disponibles datos de confianza actualizados en formatos de consumo fácil.
  
La Factoría de datos de Azure es compatible con las siguientes capacidades para permitir el consumo sencillo de los datos producidos:

- Movimiento fácil (de forma puntual o programada) de los activos de datos producidos a mercados de datos relacionales para el consumo mediante el uso de herramientas de BI existentes (Excel, Tableau, etc.).
- Consumo de los activos de datos producidos por una factoría de datos usando directamente Power Query en Excel.

Vea los temas siguientes sobre el consumo de datos mediante Power Query: 

- [Power Query: Conexión al almacenamiento de tablas de Microsoft Azure] [Power-Query-Azure-Table]
- [Power Query: Conexión al almacenamiento de blobs de Microsoft Azure] [Power-Query-Azure-Blob]
- [Power Query: Conexión a Base de datos SQL de Microsoft Azure] [Power-Query-Azure-SQL]
- [Power Query: Conexión a Microsoft SQL Server local][Power-Query-OnPrem-SQL] 

## Modelo de aplicación
El siguiente diagrama ilustra el modelo de aplicaciones que admite la factoría de datos de Azure.

![Application Model][image-data-factory-application-model]

Hay tres etapas de producción de información en una factoría de datos de Azure:


- **Conectar y recopilar**. En esta etapa, se importan datos de diversos orígenes de datos en los centros de datos.
- **Transformar y enriquecer**. En esta etapa, se procesan los datos recopilados.
- **Publicar**. En esta etapa, los datos se publican para su consumo por parte de herramientas de BI, herramientas de análisis y otras aplicaciones.

Las factorías de datos permiten a los desarrolladores crear **canalizaciones** que son grupos de actividades de movimiento y/o procesamiento de datos **que** aceptan uno o varios **conjuntos de datos** de entrada y producen uno o varios conjuntos de datos de salida.  Las canalizaciones se pueden ejecutar una vez o en un intervalo flexible de programaciones (cada hora, diariamente, semanalmente, etc.).  Un **conjunto de datos** es una vista de datos con nombre. Los datos que se describen pueden variar desde bytes simples, pasando por datos semiestructurados, como archivos CSV, hasta **tablas** o **modelos**.

**Las canalizaciones** que se componen de actividades de movimiento de datos **** (por ejemplo: actividad de copia) se usan con frecuencia para importar/exportar datos de todos los **orígenes de datos** (bases de datos, archivos, servicios SaaS, etc.) que usa la organización en un **centro de datos**.
    
Una vez que los datos están en un **centro**, las **canalizaciones** hospedadas por los servicios de proceso del centro se usan para transformar los datos de una manera adecuada para su consumo (por parte de herramientas BI, aplicaciones, clientes, etc.).  
  
Por último, las **canalizaciones** pueden encadenarse (como se muestra en el diagrama) de forma que los **conjuntos de datos** de salida de una son los de entrada de otra.  Esto permite que los flujos de datos complejos se factoricen en **canalizaciones** que se ejecutan dentro de un centro de datos o que abarcan varios centros.  El uso de las **canalizaciones** de este modo ofrece a las organizaciones los bloques de creación para componer los mejores servicios locales, en la nube y de Software como servicio (SaaS), todo ello a través del prisma de una sola factoría de datos administrada fácilmente. 


##Terminología
Esta sección presenta la terminología relacionada con la Factoría de datos de Azure.

### Factoría de datos

**La Factoría de datos de Azure** es un servicio totalmente administrado para la composición de servicios de almacenamiento, procesamiento y movimiento de datos en canalizaciones de producción de datos optimizadas, escalables y confiables. 

El servicio Factoría de datos consume, produce, administra y publica **conjuntos de datos**. Una suscripción de Azure puede tener una o más instancias de Factoría de datos de Azure. Una factoría de datos de Azure puede estar vinculada a uno o varios servicios de almacenamiento o proceso (denominados servicios vinculados).
 
Una factoría de datos de Azure puede tener una o varias canalizaciones que procesan datos en almacenamiento vinculados mediante el uso de servicios de proceso vinculados como HDInsight de Azure. Una factoría de datos de Azure no contiene los datos dentro de ella, sino que se encuentran almacenados fuera, en el sistema de almacenamiento existente de un usuario.


### Servicios vinculados
Un servicio vinculado es un servicio que está vinculado a una factoría de datos de Azure. Un servicio vinculado puede ser uno de los siguientes:


- Un almacén de datos, como el almacenamiento de Azure, una base de datos SQL de Azure y una base de datos SQL Server local
- Un servicio de proceso como HDInsight de Azure.

Un almacén de datos es un contenedor de datos o conjuntos de datos y HDInsight de Azure es el único servicio de proceso que se admite en este momento. Primero debe crear un servicio vinculado que apunte a un almacén de datos y luego definir conjuntos de datos para representar los datos de ese almacén de datos. 

### Conjunto de datos
Una vista de datos con nombre. Los datos que se describen pueden variar desde bytes simples, pasando por datos semiestructurados, como archivos CSV, hasta tablas relacionales o incluso modelos. Una **tabla** es un conjunto de datos que tiene un esquema y es rectangular.

###Canalización
Una **canalización** en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. Por ejemplo, una actividad de copia copia los datos desde un almacenamiento de origen hasta un almacenamiento de destino y las actividades de Hive y Pig utilizan un clúster de HDInsight de Azure para procesar datos mediante consultas de Hive o scripts de Pig.

Los pasos típicos para crear una instancia de la Factoría de datos de Azure son:

1. Crear una factoría de datos
2. Crear un servicio vinculado para cada almacén de datos o servicio de proceso
3. Crear conjuntos de datos de entrada y salida
4. Crear una canalización 

###Actividad
Un paso del procesamiento de datos en una canalización que toma uno o más conjuntos de datos de entrada y produce uno o más conjuntos de datos de salida.  Las actividades se ejecutan en un entorno de ejecución (por ejemplo: clúster de HDInsight de Azure) y leen o escriben datos en un almacén de datos asociado con la instancia de la Factoría de datos de Azure.
 
###Centro de datos
Un centro de datos de Azure es un contenedor de los servicios de almacenamiento y proceso de datos. Por ejemplo, un clúster de Hadoop con HDFS como almacenamiento y Hive o Pig como proceso (procesamiento) es un centro de datos. De forma similar, un almacenamiento de datos empresariales (EDW) se puede modelar como un centro de datos (base de datos como almacenamiento, procedimientos almacenados o herramienta ETL como servicios de proceso).  Las canalizaciones utilizan almacenes de datos y se ejecutan en recursos de proceso en un centro de datos. En este momento solo se admite el centro de HDInsight.

El centro de datos permite la división de una factoría de datos en agrupaciones específicas lógicas o de dominio, como el "centro de Azure del oeste de EE. UU." que administra todos los servicios vinculados (almacenes de datos y proceso) y las canalizaciones centradas en el centro de datos del oeste de EE. UU., o el "centro de EDW de ventas" que administra todos los servicios vinculados y las canalizaciones relacionadas con el llenado y el procesamiento de datos para el almacén de datos empresariales de ventas.

Una característica importante de los centros es que una canalización se ejecuta en un único centro. Esto significa que al definir una canalización, todos los servicios vinculados a los que hacen referencia las tablas o las actividades dentro de esa canalización deben tener el mismo nombre de centro que la propia canalización. Si no se especifica la propiedad HubName para un servicio vinculado, el servicio vinculado se coloca en el centro "predeterminado".

###Segmento
Una tabla de una factoría de datos de Azure se compone de segmentos sobre el eje de tiempo. El ancho de un segmento se determina por la programación: cada hora o diariamente. Cuando la programación es "cada hora", se produce un segmento cada hora con la hora de inicio y la hora de finalización de una canalización y así sucesivamente.  

Los segmentos ofrecen a los profesionales de TI la posibilidad de trabajar con un subconjunto de datos generales en una ventana de tiempo específica (por ejemplo: el segmento que se produce para la duración (hora): de 1:00 p.m. a 2:00 p.m.). También puede ver todos los segmentos de datos de nivel inferior durante un tiempo determinado interno y volver a ejecutar un segmento en caso de error.

La ejecución es una unidad de procesamiento de un segmento. Podría haber una o varias ejecuciones de un segmento en caso de reintentos o si vuelve a ejecutar el segmento en caso de error. Un segmento se identifica mediante su hora de inicio. 

###Data Management Gateway
Microsoft Data Management Gateway es software que conecta orígenes de datos locales a servicios en la nube para su consumo. Debe tener al menos una puerta de enlace instalada en su entorno corporativo y registrarla con el portal de la Factoría de datos de Azure antes de agregar orígenes de datos locales como servicios vinculados.


##Pasos siguientes
[Introducción a la Factoría de datos][datafactory-getstarted]. Este artículo ofrece un completo tutorial que le muestra cómo crear una factoría de datos de Azure de ejemplo que copia datos desde un blob de Azure hasta una base de datos SQL de Azure.

[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx


[datafactory-getstarted]: ../data-factory-get-started/

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-introduction-secenario1-source-datahub]:./media/data-factory-introduction/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]:./media/data-factory-introduction/Scenario2-OperationalizeInformationProduction.png

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png



