---
title: "Uso de la extensión de máquina virtual de Docker para Azure | Microsoft Docs"
description: "Información sobre cómo usar la extensión de máquina virtual de Docker para implementar de forma rápida y segura un entorno de Docker en Azure mediante las plantillas de Resource Manager y la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 0cef78edaeec9d45aa733b1912d82d5a058ba289
ms.contentlocale: es-es
ms.lasthandoff: 09/27/2017

---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Creación de un entorno de Docker para Azure mediante la extensión de máquina virtual de Docker
Docker es una conocida plataforma de creación de imágenes y administración de contenedores que permite trabajar rápidamente con contenedores en Linux. En Azure, hay diversas maneras de implementar Docker según sus necesidades. Este artículo se centra en el uso de la extensión de máquina virtual de Docker y las plantillas de Azure Resource Manager con la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](dockerextension-nodejs.md).

## <a name="azure-docker-vm-extension-overview"></a>Introducción a la extensión de máquina virtual de Docker para Azure
La extensión de máquina virtual de Docker para Azure permite instalar y configurar el demonio de Docker, el cliente de Docker y Docker Compose en la máquina virtual (VM) Linux. Con la extensión de máquina virtual de Docker para Azure, dispone de más control y características que si solo usa Docker Machine o si crea el host de Docker por su cuenta. Gracias a estas características adicionales, como [Docker Compose](https://docs.docker.com/compose/overview/), la extensión de máquina virtual de Docker para Azure es adecuada para entornos de producción o desarrollo más sólidos.

Para más información sobre los diferentes métodos de implementación, incluido el uso de Docker Machine e instancias de Azure Container Service, consulte los artículos siguientes:

* Para crear rápidamente el prototipo de una aplicación, puede crear un solo host de Docker con [Docker Machine](docker-machine.md).
* Para crear entornos escalables listos para la producción que añadan herramientas de administración y programación, puede implementar un [clúster de Docker Swarm en instancias de Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implementación de una plantilla con la extensión de máquina virtual de Docker para Azure
Aquí se va a usar una plantilla de inicio rápido existente para crear una máquina virtual Ubuntu que use la extensión de máquina virtual de Docker para Azure para instalar y configurar el host de Docker. Puede ver la plantilla aquí: [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)(Implementación simple de una máquina virtual de Ubuntu con Docker). Necesita tener instalada la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e iniciar sesión en una cuenta de Azure con [az login](/cli/azure/#login).

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

A continuación, implemente una VM con [az group deployment create](/cli/azure/group/deployment#create), que incluye la extensión de VM de Docker para Azure de [esta plantilla de Azure Resource Manager en GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Proporcione sus propios valores únicos para *newStorageAccountName*, *adminUsername*, *adminPassword* y *dnsNameForPublicIP* de la manera siguiente:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

La implementación tarda unos minutos en finalizar. Una vez finalizada la implementación, [vaya al paso siguiente](#deploy-your-first-nginx-container) para SSH en la VM. 

Opcionalmente, en lugar de devolver el control al símbolo del sistema y permitir que la implementación continúe en segundo plano, agregue la marca `--no-wait` al comando anterior. Este proceso permite realizar otro trabajo en la CLI mientras continúa la implementación durante unos minutos. 

Puede ver detalles sobre el estado del host de Docker con [az vm show](/cli/azure/vm#show). En el ejemplo siguiente, se comprueba el estado de la máquina virtual denominada *myDockerVM* (se trata del nombre predeterminado de la plantilla; no lo cambie) del grupo de recursos denominado *myResourceGroup*.

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

Cuando este comando devuelve un estado *correcto*, ha terminado la implementación y puede implementar SSH en la máquina virtual en el paso siguiente.

## <a name="deploy-your-first-nginx-container"></a>Implementación del primer contenedor NGINX
Para ver detalles de la máquina virtual, incluido el nombre DNS, use [az vm show](/cli/azure/vm#show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH al nuevo host Docker. Proporcione su propio nombre DNS de la siguiente manera:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Una vez que haya iniciado sesión en el host de Docker, se va a ejecutar un contenedor NGINX:

```bash
sudo docker run -d -p 80:80 nginx
```

La salida se parece al siguiente ejemplo, donde se descarga la imagen NGINX y se inicia un contenedor:

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

La salida es similar al ejemplo siguiente, que muestra que se está ejecutando el contenedor NGINX y se está efectuando el reenvío a los puertos TCP 80 y 443:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver el contenedor en acción, abra un explorador web y escriba el nombre DNS del host de Docker:

![Ejecución del contenedor ngnix](./media/dockerextension/nginxrunning.png)

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
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Puede encontrar un tutorial más detallado sobre el uso de plantillas de Resource Manager en [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Pasos siguientes
Es posible que desee [configurar el puerto TCP del demonio de Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), comprender la [seguridad de Docker](https://docs.docker.com/engine/security/security/) o implementar contenedores mediante [Docker Compose](https://docs.docker.com/compose/overview/). Para más información sobre la extensión de máquina virtual de Docker para Azure en sí, consulte el [proyecto de GitHub](https://github.com/Azure/azure-docker-extension/).

Lea más información sobre las opciones de implementación adicionales de Docker en Azure:

* [Uso de una máquina de Docker con el controlador de Azure](docker-machine.md)  
* [Introducción a Docker y Compose para definir y ejecutar una aplicación de contenedores múltiples en una máquina virtual de Azure](docker-compose-quickstart.md).
* [Implementación de un clúster del servicio Contenedor de Azure](../../container-service/dcos-swarm/container-service-deployment.md)


