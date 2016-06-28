<properties
	pageTitle="Tutorial: Introducción al cliente Python de Lote de Azure | Microsoft Azure"
	description="Aprenda los conceptos básicos de Lote de Azure y cómo desarrollar el servicio Lote con un escenario simple."
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="06/17/2016"
	ms.author="marsma"/>

# Introducción al cliente Python de Lote de Azure

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Aprenda los conceptos básicos del cliente [Lote de Azure][azure_batch] y [Python de Lote][py_azure_sdk] gracias a una pequeña aplicación de Lote escrita en Python que verá. Se examinará la forma en que estos dos scripts de ejemplo aprovechan del servicio Lote para procesar una carga de trabajo paralela en máquinas virtuales Linux en la nube, y cómo interactúan con [Almacenamiento de Azure](./../storage/storage-introduction.md) para almacenar provisionalmente archivos y recuperarlos. Aprenderá un flujo de trabajo de la aplicación Lote habitual y obtenga un conocimiento básico de los componentes principales de Lote, como trabajos, tareas, grupos y nodos de proceso.

> [AZURE.NOTE] La compatibilidad con Linux en el servicio Lote está actualmente en vista previa. Pueden cambiar algunos aspectos de la característica aquí descrita antes de que tenga disponibilidad general. Los [paquetes de aplicación](batch-application-packages.md) **no se admiten actualmente** en los nodos de proceso de Linux.

![Flujo de trabajo de soluciones de Lote (básico)][11]<br/>

## Requisitos previos

En este artículo se considera que tiene conocimientos prácticos de Python y está familiarizado con Linux. También se asume que puede cumplir los requisitos para la creación de cuentas que se especifican a continuación en Azure y los servicios Lote y Almacenamiento.

### Cuentas

- **Cuenta de Azure**: si aún no dispone de una suscripción a Azure, [cree una cuenta gratuita de Azure][azure_free_account].
- **Cuenta de Lote**: una vez que tenga una suscripción a Azure, [cree una cuenta de Lote de Azure](batch-account-create-portal.md).
- **Cuenta de almacenamiento**: consulte la sección [Crear una cuenta de Almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de Almacenamiento de Azure](../storage/storage-create-storage-account.md).

### Código de ejemplo

