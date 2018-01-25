---
title: Entornos de proceso compatibles con Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre los entornos de proceso que puede usar en las canalizaciones de Azure Data Factory (como Azure HDInsight) para transformar o procesar datos."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: f242a8a15334818d83651cf0af55e8ec39bce212
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Entornos de proceso compatibles con Azure Data Factory
En este artículo se explican distintos entornos de procesos que se pueden usar para procesar o transformar datos. También se proporcionan detalles acerca de las distintas configuraciones (a petición frente traiga su propia) admitidas por la Factoría de datos al configurar servicios vinculados que vinculan estos entornos de procesos a una Factoría de datos de Azure.

En la tabla siguiente se proporciona una lista de entornos de proceso compatibles con Data Factory y las actividades que se pueden ejecutar en ellos. 

| Entorno de procesos                      | actividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Clúster de HDInsight a petición](#azure-hdinsight-on-demand-linked-service) o [clúster HDInsight propio](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service) | [Personalizada](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Actividades de Machine Learning: ejecución de Batch y recurso de actualización](transform-data-using-machine-learning.md) |
| [Análisis con Azure Data Lake](#azure-data-lake-analytics-linked-service) | [U-SQL de análisis con Data Lake](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Procedimiento almacenado](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>Entorno de procesos a petición
En este tipo de configuración, el entorno de procesos está totalmente administrado por el servicio Azure Data Factory. El servicio Factoría de datos lo crea automáticamente antes de que se envíe un trabajo para procesar los datos y que se quite cuando finalice el trabajo. Puede crear un servicio vinculado para el entorno de procesos a petición, configurarlo y controlar la configuración granular para la ejecución del trabajo, la administración del clúster y las acciones de arranque.

> [!NOTE]
> La configuración a petición solo se admite actualmente para los clústeres de HDInsight de Azure.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Servicio vinculado a petición de HDInsight de Azure
El servicio Azure Data Factory puede crear automáticamente un clúster de HDInsight a petición para procesar los datos. El clúster se crea en la misma región que la cuenta de almacenamiento (propiedad linkedServiceName en JSON) asociada al clúster. La cuenta de almacenamiento debe ser una cuenta de almacenamiento estándar de uso general de Azure. 

Tenga en cuenta los siguientes puntos **importantes** acerca del servicio vinculado de HDInsight a petición:

* El clúster de HDInsight a petición se crea bajo la suscripción de Azure. Es capaz de ver el clúster en Azure Portal cuando el clúster está activo y en ejecución. 
* Los registros de trabajos que se ejecutan en un clúster de HDInsight a petición se copian en la cuenta de almacenamiento asociada al clúster de HDInsight. clusterUserName, clusterPassword, clusterSshUserName y clusterSshPassword que aparecen en la definición del servicio vinculado se utilizan para iniciar sesión en el clúster para la solución de problemas detallada durante el ciclo de vida del clúster. 
* Se le cobrará solo por el tiempo en el que el clúster de HDInsight esté en ejecución y realizando trabajos.

> [!IMPORTANT]
> El aprovisionamiento bajo demanda de un clúster de Azure HDInsight suele tardar **20 minutos** o más.
>
> 

### <a name="example"></a>Ejemplo
En el siguiente JSON se define un servicio vinculado de HDInsight a petición basado en Linux. El servicio Data Factory crea automáticamente un clúster de HDInsight **basado en Linux** para procesar la actividad requerida. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> El clúster de HDInsight crea un **contenedor predeterminado** en el almacenamiento de blobs que especificó en JSON (**linkedServiceName**). HDInsight no elimina este contenedor cuando se elimina el clúster. Este comportamiento es así por diseño. Con el servicio vinculado de HDInsight a petición se crea un clúster de HDInsight cada vez tenga que procesarse un segmento, a menos que haya un clúster existente activo (**timeToLive**), que se elimina cuando finaliza el procesamiento. 
>
> A medida que hay más actividad, verá numerosos contenedores en su Azure Blob Storage. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. Los nombres de estos contenedores siguen un patrón: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Use herramientas como el [Explorador de Microsoft Storage](http://storageexplorer.com/) para eliminar contenedores de Azure Blob Storage.
>
> 

### <a name="properties"></a>Properties (Propiedades)
| Propiedad                     | DESCRIPCIÓN                              | Obligatorio |
| ---------------------------- | ---------------------------------------- | -------- |
| Tipo                         | La propiedad type se debe establecer en **HDInsightOnDemand**. | Sí      |
| clusterSize                  | Número de nodos de datos o trabajo del clúster El clúster de HDInsight se crea con dos nodos principales junto con el número de nodos de trabajo que haya especificado para esta propiedad. Los nodos son de tamaño Standard_D3 con 4 núcleos, por lo que un clúster de nodos de 4 trabajos necesitará 24 núcleos (4\*4 = 16 para nodos de trabajo, más 2\*4 = 8 para nodos principales). Consulte [Configuración de clústeres en HDInsight con Hadoop, Spark, Kafka, etc.](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para detalles. | Sí      |
| linkedServiceName            | El servicio vinculado de Azure Storage que usará el clúster a petición para almacenar y procesar datos. El clúster de HDInsight se crea en la misma región que esta cuenta de Azure Storage. Azure HDInsight tiene limitaciones en el número total de núcleos que se pueden utilizar en cada región de Azure que admite. Asegúrese de que dispone de suficientes cuotas de núcleo en esa región de Azure para cumplir la propiedad clusterSize necesaria. Para detalles, consulte [Configuración de clústeres en HDInsight con Hadoop, Spark, Kafka, etc](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).<p>Actualmente, no se puede crear un clúster de HDInsight a petición que utilice una instancia de Azure Data Lake Store como almacenamiento. Si desea almacenar los datos de resultados del procesamiento de HDInsight en una instancia de Azure Data Lake Store, utilice una actividad de copia para copiar los datos desde Azure Blob Storage a Azure Data Lake Store. </p> | Sí      |
| clusterResourceGroup         | El clúster de HDInsight se crea en este grupo de recursos. | Sí      |
| timeToLive                   | El tiempo de inactividad permitido para el clúster de HDInsight a petición. Especifica cuánto tiempo permanece activo el clúster de HDInsight a petición después de la finalización de una ejecución de actividad si no hay ningún otro trabajo activo en el clúster. El valor mínimo permitido es 5 minutos (00: 05:00).<br/><br/>Por ejemplo, si una ejecución de actividad tarda 6 minutos y timetolive está establecido en 5 minutos, el clúster permanece activo durante 5 minutos después de los 6 minutos de procesamiento de la ejecución de actividad. Si se ejecuta otra actividad con un margen de 6 minutos, la procesa el mismo clúster.<br/><br/>Crear un clúster de HDInsight a petición es una operación costosa (podría tardar un poco); use esta configuración si es necesario para mejorar el rendimiento de una factoría de datos mediante la reutilización de un clúster de HDInsight a petición.<br/><br/>Si establece el valor de timetolive en 0, el clúster se elimina en cuanto se completa la ejecución de la actividad. Sin embargo, si establece un valor alto, el clúster puede permanecer inactivo para que inicie sesión con el fin de solucionar algunos problemas, pero se podrían producir altos costos. Por lo tanto, es importante que establezca el valor adecuado en función de sus necesidades.<br/><br/>Varias canalizaciones pueden compartir la instancia del clúster de HDInsight a petición si el valor de la propiedad timetolive está correctamente configurado. | Sí      |
| clusterType                  | Tipo de clúster de HDInsight que se va a crear. Los valores permitidos son "hadoop" y "spark". Si no se especifica, el valor predeterminado es hadoop. | Sin        |
| version                      | Versión del clúster de HDInsight. Si no se especifica, se usa la versión predeterminada definida de HDInsight. | Sin        |
| hostSubscriptionId           | Identificador de suscripción de Azure usado para crear el clúster de HDInsight. Si no se especifica, utiliza el identificador de suscripción de su contexto de inicio de sesión de Azure. | Sin        |
| clusterNamePrefix           | Prefijo del nombre del clúster de HDI, una marca de tiempo se agregará automáticamente al final del nombre del clúster.| Sin        |
| sparkVersion                 | Versión de spark si el tipo de clúster es "Spark" | Sin        |
| additionalLinkedServiceNames | Especifica cuentas de almacenamiento adicionales para el servicio vinculado de HDInsight, de forma que el servicio Factoría de datos pueda registrarlas en su nombre. Estas cuentas de almacenamiento deben estar en la misma región que el clúster de HDInsight, que se crea en la misma región que la cuenta de almacenamiento especificada por linkedServiceName. | Sin        |
| osType                       | Tipo de sistema operativo. Los valores permitidos son: Linux y Windows (solo para HDInsight 3.3). El valor predeterminado es Linux. | Sin        |
| hcatalogLinkedServiceName    | Nombre del servicio vinculado de SQL de Azure que apunta a la base de datos de HCatalog. El clúster de HDInsight a petición se creará usando la base de datos SQL de Azure como metaalmacén. | Sin        |
| connectVia                   | Integration Runtime que se utilizará para enviar las actividades a este servicio vinculado de HDInsight. Para el servicio vinculado de HDInsight a petición, solo admite Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. | Sin        |
| clusterUserName                   | Nombre de usuario de acceso al clúster. | Sin        |
| clusterPassword                   | Contraseña de tipo cadena segura de acceso al clúster. | Sin        |
| clusterSshUserName         | Nombre de usuario para que SSH se conecte de forma remota al nodo del clúster (para Linux). | Sin        |
| clusterSshPassword         | Contraseña de tipo cadena segura para que SSH se conecte de forma remota los nodos del clúster (para Linux). | Sin        |


> [!IMPORTANT]
> HDInsight es compatible con varias versiones de clústeres de Hadoop que se pueden implementar. Cada versión crea una versión específica de la distribución HortonWorks Data Platform (HDP) y un conjunto de componentes que están incluidos en esa distribución. La lista de versiones admitidas de HDInsight se sigue actualizando para proporcionar las correcciones y componentes de ecosistema más recientes de Hadoop. Asegúrese de que siempre hace referencia a la información más reciente de [Versiones compatibles de HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para asegurarse de que usa una versión compatible de HDInsight. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>Ejemplo JSON de additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Autenticación de entidad de servicio

El servicio vinculado de HDInsight a petición requiere una autenticación de entidad de servicio para crear clústeres de HDInsight en su nombre. Para usar autenticación de entidad de servicio, registre una entidad de aplicación en Azure Active Directory (Azure AD) y concédale el rol **Colaborador** de la suscripción o el grupo de recursos en el que se crea el clúster de HDInsight. Para los pasos detallados, consulte [Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Anote los siguientes valores; los usará para definir el servicio vinculado:

- Identificador de aplicación
- Clave de la aplicación 
- Id. de inquilino

Para usar la autenticación de la entidad de servicio, especifique las siguientes propiedades:

| Propiedad                | DESCRIPCIÓN                              | Obligatorio |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique el id. de cliente de la aplicación.     | Sí      |
| **servicePrincipalKey** | Especifique la clave de la aplicación.           | Sí      |
| **tenant**              | Especifique la información del inquilino (nombre de dominio o identificador de inquilino) en el que reside la aplicación. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. | Sí      |

### <a name="advanced-properties"></a>Propiedades avanzadas

También puede especificar las siguientes propiedades para la configuración granular del clúster de HDInsight a petición.

| Propiedad               | DESCRIPCIÓN                              | Obligatorio |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica los parámetros de configuración Core (como en core-site.xml) para crear el clúster de HDInsight. | Sin        |
| hBaseConfiguration     | Especifica los parámetros de configuración HBase (como en hbase-site.xml) para el clúster de HDInsight. | Sin        |
| hdfsConfiguration      | Especifica los parámetros de configuración HDFS (hdfs-site.xml) para el clúster de HDInsight. | Sin        |
| hiveConfiguration      | Especifica los parámetros de configuración Hive (hive-site.xml) para el clúster de HDInsight. | Sin        |
| mapReduceConfiguration | Especifica los parámetros de configuración MapReduce (mapred-site.xml) para el clúster de HDInsight. | Sin        |
| oozieConfiguration     | Especifica los parámetros de configuración Oozie (oozie-site.xml) para el clúster de HDInsight. | Sin        |
| stormConfiguration     | Especifica los parámetros de configuración Storm (storm-site.xml) para el clúster de HDInsight. | Sin        |
| yarnConfiguration      | Especifica los parámetros de configuración Yarn (yarn-site.xml) para el clúster de HDInsight. | Sin        |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Ejemplo: configuración del clúster de HDInsight a petición con propiedades avanzadas

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Tamaño de nodo
Puede especificar los tamaños de los nodos principal, de datos y de zookeeper con las siguientes propiedades: 

| Propiedad          | DESCRIPCIÓN                              | Obligatorio |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Especifica el tamaño del nodo principal. El valor predeterminado es: Standard_D3 Consulte la sección **Especificación de tamaños de nodos** para más información. | Sin        |
| dataNodeSize      | Especifica el tamaño del nodo de datos. El valor predeterminado es: Standard_D3 | Sin        |
| zookeeperNodeSize | Especifica el tamaño del nodo de Zoo Keeper. El valor predeterminado es: Standard_D3 | Sin        |

#### <a name="specifying-node-sizes"></a>Especificación de tamaños de nodo
Consulte el artículo [Tamaños de máquinas virtuales](../virtual-machines/linux/sizes.md) para conocer los valores de cadena que debe especificar para las propiedades mencionadas anteriormente. Los valores deben ser conformes a los **CMDLET y API** a los que se hace referencia en el artículo. Como puede ver en el artículo, el nodo de datos de tamaño grande (valor predeterminado) tiene 7 GB de memoria, que es posible que no sea lo suficientemente bueno para su escenario. 

Si quiere crear nodos de trabajo y principales de tamaño D4, especifique **Standard_D4** para el valor de las propiedades headNodeSize y dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Si especifica un valor incorrecto para estas propiedades, puede recibir el siguiente **error:** Error al crear el clúster. Excepción: No se puede completar la operación de creación del clúster. Error en la operación con el código '400'. El clúster generó el estado: 'Error'. Mensaje: 'PreClusterCreationValidationFailure'. Si recibe este error, asegúrese de que está usando el nombre de **CMDLET y API** de la tabla del artículo [Tamaños de las máquinas virtuales](../virtual-machines/linux/sizes.md).        

## <a name="bring-your-own-compute-environment"></a>Traer su propio entorno de procesos
En este tipo de configuración, los usuarios pueden registrar un entorno de procesos existente como un servicio vinculado en la Factoría de datos. El usuario administra el entorno de procesos y el servicio Factoría de datos lo usa para ejecutar las actividades.

Este tipo de configuración se admite para los entornos de procesos siguientes:

* HDInsight de Azure
* Azure Batch
* Azure Machine Learning
* Análisis con Azure Data Lake
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Servicio vinculado de HDInsight de Azure
Puede crear un servicio vinculado de HDInsight de Azure para registrar su propio clúster de HDInsight con la Factoría de datos.

### <a name="example"></a>Ejemplo

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Properties (Propiedades)
| Propiedad          | DESCRIPCIÓN                              | Obligatorio |
| ----------------- | ---------------------------------------- | -------- |
| Tipo              | La propiedad type se debe establecer en **HDInsight**. | Sí      |
| clusterUri        | El URI del clúster de HDInsight.        | Sí      |
| Nombre de usuario          | Especifique el nombre de usuario que se usará para conectarse a un clúster de HDInsight existente. | Sí      |
| contraseña          | Especifique la contraseña para la cuenta de usuario.   | Sí      |
| linkedServiceName | Nombre del servicio vinculado para Azure Storage que hace referencia al almacenamiento Azure Blob Storage que usa el clúster de HDInsight. <p>Actualmente, no se puede especificar un servicio vinculado de Azure Data Lake Store para esta propiedad. Puede acceder a los datos de Azure Data Lake Store desde scripts de Hive o Pig si el clúster de HDInsight tiene acceso a Data Lake Store. </p> | Sí      |
| connectVia        | Integration Runtime que se utilizará para enviar las actividades a este servicio vinculado. Puede usar Azure Integration Runtime o Integration Runtime autohospedado. Si no se especifica, se usará Azure Integration Runtime. | Sin        |

> [!IMPORTANT]
> HDInsight es compatible con varias versiones de clústeres de Hadoop que se pueden implementar. Cada versión crea una versión específica de la distribución HortonWorks Data Platform (HDP) y un conjunto de componentes que están incluidos en esa distribución. La lista de versiones admitidas de HDInsight se sigue actualizando para proporcionar las correcciones y componentes de ecosistema más recientes de Hadoop. Asegúrese de que siempre hace referencia a la información más reciente de [Versiones compatibles de HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para asegurarse de que usa una versión compatible de HDInsight. 
>

## <a name="azure-batch-linked-service"></a>Servicio vinculado de Azure Batch

Puede crear un servicio vinculado de Azure Batch para registrar un grupo de lotes de máquinas virtuales (VM) en una factoría de datos. Puede ejecutar la actividad personalizada con Azure Batch.

Consulte los temas siguientes si no está familiarizado con el servicio Azure Batch:

* [Aspectos básicos de Azure Batch](../batch/batch-technical-overview.md) para información general del servicio Azure Batch.
* Cmdlet [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) para crear una cuenta de Azure Batch o [Azure Portal](../batch/batch-account-create-portal.md) para crear la cuenta de Azure Batch con Azure Portal. Consulte el tema [Uso de Azure PowerShell para administrar la cuenta de Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obtener instrucciones detalladas sobre cómo usar este cmdlet.
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) para crear un grupo de Lote de Azure.

### <a name="example"></a>Ejemplo

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Properties (Propiedades)
| Propiedad          | DESCRIPCIÓN                              | Obligatorio |
| ----------------- | ---------------------------------------- | -------- |
| Tipo              | La propiedad type se debe establecer en **AzureBatch**. | Sí      |
| accountName       | Nombre de la cuenta de Azure Batch.         | Sí      |
| accessKey         | Clave de acceso de la cuenta de Azure Batch.  | Sí      |
| batchUri          | Dirección URL a la cuenta de Azure Batch, con el formato https://*nombrecuentabatch.región*.batch.azure.com. | Sí      |
| poolName          | Nombre del grupo de máquinas virtuales.    | Sí      |
| linkedServiceName | Nombre del servicio vinculado de Azure Storage asociado a este servicio vinculado de Azure Batch. Este servicio vinculado se usa para los archivos de almacenamiento provisional necesarios para ejecutar la actividad. | Sí      |
| connectVia        | Integration Runtime que se utilizará para enviar las actividades a este servicio vinculado. Puede usar Azure Integration Runtime o Integration Runtime autohospedado. Si no se especifica, se usará Azure Integration Runtime. | Sin        |

## <a name="azure-machine-learning-linked-service"></a>Servicio vinculado de Azure Machine Learning
Un servicio vinculado de Azure Machine Learning se crea para registrar un punto de conexión de puntuación por lotes de Machine Learning en una factoría de datos.

### <a name="example"></a>Ejemplo

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Properties (Propiedades)
| Propiedad               | DESCRIPCIÓN                              | Obligatorio                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| type                   | La propiedad type se debe establecer en: **AzureML**. | Sí                                      |
| mlEndpoint             | La dirección URL de puntuación por lotes.                   | Sí                                      |
| apiKey                 | La API del modelo de área de trabajo publicado.     | Sí                                      |
| updateResourceEndpoint | Dirección URL de recursos de actualización para un punto de conexión de servicio web de Azure ML utilizado para actualizar el servicio web de predicción con el archivo de modelo entrenado. | Sin                                        |
| servicePrincipalId     | Especifique el id. de cliente de la aplicación.     | Obligatorio si se especifica updateResourceEndpoint |
| servicePrincipalKey    | Especifique la clave de la aplicación.           | Obligatorio si se especifica updateResourceEndpoint |
| tenant                 | Especifique la información del inquilino (nombre de dominio o identificador de inquilino) en el que reside la aplicación. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. | Obligatorio si se especifica updateResourceEndpoint |
| connectVia             | Integration Runtime que se utilizará para enviar las actividades a este servicio vinculado. Puede usar Azure Integration Runtime o Integration Runtime autohospedado. Si no se especifica, se usará Azure Integration Runtime. | Sin                                        |

## <a name="azure-data-lake-analytics-linked-service"></a>Servicio vinculado de Azure Data Lake Analytics
Cree un servicio vinculado de **Azure Data Lake Analytics** para vincular un servicio de proceso de Azure Data Lake Analytics a una instancia de Azure Data Factory. La actividad de U-SQL de Data Lake Analytics de la canalización hace referencia a este servicio vinculado. 

### <a name="example"></a>Ejemplo

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Properties (Propiedades)

| Propiedad             | DESCRIPCIÓN                              | Obligatorio                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| Tipo                 | La propiedad type se debe establecer en: **AzureDataLakeAnalytics**. | Sí                                      |
| accountName          | Nombre de la cuenta de Análisis de Azure Data Lake  | Sí                                      |
| dataLakeAnalyticsUri | Identificador URI de Análisis de Azure Data Lake.           | Sin                                        |
| subscriptionId       | Identificador de suscripción de Azure                    | No (si no se especifica, se usa la suscripción de Data Factory). |
| resourceGroupName    | Nombre del grupo de recursos de Azure.                | No (si no se especifica, se usa el grupo de recursos de la factoría de datos). |
| servicePrincipalId   | Especifique el id. de cliente de la aplicación.     | Sí                                      |
| servicePrincipalKey  | Especifique la clave de la aplicación.           | Sí                                      |
| tenant               | Especifique la información del inquilino (nombre de dominio o identificador de inquilino) en el que reside la aplicación. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. | Sí                                      |
| connectVia           | Integration Runtime que se utilizará para enviar las actividades a este servicio vinculado. Puede usar Azure Integration Runtime o Integration Runtime autohospedado. Si no se especifica, se usará Azure Integration Runtime. | Sin                                        |



## <a name="azure-sql-database-linked-service"></a>Servicio vinculado a Azure SQL Database
Cree un servicio vinculado de Azure SQL y úselo con la [actividad de procedimiento almacenado](transform-data-using-stored-procedure.md) para invocar un procedimiento almacenado desde una canalización de Factoría de datos. Vea el artículo [Conector SQL de Azure](connector-azure-sql-database.md#linked-service-properties) para más información sobre este servicio vinculado.

## <a name="azure-sql-data-warehouse-linked-service"></a>Servicio vinculado de Azure SQL Data Warehouse
Cree un servicio vinculado de SQL Data Warehouse y úselo con la [actividad de procedimiento almacenado](transform-data-using-stored-procedure.md) para invocar un procedimiento almacenado desde una canalización de Data Factory. Consulte el artículo sobre el [conector de Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#linked-service-properties) para más información acerca de este servicio vinculado.

## <a name="sql-server-linked-service"></a>Servicio vinculado de SQL Server
Cree un servicio vinculado de SQL Server y úselo con la [actividad de procedimiento almacenado](transform-data-using-stored-procedure.md) para invocar un procedimiento almacenado desde una canalización de Data Factory. Consulte el artículo sobre el [conector de SQL Server](connector-sql-server.md#linked-service-properties) para más información acerca de este servicio vinculado.

## <a name="azure-data-factory---naming-rules"></a>Azure Data Factory: reglas de nomenclatura
La tabla siguiente proporciona las reglas de nomenclatura para los artefactos de Factoría de datos.

| NOMBRE                             | Exclusividad del nombre                          | Comprobaciones de validación                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Data Factory                     | Único en Microsoft Azure. Los nombres no distinguen mayúsculas de minúsculas, es decir, `MyDF` y `mydf` hacen referencia a la misma factoría de datos. | <ul><li>Cada factoría de datos está asociada exactamente a una suscripción de Azure.</li><li>Los nombres de objeto deben comenzar por una letra o un número, y pueden contener solo letras, números y el carácter de guión (-).</li><li>Los caracteres de guión (-) debe estar inmediatamente precedidos y seguidos por una letra o un número. No se permiten guiones consecutivos en los nombres de contenedor.</li><li>El nombre puede tener entre 3 y 63 caracteres.</li></ul> |
| Servicios, tablas o canalizaciones vinculadas | Único en una factoría de datos. Los nombres no distinguen mayúsculas de minúsculas. | <ul><li>Número máximo de caracteres incluido en un nombre de tabla: 260.</li><li>Los nombres de objeto deben empezar con una letra, un número o un carácter de subrayado (_).</li><li>No se permiten los caracteres siguientes: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |
| Grupo de recursos                   | Único en Microsoft Azure. Los nombres no distinguen mayúsculas de minúsculas. | <ul><li>Número máximo de caracteres: 1000.</li><li>El nombre puede contener letras, dígitos y los siguientes caracteres: “-”, “_”, “,” y “.”</li></ul> |

## <a name="next-steps"></a>pasos siguientes
Para una lista de actividades de transformación compatibles con Azure Data Factory, consulte [Transform data](transform-data.md) (Transformación de datos).
