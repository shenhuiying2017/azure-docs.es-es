---
title: "Uso de la extensión de máquina virtual de Docker para Azure | Microsoft Docs"
description: "Aprenda a usar la extensión de máquina virtual de Docker para implementar de forma rápida y segura un entorno de Docker en Azure mediante plantillas de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: e97981118cb8f2c4c2821ed9db50095f2385342c


---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Creación de un entorno de Docker para Azure mediante la extensión de máquina virtual de Docker
Docker es una conocida plataforma de creación de imágenes y administración de contenedores que le permite trabajar rápidamente con contenedores en Linux (y también Windows). En Azure, hay diversas maneras de implementar Docker según sus necesidades. Este artículo se centra en el uso de la extensión de máquina virtual de Docker y las plantillas de Azure Resource Manager. 

Para más información sobre los diferentes métodos de implementación, incluido el uso de Docker Machine e instancias de Azure Container Service, consulte los artículos siguientes:

* Para crear rápidamente el prototipo de una aplicación, puede crear un solo host de Docker con [Docker Machine](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para entornos más grandes y estables, puede usar la extensión de máquina virtual de Docker para Azure, también compatible con [Docker Compose](https://docs.docker.com/compose/overview/), para generar implementaciones de contenedor coherentes. En este artículo, se ofrecen más detalles sobre la extensión de máquina virtual de Docker para Azure.
* Para crear entornos escalables listos para la producción que añadan herramientas de administración y programación, puede implementar un [clúster de Docker Swarm en instancias de Azure Container Service](../container-service/container-service-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Introducción a la extensión de máquina virtual de Docker para Azure
La extensión de máquina virtual de Docker para Azure permite instalar y configurar el demonio de Docker, el cliente de Docker y Docker Compose en la máquina virtual (VM) Linux. Con la extensión de máquina virtual de Docker para Azure, dispone de más control y características que si solo usa Docker Machine o si crea el host de Docker por su cuenta. Gracias a estas características adicionales, como [Docker Compose](https://docs.docker.com/compose/overview/), la extensión de máquina virtual de Docker para Azure es adecuada para entornos de producción o desarrollo más sólidos.

Las plantillas de Azure Resource Manager definen la estructura completa de su entorno. Las plantillas permiten crear y configurar recursos tales como las máquinas virtuales del host de Docker, el almacenamiento, los controles de acceso basado en rol (RBAC) y los diagnósticos. Además, puede volver a usarlas para crear más implementaciones de manera coherente. Para más información sobre Azure Resource Manager, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implementación de una plantilla con la extensión de máquina virtual de Docker para Azure
Aquí se va a usar una plantilla de inicio rápido existente para crear una máquina virtual Ubuntu que use la extensión de máquina virtual de Docker para Azure para instalar y configurar el host de Docker. Puede ver la plantilla aquí: [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)(Implementación simple de una máquina virtual de Ubuntu con Docker). 

Necesita tener instalada la [CLI de Azure más reciente](../xplat-cli-install.md) y haber iniciado sesión con el modo Resource Manager de la forma siguiente:

```azurecli
azure config mode arm
```

Implemente la plantilla mediante la CLI de Azure y especifique el URI de la plantilla. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `WestUS`. Use el nombre y la ubicación de su grupo de recursos como sigue:

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Responda a los mensajes para asignar nombre a la cuenta de almacenamiento y proporcionar un nombre de usuario y una contraseña, seguidos de un nombre DNS. La salida es similar a la del ejemplo siguiente:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

La CLI de Azure regresa al símbolo del sistema tras unos segundos, pero la extensión de máquina virtual de Docker para Azure sigue creando y configurando el host de Docker. La implementación tarda unos minutos en finalizar. Puede ver detalles sobre el estado del host de Docker con el comando `azure vm show`.

En el ejemplo siguiente, se comprueba el estado de la máquina virtual denominada `myDockerVM` (se trata del nombre predeterminado de la plantilla; no lo cambie) del grupo de recursos denominado `myResourceGroup`. Escriba el nombre del grupo de recursos que creó en el paso anterior:

```azurecli
azure vm show -g myResourceGroup -n myDockerVM
```

La salida del comando `azure vm show` es similar al ejemplo siguiente:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Cerca de la parte superior de la salida, verá el elemento `ProvisioningState` de la máquina virtual. Cuando se muestra `Succeeded`, la implementación ha finalizado y puede conectarse mediante SSH a la máquina virtual.

Hacia el final de la salida, `FQDN` muestra el nombre de dominio completo (FQDN) del host de Docker. Este FQDN sirve para conectarse mediante SSH al host de Docker en los pasos que quedan.

## <a name="deploy-your-first-nginx-container"></a>Implementación del primer contenedor nginx
Una vez finalizada la implementación, conéctese mediante SSH al nuevo host de Docker desde el equipo local. Escriba su nombre de usuario y el FQDN como sigue:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Una vez que haya iniciado sesión en el host de Docker, se va a ejecutar un contenedor nginx:

```bash
sudo docker run -d -p 80:80 nginx
```

La salida se parece al siguiente ejemplo, donde se descarga la imagen nginx y se inicia un contenedor:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Compruebe el estado de los contenedores que se ejecutan en el host de Docker como se indica a continuación:

```bash
sudo docker ps
```

La salida es similar al ejemplo siguiente, que muestra que se está ejecutando el contenedor nginx y se está efectuando el reenvío a los puertos TCP 80 y 443:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver el contenedor en acción, abra un explorador web y escriba el nombre FQDN del host de Docker:

![Ejecución del contenedor ngnix](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Referencia sobre la plantilla de la extensión de máquina virtual de Docker para Azure
En el ejemplo anterior, se usa una plantilla de inicio rápido existente. También puede implementar la extensión de máquina virtual de Docker para Azure con sus propias plantillas de Resource Manager. Para hacerlo, agregue lo siguiente a sus plantillas de Resource Manager y defina el elemento `vmName` de la máquina virtual correctamente:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Puede encontrar un tutorial más detallado sobre el uso de plantillas de Resource Manager en [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Pasos siguientes
Es posible que desee [configurar el puerto TCP del demonio de Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), comprender la [seguridad de Docker](https://docs.docker.com/engine/security/security/) o implementar contenedores mediante [Docker Compose](https://docs.docker.com/compose/overview/). Para más información sobre la extensión de máquina virtual de Docker para Azure en sí, consulte el [proyecto de GitHub](https://github.com/Azure/azure-docker-extension/).

Lea más información sobre las opciones de implementación adicionales de Docker en Azure:

* [Uso de una máquina de Docker con el controlador de Azure](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  
* [Introducción a Docker y Compose para definir y ejecutar una aplicación de contenedores múltiples en una máquina virtual de Azure](virtual-machines-linux-docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Implementación de un clúster del servicio Contenedor de Azure](../container-service/container-service-deployment.md)




<!--HONumber=Nov16_HO3-->