El ejemplo de código del tutorial de Python es uno de los muchos ejemplos de código de Lote que se encuentran en el repositorio [azure-batch-samples][github_samples] de GitHub. Para descargar todos los ejemplos, haga clic en **Clone or download > Download ZIP** (Clonar o descargar > Descargar ZIP), en la página principal del repositorio o haga clic en el vínculo de descarga directa [azure-batch-samples-master.zip][github_samples_zip]. Una vez que haya extraído el contenido del archivo ZIP, los dos scripts de este tutorial estarán en el directorio de `article_samples`:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/> `/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### Entorno de Python

Para ejecutar el script de ejemplo *python\_tutorial\_client.py*, en su estación de trabajo local, necesitará un **intérprete de Python** compatible con la versión **2.7** o **3.3-3.5**. El script se ha probado en Linux y Windows.

También necesitará instalar los paquetes de Python **Lote de Azure** y **Almacenamiento de Azure**. Esto puede hacerse con el archivo *requirements.txt*, que se encuentra aquí:

`/azure-batch-samples/Python/Batch/requirements.txt`

Emita el siguiente comando **pip** para instalar los paquetes Lote y Almacenamiento:

`pip install -r requirements.txt`

Si lo prefiere, puede instalar manualmente los paquetes de Python [azure-batch][pypi_batch] y [azure-storage][pypi_storage].

> [AZURE.TIP] Puede que necesite agregar a los comandos el prefijo `sudo`; por ejemplo `sudo pip install -r requirements.txt`, si utiliza una cuenta sin privilegios (recomendada). Para más información acerca de cómo instalar los paquetes de Python, consulte [Installing Packages][pypi_install] (Instalación de paquetes) en readthedocs.io.

## Ejemplo de código del tutorial de Python de Lote

El ejemplo de código del tutorial de Python de Lote está formado por dos scripts de Python y algunos archivos de datos.

- **python\_tutorial\_client.py**: interactúa con los servicios Lote y Almacenamiento para ejecutar una carga de trabajo paralela en los nodos de proceso (máquinas virtuales). El script *python\_tutorial\_client.py* se ejecuta en la estación de trabajo local.

- **python\_tutorial\_task.py**: script que se ejecuta en los nodos de proceso de Azure para realizar el trabajo real. En el ejemplo, *python\_tutorial\_task.py* analiza el texto de un archivo descargado de Almacenamiento de Azure (el archivo de entrada). Luego genera un archivo de texto (el archivo de salida) que contiene una lista de las tres palabras que más veces aparecen en el archivo de entrada. Después de crear el archivo de salida, *python\_tutorial\_task.py* lo carga en Almacenamiento de Azure. Esto hace que esté disponible para su descarga en el script de cliente que se ejecuta en la estación de trabajo. El script *python\_tutorial\_task.py* se ejecuta en paralelo en varios nodos de proceso del servicio Lote.

- **./data/taskdata*.txt**: estos tres archivos de texto proporcionan la entrada para las tareas que se ejecutan en los nodos de proceso.

El siguiente diagrama ilustra las operaciones principales realizadas por los scripts de cliente y de tarea. Este flujo de trabajo básico es típico de muchas soluciones de proceso que se crean con Lote. Aunque no muestra todas las características disponibles en el servicio Lote, casi todos los escenarios de Lote incluyen partes de este flujo de trabajo.

![Flujo de trabajo de ejemplo de Lote][8]<br/>

[**Paso 1.**](#step-1-create-storage-containers) Crear **contenedores** en Almacenamiento de blobs de Azure.<br/> [**Paso 2.**](#step-2-upload-task-script-and-data-files) Cargar un script de tarea y archivos de entrada en los contenedores.<br/> [**Paso 3.**](#step-3-create-batch-pool) Crear un **grupo** de Lote.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**3a.** La clase **StartTask** del grupo descarga el script de tarea (python\_tutorial\_task.py) en los nodos cuando se unen al grupo.<br/> [**Paso 4.**](#step-4-create-batch-job) Crear un **trabajo** de Lote.<br/> [**Paso 5.**](#step-5-add-tasks-to-job) Agregar **tareas** al trabajo.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Las tareas se programan para que se ejecuten en los nodos.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarea descarga sus datos de entrada de Almacenamiento de Azure y comienza la ejecución.<br/> [**Paso 6.**](#step-6-monitor-tasks) Supervisar tareas.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6a.** A medida que se completan las tareas, cargan sus datos de salida en Almacenamiento de Azure.<br/> [**Paso 7.**](#step-7-download-task-output) Descargar el resultado de la tarea de Almacenamiento

Tal y como se dijo, no todas las soluciones de Lote realizarán estos mismos pasos y puede haber muchos otros pasos; sin embargo, este ejemplo muestra los procesos comunes que se encuentran en una solución de Lote.

## Preparación del script de cliente

Antes de ejecutar el ejemplo, agregue las credenciales de la cuenta de Lote y Almacenamiento para *python\_tutorial\_client.py*. Si aún no lo ha hecho, abra el archivo en su editor favorito y actualice las líneas siguientes con las credenciales.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Puede buscar las credenciales de las cuentas de Lote y de Almacenamiento en el [Portal de Azure][azure_portal], en la hoja Cuenta de cada servicio:

![Credenciales de Lote en el portal][9] ![Credenciales de Almacenamiento en el portal][10]<br/>

En las secciones siguientes se analizan los pasos utilizados por los scripts para procesar una carga de trabajo en el servicio Lote. Se recomienda consultar periódicamente los scripts en el editor mientras se trabaja con el resto del artículo.

Vaya a la siguiente línea en **python\_tutorial\_client.py** para comenzar con el paso 1:

```python
if __name__ == '__main__':
```

## Paso 1: Crear contenedores de Almacenamiento

![Crear contenedores en Almacenamiento de Azure][1] <br/>

Lote incluye compatibilidad integrada con la interacción con Almacenamiento de Azure. Los contenedores de la cuenta de Almacenamiento proporcionarán los archivos que necesitarán las tareas que se ejecutan en la cuenta de Lote. Los contenedores también proporcionan un lugar para almacenar los datos de salida que producen las tareas. Lo primero que hace el script *python\_tutorial\_client.py* es crear tres contenedores en [Almacenamiento de blobs de Azure](../storage/storage-introduction.md#blob-storage):

- **aplicación**: este contenedor almacenará el script de Python, *python\_tutorial\_task.py*, ejecutado por las tareas.
- **entrada**: las tareas descargarán los archivos de datos que se van a procesar desde el contenedor *entrada*.
- **salida**: cuando las tareas completen el procesamiento de los archivos de entrada, cargarán los resultados en el contenedor *salida*.

Para interactuar con una cuenta de Almacenamiento y crear contenedores, se usa el paquete [azure-storage][pypi_storage] para crear un objeto [BlockBlobService][py_blockblobservice] (el "cliente de blob"). A continuación, se crearán tres contenedores en la cuenta de Almacenamiento mediante el cliente de blob.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Una vez creados los contenedores, la aplicación ya puede cargar los archivos que utilizarán las tareas.

> [AZURE.TIP] [How to use Azure Blob storage from Python](../storage/storage-python-how-to-use-blob-storage.md) ofrece una buena introducción sobre cómo trabajar con los blobs y contenedores de Almacenamiento de Azure. Al empezar a trabajar con Lote, debe encontrarse cerca de la parte superior de la lista de lectura.

## Paso 2: Cargar un script de tarea y archivos de entrada

![Cargar una aplicación de tarea y archivos de entrada (datos) en los contenedores][2] <br/>

En la operación de carga de archivos, *python\_tutorial\_client.py* define en primer lugar las colecciones de las rutas de acceso a los archivos de **aplicación** y **entrada** tal como existen en la máquina local. Después cargará estos archivos en los contenedores que creó en el paso anterior.

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
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Mediante la comprensión de la lista, se llama a la función `upload_file_to_container` para cada archivo de las colecciones y se llenan dos colecciones [ResourceFile][py_resource_file]. A continuación aparece la función `upload_file_to_container`:

```
def upload_file_to_container(block_blob_client, container_name, file_path):
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
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
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

