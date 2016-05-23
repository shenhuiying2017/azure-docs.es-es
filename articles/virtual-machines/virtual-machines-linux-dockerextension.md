<properties
   pageTitle="Información sobre la extensión de VM de Docker en Azure | Microsoft Azure"
   description="Información sobre el uso de la extensión de VM de Docker para realizar una implementación rápida y segura de un entorno de Docker en Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/04/2016"
   ms.author="iainfou"/>

# Uso de la extensión de VM de Docker para implementar el entorno

Docker es una conocida plataforma de creación de imágenes y administración de contenedores que le permite trabajar rápidamente con contenedores en Linux (y también Windows). Con Azure, tiene la flexibilidad de implementar Docker de algunas maneras diferentes según sus necesidades:

- Para crear rápidamente un prototipo de una aplicación o si ya conoce y usa Docker Machine, puede usar [el controlador de Azure de Docker Machine](./virtual-machines-linux-docker-machine.md) para implementar los hosts del docker en Azure.
- Para una implementación basada en una plantilla, puede utilizarse la extensión de máquina virtual de Docker para máquinas virtuales de Azure. Este enfoque puede integrarse con las implementaciones de plantilla de Azure Resource Manager e incluye todas las ventajas relacionadas como el acceso basado en rol, el diagnóstico y la configuración posterior a la implementación.
- También puede [implementar un clúster de Docker Swarm completo en los servicios de contenedor de Azure](../container-service/container-service-deployment.md) para implementaciones escalables y preparadas para la producción que aprovechen las herramientas de administración y programación adicionales que proporciona Swarm.

Este artículo se centra en el uso de plantillas de Resource Manager para administrar la extensión de VM de Docker en un entorno personalizado preparado para la producción que defina.

## Extensión de VM de Docker para Azure para implementaciones de plantilla

La extensión de máquina virtual de Docker para Azure permite instalar y configurar el demonio de Docker, el cliente de Docker y Docker Compose en la máquina virtual de Linux. También se puede utilizar la extensión para definir e implementar aplicaciones contenedoras, mediante Docker Compose. Mediante el uso de plantillas de Resource Manager, el entorno se puede volver a implementar de forma coherente. Se recomienda el uso de la extensión de máquina virtual de Docker para Azure para un desarrollador o entornos de producción más resistentes, ya que presenta algunos controles adicionales en comparación con el uso simplemente de Docker Machine o la creación del host del docker usted mismo.

Mediante Azure Resource Manager, puede crear e implementar plantillas que definan la estructura completa de su entorno, como los hosts de Docker, el almacenamiento, los controles de acceso basados en roles (RBAC), los diagnósticos, etc. También puede [obtener más información sobre Resource Manager](../resource-group-overview.md) y plantillas para entender mejor algunas de las ventajas. La ventaja de usar plantillas de Resource Manager en comparación con el simple uso de Docker Machine es que puede definir hosts de Docker adicionales, almacenamiento, controles de acceso, etc. y ser capaz de reproducir las implementaciones según sea necesario en el futuro.

## Implementación de una plantilla con la extensión de VM de Docker:

Vamos a usar una plantilla existente de inicio rápido para mostrar cómo implementar una VM de Ubuntu que tenga una extensión de VM de Docker instalada. Puede ver la plantilla aquí: [Implementación simple de una VM de Ubuntu con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)

Implemente la plantilla con la CLI de Azure. Para ello, especifique un nombre para el nuevo grupo de recursos (aquí `myDockerResourceGroup`) junto con el identificador URI de la plantilla:

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Responda a las indicaciones para asignar nombres a su cuenta de almacenamiento, el nombre DNS, el nombre de usuario, etc. y, a continuación, proporciónele algunos minutos para que finalice la implementación. Debería ver una salida similar a la siguiente:

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

## Implementación del primer contenedor nginx

Una vez que haya terminado la implementación, aplique SSH a su nuevo host de Docker mediante el nombre DNS que proporcionó durante la implementación. Las herramientas de Docker ya estarán instaladas, por lo que vamos a intentar ejecutar un contenedor nginx:

```
docker run -d -p 80:80 nginx
```

Debería ver una salida similar a la siguiente:

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

Examine el contenedor que se ejecuta en el host mediante `docker ps`:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   nostalgic_murdock
```

Abra un explorador web y escriba el nombre DNS especificado durante la implementación para ver el contenedor en acción:

![Ejecución del contenedor ngnix](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Para más información adicional sobre la extensión de máquina virtual de Docker como configurar el puerto TCP del demonio de Docker, configurar la seguridad e implementar contenedores mediante Docker Compose, consulte la [Azure Virtual Machine Extension for Docker GitHub project](https://github.com/Azure/azure-docker-extension/) (Extensión de máquina virtual de Azure para el proyecto Docker de GitHub).

## Referencia de plantilla de JSON de la extensión de VM de Docker

En este ejemplo se utiliza una plantilla de inicio rápido. Puede utilizar sus propias plantillas de Resource Manager existentes para instalar la extensión de VM de Docker a máquinas virtuales en la plantilla agregando lo siguiente al archivo de definición de JSON:

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

Puede encontrar un tutorial más detallado sobre el uso de plantillas de Resource Manager mediante la lectura de [Información general de Azure Resource Manager](../resource-group-overview.md).

## Pasos siguientes

Puede obtener más pasos detallados para las distintas opciones de implementación:

1. [Uso de una máquina de Docker con el controlador de Azure](./virtual-machines-linux-docker-machine.md)  
2. [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)](./virtual-machines-linux-classic-cli-use-docker.md)  
3. [Implementación de un clúster del servicio Contenedor de Azure](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0511_2016-->