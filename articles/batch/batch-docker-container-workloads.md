---
title: Cargas de trabajo de contenedor en Azure Batch | Microsoft Docs
description: "Aprenda a ejecutar aplicaciones desde imágenes de contenedor en Azure Batch."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 12/01/2017
ms.author: v-dotren
ms.openlocfilehash: 1795bdde5506f599849a30d4e59ed7b916595ac4
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="run-container-applications-on-azure-batch"></a>Ejecución de aplicaciones de contenedor en Azure Batch

Azure Batch permite ejecutar y escalar un gran número de trabajos de computación por lotes en Azure. Hasta ahora, las tareas por lotes se ejecutaban directamente en máquinas virtuales en un grupo de Batch, pero ahora puede configurar un grupo de Batch para ejecutar tareas en contenedores de Docker.

El uso de contenedores proporciona una manera sencilla para ejecutar tareas por lotes sin tener que administrar paquetes de aplicaciones ni dependencias. Los contenedores implementan aplicaciones como unidades ligeras, portátiles y autosuficientes que se pueden ejecutar en una variedad de entornos. Por ejemplo, puede crear y probar localmente un contenedor y, después cargar la imagen de contenedor en un registro en Azure o en otro lugar. El modelo de implementación de contenedor garantiza que el entorno en tiempo de ejecución de la aplicación siempre esté correctamente instalado y configurado, con independencia de donde se hospede la aplicación. En este tutorial se muestra cómo utilizar el SDK de .NET de Batch para crear un grupo de nodos de proceso que admiten tareas de contenedor en ejecución y cómo ejecutar dichas tareas en el grupo.

En este artículo se da por supuesto que está familiarizado con los conceptos de contenedor de Docker y cómo crear un grupo y un trabajo de Batch mediante el SDK de .NET. Los fragmentos de código están pensados para usarse en una aplicación cliente similar a la del [ejemplo DotNetTutorial](batch-dotnet-get-started.md) y son ejemplos de código que se necesitan para admitir aplicaciones de contenedor en Batch.


## <a name="prerequisites"></a>Requisitos previos

* Versiones de SDK: los SDK de Batch admiten las imágenes de contenedor en las siguientes versiones:
    * API de REST de Batch versión 2017-09-01.6.0
    * SDK de .NET de Batch versión 8.0.0
    * SDK de Python de Batch versión 4.0
    * SDK de Java de Batch versión 3.0
    * SDK de Node.js de Batch versión 3.0

* Cuentas: en su cuenta de Azure, debe crear una cuenta de Batch y, opcionalmente, una cuenta de Storage de uso general.

* Una imagen de máquina virtual compatible. Los contenedores solo se admiten en grupos creados con la configuración de máquinas virtuales a partir de imágenes que se detallan en la siguiente sección, "Imágenes de máquinas virtuales admitidas".

* Si se proporciona una imagen personalizada, la aplicación debe utilizar la autenticación de Azure Active Directory (Azure AD) para ejecutar cargas de trabajo basadas en contenedores. Si se utiliza una imagen de Azure Marketplace, no necesita la autenticación de Azure AD; la autenticación de clave compartida servirá. La compatibilidad de Azure Batch con Azure AD se documenta en [Autenticación de soluciones de servicio de Batch con Active Directory](batch-aad-auth.md).


## <a name="supported-virtual-machine-images"></a>Imágenes de máquinas virtuales admitidas

Debe proporcionar una imagen Windows o Linux para crear un grupo de nodos de proceso de máquinas virtuales.

### <a name="windows-images"></a>Imágenes de Windows

Para cargas de trabajo de contenedor Windows, el servicio Batch actualmente admite imágenes personalizadas que se crean desde máquinas virtuales que ejecutan Docker en Windows, o bien puede usar Windows Server 2016 Datacenter con imágenes de contenedores desde Azure Marketplace. Esta imagen es compatible con el identificador de SKU del agente del nodo `batch.node.windows amd64`. El tipo de contenedor admitido está limitado actualmente a Docker.

### <a name="linux-images"></a>Imágenes de Linux