### ResourceFiles

[ResourceFile][py_resource_file] proporciona tareas de Lote con la dirección URL a un archivo de Almacenamiento de Azure que se descargará en un nodo de proceso antes de que la tarea se ejecute. La propiedad [ResourceFile][py_resource_file].**blob\_source** especifica la dirección URL completa del archivo, tal como existe en Almacenamiento de Azure. La dirección URL también puede incluir una firma de acceso compartido (SAS) que proporcione acceso seguro al archivo. La mayoría de los tipos de tareas de Lote contienen una propiedad *ResourceFiles*, que incluye:

- [CloudTask][py_task]
- [StartTask][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

Este ejemplo no usa los tipos de tarea JobPreparationTask o JobReleaseTask, pero puede leer más acerca de estos tipos de tarea en [Ejecución de tareas de preparación y finalización de trabajos en nodos de proceso de Lote de Azure](batch-job-prep-release.md).

### Firma de acceso compartido (SAS)

Las Firmas de acceso compartido son cadenas que proporcionan acceso seguro a contenedores y blobs en Almacenamiento de Azure. El script *python\_tutorial\_client.py* utiliza las firmas de acceso compartido de blobs y contenedores, y muestra cómo obtener estas cadenas de las firmas de acceso compartido en el servicio Almacenamiento.

- **Firmas de acceso compartido de blobs**: la clase StartTask del grupo usa las firmas de acceso compartido de blobs al descargar el script de la tarea y los archivos de datos de entrada de Almacenamiento (consulte el [paso 3](#step-3-create-batch-pool)). La función `upload_file_to_container` en *python\_tutorial\_client.py* contiene el código obtenido por cada firma de acceso compartido de blobs. Lo hace mediante una llamada a [BlockBlobService.make\_blob\_url][py_make_blob_url] en el módulo Almacenamiento.

- **Firma de acceso compartido de contenedores**: cuando cada tarea finaliza su trabajo en el nodo de proceso, carga su archivo de salida en el contenedor *salida* de Almacenamiento de Azure. Para ello, *python\_tutorial\_task.py* utiliza una firma de acceso compartido de contenedores que proporciona un acceso de escritura al contenedor. La función `get_container_sas_token` en *python\_tutorial\_client.py* obtiene la firma de acceso compartido de contenedores, que se pasa como un argumento de la línea de comandos a las tareas. En el paso 5, [Agregar tareas a un trabajo](#step-5-add-tasks-to-job), se describe el uso de la SAS del contenedor.

> [AZURE.TIP] Consulte la serie de dos partes sobre las firmas de acceso compartido, [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) y [Firmas de acceso compartido, Parte 2: Creación y uso de una SAS con Almacenamiento de blobs](../storage/storage-dotnet-shared-access-signature-part-2.md), para más información sobre cómo proporcionar acceso seguro a los datos de la cuenta de Almacenamiento.

## Paso 3: Crear el grupo de Lote

![Crear un grupo de Lote][3] <br/>

Un **grupo** de Lote es una colección de nodos de proceso (máquinas virtuales) en los que Lote ejecuta tareas de un trabajo.

Después de cargar los archivos de datos y el script de la tarea en la cuenta de Almacenamiento, *python\_tutorial\_client.py* inicia su interacción con el servicio Lote mediante el módulo Python de Lote. Para ello, se crea una instancia de [BatchServiceClient][py_batchserviceclient]\:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

A continuación, se crea un grupo de nodos de proceso en la cuenta de Lote con una llamada a `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, distro, version):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str distro: The Linux distribution that should be installed on the
    compute nodes, e.g. 'Ubuntu' or 'CentOS'.
    :param str version: The version of the operating system for the compute
    nodes, e.g. '15' or '14.04'.
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
        # Install pip and then the azure-storage module so that the task
        # script can access Azure Blob storage
        'apt-get update',
        'apt-get -y install python-pip',
        'pip install azure-storage']

    # Get the virtual machine configuration for the desired distro and version.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    vm_config = get_vm_config_for_distro(batch_service_client, distro, version)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=vm_config,
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
}
```

Cuando crea un grupo, se define una instancia de [PoolAddParameter][py_pooladdparam] que especifica varias propiedades para el grupo:

- **Identificador** del grupo (*id*: necesario)<p/>Como con la mayoría de las entidades en Lote, el nuevo grupo debe tener un identificador único dentro de la cuenta de Lote. El código hará referencia a este grupo con su identificador y sirve para identificar el grupo en el [Portal][azure_portal] de Azure.

- **Número de nodos de proceso** (*target\_dedicated*: necesario)<p/>Especifica cuántas máquinas virtuales deben implementarse en el grupo. Es importante tener en cuenta que todas las cuentas de Lote tienen una **cuota** predeterminada que limita el número de **núcleos** (y por tanto, los nodos de proceso) en una cuenta de Lote. Encontrará las cuotas predeterminadas y las instrucciones sobre cómo [aumentar una cuota](batch-quota-limit.md#increase-a-quota) (por ejemplo, el número máximo de núcleos de su cuenta de Lote) en [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md). Si se pregunta "¿Por qué mi grupo no llega a más de X nodos?", esta cuota de núcleos puede ser la causa.

- **Sistema operativo** para nodos (*virtual\_machine\_configuration* **o** *cloud\_service\_configuration*: necesario)<p/>En *python\_tutorial\_client.py*, se crea un grupo de nodos de Linux con una instancia de [VirtualMachineConfiguration][py_vm_config] obtenida con nuestra función auxiliar `get_vm_config_for_distro`. Esta función auxiliar usa [list\_node\_agent\_skus][py_list_skus] para obtener y seleccionar una imagen de una lista de compatible con imágenes de [Marketplace de máquinas virtuales de Azure][vm_marketplace]. Si lo prefiere, puede especificar una instancia de [CloudServiceConfiguration][py_cs_config] y crear el grupo de nodos de Windows desde Servicios en la nube. Consulte [Aprovisionamiento de nodos de proceso de Linux en grupos del servicio Lote de Azure](batch-linux-nodes.md) para más información acerca de las dos configuraciones.

- **Tamaño de los nodos de proceso** (*vm\_size*: necesario)<p/>Puesto que se están especificando nodos de Linux para nuestra instancia de [VirtualMachineConfiguration][py_vm_config], especifique un tamaño de máquina virtual (`STANDARD_A1` en este ejemplo) en [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/virtual-machines-linux-sizes.md). De nuevo, consulte [Aprovisionamiento de nodos de proceso de Linux en grupos del servicio Lote de Azure](batch-linux-nodes.md) para más información.

- **Tarea de inicio** (*start\_task*: no necesario)<p/>Junto con las anteriores propiedades del nodo físico, también puede especificar una tarea [StartTask][py_starttask] para el grupo (no es necesario). La clase StartTask se ejecutará en cada nodo cuando el nodo se una al grupo, así como cada vez que se reinicie el nodo. La tarea StartTask es especialmente útil para preparar los nodos de proceso para la ejecución de tareas, como la instalación de las aplicaciones que las tareas ejecutarán.<p/>En esta aplicación de ejemplo, la tarea StartTask copia los archivos que descarga de Almacenamiento (especificados mediante la propiedad **resource\_files** de StartTask) desde el *directorio de trabajo* de StartTask en el directorio *compartido* al que pueden acceder todas las tareas que se ejecutan en el nodo. Básicamente, copia `python_tutorial_task.py` y sus dependencias en el directorio compartido de cada nodo cuando el nodo se une al grupo, con el fin de que las tareas que se ejecutan en el nodo puedan acceder a él.

Puede ver la llamada a la función auxiliar `wrap_commands_in_shell`. Esta función toma una colección de comandos independientes y crea una línea de comandos adecuada para la propiedad de la línea de comandos de la tarea.

En el fragmento de código anterior, cabe destacar también el uso de dos variables de entorno en la propiedad **command\_line** de StartTask: `AZ_BATCH_TASK_WORKING_DIR` y `AZ_BATCH_NODE_SHARED_DIR`. Cada nodo de proceso de un grupo de Lote se configura automáticamente con un número de variables de entorno específicas de Lote. Todos los procesos que ejecute una tarea tienen acceso a estas variables de entorno.

> [AZURE.TIP] Para más información acerca de las variables de entorno disponibles en los nodos de proceso de un grupo de Lote, así como sobre los directorios de trabajo de las tareas, consulte **Configuración del entorno para las tareas** y **Archivos y directorios** en [Información general de las características de Lote de Azure](batch-api-basics.md).

## Paso 4: Crear el trabajo de Lote

![Crear trabajo de Lote][4]<br/>

Un **trabajo** de Lote es básicamente una colección de tareas asociadas a un grupo de nodos de proceso. Las tareas de un trabajo se ejecutan en los nodos de proceso del grupo asociado.

Se puede usar un trabajo no solo para la organización y seguimiento de las tareas en las cargas de trabajo relacionadas, sino también para imponer ciertas restricciones, como el tiempo máximo de ejecución del trabajo (y, por extensión, sus tareas), así como la prioridad del trabajo, en relación con los restantes trabajos de la cuenta de Lote. Sin embargo, en este ejemplo, el trabajo está asociado solo con el grupo que se creó en el paso 3. No hay propiedades adicionales configuradas.

Todos los trabajos de Lote están asociados a un grupo específico. Esta asociación indica en qué nodos se ejecutarán las tareas del trabajo. Para especificarlo, use la propiedad [PoolInformation][py_poolinfo], como se muestra en el siguiente fragmento de código.

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

## Paso 5: Agregar tareas al trabajo

![Agregar tareas al trabajo][5]<br/> *(1) Las tareas se agregan al trabajo, (2) las tareas se programan para ejecutarse en los nodos y (3) las tareas descargan los archivos de datos que se van a procesar*.

Las **tareas** de Lote son unidades de trabajo individuales que se ejecutan en los nodos de proceso. Una tarea tiene una línea de comandos y ejecuta los scripts o archivos ejecutables que se especifican en la línea de comandos.

Para realizar el trabajo, las tareas deben agregarse a un trabajo. Cada tarea [CloudTask][py_task] se configura mediante una propiedad de la línea de comandos y el objeto [ResourceFiles][py_resource_file] (al igual que con la tarea StartTask del grupo) que la tarea descarga en el nodo antes de que su línea de comandos se ejecute automáticamente. En el ejemplo, cada tarea procesa un solo archivo. Por lo tanto, su colección ResourceFiles contiene un único elemento.

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

> [AZURE.IMPORTANT] Cuando acceden a variables de entorno como `$AZ_BATCH_NODE_SHARED_DIR` o ejecutan una aplicación que no se encuentra en el elemento `PATH` del nodo, las líneas de comandos de la tarea deben invocar el shell explícitamente, como con `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Este requisito no es necesario si las tareas ejecutan una aplicación en el elemento `PATH` del nodo y no hacen referencia a ninguna variable de entorno.

En el bucle `for` del fragmento de código anterior, puede ver que la línea de comandos de la tarea está construida con cinco argumentos de la línea de comandos que se pasan a *python\_tutorial\_task.py*:

1. **filepath**: se trata de la ruta de acceso local al archivo, tal como existe en el nodo. Cuando en el paso 2 se creó el objeto ResourceFile en `upload_file_to_container`, se usó el nombre de archivo para esta propiedad (el parámetro `file_path` del constructor ResourceFile). Esto indica que el archivo puede encontrarse en el mismo directorio del nodo que *python\_tutorial\_task.py*.

2. **numwords**: las primeras *N* palabras deben escribirse en el archivo de salida.

3. **storageaccount**: nombre de la cuenta de Almacenamiento propietaria del contenedor en el que se debe cargar la salida de la tarea.

4. **storagecontainer**: nombre del contenedor de Almacenamiento en el que se deben cargar los archivos de salida.

5. **sastoken**: la firma de acceso compartido (SAS) que proporciona acceso de escritura al contenedor **salida** de Almacenamiento de Azure. El script *python\_tutorial\_task.py* utiliza esta firma de acceso compartido cuando crea su referencia a BlockBlobService. Esto proporciona acceso de escritura al contenedor sin necesidad de una tecla de acceso para la cuenta de almacenamiento.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## Paso 6: Supervisar tareas

![Supervisar tareas][6]<br/> *El script (1) supervisa las tareas para comprobar su estado de finalización y (2) las tareas cargan los datos resultantes en Almacenamiento de Azure*

Cuando las tareas se agregan a un trabajo, automáticamente se ponen en cola y se programan para su ejecución en los nodos de ejecución del grupo asociado al trabajo. Según la configuración que especifique, Lote controla la administración de las colas, programación y reintentos de todas las tareas, así como otros cometidos de administración de tareas por usted.

Existen varios enfoques para supervisar la ejecución de tareas. La función `wait_for_tasks_to_complete` en *python\_tutorial\_client.py* proporciona un ejemplo sencillo de tareas de supervisión para un estado determinado; en este caso, el estado [completado][py_taskstate].

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

## Paso 7: Descargar el resultado de la tarea

![Descargar el resultado de la tarea desde Almacenamiento][7]<br/>

Ahora que se ha completado el trabajo, el resultado de las tareas se puede descargar desde Almacenamiento de Azure. Esto se realiza mediante una llamada a `download_blobs_from_container` en *python\_tutorial\_client.py*:

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

> [AZURE.NOTE] La llamada a `download_blobs_from_container` en *python\_tutorial\_client.py* especifica que se deben descargar los archivos en el directorio principal del usuario. Puede modificar si lo desea esta ubicación de salida.

## Paso 8: Eliminar contenedores

Como se le cobrará por los datos que residen en Almacenamiento de Azure, siempre es una buena idea quitar los blobs que ya no sean necesario para los trabajos de Lote. En *python\_tutorial\_client.py*, esto se realiza con tres llamadas a [BlockBlobService.delete\_container][py_delete_container]\:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## Paso 9: Eliminar el trabajo y el grupo

En el último paso, se pedirá al usuario que elimine el trabajo y el grupo creado por el script *python\_tutorial\_client.py*. Aunque no se cobran los trabajos y tareas, *sí* se cobran los nodos de proceso. Por consiguiente, se recomienda asignar solo los nodos necesarios. La eliminación de los grupos que no se usen puede formar parte del proceso de mantenimiento.

Las propiedades [JobOperations][py_job] y [PoolOperations][py_pool] de BatchServiceClient tienen sus métodos de eliminación correspondientes, a los que se llama si el usuario confirma la eliminación:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] Tenga en cuenta que los recursos de proceso se cobran (la eliminación de grupos sin usar reducirá el costo). Tenga en cuenta que al eliminar un grupo se eliminan todos los nodos de proceso del grupo y que los datos de los nodos no se podrán recuperar cuando se elimine el grupo.

## Ejecución del script de ejemplo

Al ejecutar el script *python\_tutorial\_client.py*, la salida de la consola será similar a la siguiente. Verá una pausa en `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` mientras se crean e inician los nodos de proceso del grupo, y se ejecutan los comandos de la tarea de inicio del grupo. Use el [Portal de Azure][azure_portal] para supervisar el grupo, los nodos de proceso, el trabajo y las tareas durante la ejecución y después de ella. Use el [Portal de Azure][azure_portal] o el [Explorador de Almacenamiento de Microsoft Azure][storage_explorer] para ver los recursos de Almacenamiento (contenedores y blobs) creados por la aplicación.

El tiempo de ejecución suele ser de **aproximadamente de 5-7 minutos** cuando se ejecuta la aplicación en su configuración predeterminada.

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

## Pasos siguientes

No dude en realizar cambios en *python\_tutorial\_client.py* y *python\_tutorial\_task.py* para experimentar con diferentes escenarios de proceso. Por ejemplo, pruebe a agregar a *python\_tutorial\_task.py* un retraso en la ejecución para simular tareas de ejecución prolongada y supervisarlas en el portal. Pruebe a agregar más tareas o a ajustar el número de nodos de proceso. Agregue lógica para comprobar y permitir el uso de un grupo existente para acelerar el tiempo de ejecución.

Ahora que está familiarizado con el flujo de trabajo básico de una solución de Lote, ha llegado el momento de adentrarse en las características adicionales del servicio Lote.

- Consulte el artículo [Información general de las características de Lote de Azure](batch-api-basics.md), especialmente recomendado si no conoce el servicio.
- Comience por los restantes artículos de desarrollo de Lote, en la sección **Desarrollo en profundidad** de la [ruta de aprendizaje de Lote][batch_learning_path].
- Consulte otra implementación del procesamiento de la carga de trabajo de "las N palabras principales" con Lote en el ejemplo [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords

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

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Crear contenedores en Almacenamiento de Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Cargar una aplicación de tarea y archivos de entrada (datos) en los contenedores"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Crear grupo de Lote"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Crear trabajo de Lote"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Agregar tareas al trabajo"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Supervisar tareas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Descargar el resultado de la tarea desde Almacenamiento"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Flujo de trabajo de solución de Lote (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciales de Lote en el Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciales de Almacenamiento en el Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Flujo de trabajo de solución de Lote (diagrama mínimo)"

<!---HONumber=AcomDC_0622_2016-->