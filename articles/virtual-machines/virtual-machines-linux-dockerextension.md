---
title: Información sobre la extensión de máquina virtual de Docker en Azure | Microsoft Docs
description: Información sobre el uso de la extensión de VM de Docker para realizar una implementación rápida y segura de un entorno de Docker en Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/10/2016
ms.author: iainfou

---
# <a name="using-the-docker-vm-extension-to-deploy-your-environment"></a>Uso de la extensión de VM de Docker para implementar el entorno
Docker es una conocida plataforma de creación de imágenes y administración de contenedores que le permite trabajar rápidamente con contenedores en Linux (y también Windows). Con Azure, tiene la flexibilidad de implementar Docker de algunas maneras diferentes según sus necesidades:

* Para crear rápidamente un prototipo de una aplicación, puede usar [el controlador de Azure de Docker Machine](virtual-machines-linux-docker-machine.md) para implementar los hosts del docker en Azure.
* Se utiliza la extensión de máquina virtual de Docker para máquinas virtuales de Azure para implementaciones basadas en una plantilla. Este enfoque puede integrarse con las implementaciones de plantilla de Azure Resource Manager e incluye todas las ventajas relacionadas como el acceso basado en roles, el diagnóstico y la configuración posterior a la implementación.
* La extensión de máquina virtual de Docker también admite Docker Compose. Docker Compose utiliza un archivo YAML declarativo con el fin de tomar una aplicación modelada por el desarrollador en cualquier entorno y generar una implementación coherente.
* También puede [implementar un clúster de Docker Swarm completo en los servicios de contenedor de Azure](../container-service/container-service-deployment.md) para realizar implementaciones escalables y preparadas para la producción que usan las herramientas de administración y programación adicionales que proporciona Swarm.

Este artículo se centra en el uso de plantillas de Resource Manager para administrar la extensión de máquina virtual de Docker en un entorno personalizado preparado para la producción que defina.

## <a name="azure-docker-vm-extension-for-template-deployments"></a>Extensión de VM de Docker para Azure para implementaciones de plantilla
La extensión de máquina virtual de Docker para Azure permite instalar y configurar el demonio de Docker, el cliente de Docker y Docker Compose en la máquina virtual de Linux. También se utiliza la extensión para definir e implementar aplicaciones contenedoras, mediante Docker Compose. Cuenta con algunos controles adicionales en comparación con el uso de Docker Machine o la creación del host del docker usted mismo, con lo que es más adecuado para entornos de producción o de desarrollador más resistentes.

Mediante Azure Resource Manager, puede crear e implementar plantillas que definen la estructura completa del entorno. Las plantillas permiten definir hosts de Docker, almacenamiento, controles de acceso basado en roles (RBAC), diagnósticos, etc. Puede [obtener más información sobre Resource Manager](../resource-group-overview.md) y las plantillas para entender mejor algunas de las ventajas. Mediante las plantillas de Resource Manager, también podrá reproducir en el futuro las implementaciones según sea necesario.

## <a name="deploy-a-template-with-the-docker-vm-extension:"></a>Implementación de una plantilla con la extensión de VM de Docker:
Vamos a usar una plantilla existente de inicio rápido para mostrar cómo implementar una máquina virtual de Ubuntu que tenga una extensión de máquina virtual de Docker instalada. Puede ver la plantilla aquí: [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)(Implementación simple de una máquina virtual de Ubuntu con Docker). También necesita la [versión más reciente de la CLI de Azure](../xplat-cli-install.md) en modo de Resource Manager (`azure config mode arm`).

Implemente la plantilla con la CLI de Azure. Para ello, especifique un nombre para el nuevo grupo de recursos (aquí `myDockerResourceGroup`) junto con el identificador URI de la plantilla:

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Responda a los mensajes para asignar nombres a su cuenta de almacenamiento, nombre de usuario y contraseña, y nombre DNS. Debería ver una salida similar a la del siguiente ejemplo:

```
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

La CLI de Azure le redirige al símbolo del sistema después de unos pocos segundos, pero, en segundo plano, la plantilla se está implementando en el grupo de recursos que ha creado. Espere unos minutos a que finalice la implementación antes de tratar de conectarse mediante SSH a la máquina virtual.

Puede obtener información de la implementación y el nombre DNS de la máquina virtual mediante el comando `azure vm show` . En el ejemplo siguiente, reemplace `myResourceGroup` por el nombre que especificó en el paso anterior (el nombre de la máquina virtual predeterminado de la plantilla es `myDockerVM`, de modo que no lo cambie):

```bash
azure vm show -g myResourceGroup -n myDockerVM
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/mydockerresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
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

Hacia el final de la salida, `FQDN` muestra el nombre de dominio completo basado en el nombre DNS proporcionado y la ubicación seleccionada. Este FQDN es lo que se utiliza para conectarse mediante SSH a la máquina virtual en los pasos restantes.

## <a name="deploy-your-first-nginx-container"></a>Implementación del primer contenedor nginx
Una vez que haya terminado la implementación, aplique SSH a su nuevo host de Docker mediante el nombre DNS que proporcionó durante la implementación:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Vamos a intentar ejecutar un contenedor de nginx:

```
sudo docker run -d -p 80:80 nginx
```

Debería ver una salida similar a la del siguiente ejemplo:

```
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

Examine el contenedor que se ejecuta en el host mediante `sudo docker ps`:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver el contenedor en acción, abra un explorador web y escriba el nombre DNS especificado durante la implementación:

![Ejecución del contenedor ngnix](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Puede configurar el puerto TCP del demonio de Docker, la seguridad, o bien implementar contenedores mediante Docker Compose. Consulte la [extensión de máquina virtual de Azure para el proyecto de GitHub de Docker](https://github.com/Azure/azure-docker-extension/)para obtener más información.

## <a name="docker-vm-extension-json-template-reference"></a>Referencia de plantilla de JSON de la extensión de VM de Docker
En este ejemplo se utiliza una plantilla de inicio rápido. Para implementar la extensión de máquina virtual de Azure Docker con sus propias plantillas de Resource Manager, agregue el siguiente código JSON:

```
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

Puede encontrar un tutorial más detallado sobre el uso de plantillas de Azure Resource Manager en [Información general de Azure Resource Manager](../resource-group-overview.md)

## <a name="next-steps"></a>Pasos siguientes
Puede obtener más pasos detallados para las distintas opciones de implementación:

1. [Uso de una máquina de Docker con el controlador de Azure](virtual-machines-linux-docker-machine.md)  
2. [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)](virtual-machines-linux-classic-cli-use-docker.md)  
3. [Introducción a Docker y Compose para definir y ejecutar una aplicación de contenedores múltiples en una máquina virtual de Azure](virtual-machines-linux-docker-compose-quickstart.md).
4. [Implementación de un clúster del servicio Contenedor de Azure](../container-service/container-service-deployment.md)

<!--HONumber=Oct16_HO2-->


