---
title: 'Tutorial: uso del SDK de Azure Batch para Python | Microsoft Docs'
description: "Aprenda los conceptos básicos de Azure Batch y cree una solución sencilla mediante Python."
services: batch
documentationcenter: python
author: tamram
manager: timlt
editor: 
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd5a977c10d3955639beb893cd7a37581b14f7c0
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Introducción al SDK de Batch para Python

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Aprenda los conceptos básicos del cliente de [Azure Batch][azure_batch] y [Python de Batch][py_azure_sdk] gracias a una pequeña aplicación de Batch escrita en Python que verá. Se examina la forma en que dos scripts de ejemplo usan el servicio Batch para procesar una carga de trabajo paralela en máquinas virtuales Linux en la nube, y cómo interactúan con [Azure Storage](../storage/common/storage-introduction.md) para almacenar provisionalmente archivos y recuperarlos. Aprenderá un flujo de trabajo de la aplicación Batch habitual y obtenga un conocimiento básico de los componentes principales de Batch, como trabajos, tareas, grupos y nodos de proceso.

![Flujo de trabajo de soluciones de Batch (básico)][11]<br/>

## <a name="prerequisites"></a>Requisitos previos
En este artículo se considera que tiene conocimientos prácticos de Python y está familiarizado con Linux. También se asume que puede cumplir los requisitos para la creación de cuentas que se especifican a continuación en Azure y los servicios Batch y Storage.

### <a name="accounts"></a>Cuentas
* **Cuenta de Azure**: si aún no tiene ninguna suscripción a Azure, [cree una cuenta gratuita de Azure][azure_free_account].
* **Cuenta de Batch**: una vez que tenga una suscripción a Azure, [cree una cuenta de Azure Batch](batch-account-create-portal.md).
* **Cuenta de almacenamiento**: consulte la sección [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de Azure Storage](../storage/common/storage-create-storage-account.md).

### <a name="code-sample"></a>Código de ejemplo
El [ejemplo de código ][github_article_samples]del tutorial de Python es uno de los muchos ejemplos de código de Batch que se encuentran en el repositorio [azure-batch-samples][github_samples] de GitHub. Para descargar todos los ejemplos, haga clic en **Clone or download > Download ZIP** (Clonar o descargar > Descargar ZIP) en la página principal del repositorio o haga clic en el vínculo de descarga directa [azure-batch-samples-master.zip][github_samples_zip]. Una vez que haya extraído el contenido del archivo ZIP, los dos scripts de este tutorial estarán encuentran en el directorio de `article_samples` :

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Entorno de Python
Para ejecutar el script de ejemplo *python_tutorial_client.py* en una estación de trabajo local, se necesita un **intérprete de Python** compatible con las versiones **2.7** o **3.3+**. El script se ha probado en Linux y Windows.

### <a name="cryptography-dependencies"></a>Dependencias de cryptography
Debe instalar las dependencias de la biblioteca [cryptography][crypto], necesaria para los paquetes de Python `azure-batch` y `azure-storage`. Realice una de las siguientes operaciones adecuadas para su plataforma, o bien consulte la información detallada de la [instalación de cryptography][crypto_install]:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-devel libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Si va a instalar Python 3.3 + en Linux, utilice los equivalentes de python3 para las dependencias de Python. Por ejemplo, en Ubuntu: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Paquetes de Azure
A continuación, instale los paquetes de Python **Azure Batch** y **Azure Storage**. Puede instalar ambos paquetes mediante **pip** y *requirements.txt*, que se encuentran aquí:

`/azure-batch-samples/Python/Batch/requirements.txt`

Emita el siguiente comando **pip** para instalar los paquetes Batch y Storage:

`pip install -r requirements.txt`

O bien, puede instalar manualmente los paquetes de Python [azure-batch][pypi_batch] y [azure-storage][pypi_storage]:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Si utiliza una cuenta sin privilegios, es posible que necesite agregar a los comandos el prefijo `sudo`. Por ejemplo: `sudo pip install -r requirements.txt`. Para más información acerca de cómo instalar los paquetes de Python, consulte [Installing Packages][pypi_install] (Instalación de paquetes) en python.org.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Ejemplo de código del tutorial de Python de Batch
El ejemplo de código del tutorial de Python de Batch está formado por dos scripts de Python y algunos archivos de datos.

