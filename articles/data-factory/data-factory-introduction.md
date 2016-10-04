<properties 
	pageTitle="Introducción a Data Factory, un servicio de integración de datos | Microsoft Azure" 
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
	ms.date="09/22/2016" 
	ms.author="spelluru"/>

# Introducción al servicio Data Factory de Azure, un servicio de integración de datos en la nube

## ¿Qué es la factoría de datos de Azure? 
Data Factory es un servicio de integración de datos basado en la nube que orquesta y automatiza el **movimiento** y la **transformación** de datos. Con el servicio Data Factory se pueden crear soluciones de integración de datos que pueden ingerir datos de distintos almacenes de datos, transformar y procesar los datos, y publicar los datos resultantes en los almacenes de datos.

El servicio Data Factory permite crear canalizaciones de datos que mueven y transforman datos y, después, ejecutar los procesos según una programación especificada (cada hora, diariamente, semanalmente, etc.). También proporciona excelentes visualizaciones para mostrar el linaje y las dependencias entre las canalizaciones de datos y para poder supervisar todas las canalizaciones de datos desde una única vista unificada, con el fin de facilitar la identificación de los problemas y la configuración de alertas de supervisión.

![Diagrama: Información general de Data Factory, un servicio de integración de datos](./media/data-factory-introduction/what-is-azure-data-factory.png)**Ilustración 1.** Ingiera datos de varios orígenes, prepárelos, transfórmelos y analícelos, y después, y publique unos datos listos para usar que puedan consumirse.

## Canalizaciones y actividades
En una solución de Data Factory, se crean una o varias **canalizaciones** de datos. Una canalización es una agrupación lógica de actividades. Se utilizan para agrupar actividades en una unidad que realizan conjuntamente una tarea.

Las **actividades** definen las acciones que se van a realizar en los datos. Por ejemplo, puede utilizar una actividad de copia para copiar datos de un almacén de datos a otro. De igual forma, puede usar una actividad de Hive, que ejecuta una consulta de Hive en un clúster de Azure HDInsight para transformar o analizar los datos. Data Factory admite dos tipos de actividades: actividades de movimiento de datos y actividades de transformación de datos.
  
## Actividades de movimiento de datos 
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Para más información, consulte [Actividades de movimiento de datos](data-factory-data-movement-activities.md).

