<properties 
	pageTitle="Servicios vinculados de procesos | Factoría de datos de Azure" 
	description="Obtenga información sobre los entornos de procesos que puede usar en las canalizaciones de la Factoría de datos de Azure para transformar y procesar datos." 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Servicios vinculados de procesos

En este artículo se explican distintos entornos de procesos que se pueden usar para procesar o transformar datos. También se proporcionan detalles acerca de las distintas configuraciones (a petición frente traiga su propia) admitidas por la Factoría de datos al configurar servicios vinculados que vinculan estos entornos de procesos a una Factoría de datos de Azure.

## Entorno de procesos a petición

En este tipo de configuración, el entorno de procesos está totalmente administrado por el servicio Factoría de datos de Azure. El servicio Factoría de datos lo crea automáticamente antes de que se envíe un trabajo para procesar los datos y que se quite cuando finalice el trabajo. Puede crear un servicio vinculado para el entorno de procesos a petición, configurarlo y controlar la configuración granular para la ejecución del trabajo, la administración del clúster y las acciones de arranque.

> [AZURE.NOTE]La configuración a petición solo se admite actualmente para los clústeres de HDInsight de Azure.

## Servicio vinculado a petición de HDInsight de Azure

El servicio Factoría de datos de Azure crea el clúster de HDInsight a petición para procesar los datos. El clúster se crea en la misma región que la de la cuenta de almacenamiento (propiedad linkedServiceName en JSON) asociada al clúster.

Tenga en cuenta los siguientes puntos **importantes** acerca del servicio vinculado de HDInsight a petición:

- No verá el clúster de HDInsight a petición creado en su suscripción de Azure; el servicio Factoría de datos de Azure administra el clúster de HDInsight a petición en su nombre.
- Los registros de trabajos que se ejecutan en un clúster de HDInsight a petición se copian en la cuenta de almacenamiento asociada al clúster de HDInsight. Puede tener acceso a estos registros desde el Portal de Azure en la hoja **Detalles de ejecución de la actividad**. Consulte el artículo [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md) para obtener más información. 
- Se le cobrará solo por el tiempo en el que el clúster de HDInsight esté en ejecución y realizando trabajos.

> [AZURE.IMPORTANT]Normalmente se tarda más de **15 minutos** en aprovisionar un clúster de HDInsight a petición de Azure.

### Ejemplo

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 4,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "00:05:00",
	      "version": "3.1",
	      "linkedServiceName": "MyBlobStore"
	      "additionalLinkedServiceNames": [
	        "otherLinkedServiceName1",
	        "otherLinkedServiceName2"
	      ]
	    }
	  }
	}

### Propiedades

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type se debe establecer en **HDInsightOnDemand**. | Sí
clusterSize | El tamaño del clúster a petición. Especifique el número de nodos que desea que haya en este clúster a petición. | Sí 
jobscontainer | El contenedor de blobs que contiene los datos usados por los trabajos de pig/hive/paquete y donde se almacenarán los registros de clúster. | Sí
timetolive | <p>El tiempo de inactividad permitido para el clúster de HDInsight a petición. Especifica cuánto tiempo permanecerá el clúster de HDInsight a petición activo después de la finalización de una ejecución de actividad si no hay ningún otro trabajo activo en el clúster.</p><p>Por ejemplo, si una ejecución de actividad tarda 6 minutos y timetolive está establecido en 5 minutos, el clúster permanece activo durante 5 minutos después de los 6 minutos de procesamiento de la ejecución de actividad. Si se realiza otra ejecución de actividad con un margen de 6 minutos, la procesa el mismo clúster.</p><p>La creación de un clúster de HDInsight a petición es una operación costosa (puede tardar bastante), por lo que se recomienda usar esta opción cuando sea necesario para mejorar el rendimiento de una factoría de datos mediante la reutilización de un clúster de HDInsight a petición.</p><p>Si establece el valor de timetolive en 0, el clúster se elimina en cuanto se procese la ejecución de actividad. Por otra parte, si establece un valor alto, el clúster puede permanecer inactivo innecesariamente, lo que conlleva unos costos elevados. Por lo tanto, es importante que establezca el valor adecuado en función de sus necesidades.</p><p>Varias canalizaciones pueden compartir la misma instancia del clúster de HDInsight a petición si el valor de la propiedad timetolive está correctamente configurado</p> | Sí
versión | Versión del clúster de HDInsight. | No
linkedServiceName | El almacén de blobs que usará el clúster a petición para almacenar y procesar datos. | Sí
additionalLinkedServiceNames | Especifica cuentas de almacenamiento adicionales para el servicio vinculado de HDInsight, de forma que el servicio Factoría de datos pueda registrarlas en su nombre. | No

### Propiedades avanzadas