* **python_tutorial_client.py**: interactúa con los servicios Batch y Storage para ejecutar una carga de trabajo paralela en los nodos de proceso (máquinas virtuales). El script *python_tutorial_client.py* se ejecuta en su estación de trabajo local.
* **python_tutorial_task.py**: script que se ejecuta en nodos de proceso de Azure para realizar el trabajo en sí. En el ejemplo, *python_tutorial_task.py* analiza el texto de un archivo descargado de Azure Storage (el archivo de entrada). Luego genera un archivo de texto (el archivo de salida) que contiene una lista de las tres palabras que más veces aparecen en el archivo de entrada. Después de crear el archivo de salida, *python_tutorial_task.py* lo carga en Azure Storage. Esto hace que esté disponible para su descarga en el script de cliente que se ejecuta en la estación de trabajo. El script *python_tutorial_task.py* se ejecuta en paralelo en varios nodos de proceso del servicio Batch.
* **./data/taskdata\*.txt**: estos tres archivos de texto proporcionan la entrada para las tareas que se ejecutan en los nodos de proceso.

El siguiente diagrama ilustra las operaciones principales realizadas por los scripts de cliente y de tarea. Este flujo de trabajo básico es típico de muchas soluciones de proceso que se crean con Batch. Aunque no muestra todas las características disponibles en el servicio Batch , casi todos los escenarios de Batch incluyen partes de este flujo de trabajo.

![Flujo de trabajo de ejemplo de Batch][8]<br/>

