<properties 
	pageTitle="¿Qué es Data Factory? Servicio de integración de datos | Microsoft Azure" 
	description="Sepa lo que es Data Factory de Azure: un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos." 
	keywords="integración de datos, integración de datos de nube, qué es data factory de azure"
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
	ms.topic="get-started-article" 
	ms.date="07/12/2016" 
	ms.author="spelluru"/>

# Introducción al servicio Data Factory de Azure, un servicio de integración de datos en la nube

## ¿Qué es la factoría de datos de Azure? 
Factoría de datos es un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos. Del mismo modo que una fábrica que pone equipo en funcionamiento para tomar materias primas y transformarlas en productos terminados, la Factoría de datos organiza los servicios existentes que recopilan datos sin procesar y los transforman en información lista para usar.

La Factoría de datos funciona transversalmente en orígenes de datos locales y en la nube y SaaS para introducir, preparar, transformar, analizar y publicar los datos. Use Factoría de datos para componer servicios en canalizaciones de flujo de datos administrados para transformar los datos con servicios como [HDInsight de Azure (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) y [Lote de Azure](https://azure.microsoft.com/documentation/services/batch/) a fin de satisfacer sus necesidades de informática de Big Data, y con [Aprendizaje automático de Azure](https://azure.microsoft.com/documentation/services/machine-learning/) con objeto de poner operativas sus soluciones de análisis. No se conforme solo con una vista tabular de supervisión y use las visualizaciones ricas en contenido de la Factoría de datos para ver rápidamente el linaje y las dependencias entre las canalizaciones de datos. Supervise todas las canalizaciones de flujo de datos desde una única vista unificada para identificar fácilmente los problemas y configurar alertas de supervisión.

![Diagrama: Información general de Data Factory, un servicio de integración de datos](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Ilustración 1.** Recopile datos de distintos orígenes de datos locales, introdúzcalos y prepárelos, organícelos y analícelos con una serie de transformaciones, y publique datos listos para usar para su consumo.

Puede usar la Factoría de datos cada vez que tenga que recopilar datos de diferentes formas y tamaños, transformarlos y publicarlos para extraer conocimientos profundos: todo en una programación confiable. La Factoría de datos se usa para crear canalizaciones de flujo de datos de alta disponibilidad para muchos escenarios en diferentes sectores para sus necesidades de canalización de análisis. Los distribuidores en línea la usan para generar [recomendaciones de producto](data-factory-product-reco-usecase.md) personalizadas en función del comportamiento de exploración del cliente. Los estudios de juegos la usan para comprender la [eficacia de sus campañas de marketing](data-factory-customer-profiling-usecase.md). Sepa directamente de nuestros clientes cómo y por qué usan la Factoría de datos revisando[casos prácticos de clientes](data-factory-customer-case-studies.md).

> [AZURE.VIDEO azure-data-factory-overview]

## Conceptos clave

Factoría de datos de Azure tiene algunas entidades clave que funcionan conjuntamente para definir la entrada y salida de datos, los eventos de procesamiento, así como la programación y los recursos necesarios para ejecutar el flujo de datos que quiera.

![Diagrama: Data Factory, un servicio de integración de datos en la nube, conceptos clave](./media/data-factory-introduction/data-integration-service-key-concepts.png)

**Ilustración 2.** Relaciones entre conjuntos de datos, actividades, canalizaciones y servicios vinculados


### Actividades
Las actividades definen las acciones que se van a realizar en los datos. Cada actividad tiene cero o más [conjuntos de datos](data-factory-create-datasets.md) como entradas y genera uno o varios conjuntos de datos como salidas. Una actividad es una unidad de orquestación en Factoría de datos de Azure. Por ejemplo, puede usar una [actividad de copia](data-factory-data-movement-activities.md) para organizar la copia de datos de un conjunto de datos a otro. De manera similar, puede usar una [actividad de Hive](data-factory-data-transformation-activities.md) que ejecute una consulta de Hive en un clúster de HDInsight de Azure para transformar o analizar los datos. Factoría de datos de Azure ofrece una amplia gama de actividades de movimiento de datos, análisis y transformación de datos.

### Procesos
Las [canalizaciones](data-factory-create-pipelines.md) son una agrupación lógica de actividades. Se utilizan para agrupar actividades en una unidad que realizan conjuntamente una tarea. Por ejemplo, puede ser necesaria una secuencia de varias actividades de transformación para limpiar datos de archivos de registro. Esta secuencia podría tener una programación compleja y dependencias que hay que organizar y automatizar. Todas estas actividades se podrían agrupar en una sola canalización denominada "CleanLogFiles". Después se podría implementar, programar o eliminar "CleanLogFiles" como una sola unidad en lugar de administrar independientemente cada actividad.

### Conjuntos de datos
Los [conjuntos de datos](data-factory-create-datasets.md) son punteros o referencias con nombre a los datos que se quieran usar como entrada o salida de una actividad. Los conjuntos de datos identifican estructuras de datos en distintos almacenes de datos, que incluyen tablas, archivos, carpetas y documentos.

### Servicios vinculados
Los servicios vinculados definen la información necesaria para que Factoría de datos se conecte a recursos externos. Los servicios vinculados se utilizan con dos fines en Factoría de datos:

- Para representar un almacén de datos que incluye, entre otros, un SQL Server local, Oracle DB, Recurso compartido de archivos o una cuenta de Almacenamiento de blobs de Azure. Como se dijo anteriormente, los conjuntos de datos representan las estructuras de los almacenes de datos conectados a Factoría de datos a través de un servicio vinculado.
- Para representar un recurso de proceso que puede alojar la ejecución de una actividad. Por ejemplo, la actividad "HDInsightHive" se ejecuta en un clúster de Hadoop para HDInsight.

Con los cuatro conceptos sencillos de conjuntos de datos, actividades, canalizaciones y servicios vinculados, está listo para comenzar. Puede [compilar su primera canalización](data-factory-build-your-first-pipeline.md) desde el principio o implementar una muestra lista para usar siguiendo las instrucciones que se ofrecen en nuestro artículo [Ejemplos de la Factoría de datos](data-factory-samples.md).

## Regiones admitidas
Actualmente, se pueden crear factorías de datos en las regiones de **oeste de EE. UU.**, **este de EE.UU.** y **Europa del Norte**. Sin embargo, una factoría de datos puede acceder a almacenes de datos y a servicios de proceso en otras regiones de Azure para mover datos entre los almacenes de datos o para procesar datos mediante servicios de proceso.

Data Factory de Azure no almacena ningún dato. Permite crear flujos controlados por datos para organizar el movimiento de los datos entre los [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores) y el procesamiento de datos mediante [servicios de proceso](data-factory-compute-linked-services.md) en otras regiones o en un entorno local. También permite [supervisar y administrar flujos de trabajo](data-factory-monitor-manage-pipelines.md) mediante mecanismos de programación y la interfaz de usuario.

Tenga en cuenta que aunque Data Factory de Azure solo está disponible en las regiones de **oeste de EE. UU.**. **este de EE.UU.** y [Europa del Norte](data-factory-data-movement-activities.md#global), el servicio que atiende el movimiento de datos en Data Factory está disponible **globalmente** en varias regiones. Si un almacén de datos se encuentra detrás de un firewall, los datos los moverá [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md), siempre que esté instalado en su entorno de local.

Por ejemplo, supongamos que sus entornos de proceso, tales como clúster de Azure HDInsight y Aprendizaje automático de Azure, se ejecutan fuera de la región de Europa occidental. Puede crear y aprovechar una instancia de Data Factory de Azure en Europa del Norte y usarla para programar trabajos en los entornos de proceso en Europa Occidental. El servicio Data Factory tarda unos milisegundos en desencadenar el trabajo en su entorno de proceso, pero el tiempo para ejecutar el trabajo en el entorno de proceso no cambia.

En el futuro, pretendemos tener Data Factory de Azure en todas las ubicaciones geográficas admitidas por Azure.
  
## Pasos siguientes
Siga las instrucciones paso a paso en los siguientes tutoriales para aprender a crear factorías de datos con canalizaciones de datos.

Tutorial | Descripción
-------- | -----------
[Creación de una canalización de datos que procese los datos con el clúster de Hadoop](data-factory-build-your-first-pipeline.md) | En este tutorial, va a crear su primera factoría de datos de Azure con una canalización de datos que **procesa los datos** ejecutando el script de Hive en un clúster de HDInsight de Azure (Hadoop). |
[Creación de una canalización de datos para mover datos entre dos almacenes de datos en la nube](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | En este tutorial, creará una factoría de datos con una canalización que **mueve datos** del Almacenamiento de blobs a Base de datos SQL.
[Creación de una canalización de datos para mover datos entre un almacén de datos local y un almacén de datos en la nube mediante la puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md) | En este tutorial, creará una factoría de datos con una canalización que **mueve los datos** de una base de datos de SQL Server **local** a un blob de Azure. Como parte del tutorial, instalará y configurará la puerta de enlace de administración de datos en su máquina. 

<!---HONumber=AcomDC_0713_2016-->