## Actividades de transformación de datos
[AZURE.INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Para más información, consulte [Actividades de transformación de datos](data-factory-data-transformation-activities.md).

Si necesita mover datos a un almacén de datos, o desde este, que no sean compatibles con la actividad de copia, o bien transformar datos con su propia lógica, cree una **actividad de .NET personalizada**. Consulte el artículo [Uso de actividades personalizadas en una canalización de Data Factory de Azure](data-factory-use-custom-activities.md) para obtener más información sobre la creación y el uso de una actividad personalizada.

## Servicios vinculados
Los servicios vinculados definen la información necesaria para que Data Factory se conecte a recursos externos (ejemplos: Azure Storage, SQL Server local, Azure HDInsight). Los servicios vinculados se utilizan con dos fines en Factoría de datos:

- Para representar un **almacén de datos** que incluye, entre otros, una instancia de SQL Server local, una base de datos de Oracle, un recurso compartido de archivos o una cuenta de Azure Blob Storage. Para ver una lista de los almacenes de datos compatibles, consulte la sección [Actividades de movimiento de datos](data-factory-data-movement-activities.md).
- Para representar un **recurso de proceso** que puede hospedar la ejecución de una actividad. Por ejemplo, la actividad HDInsightHive se ejecuta en un clúster de Hadoop para HDInsight. Para ver una lista de los entornos de proceso que se admiten, consulte [Actividades de transformación de datos](data-factory-data-transformation-activities.md).

## Conjuntos de datos 
Los servicios vinculados vinculan almacenes de datos o servicios de proceso a una instancia de Azure Data Factory. Los conjuntos de datos representan estructuras de datos en los almacenes de datos. Por ejemplo, un servicio vinculado de Azure Storage proporciona la información de conexión necesaria para que Data Factory se conecte a una cuenta de almacenamiento de Azure Storage. Un conjunto de datos del blob de Azure especifica el contenedor de blobs y la carpeta de Azure Blob Storage de los que la canalización debe leer los datos. De igual forma, un servicio vinculado de SQL Azure proporciona información de conexión para una instancia de Azure SQL Database y un conjunto de datos de SQL Azure especifica la tabla que contiene los datos.

## Relación entre las entidades de Data Factory
Data Factory tiene algunas entidades clave que funcionan conjuntamente para definir la entrada y salida de datos, los eventos de procesamiento, así como la programación y los recursos necesarios para ejecutar el flujo de datos que quiera.

![Diagrama: Data Factory, un servicio de integración de datos en la nube, conceptos clave](./media/data-factory-introduction/data-integration-service-key-concepts.png) **Ilustración 2.** Relaciones entre conjuntos de datos, actividades, canalizaciones y servicios vinculados

Con los cuatro conceptos básicos de servicios vinculados, conjuntos de datos, actividades y canalizaciones, está listo para comenzar. Puede [crear su primera canalización](data-factory-build-your-first-pipeline.md).

## Regiones admitidas
Actualmente, se pueden crear factorías de datos en las regiones de **oeste de EE. UU.**, **este de EE.UU.** y **Europa del Norte**. Sin embargo, una factoría de datos puede acceder a almacenes de datos y a servicios de proceso en otras regiones de Azure para mover datos entre los almacenes de datos o para procesar datos mediante servicios de proceso.

Data Factory de Azure no almacena ningún dato. Permite crear flujos controlados por datos para organizar el movimiento de los datos entre los [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores) y el procesamiento de datos mediante [servicios de proceso](data-factory-compute-linked-services.md) en otras regiones o en un entorno local. También permite [supervisar y administrar flujos de trabajo](data-factory-monitor-manage-pipelines.md) mediante mecanismos de programación y la interfaz de usuario.

Aunque Azure Data Factory solo está disponible en las regiones de **oeste de EE. UU.**. **este de EE.UU.** y [Europa del Norte](data-factory-data-movement-activities.md#global), el servicio que atiende el movimiento de datos en Data Factory está disponible **globalmente** en varias regiones. Si un almacén de datos se encuentra detrás de un firewall, los datos los moverá a la [puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md), siempre que esté instalada en su entorno de local.

Por ejemplo, supongamos que sus entornos de proceso, tales como clúster de Azure HDInsight y Azure Machine Learning, se ejecutan fuera de la región de Europa Occidental. Puede crear y usar una instancia de Azure Data Factory en Europa del Norte y usarla para programar trabajos en los entornos de proceso en Europa Occidental. Data Factory tarda unos milisegundos en desencadenar el trabajo en su entorno de proceso, pero el tiempo para ejecutar el trabajo en el entorno de proceso no cambia.

En el futuro, pretendemos tener Data Factory de Azure en todas las ubicaciones geográficas admitidas por Azure.
  
## Pasos siguientes
Para aprender a compilar factorías de datos con canalizaciones de datos, siga las instrucciones paso a paso en los siguientes tutoriales.

Tutorial | Description
-------- | -----------
[Creación de una canalización de datos que procese los datos con el clúster de Hadoop](data-factory-build-your-first-pipeline.md) | En este tutorial, va a crear su primera instancia de Azure Data Factory con una canalización de datos que **procesa los datos** ejecutando el script de Hive en un clúster de Azure HDInsight (Hadoop). |
[Creación de una canalización de datos para mover datos entre dos almacenes de datos en la nube](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | En este tutorial, va a crear una factoría de datos con una canalización que **mueve datos** de Blob Storage a SQL Database.
[Creación de una canalización de datos para mover datos entre un almacén de datos local y un almacén de datos en la nube mediante la puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md) | En este tutorial, va a compilar una factoría de datos con una canalización que **mueve los datos** de una base de datos de SQL Server **local** a un blob de Azure. Como parte del tutorial, instalará y configurará la puerta de enlace de administración de datos en su máquina. 

<!---HONumber=AcomDC_0928_2016-->