[**Paso 1.**](#step-1-create-storage-containers) Crear **contenedores** en Azure Blob Storage.<br/>
[**Paso 2.**](#step-2-upload-task-script-and-data-files) Cargue un script de tarea y archivos de entrada en los contenedores.<br/>
[**Paso 3.**](#step-3-create-batch-pool) Cree un **grupo** de Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** La clase **StartTask** del grupo descarga el script de tarea (python_tutorial_task.py) en los nodos cuando se unen al grupo.<br/>
[**Paso 4.**](#step-4-create-batch-job) Cree un **trabajo** de Batch.<br/>
[**Paso 5.**](#step-5-add-tasks-to-job) Agregue **tareas** al trabajo.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Las tareas se programan para ejecutarse en los nodos.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarea descarga sus datos de entrada de Azure Storage y comienza la ejecución.<br/>
[**Paso 6.**](#step-6-monitor-tasks) Supervisar las tareas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** A medida que se completan las tareas, cargan sus datos de salida en Azure Storage.<br/>
[**Paso 7.**](#step-7-download-task-output) Descargar el resultado de la tarea de Storage

Como se ha indicado, no todas las soluciones de Batch realizan estos mismos pasos y se puede haber muchos otros; sin embargo, sin embargo, este ejemplo muestra los procesos comunes que se encuentran en una solución de Batch.

## <a name="prepare-client-script"></a>Preparación del script de cliente
Antes de ejecutar el ejemplo, agregue las credenciales de las cuentas de Batch y Storage a *python_tutorial_client.py*. Si aún no lo ha hecho, abra el archivo en su editor favorito y actualice las líneas siguientes con las credenciales.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
BATCH_ACCOUNT_NAME = ""
BATCH_ACCOUNT_KEY = ""
BATCH_ACCOUNT_URL = ""

# Storage account credentials
STORAGE_ACCOUNT_NAME = ""
STORAGE_ACCOUNT_KEY = ""
```

Puede buscar las credenciales de las cuentas de Batch y de Storage en la hoja de la cuenta de cada uno de estos servicios en [Azure Portal][azure_portal]:

![Credenciales de Batch en el portal][9]
![Credenciales de Storage en el portal][10]<br/>

En las siguientes secciones se analizan los pasos que usan los scripts para procesar una carga de trabajo en el servicio Batch. Se recomienda consultar periódicamente los scripts en el editor mientras se trabaja con el resto del artículo.

Vaya a la siguiente línea en **python_tutorial_client.py** para comenzar con el paso 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Paso 1: Crear contenedores de Almacenamiento
![Crear contenedores en Azure Storage][1]
<br/>

Batch incluye compatibilidad integrada con la interacción con Azure Storage. Los contenedores de la cuenta de Storage proporcionarán los archivos que necesitarán las tareas que se ejecutan en la cuenta de Batch. Los contenedores también proporcionan un lugar para almacenar los datos de salida que producen las tareas. Lo primero que hace el script *python_tutorial_client.py* es crear tres contenedores en [Azure Blob Storage](../storage/common/storage-introduction.md#blob-storage):

* **application**: este contenedor almacenará el script de Python ejecutado por las tareas, *python_tutorial_task.py*.
* **input**: las tareas descargarán los archivos de datos que se van a procesar desde el contenedor *input* .
* **output**: cuando las tareas completen el procesamiento de los archivos de entrada, cargarán los resultados en el contenedor *output* .

Para interactuar con una cuenta de Storage y crear contenedores, se usa el paquete [azure-storage][pypi_storage] para crear un objeto [BlockBlobService][py_blockblobservice] (el "cliente del blob"). A continuación, se crearán tres contenedores en la cuenta de Almacenamiento mediante el cliente de blob.

```python
import azure.storage.blob as azureblob

# Create the blob client, for use in obtaining references to
# blob storage containers and uploading files to containers.
blob_client = azureblob.BlockBlobService(
    account_name=STORAGE_ACCOUNT_NAME,
    account_key=STORAGE_ACCOUNT_KEY)

# Use the blob client to create the containers in Azure Storage if they
# don't yet exist.
APP_CONTAINER_NAME = 'application'
INPUT_CONTAINER_NAME = 'input'
OUTPUT_CONTAINER_NAME = 'output'
blob_client.create_container(APP_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(INPUT_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(OUTPUT_CONTAINER_NAME, fail_on_exist=False)
```

Una vez creados los contenedores, la aplicación ya puede cargar los archivos que utilizarán las tareas.

> [!TIP]
> [Uso de Azure Blob Storage desde Python](../storage/blobs/storage-python-how-to-use-blob-storage.md) ofrece una buena introducción sobre cómo trabajar con los blobs y contenedores de Azure Storage. Al empezar a trabajar con Batch, debe encontrarse cerca de la parte superior de la lista de lectura.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>Paso 2: Cargar un script de tarea y archivos de entrada
![Cargar una aplicación de tarea y archivos de entrada (datos) en los contenedores][2]
<br/>

En la operación de carga de archivos, *python_tutorial_client.py* define en primer lugar las colecciones de las rutas de acceso a los archivos de **application** e **input** tal como existen en la máquina local. Después cargará estos archivos en los contenedores que creó en el paso anterior.

```python
# Paths to the task script. This script will be executed by the tasks that
# run on the compute nodes.
application_file_paths = [os.path.realpath('python_tutorial_task.py')]

# The collection of data files that are to be processed by the tasks.
input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                    os.path.realpath('./data/taskdata2.txt'),
                    os.path.realpath('./data/taskdata3.txt')]

# Upload the application script to Azure Storage. This is the script that
# will process the data files, and is executed by each of the tasks on the
# compute nodes.
application_files = [
    upload_file_to_container(blob_client, APP_CONTAINER_NAME, file_path)
    for file_path in application_file_paths]

# Upload the data files. This is the data that will be processed by each of
# the tasks executed on the compute nodes in the pool.
input_files = [
    upload_file_to_container(blob_client, INPUT_CONTAINER_NAME, file_path)
    for file_path in input_file_paths]
```

Mediante la comprensión de la lista, se llama a la función `upload_file_to_container` de cada archivo de las colecciones y se rellenan dos colecciones [ResourceFile][py_resource_file]. A continuación aparece la función `upload_file_to_container` :

```python
def upload_file_to_container(block_blob_client, container_name, path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """

    import datetime
    import azure.storage.blob as azureblob
    import azure.batch.models as batchmodels

    blob_name = os.path.basename(path)

    print('Uploading file {} to container [{}]...'.format(path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
Un objeto [ResourceFile][py_resource_file] proporciona tareas de Batch con la dirección URL a un archivo de Azure Storage que se descarga en un nodo de proceso antes de que la tarea se ejecute. La propiedad [ResourceFile][py_resource_file].**blob_source** especifica la dirección URL completa del archivo, tal como existe en Azure Storage. La dirección URL también puede incluir una firma de acceso compartido (SAS) que proporcione acceso seguro al archivo. La mayoría de los tipos de tareas de Batch contienen una propiedad *ResourceFiles* , que incluye:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

Este ejemplo no usa los tipos de tarea JobPreparationTask o JobReleaseTask, pero puede obtener más información sobre ellos en [Ejecución de tareas de preparación y finalización de trabajos en nodos de proceso de Azure Batch](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Firma de acceso compartido (SAS)
Las Firmas de acceso compartido son cadenas que proporcionan acceso seguro a contenedores y blobs en Azure Storage. El script *python_tutorial_client.py* utiliza las firmas de acceso compartido de blobs y contenedores, y muestra cómo obtener estas cadenas de firmas de acceso compartido del servicio Storage.

* **Firmas de acceso compartido de blobs**: la clase StartTask del grupo usa las firmas de acceso compartido de blobs al descargar el script de tarea y los archivos de datos de entrada de Almacenamiento (consulte el [paso 3](#step-3-create-batch-pool) ). La función `upload_file_to_container` de *python_tutorial_client.py* contiene el código que obtiene la firma de acceso compartido de cada uno de los blobs. Lo hace mediante una llamada a [BlockBlobService.make_blob_url][py_make_blob_url] en el módulo Storage.
* **Firma de acceso compartido de contenedores**: cuando cada tarea finaliza su trabajo en el nodo de proceso, carga su archivo de salida en el contenedor *output* de Azure Storage. Para ello, *python_tutorial_task.py* utiliza una firma de acceso compartido de contenedores que proporciona acceso de escritura al contenedor. La función `get_container_sas_token` de *python_tutorial_client.py* obtiene la firma de acceso compartido del contenedor, que luego se pasa como argumento de la línea de comandos a las tareas. En el paso 5, [Agregar tareas a un trabajo](#step-5-add-tasks-to-job), se describe el uso de la SAS de contenedores.

> [!TIP]
> Consulte la serie de dos partes sobre las firmas de acceso compartido, [Parte 1: Descripción del modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) y [Parte 2: Creación y uso de una SAS con Blob service](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md), para más información sobre cómo proporcionar acceso seguro a los datos de la cuenta de Storage.
>
>

## <a name="step-3-create-batch-pool"></a>Paso 3: Crear el grupo de Batch
![Crear un grupo de Batch][3]
<br/>

Un **grupo** de Batch es una colección de nodos de proceso (máquinas virtuales) en los que Batch ejecuta tareas de un trabajo.

Después de cargar los archivos de datos y el script de tarea en la cuenta de Storage, *python_tutorial_client.py* inicia su interacción con el servicio Batch mediante el módulo Python de Batch. Para ello, se crea una instancia de [BatchServiceClient][py_batchserviceclient]:

```python
# Create a Batch service client. We'll now be interacting with the Batch
# service in addition to Storage.
credentials = batchauth.SharedKeyCredentials(BATCH_ACCOUNT_NAME,
                                             BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL)
```

Después, se crea un grupo de nodos de proceso en la cuenta de Batch con una llamada a `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Cuando se crea un grupo, se define una instancia de [PoolAddParameter][py_pooladdparam] que especifica varias propiedades para el grupo:

* **Id**. del grupo (*id*: necesario)<p/>Lo mismo que sucede con la mayoría de las entidades en Batch, el nuevo grupo debe tener un identificador único dentro de la cuenta de Batch. El código hace referencia a este grupo mediante su identificador y es la forma en que el grupo se identifica en Azure [Portal][azure_portal].
* **Número de nodos de proceso** (*target_dedicated*: necesario)<p/>Esta propiedad especifica el número de máquinas virtuales que deben implementarse en el grupo. Es importante tener en cuenta que todas las cuentas de Batch tienen una **cuota** predeterminada que limita el número de **núcleos** (y, por tanto, los nodos de proceso) en una cuenta de Batch. Tanto las cuotas predeterminadas como las instrucciones para [aumentar una cuota](batch-quota-limit.md#increase-a-quota) (por ejemplo, el número máximo de núcleos de su cuenta de Batch) se pueden encontrar en [Cuotas y límites del servicio Batch de Azure](batch-quota-limit.md). Si se pregunta "¿Por qué mi grupo no llega a más de X nodos?", esta cuota de núcleos puede ser la causa.
* **Sistema operativo** para los nodos (*virtual_machine_configuration***o***cloud_service_configuration*: necesario)<p/>En *python_tutorial_client.py*, se crea un grupo de nodos de Linux con [VirtualMachineConfiguration][py_vm_config]. La función `select_latest_verified_vm_image_with_node_agent_sku` de `common.helpers` simplifica el trabajo con imágenes del [Catálogo de Azure Virtual Machines][vm_marketplace]. Para más información acerca del uso de imágenes de Marketplace, consulte [Aprovisionamiento de nodos de proceso de Linux en grupos del servicio Azure Batch](batch-linux-nodes.md) .
* **Tamaño de los nodos de proceso** (*vm_size*: necesario)<p/>Dado que especificamos nodos de Linux para nuestra instancia de [VirtualMachineConfiguration][py_vm_config], especificamos también un tamaño de máquina virtual (`STANDARD_A1` en este ejemplo) de los que aparecen en [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para más información, vuelva a consultar [Aprovisionamiento de nodos de proceso de Linux en grupos del servicio Azure Batch](batch-linux-nodes.md).
* **Tarea de inicio** (*start_task*: no necesario)<p/>Junto con las anteriores propiedades de nodo físico, también puede especificar una tarea [StartTask][py_starttask] para el grupo (no es necesario). StartTask se ejecutará en cada nodo cuando este se una al grupo, así como cada vez que se reinicie. StartTask es especialmente útil para preparar los nodos de proceso para la ejecución de tareas, como la instalación de las aplicaciones que las tareas ejecutan.<p/>En esta aplicación de ejemplo, la clase StartTask copia los archivos que descarga de Storage (que se especifican mediante la propiedad **resource_files** de StartTask) del *directorio de trabajo* de StartTask al directorio *compartido* al que pueden acceder todas las tareas que se ejecutan en el nodo. Básicamente, copia `python_tutorial_task.py` en el directorio compartido de cada nodo cuando el nodo se une al grupo, con el fin de que las tareas que se ejecutan en el nodo puedan acceder a él.

Fíjese en la llamada a la función auxiliar `wrap_commands_in_shell` . Esta función toma una colección de comandos independientes y crea una línea de comandos adecuada para la propiedad de la línea de comandos de la tarea.

En el fragmento de código anterior, también cabe destacar el uso de dos variables de entorno en la propiedad **command_line** de StartTask: `AZ_BATCH_TASK_WORKING_DIR` y `AZ_BATCH_NODE_SHARED_DIR`. Cada nodo de proceso de un grupo de Batch se configura automáticamente con un número de variables de entorno específicas de Batch. Todos los procesos que ejecute una tarea tienen acceso a estas variables de entorno.

> [!TIP]
> Para más información acerca de las variables de entorno disponibles en los nodos de proceso de un grupo de Batch, así como de los directorios de trabajo de las tareas, consulte **Configuración del entorno para las tareas** y **Archivos y directorios** en la [introducción a las características de Azure Batch](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Paso 4: Crear el trabajo de Batch
![Crear un trabajo de Batch][4]<br/>

Un **trabajo** de Batch es una colección de tareas y está asociado a un grupo de nodos de proceso. Las tareas de un trabajo se ejecutan en los nodos de proceso del grupo asociado.

Los trabajos se pueden usar no solo para la organización y seguimiento de las tareas de las cargas de trabajo relacionadas, sino también para imponer ciertas restricciones, como el tiempo máximo de ejecución del trabajo (y, por extensión, de sus tareas), así como la prioridad del trabajo, en relación con los restantes trabajos de la cuenta de Batch. Sin embargo, en este ejemplo, el trabajo está asociado solo con el grupo que se creó en el paso 3. No hay propiedades adicionales configuradas.

Todos los trabajos de Batch están asociados a un grupo específico. Esta asociación indica en qué nodos se ejecutan las tareas del trabajo. Para especificar el grupo, use la propiedad [PoolInformation][py_poolinfo], como se muestra en el siguiente fragmento de código.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Ahora que se ha creado un trabajo, se agregan las tareas para realizar dicho trabajo.

## <a name="step-5-add-tasks-to-job"></a>Paso 5: Agregar tareas al trabajo
![Agregar tareas al trabajo][5]<br/>
*(1) Las tareas se agregan al trabajo, (2) las tareas se programan para ejecutarse en los nodos y (3) las tareas descargan los archivos de datos que se van a procesar*

Las **tareas** de Batch son unidades de trabajo individuales que se ejecutan en los nodos de proceso. Una tarea tiene una línea de comandos y ejecuta los scripts o archivos ejecutables que se especifican en la línea de comandos.

Para realizar el trabajo, las tareas deben agregarse a un trabajo. Cada tarea [CloudTask][py_task] se configura mediante una propiedad de línea de comandos y el objeto [ResourceFiles][py_resource_file] (al igual que con la tarea StartTask del grupo) que la tarea descarga en el nodo antes de que su línea de comandos se ejecute automáticamente. En el ejemplo, cada tarea procesa un solo archivo. Por lo tanto, su colección ResourceFiles contiene un único elemento.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> Cuando acceden a variables de entorno como `$AZ_BATCH_NODE_SHARED_DIR` o ejecutan una aplicación que no se encuentra en el elemento `PATH` del nodo, las líneas de comandos de la tarea deben invocar el shell explícitamente, como con `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Este requisito no es necesario si las tareas ejecutan una aplicación en el elemento `PATH` del nodo y no hacen referencia a ninguna variable de entorno.
>
>

En el bucle `for` del fragmento de código anterior, puede ver que la línea de comandos de la tarea está compuesta por cinco argumentos de la línea de comandos que se pasan a *python_tutorial_task.py*:

1. **filepath**: es la ruta de acceso local al archivo, tal como existe en el nodo. Cuando en el paso 2 se creó el objeto ResourceFile en `upload_file_to_container`, se usó el nombre de archivo de esta propiedad (el parámetro `file_path` del constructor ResourceFile). Esto indica que el archivo puede encontrarse en el mismo directorio del nodo que *python_tutorial_task.py*.
2. **numwords**: las *N* palabras más usadas deben escribirse en el archivo de salida.
3. **storageaccount**: nombre de la cuenta de Almacenamiento propietaria del contenedor en el que se debe cargar la salida de la tarea.
4. **storagecontainer**: nombre del contenedor de Almacenamiento en el que se deben cargar los archivos de salida.
5. **sastoken**: la firma de acceso compartido (SAS) que proporciona acceso de escritura al contenedor **output** de Azure Storage. El script *python_tutorial_task.py* usa esta firma de acceso compartido cuando crea su referencia a BlockBlobService. Esto proporciona acceso de escritura al contenedor sin necesidad de una tecla de acceso para la cuenta de almacenamiento.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Paso 6: Supervisar tareas
![Supervisar tareas][6]<br/>
*El script (1) supervisa el estado de finalización de las tareas y (2) las tareas cargan los datos resultantes en Azure Storage*

Cuando las tareas se agregan a un trabajo, automáticamente se ponen en cola y se programan para su ejecución en los nodos de ejecución del grupo asociado al trabajo. Según la configuración que especifique, Batch controla la administración de las colas, programación y reintentos de todas las tareas, así como otros cometidos de administración de tareas por usted.

Existen varios enfoques para supervisar la ejecución de tareas. La función `wait_for_tasks_to_complete` de *python_tutorial_client.py* proporciona un ejemplo sencillo de tareas de supervisión para un estado determinado; en este caso, el estado [completado][py_taskstate].

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Paso 7: Descargar el resultado de la tarea
![Descargar el resultado de la tarea de Storage][7]<br/>

Ahora que se ha completado el trabajo, el resultado de las tareas se puede descargar desde Azure Storage. Esto se realiza mediante una llamada a `download_blobs_from_container` en *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> La llamada a `download_blobs_from_container` en *python_tutorial_client.py* especifica que los archivos se deben descargar en su directorio principal. Puede modificar si lo desea esta ubicación de salida.
>
>

## <a name="step-8-delete-containers"></a>Paso 8: Eliminar contenedores
Como se le cobrará por los datos que residen en Azure Storage, siempre es una buena idea quitar los blobs que ya no sean necesario para los trabajos de Batch. En *python_tutorial_client.py*, esto se realiza con tres llamadas a [BlockBlobService.delete_container][py_delete_container]:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Paso 9: Eliminar el trabajo y el grupo
En el último paso, se le solicita que elimine tanto el trabajo como el grupo que ha creado con el script *python_tutorial_client.py*. Aunque no se cobran los trabajos y tareas, *sí* se cobran los nodos de proceso. Por consiguiente, se recomienda asignar solo los nodos necesarios. La eliminación de los grupos que no se usen puede formar parte del proceso de mantenimiento.

Las propiedades [JobOperations][py_job] y [PoolOperations][py_pool] de BatchServiceClient tienen sus métodos de eliminación correspondientes, a los que se llama si el usuario confirma la eliminación:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> Tenga en cuenta que los recursos de proceso se cobran (la eliminación de grupos sin usar reducirá el costo). Tenga en cuenta que al eliminar un grupo se eliminan todos los nodos de proceso del grupo y que los datos de los nodos no se podrán recuperar cuando se elimine el grupo.
>
>

## <a name="run-the-sample-script"></a>Ejecución del script de ejemplo
Al ejecutar el script *python_tutorial_client.py*, desde el [ejemplo de código][github_article_samples] del tutorial, la salida de la consola será similar a la siguiente. Se produce una pausa en `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` mientras se crean e inician los nodos de proceso del grupo, y se ejecutan los comandos de la tarea de inicio del grupo. Use [Azure Portal][azure_portal] para supervisar el grupo, los nodos de proceso, el trabajo y las tareas durante la ejecución y después de ella. Use [Azure Portal][azure_portal] o el [Explorador de Microsoft Azure Storage][storage_explorer] para ver los recursos de Storage (contenedores y blobs) creados por la aplicación.

> [!TIP]
> Ejecute el script *python_tutorial_client.py* desde el directorio `azure-batch-samples/Python/Batch/article_samples`. Utiliza una ruta de acceso relativa para la importación del módulo `common.helpers`, por lo que es posible que vea `ImportError: No module named 'common'` si no ejecuta el script desde este directorio.
>
>

El tiempo de ejecución suele ser de **aproximadamente de 5-7 minutos** cuando el ejemplo se ejecuta con su configuración predeterminada.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Pasos siguientes
No dude en realizar cambios en *python_tutorial_client.py* y *python_tutorial_task.py* para experimentar con diferentes escenarios de proceso. Por ejemplo, pruebe a agregar un retraso de ejecución a *python_tutorial_task.py* para simular tareas de ejecución prolongada y supervisarlas en el portal. Pruebe a agregar más tareas o a ajustar el número de nodos de proceso. Agregue lógica para comprobar y permitir el uso de un grupo existente para acelerar el tiempo de ejecución.

Ahora que está familiarizado con el flujo de trabajo básico de una solución de Batch, ha llegado el momento de adentrarse en las características adicionales del servicio Batch.

* Consulte el artículo [Información general de las características de Azure Batch](batch-api-basics.md) , que es especialmente recomendable si no se conoce el servicio.
* Comience por los restantes artículos de desarrollo de Batch, en la sección **Desarrollo en profundidad** de la [ruta de aprendizaje de Batch][batch_learning_path].
* Consulte otra implementación del procesamiento de la carga de trabajo de "las N palabras más usadas" con Batch en el ejemplo [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Crear contenedores en Azure Storage"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Cargar una aplicación de tarea y archivos de entrada (datos) en los contenedores"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Crear el grupo de Batch"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Crear trabajo de Batch"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Agregar tareas al trabajo"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Supervisar tareas"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Descargar el resultado de la tarea de Storage"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Flujo de trabajo de soluciones de Batch (diagrama completo)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Credenciales de Batch en el portal"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Credenciales de Storage en el portal"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Flujo de trabajo de soluciones de Batch (diagrama mínimo)"
