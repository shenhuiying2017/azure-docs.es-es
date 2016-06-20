<properties
	pageTitle="Nodos de Linux en grupos del servicio de Lote de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo procesar las cargas de trabajo de proceso paralelas en grupos de máquinas virtuales de Linux en el servicio Lote de Azure."
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="na"
	ms.date="06/03/2016"
	ms.author="marsma" />

# Aprovisionamiento de nodos de proceso de Linux en grupos del servicio Lote de Azure

El servicio Lote de Azure le permite ejecutar cargas de trabajo de proceso paralelas en máquinas virtuales de Linux y Windows. Este artículo detalla cómo crear grupos de nodos de proceso de Linux en el servicio Lote mediante las bibliotecas de cliente de [Python de Lote][py_batch_package] y [.NET de Lote][api_net].

> [AZURE.NOTE] La compatibilidad con Linux en el servicio Lote está actualmente en vista previa. Pueden cambiar algunos aspectos de la característica aquí descrita antes de que tenga disponibilidad general. Los [paquetes de aplicación](batch-application-packages.md) **no se admiten actualmente** en los nodos de proceso de Linux.

## Configuración de la máquina virtual

Cuando crea un grupo de nodos de proceso en el servicio Lote, tiene dos opciones para seleccionar el sistema operativo y el tamaño de nodo: **Configuración de servicios en la nube** y **Configuración de máquina virtual**.