Para cargas de trabajo de contenedor Linux, el servicio Batch actualmente admite solo imágenes personalizadas creadas a partir de máquinas virtuales que ejecutan Docker en las siguientes distribuciones Linux: Ubuntu 16.04 LTS o CentOS 7.3. Si elige proporcionar su propia imagen personalizada de Linux, consulte las instrucciones de [Uso de una imagen personalizada administrada para crear un grupo de máquinas virtuales](batch-custom-images.md).

Puede usar [Docker Community Edition (CE)](https://www.docker.com/community-edition) o [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Si desea aprovechar el rendimiento de la GPU de los tamaños de máquina virtual NC o NV de Azure, debe instalar controladores NVIDIA en la imagen. Además, debe instalar y ejecutar la utilidad de motor de Docker para GPU NVIDIA, [Docker NVIDIA](https://github.com/NVIDIA/nvidia-docker).

Para acceder a la red RDMA de Azure, utilice máquinas virtuales de los tamaños siguientes: A8, A9, H16r, H16mr o NC24r. Los controladores RDMA necesarios se instalan en las imágenes de CentOS 7.3 HPC y Ubuntu 16.04 LTS de Azure Marketplace. Puede ser necesaria una configuración adicional para ejecutar cargas de trabajo MPI. Consulte [Uso de instancias compatibles con RDMA o habilitadas para GPU en grupos de Batch](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Limitaciones

* El servicio Batch proporciona compatibilidad con RDMA solo para contenedores que se ejecutan en grupos Linux.


## <a name="authenticate-using-azure-active-directory"></a>Autenticación mediante Azure Active Directory

Si utiliza una imagen de máquina virtual personalizada para crear el grupo de Batch, la aplicación cliente debe autenticarse mediante la autenticación integrada de Azure AD (la autenticación de clave compartida no funciona). Antes de ejecutar la aplicación, asegúrese de registrarla en Azure AD para establecer su identidad y especificar sus permisos para otras aplicaciones.

Además, cuando se usa una imagen de máquina virtual personalizada, deberá conceder control de acceso IAM a la aplicación para acceder a la imagen de máquina virtual. En Azure Portal, abra **Todos los recursos**, seleccione la imagen de contenedor y, desde la sección **Control de acceso (IAM)** de la hoja de imagen, haga clic en **Agregar**. En la hoja **Agregar permisos**, especifique un **rol**, en **Asignar acceso a**, seleccione **Usuario, grupo o aplicación de Azure AD**; a continuación, en **Seleccionar**, escriba el nombre de la aplicación.

En la aplicación, pase un token de autenticación de Azure AD al crear el cliente Batch mediante [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), tal y como se describe en [Autenticación de soluciones de servicio de Batch con Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Referencia a una imagen de máquina virtual para la creación de grupo

En el código de la aplicación, proporcione una referencia a la imagen de máquina virtual que se usará al crear los nodos de proceso del grupo. Para ello, cree un objeto [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference). Puede especificar la imagen que se usará en una de las maneras siguientes:

* Si está utilizando una imagen personalizada, proporcione un identificador de recursos de Azure Resource Manager para la imagen de máquina virtual. El identificador de la imagen tiene un formato de ruta de acceso, como se muestra en el ejemplo siguiente:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Para obtener el identificador de imagen desde Azure Portal, abra **Todos los recursos**, seleccione la imagen personalizada y, en la sección **Introducción** de la hoja de imagen, copie la ruta de acceso en **Id. de recurso**.

* Si usa una imagen de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based), proporcione un grupo de parámetros que describan la imagen: el tipo de oferta, el editor, la SKU y la versión de la imagen, como se muestra en la [lista de imágenes de máquina virtual](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Configuración del contenedor de grupo de Batch

Un grupo de Batch es una colección de nodos de proceso en los que el servicio Batch ejecuta tareas de un trabajo. Cuando se crea el grupo, se le proporciona la configuración de máquina virtual para los nodos de proceso. El objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) contiene una referencia al objeto [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration). Para habilitar cargas de trabajo de contenedor en el grupo, especifique la configuración `ContainerConfiguration`. La configuración de máquina virtual también es donde se especifica la referencia de la imagen y el identificador de la SKU del agente de nodo de la imagen, como se muestra en los ejemplos siguientes.

Existen varias opciones para la creación de grupos. Puede crear un grupo con o sin imágenes de contenedor previamente capturadas. 

El proceso de extracción (o captura previa) le permite precargar imágenes de contenedor desde Docker Hub u otro registro de contenedor en Internet. La ventaja de capturar previamente las imágenes de contenedor es que, cuando las tareas comienzan a ejecutarse por primera vez, no tienen que esperar a que la imagen del contenedor se descargue. La configuración de contenedor extrae las imágenes de contenedor de las máquinas virtuales cuando se crea el grupo. Las tareas que se ejecutan en el grupo pueden entonces hacer referencia a la lista de las imágenes de contenedor y opciones de ejecución de contenedor.



### <a name="pool-without-prefetched-container-images"></a>Grupo sin imágenes de contenedor previamente capturadas

Para configurar el grupo sin imágenes de contenedor previamente capturadas, defina los objetos `ContainerConfiguration` y `VirtualMachineConfiguration` como se muestra en el ejemplo siguiente. Este ejemplo y los siguientes asumen que se está utilizando una imagen personalizada de Ubuntu 16.04 LTS con motor de Docker instalado.

```csharp
// Specify container configuration
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Captura previa de imágenes para la configuración de contenedor

Para la captura previa de las imágenes de contenedor en el grupo, agregue la lista de imágenes de contenedor (`containerImageNames`) a la `ContainerConfiguration` y asigne un nombre a la imagen de la lista. En el siguiente ejemplo se asume que está utilizando una imagen personalizada de Ubuntu 16.04 LTS, que ha cargado previamente una imagen de TensorFlow de [Docker Hub](https://hub.docker.com) y que ha iniciado TensorFlow en una tarea de inicio.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Captura previa de imágenes desde un registro de contenedor privado

También puede capturar previamente imágenes de contenedor mediante la autenticación a un servidor de registro de contenedor privado. En el siguiente ejemplo los objetos `ContainerConfiguration` y `VirtualMachineConfiguration` utilizan una imagen personalizada de Ubuntu 16.04 LTS y realizan una captura previa de una imagen de TensorFlow privada desde un registro de contenedor de Azure privado.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Configuración de contenedor para la tarea

Cuando configure las tareas que se van a ejecutar en los nodos de proceso, debe especificar opciones específicas de contenedor, como las opciones de ejecución de tareas, las imágenes que se van a utilizar y el registro.

Use la propiedad ContainerSettings de las clases de tarea para configurar opciones específicas de contenedor. Esta configuración se define por la clase [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

Si ejecuta tareas en imágenes de contenedor, la [tarea en la nube](/dotnet/api/microsoft.azure.batch.cloudtask) y la [tarea del administrador de trabajos](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) requieren la configuración del contenedor. Sin embargo, la [tarea de inicio](/dotnet/api/microsoft.azure.batch.starttask), la [tarea de preparación de trabajos](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) y la [tarea de liberación de trabajos](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) no requieren configuración del contenedor (es decir, pueden ejecutarse en un contexto de contenedor o directamente en el nodo).

Al configurar las opciones de contenedor, todos los directorios recursivamente debajo de `AZ_BATCH_NODE_ROOT_DIR` (la raíz de los directorios de Azure Batch en el nodo) se asignan al contenedor, todas las variables de entorno de la tarea se asignan al contenedor y la línea de comandos de tarea se ejecuta en el contenedor.

El ejemplo de código de [Captura previa de imágenes para la configuración de contenedor](#prefetch-images-for-container-configuration) muestra cómo especificar una configuración de contenedor para una tarea de inicio. En el ejemplo de código siguiente se muestra cómo especificar la configuración del contenedor para una tarea en la nube:

```csharp
// Simple task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Pasos siguientes

* Para información general más detallada acerca de Batch, consulte [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md).

* Para más información sobre cómo instalar y usar Docker CE en Linux, consulte la documentación de [Docker](https://docs.docker.com/engine/installation/).

* Para más información sobre imágenes personalizadas, consulte [Uso de una imagen personalizada administrada para crear un grupo de máquinas virtuales](batch-custom-images.md).
