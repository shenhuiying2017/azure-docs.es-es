---
title: "Creación de una instancia de Azure Data Factory con Python | Microsoft Docs"
description: "Cree una instancia de Azure Data Factory para copiar datos desde una ubicación de Azure Blob Storage a otra de la misma instancia de Blob Storage."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9c6f7c8e052aed2ab5aba2ee54065bf40e81a022
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-data-factory-and-pipeline-using-python"></a>Creación de una factoría de datos y una canalización con Python
Azure Data Factory es un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo basados en datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos. Mediante Azure Data Factory, puede crear y programar flujos de trabajo basados en datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse, para que los consuman las aplicaciones de inteligencia empresarial (BI). 

En esta guía de inicio rápido se describe cómo usar Python para crear una instancia de Azure Data Factory. La canalización de esta factoría de datos copia datos de una carpeta a otra en Azure Blob Storage.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* **Cuenta de Almacenamiento de Azure**. El almacenamiento de blobs se puede usar como almacén de datos de **origen** y **receptor**. Si no tiene una cuenta de almacenamiento de Azure, consulte el artículo [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación. 
* **Cree una aplicación en Azure Active Directory**. Para hacerlo, siga [estas instrucciones](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Tome nota de los valores siguientes; los usará en pasos posteriores: **Id. de aplicación**, **clave de autenticación** e **id. de inquilino**. Siga las instrucciones del mismo artículo para asignar la aplicación al rol "**Colaborador**". 

### <a name="create-and-upload-an-input-file"></a>Crear y cargar un archivo de entrada

1. Inicie el Bloc de notas. Copie el texto siguiente y guárdelo como archivo **input.txt** en el disco.
    
    ```
    John|Doe
    Jane|Doe
    ```
2.  Use herramientas como [Explorador de Azure Storage](http://storageexplorer.com/) para crear el contenedor **adfv2tutorial** y cargar el archivo input.txt en el contenedor. 

## <a name="install-the-python-package"></a>Instalar el paquete de Python
1. Abra un terminal o símbolo del sistema con privilegios de administrador.  
2. Para instalar el paquete de Python para Data Factory, ejecute el siguiente comando:

    ```
    pip install azure-mgmt-datafactory
    ```

    El [SDK de Python para Data Factory](https://github.com/Azure/azure-sdk-for-python) admite Python 2.7, 3.3, 3.4, 3.5 y 3.6.
## <a name="create-a-data-factory-client"></a>Creación de un cliente de factoría de datos

1. Cree un archivo denominado **datafactory.py**. Agregue las siguientes instrucciones para agregar referencias a espacios de nombres.
    
    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from msrestazure.azure_cloud import Cloud, CloudEndpoints, CloudSuffixes
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    import json
    import time    
    ```
2. Agregue las siguientes funciones que imprimen información. 

    ```python
    def print_item(group):
      """Print an Azure object instance."""
      print("\tName: {}".format(group.name))
      print("\tId: {}".format(group.id))
      if hasattr(group, 'location'):
          print("\tLocation: {}".format(group.location))
      if hasattr(group, 'tags'):
          print("\tTags: {}".format(group.tags))
      if hasattr(group, 'properties'):
          print_properties(group.properties)
    
    def print_properties(props):
      """Print a ResourceGroup properties instance."""
      if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
          print("\tProperties:")
          print("\t\tProvisioning State: {}".format(props.provisioning_state))
      print("\n\n")    
    ```
3. Agregue el código siguiente al método **main** que crea una instancia de la clase DataFactoryManagementClient. Este objeto se usa para crear la factoría de datos, el servicio vinculado, los conjuntos de datos y la canalización. También se usa para supervisar los detalles de ejecución de la canalización.

    ```python   
    subscription_id = '<your subscription ID where the factory resides>'
    credentials = ServicePrincipalCredentials(
            client_id='<yourClientId>',
            secret='<YourPassword>',
            tenant='<YourTenandId>'
    )
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}
    rg_name = '<Your Resource Group Name>'
    df_name = '<Your Data Factory Name>'
    ```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

Agregue el código siguiente al método **main** que crea una **factoría de datos**. 

```python
#Create Resource Group
resource_client.resource_groups.create_or_update(rg_name, rg_params)

#Create Data Factory
df_resource = Factory(location='eastus')
df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
print_item(df)
while df.provisioning_state != 'Succeeded':
    df = adf_client.factories.get(rg_name, df_name)
    time.sleep(1)
print_item(adf_client.factories.get(rg_name, df_name))
```

## <a name="create-a-linked-service"></a>Creación de un servicio vinculado

Agregue el código siguiente al método **main** que crea un **servicio vinculado de Azure Storage**.

Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En esta guía de inicio rápido, basta con crear un servicio vinculado de Azure Storage como almacén de origen de copia y receptor. En el ejemplo, se denomina "AzureStorageLinkedService".

```python
#Create Storage Linked Service
ls_name = 'storageLinkedService'

#Replace Storage String with your credentials
storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<replace>;AccountKey=<replace>')

ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
print_item(ls)
```
## <a name="create-datasets"></a>Creación de conjuntos de datos
En esta sección, se crean dos conjuntos de datos: uno para el origen y otro para el receptor.

### <a name="create-a-dataset-for-source-azure-blob"></a>Crear un conjunto de datos para un blob de Azure
Agregue el código siguiente al método main que crea un conjunto de datos de blob de Azure. Para obtener más información sobre las propiedades del conjunto de datos de blob, consulte el artículo [Conector de blob de Azure](connector-azure-blob-storage.md#dataset-properties). 

Se define un conjunto de datos que representa los datos de origen del blob de Azure. Este conjunto de datos de blob hace referencia al servicio vinculado de Azure Storage que creó en el paso anterior.

```python
#Create Dataset Input
ds_name = 'ds_in'
ds_ls = LinkedServiceReference(ls_name)
blob_path= 'adfv2tutorial/'
blob_filename = 'input.txt'
ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Crear un conjunto de datos para un blob de Azure de receptor
Agregue el código siguiente al método main que crea un conjunto de datos de blob de Azure. Para obtener más información sobre las propiedades del conjunto de datos de blob, consulte el artículo [Conector de blob de Azure](connector-azure-blob-storage.md#dataset-properties). 

Se define un conjunto de datos que representa los datos de origen del blob de Azure. Este conjunto de datos de blob hace referencia al servicio vinculado de Azure Storage que creó en el paso anterior.

```python
#Create Dataset Output
dsOut_name = 'ds_out'
output_blobpath = 'output/'
dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Crear una canalización

Agregue el código siguiente al método **main** que crea una **canalización con una actividad de copia**.

```python
#Create 1st activity: Copy Activity
act_name =  'copyBlobtoBlob'
blob_source = BlobSource()
blob_sink = BlobSink()
dsin_ref = DatasetReference(ds_name)
dsOut_ref = DatasetReference(dsOut_name)
copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

#Create Pipeline
p_name =  'copyPipeline'
params_for_pipeline = {}
p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
print_item(p)
```


## <a name="create-a-pipeline-run"></a>Crear una ejecución de canalización

Agregue el código siguiente al método **main** que **desencadena una ejecución de canalización**.

```python
#Create Pipeline Run
print(adf_client.pipelines.create_run(rg_name, df_name, p_name,
    {
    }
))
```

## <a name="run-the-code"></a>Ejecución del código
Compile e inicie la aplicación y, a continuación, compruebe la ejecución de la canalización.

La consola imprime el progreso de la creación de la factoría de datos, el servicio vinculado, los conjuntos de datos, la canalización y la ejecución de canalización. Espere hasta que vea los detalles de ejecución de actividad de copia con el tamaño de los datos leídos/escritos. A continuación, use herramientas como [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para comprobar que los blobs se copian a "outputBlobPath" desde "inputBlobPath", como se especificó en las variables.


## <a name="clean-up-resources"></a>Limpieza de recursos
Para eliminar la factoría de datos mediante programación, agregue las líneas de código siguientes al programa: 

```csharp
adf_client.data_factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Consulte los [tutoriales](tutorial-copy-data-dot-net.md) para obtener información acerca del uso de Data Factory en otros escenarios. 