También puede especificar las siguientes propiedades para la configuración granular del clúster de HDInsight a petición.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
coreConfiguration | Especifica los parámetros de configuración Core (como en core-site.xml) para crear el clúster de HDInsight. | No
hBaseConfiguration | Especifica los parámetros de configuración HBase (como en hbase-site.xml) para el clúster de HDInsight. | No
hdfsConfiguration | Especifica los parámetros de configuración HDFS (hdfs-site.xml) para el clúster de HDInsight. | No
hiveConfiguration | Especifica los parámetros de configuración Hive (hive-site.xml) para el clúster de HDInsight. | No
mapReduceConfiguration | Especifica los parámetros de configuración MapReduce (mapred-site.xml) para el clúster de HDInsight. | No
oozieConfiguration | Especifica los parámetros de configuración Oozie (oozie-site.xml) para el clúster de HDInsight. | No
stormConfiguration | Especifica los parámetros de configuración Storm (storm-site.xml) para el clúster de HDInsight. | No
yarnConfiguration | Especifica los parámetros de configuración Yarn (yarn-site.xml) para el clúster de HDInsight. | No

#### Ejemplo: configuración del clúster de HDInsight a petición con propiedades avanzadas

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "01:30:00",
	      "version": "3.1",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx8000m",
	        "mapreduce.map.java.opts": "-Xmx8000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

## Traer su propio entorno de procesos 

En este tipo de configuración, los usuarios pueden registrar un entorno de procesos existente como un servicio vinculado en la Factoría de datos. El usuario administra el entorno de procesos y el servicio Factoría de datos lo usa para ejecutar las actividades.
 
Este tipo de configuración se admite para los entornos de procesos siguientes:

- HDInsight de Azure
- Azure Batch 
- Aprendizaje automático de Azure.

## Servicio vinculado de HDInsight de Azure

Puede crear un servicio vinculado de HDInsight de Azure para registrar su propio clúster de HDInsight con la Factoría de datos.

### Ejemplo

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "location": "WestUS",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### Propiedades

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type se debe establecer en **HDInsight**. | Sí
clusterUri | El URI del clúster de HDInsight. | Sí
nombre de usuario | Especifique el nombre de usuario que se usará para conectarse a un clúster de HDInsight existente. | Sí
contraseña | Especifique la contraseña para la cuenta de usuario. | Sí
location | Especifique la ubicación del clúster de HDInsight (por ejemplo: WestUS). | Sí
linkedServiceName | Nombre del servicio vinculado para el almacenamiento de blobs usado por este clúster de HDInsight. | Sí

## Servicio vinculado de Lote de Azure

Puede crear un servicio vinculado de Lote de Azure para registrar un grupo de lotes de máquinas virtuales (VM) en una factoría de datos. Puede ejecutar actividades personalizadas .NET con Lote de Azure o HDInsight de Azure.

Consulte los temas siguientes si no está familiarizado con el servicio Lote de Azure:
 

- [Información técnica de Lote de Azure](../batch/batch-technical-overview.md) para obtener información general del servicio Lote de Azure.
- Cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) para crear una cuenta de Lote de Azure (o) [Portal de administración de Azure](../batch/batch-technical-overview.md) para crear la cuenta de Lote de Azure mediante el Portal de administración de Azure. Consulte el tema [Uso de Azure PowerShell para administrar la cuenta de Lote de Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obtener instrucciones detalladas sobre cómo usar este cmdlet.
- Cmdlet [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) para crear un grupo de Lote de Azure.

### Ejemplo

	{
	  "name": "AzureBatchLinkedService",
	  "properties": {
	    "type": "AzureBatch",
	    "typeProperties": {
	      "accountName": "<Azure Batch account name>",
	      "accessKey": "<Azure Batch account key>",
	      "poolName": "<Azure Batch pool name>",
	      "linkedServiceName": "<Specify associated storage linked service reference here>"
	    }
	  }
	}

Agregue "**.<nombre de región>**" al nombre de la cuenta de lote para la propiedad **accountName**. Ejemplo:
	
			"accountName": "mybatchaccount.eastus" 

Otra opción es proporcionar el extremo batchUri tal como se muestra a continuación.

			accountName: "adfteam",
			batchUri: "https://eastus.batch.azure.com",

### Propiedades

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type se debe establecer en **AzureBatch**. | Sí
accountName | Nombre de la cuenta de Lote de Azure. | Sí
accessKey | Clave de acceso de la cuenta de Lote de Azure. | Sí
poolName | Nombre del grupo de máquinas virtuales. | Sí
linkedServiceName | Nombre del servicio vinculado de Almacenamiento de Azure asociado a este servicio vinculado de Lote de Azure. Este servicio vinculado se usa para los archivos de almacenamiento provisional necesarios para ejecutar la actividad y almacenar los registros de ejecución de la actividad. | Sí


## Servicio vinculado de Lote de Azure

Un servicio vinculado de aprendizaje automático de Azure se crea para registrar un extremo de puntuación por lotes de aprendizaje automático en una factoría de datos.

### Ejemplo

	{
	  "name": "AzureMLLinkedService",
	  "properties": {
	    "type": "AzureML",
	    "typeProperties": {
	      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
	      "apiKey": "<apikey>"
	    }
	  }
	}

### Propiedades

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
Tipo | La propiedad type se debe establecer en: **AzureML**. | Sí
mlEndpoint | La dirección URL de puntuación por lotes. | Sí
apiKey | La API del modelo de área de trabajo publicado. | Sí





  



     
 
   

<!---HONumber=August15_HO6-->