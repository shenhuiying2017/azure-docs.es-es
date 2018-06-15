---
title: 'Guía de inicio rápido de Azure: Aprendizaje de CNTK con Batch AI mediante Python | Microsoft Docs'
description: Aprenda rápidamente a ejecutar un trabajo de aprendizaje de CNTK con Batch AI mediante el SDK de Python.
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: da5c1181f9c4d311bdeabe837435ae4e0eb3dc1a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31513255"
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Ejecución de un trabajo de aprendizaje de CNTK mediante el SDK de Python de Azure

Esta guía de inicio rápido detalla el uso del SDK de Python de Azure para ejecutar un trabajo de aprendizaje de Microsoft Cognitive Toolkit (CNTK) mediante el servicio de Batch IA.

En este ejemplo se utiliza la base de datos MNIST de imágenes manuscritas para entrenar una red neuronal convolucional (CNN) en un clúster de GPU de un solo nodo.

## <a name="prerequisites"></a>requisitos previos

* Suscripción a Azure: si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

* SDK de Python de Azure: consulte las [instrucciones de instalación](/python/azure/python-sdk-azure-install)

* Cuenta de almacenamiento de Azure: consulte el artículo sobre la [creación de una cuenta de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).

* Credenciales de entidad de servicio de Azure Active Directory: consulte [cómo crear una entidad de servicio con la CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Registre el proveedor de recursos de Batch AI una vez para su suscripción mediante Azure Cloud Shell o la CLI de Azure. Un registro de proveedor puede tardar hasta 15 minutos.

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>Configuración de credenciales
Agregue el código siguiente al archivo de script y reemplace `FILL-IN-HERE` por los valores adecuados:

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'
```

Tenga en cuenta que no conviene colocar las credenciales en el código fuente; aquí se hace para simplificar el inicio rápido.
En su lugar, considere la posibilidad de usar variables de entorno u otro archivo de configuración.

## <a name="create-batch-ai-client"></a>Creación de un cliente de Batch AI

El siguiente código crea un objeto de credenciales de entidad de seguridad y un cliente de Batch AI:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Los clústeres y los trabajos de Batch AI son recursos de Azure y se deben colocar en un grupo de recursos de Azure. En el fragmento de código siguiente se crea un grupo de recursos:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'
resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)
resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Preparación de un recurso compartido de Azure Files
Con fines meramente ilustrativos, en esta guía de inicio rápido se usa un recurso compartido de archivos de Azure para hospedar los datos de aprendizaje y los scripts para el trabajo de aprendizaje.

1. Cree un recurso compartido de archivos llamado `batchaiquickstart`.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

2. Cree un directorio en el recurso compartido llamado `mnistcntksample`.

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory,
                         fail_on_exist=False)
```
3. Descargue el [paquete de ejemplo](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) y descomprímalo en el directorio actual. El siguiente código carga los archivos necesarios en el recurso compartido de archivos de Azure:

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-gpu-cluster"></a>Creación del clúster de GPU

Cree un clúster de Batch AI. En este ejemplo, el clúster está formado por un único nodo de la máquina virtual STANDARD_NC6. El tamaño de la máquina virtual tiene una GPU NVIDIA K80. Monte el recurso compartido de archivos en una carpeta denominada `azurefileshare`. La ruta de acceso completa de esta carpeta en el nodo de proceso de GPU es `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare'

parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus',
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6',
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
        admin_user_name=admin_user_name,
        admin_user_password=admin_user_password),
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=1)
    ),
    # Configure each node in the cluster
    node_setup=models.NodeSetup(
        # Mount shared volumes to the host
        mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=storage_account_key),
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>Obtención del estado del clúster

Supervise el estado de clúster con el comando siguiente:

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

El código anterior imprime información básica de asignación del clúster como en el ejemplo siguiente:

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

El clúster está listo cuando los nodos se han asignado y ha finalizado la preparación (consulte el atributo `nodeStateCounts`). Si se produjo un error, el atributo `errors` contiene la descripción del error.

## <a name="create-training-job"></a>Creación de un trabajo de aprendizaje

Cuando el clúster esté listo, configure y envíe el trabajo de aprendizaje:

```Python
job_name = 'myjob'

parameters = models.job_create_parameters.JobCreateParameters(
    # The job and cluster must be created in the same location
    location=cluster.location,
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>Supervisar el trabajo
Puede inspeccionar el estado del trabajo mediante el código siguiente:

```Python
job = batchai_client.jobs.get(resource_group_name, job_name)

print('Job state: {0} '.format(job.execution_state.name))
```

La salida es parecida a esta: `Job state: running`.

`executionState` contiene el estado de ejecución actual del trabajo:
* `queued`: el trabajo está esperando que los nodos del clúster estén disponibles
* `running`: el trabajo se está ejecutando
* `succeeded` (o `failed`): el trabajo finaliza y `executionInfo` contiene los detalles del resultado

## <a name="list-stdout-and-stderr-output"></a>Enumeración de la salida stdout y stderr
Use el código siguiente para mostrar los archivos stdout, stderr y de registro generados:

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>Mostrar archivo de modelo generado
Use el código siguiente para mostrar los archivos de modelo generados:
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>Eliminar recursos

Use el código siguiente para eliminar el trabajo:
```Python
batchai_client.jobs.delete(resource_group_name, job_name)
```

Use el código siguiente para eliminar el clúster:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```

Use el código siguiente para eliminar todos los recursos asignados:
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido cómo ejecutar un trabajo de aprendizaje de CNTK en un clúster de Batch AI, mediante el SDK de Python de Azure. Para más información sobre el uso de Batch AI con kits de herramientas diferentes, consulte las [recetas de aprendizaje](https://github.com/Azure/BatchAI).
