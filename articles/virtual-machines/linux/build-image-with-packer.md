---
title: "Creación de imágenes de VM de Azure Linux con Packer | Microsoft Docs"
description: "Aprenda a usar Packer para crear imágenes de máquinas virtuales Linux en Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: d548d3df209df2a9ae8fa3f8ee684190bc140175
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Uso de Packer para crear imágenes de máquinas virtuales Linux en Azure
Cada máquina virtual (VM) en Azure se crea a partir de una imagen que define la distribución de Linux y la versión del sistema operativo. Las imágenes pueden incluir configuraciones y aplicaciones preinstaladas. Azure Marketplace proporciona muchas imágenes propias y de terceros para los entornos de aplicaciones y distribuciones más comunes, pero también puede crear sus propias imágenes personalizadas adaptadas a sus necesidades. En este artículo se detalla cómo utilizar la herramienta de código abierto [Packer](https://www.packer.io/) para definir y crear imágenes personalizadas en Azure.


## <a name="create-azure-resource-group"></a>Creación del grupo de recursos de Azure
Durante el proceso de compilación, Packer crea recursos de Azure temporales mientras genera la máquina virtual de origen. Para capturar dicha máquina virtual para usarla como imagen, debe definir un grupo de recursos. La salida del proceso de compilación de Packer se almacena en este grupo de recursos.

Cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Creación de credenciales de Azure
Packer se autentica con Azure mediante una entidad de servicio. Las entidades de servicio de Azure son identidades de seguridad que pueden usarse con aplicaciones, servicios y herramientas de automatización como Packer. El usuario controla los permisos y los define con respecto a cuáles son las operaciones que la entidad de servicio puede realizar en Azure.

Cree una entidad de servicio con [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) y genere los credenciales que Packer necesita:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

A continuación puede ver un ejemplo del resultado de los comandos anteriores:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Para autenticarse en Azure, también necesita obtener el identificador de la suscripción de Azure con [az account show](/cli/azure/account#show):

```azurecli
az account show --query "{ subscription_id: id }"
```

El resultado de estos dos comandos se usa en el siguiente paso.


## <a name="define-packer-template"></a>Definición de la plantilla de Packer
Para crear imágenes, es preciso crear una plantilla en forma de archivo JSON. En la plantilla, se definen los generadores y aprovisionadores que realizan el proceso de creación real. Packer tiene un [aprovisionador para Azure](https://www.packer.io/docs/builders/azure.html) que permite definir los recursos de Azure, como las credenciales de la entidad de servicio creadas en el paso anterior.

Cree un archivo denominado *ubuntu.json* y pegue el siguiente contenido. Escriba sus propios valores para los siguientes elementos:

| .                           | Dónde se obtiene |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Primera línea de la salida de `az ad sp` create command - *appId* |
| *client_secret*                     | Segunda línea de la salida de `az ad sp` create command - *password* |
| *tenant_id*                         | Tercera línea de la salida de `az ad sp` create command - *tenant* |
| *subscription_id*                   | Salida del comando `az account show` |
| *managed_image_resource_group_name* | Nombre del grupo de recursos que creó en el primer paso |
| *managed_image_name*                | Nombre de la imagen de disco administrado que se crea |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Esta plantilla crea una imagen de Ubuntu 16.04 LTS, instala NGINX y desaprovisiona la máquina virtual.

> [!NOTE]
> Si expande esta plantilla para aprovisionar las credenciales del usuario, ajuste el comando aprovisionador que desaprovisiona el agente de Azure para que sea `-deprovision`, en lugar de `deprovision+user`.
> La marca `+user` quita todas las cuentas de usuario de la máquina virtual de origen.


## <a name="build-packer-image"></a>Creación de una imagen de Packer
Si Packer aún no está instalado en el equipo local, de compresor [siga las instrucciones de instalación de Packer](https://www.packer.io/docs/install/index.html).

Para generar la imagen, especifique el archivo de plantilla de Packer como sigue:

```bash
./packer build ubuntu.json
```

A continuación puede ver un ejemplo del resultado de los comandos anteriores:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer tarda unos minutos en crear la máquina virtual, ejecutar los aprovisionadores y limpiar la implementación.


## <a name="create-vm-from-azure-image"></a>Creación de una máquina virtual desde una imagen de Azure
Ya puede crear una máquina virtual a partir de la imagen con [az vm create](/cli/azure/vm#create). Especifique la imagen que ha creado con el parámetro `--image`. El siguiente ejemplo crea una máquina virtual llamada *myVM* a partir de *myPackerImage* y genera claves SSH, en caso de que no existan:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

La operación de creación de la máquina virtual tarda unos minutos. Cuando se haya creado la máquina virtual, anote el valor `publicIpAddress` que muestra la CLI de Azure. Esta dirección se usa para acceder al sitio de NGINX mediante un explorador web.

Para permitir que el tráfico web llegue a la máquina virtual, abra el puerto 80 desde Internet con el comando [az vm open-port](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Pruebas de la máquina virtual y de NGINX
Ahora puede abrir un explorador web y escribir `http://publicIpAddress` en la barra de direcciones. Proporcione su propia dirección IP pública obtenida del proceso de creación de la máquina virtual. La página predeterminada de NGINX es como la del ejemplo siguiente:

![Sitio NGINX predeterminado](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>pasos siguientes
En este ejemplo, ha utilizado Packer para crear una imagen de máquina virtual con NGINX instalado. Esta imagen se puede usar junto con los flujos de trabajo de la implementación existentes, como implementar la aplicación en las máquinas virtuales que se crean a partir de la imagen con Ansible, Chef o Puppet.

Para ver más plantillas de Packer de ejemplo para otras distribuciones de Linux, consulte [este repositorio de GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).