**Configuración de servicios en la nube** *solo* proporciona nodos de proceso de Windows. Los tamaños de nodos de proceso disponibles se muestran en [Tamaños de los servicios en la nube](../cloud-services/cloud-services-sizes-specs.md) y los sistemas operativos disponibles se enumeran en [Matriz de compatibilidad del SDK y versiones del SO invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Cuando se crea un grupo que contiene los nodos de servicios en la nube, debe especificar únicamente el tamaño del nodo y la "familia del SO" que se encuentran en estos artículos. Cuando se crean grupos de nodos de proceso de Windows, la mayoría de las veces se utilizan los servicios en la nube.

La **Configuración de máquina virtual** proporciona imágenes de Linux y Windows para los nodos de proceso. Los tamaños de nodos de proceso disponibles se muestran en [Tamaños de las máquinas virtuales en Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) y [Tamaños de las máquinas virtuales en Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Cuando se crea un grupo que contiene los nodos de configuración de máquina virtual, debe especificar no solo el tamaño de los nodos, sino también la **referencia de la imagen de máquina virtual** y el **SKU del agente de nodo** del servicio Lote que desea instalar en los nodos.

### Referencia de imagen de máquina virtual

El servicio Lote utiliza [conjuntos de escalas de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) de forma interna para proporcionar los nodos de proceso de Linux y las imágenes del sistema operativo para estas máquinas virtuales las proporciona el [Catálogo de máquinas virtuales de Azure Marketplace][vm_marketplace]. Al configurar una referencia de la imagen de máquina virtual, especifique las propiedades de una imagen de máquina virtual de Marketplace. Las propiedades siguientes son necesarias cuando se crea una referencia de la imagen de máquina virtual:

| **Propiedades de la referencia de la imagen** | **Ejemplo** |
| ----------------- | ------------------------ |
| Publicador | Canonical |
| Oferta | UbuntuServer |
| SKU | 14\.04.4-LTS |
| Versión | más reciente |

> [AZURE.TIP] Puede encontrar más información acerca de estas propiedades y cómo mostrar imágenes de Marketplace en [Navegación y selección de las imágenes de máquina virtual Linux en Azure con la CLI o Powershell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Tenga en cuenta que no todas las imágenes de Marketplace son actualmente compatibles con el servicio Lote, consulte [SKU del agente de nodo](#node-agent-sku) a continuación.

### SKU del agente de nodo

El agente de nodo del servicio Lote es un programa que se ejecuta en cada nodo del grupo y proporciona la interfaz de comando y control entre el nodo y el servicio. Hay diferentes implementaciones del agente de nodo, conocidas como SKU, para distintos sistemas operativos. Básicamente, al crear una configuración de máquina virtual, debe especificar primero la referencia de la imagen de máquina virtual y, a continuación, el agente de nodo que se va a instalar en la imagen. Normalmente, cada SKU del agente de nodo es compatible con varias imágenes de máquina virtual. Estos son algunos ejemplos de SKU del agente de nodo:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [AZURE.IMPORTANT] No todas las imágenes de máquinas virtuales disponibles en Marketplace son compatibles con los agentes de nodo del servicio Lote disponibles actualmente. Debe utilizar los SDK del servicio Lote para mostrar las SKU del agente de nodo disponibles y las imágenes de máquina virtual con las que son compatibles. Consulte la [Lista de imágenes de máquinas virtuales](#list-of-virtual-machine-images) a continuación para más información.

## Cree un grupo de Linux: Python de Lote

El fragmento de código siguiente muestra la creación de un grupo de nodos de proceso de servidor de Ubuntu mediante la [biblioteca de cliente del servicio Lote de Microsoft Azure para Python][py_batch_package]. Se puede encontrar documentación de referencia para el módulo de Python de Lote en[azure.batch package ][py_batch_docs] en la sección Lea los documentos.

En este fragmento de código, se va a crear una [ImageReference][py_imagereference] explícitamente, especificando cada una de sus propiedades (publicador, oferta, sku, versión). Se recomienda, no obstante, que en el código de producción use el método [list\_node\_agent\_skus][py_list_skus] para determinar y seleccionar entre las combinaciones disponibles de SKU del agente de nodo y las imágenes en tiempo de ejecución.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Tal y como se mencionó anteriormente, es recomendable que en lugar de crear una [ImageReference][py_imagereference] explícitamente, utilice el método [list\_node\_agent\_skus][py_list_skus] para seleccionar dinámicamente entre las combinaciones de imágenes de Marketplace y los agentes de nodo admitidas actualmente. El siguiente fragmento de código de Python muestra el uso de este método.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## Cree un grupo de Linux: .NET de Lote

El fragmento de código siguiente muestra la creación de un grupo de nodos de proceso de servidor de Ubuntu mediante la biblioteca de cliente de [.NET de Lote][nuget_batch_net]. Puede encontrar la [documentación de referencia de .NET de Lote][api_net] en MSDN.

El fragmento de código siguiente utiliza el método [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] para seleccionar entre la lista de combinaciones de imágenes de Marketplace y SKU del agente de nodo admitidas actualmente. Esta técnica es recomendable porque la lista de combinaciones admitidas puede cambiar de vez en cuando (normalmente se agregan más combinaciones admitidas).

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Aunque el fragmento de código anterior usa el método [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] para mostrar y seleccionar entre las combinaciones admitidas de imágenes y SKU del agente de nodo (recomendadas), también puede configurar una [ImageReference][net_imagereference] explícitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## Lista de imágenes de máquinas virtuales

La tabla siguiente enumera las imágenes de máquinas virtuales de Marketplace que son compatibles con los agentes de nodo del servicio Lote disponibles **en el momento de redactar este artículo**. Es importante tener en cuenta que esta lista no es definitiva, ya que se pueden agregar o quitar imágenes y agentes de nodo en cualquier momento. Se recomienda que los servicios y aplicaciones de Lote utilicen siempre los métodos [list\_node\_agent\_skus][py_list_skus] (Python) y [ListNodeAgentSkus][net_list_skus] (.NET de Lote) para determinar y seleccionar entre las SKU disponibles actualmente.

> [AZURE.WARNING] La siguiente lista puede cambiar en cualquier momento. Utilice siempre los métodos **list\_node\_agent\_SKU** disponibles en las API de Lote para mostrar y seleccionar entre las máquinas virtuales y SKU del agente de nodo disponibles al ejecutar los trabajos del servicio Lote.

| **Publicador** | **Oferta** | **SKU de imagen** | **Versión** | **Identificador de SKU del agente de nodo** |
| ------- | ------- | ------- | ------- | ------- |
| Canonical | UbuntuServer | 14\.04.0-LTS | más reciente | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.1-LTS | más reciente | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.2-LTS | más reciente | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.3-LTS | más reciente | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.4-LTS | más reciente | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 15\.10 | más reciente | batch.node.debian 8 |
| Canonical | UbuntuServer | 16\.04.0-LTS | más reciente | batch.node.ubuntu 16.04 |
| Credativ | Debian | 8 | más reciente | batch.node.debian 8 |
| OpenLogic | CentOS | 7\.0 | más reciente | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.1 | más reciente | batch.node.centos 7 |
| OpenLogic | CentOS | 7,2 | más reciente | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7\.1 | más reciente | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7\.0 | más reciente | batch.node.centos 7 |
| SUSE | SLES | 12 | más reciente | batch.node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | más reciente | batch.node.opensuse 42.1 |
| SUSE | SLES-HPC | 12 | más reciente | batch.node.opensuse 42.1 |
| SUSE | openSUSE | 13\.2 | más reciente | batch.node.opensuse 13.2 |
| SUSE | openSUSE-Leap | 42\.1 | más reciente | batch.node.opensuse 42.1 |
| Microsoft Windows Server | Windows Server | 2008-R2-SP1 | más reciente | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012 | más reciente | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012-R2 | más reciente | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | Windows-Server-Technical-Preview | más reciente | batch.node.windows amd64 |

## Conexión a los nodos de Linux

Durante el desarrollo o solución de problemas, es posible que necesite iniciar sesión en los nodos del grupo. A diferencia de los nodos de proceso de Windows, no puede utilizar el protocolo de escritorio remoto (RDP) para conectarse a los nodos de Linux. En su lugar, el servicio de Lote habilita el acceso SSH en cada nodo para la conexión remota.

El siguiente fragmento de código de Python crea un usuario en cada nodo de un grupo, lo cual es un requisito para la conexión remota y, a continuación, imprime la información de conexión de SSH para cada nodo.

```python
import getpass

# Specify the username and prompt for a password
username = "linuxuser"
password = getpass.getpass()

# Create the user that will be added to each node
# in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = client.compute_node.get_remote_login_settings(pool_id,
                                                          node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Este es el resultado del ejemplo de código anterior para un grupo que contiene cuatro nodos de Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Tenga en cuenta que, en lugar de una contraseña, puede especificar una clave pública SSH al crear un usuario en un nodo. En el SDK de Python, esto se lleva a cabo mediante el parámetro **ssh\_public\_key** en [ComputeNodeUser][py_computenodeuser], y en. NET, esto se realiza mediante la propiedad [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## Precios

El servicio Lote de Azure se basa en la tecnología de Servicios en la nube de Azure y en la de Máquinas virtuales de Azure. El propio servicio de Lote se ofrece sin costo, lo cual significa que se cobrará solo por los recursos de proceso utilizados por las soluciones del servicio. Cuando elige **Configuración de servicios en la nube** se le cobrará según la estructura de [precios de Servicios en la nube][cloud_services_pricing]. Cuando elige **Configuración de la máquina virtual** se le cobrará según la estructura de [precios de Máquinas virtuales][vm_pricing].

## Pasos siguientes

### Tutorial de Python de Lote

Para obtener un tutorial más completo sobre cómo trabajar con Lote mediante Python, consulte [Introducción al cliente Python de Lote de Azure](batch-python-tutorial.md). El [código de ejemplo][github_samples_pyclient] complementario incluye una función auxiliar, `get_vm_config_for_distro`, que muestra otra técnica para obtener una configuración de máquina virtual.

### Ejemplos de código de Python de lote

Extraiga los otros [ejemplos de código de Python][github_samples_py] del repositorio [azure-batch-samples][github_samples] en GitHub para obtener varios scripts que muestran cómo realizar operaciones comunes del servicio de Lote como crear grupos, trabajos, tareas y muchas más. El archivo [LÉAME][github_py_readme] que acompaña a los ejemplos de Python incluye detalles acerca de cómo instalar los paquetes necesarios.

### Foro de Batch

El [foro de Lote de Azure][forum] en MSDN es un lugar excelente para debatir y formular preguntas acerca del servicio. Lea los mensajes útiles publicados y envíe sus preguntas a medida que surjan mientras compila sus soluciones del servicio de Lote.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/es-ES/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nivel de paquetes de aplicación"

<!---HONumber=AcomDC_0608_2016-->