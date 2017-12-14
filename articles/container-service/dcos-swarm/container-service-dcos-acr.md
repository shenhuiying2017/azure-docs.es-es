---
title: "Usar ACR con un clúster DC/OS de Azure"
description: "Use un Azure Container Registry con un clúster DC/OS en Azure Container Service"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 4a3213c28f24e9d1dfc309c6d34771ccc062dae4
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Use ACR con un clúster de DC/OS para implementar la aplicación

En este artículo, exploraremos cómo usar Azure Container Registry con un clúster de DC/OS. Usar ACR le permite almacenar y administrar imágenes del contenedor de forma privada. En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Implementación de Azure Container Registry (en caso de ser necesaria)
> * Configuración de la autenticación de ACR en un clúster de DC/OS
> * Carga de una imagen en Azure Container Registry
> * Ejecución de una imagen de contenedor de Azure Container Registry

Necesita un clúster de DC/OS de ACS para completar los pasos de este tutorial. Si es necesario, este [script de ejemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) puede crear uno.

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Implementación de Azure Container Registry

Si es necesario, cree un Azure Container Registry con el comando [az acr create](/cli/azure/acr#create). 

En el ejemplo siguiente, se crea un registro con un nombre generado de forma aleatoria. El registro también se configura con una cuenta de administrador mediante el argumento `--admin-enabled`.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

Una vez creado el registro, la CLI de Azure da como resultado datos similares a los que aparecen a continuación. Anote `name` y `loginServer`, ya que se usarán en pasos posteriores.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Obtenga las credenciales del registro de contenedor mediante el comando [az acr credential show](/cli/azure/acr/credential). Sustituya el `--name` con el que anotó en el último paso. Tome nota de una contraseña, ya que es necesaria más adelante.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Para obtener más información sobre Azure Container Registry, consulte [Introducción a los registros privados del contenedor Docker](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Administración de la autenticación de ACR

La manera convencional de insertar y extraer una imagen de un registro privado es, en primer lugar, autenticarse en el registro. Para ello, usaría el comando `docker login` en cualquier cliente que necesite para tener acceso al registro privado. Debido a que el clúster de DC/OS puede contener muchos nodos, los que deben autenticarse con ACR, es útil automatizar este proceso en cada nodo. 

### <a name="create-shared-storage"></a>Creación de almacenamiento compartido

Este proceso utiliza un recurso compartido de archivos de Azure que se montó en cada nodo del clúster. Si aún no se ha configurado el almacenamiento compartido, consulte [Configuración de un recurso compartido de archivos dentro de un clúster de DC/OS](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Configuración de la autenticación de ACR

En primer lugar, obtenga el FQDN del patrón de DC/OS y guárdelo en una variable.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Cree una conexión SSH con el patrón (o el primer patrón) del clúster basado en DC/OS. Actualice el nombre de usuario si se utilizó un valor no predeterminado al crear el clúster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Ejecute el siguiente comando para iniciar sesión en Azure Container Registry. Reemplace el `--username` con el nombre del registro del contenedor y la `--password` con una de las contraseñas proporcionadas. Reemplace el último argumento *mycontainerregistry.azurecr.io* en el ejemplo con el nombre loginServer del registro de contenedor. 

Este comando almacena los valores de autenticación localmente en la ruta de acceso `~/.docker`.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Cree un archivo comprimido que contenga los valores de autenticación del contenedor de registro.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Copie este archivo en el almacenamiento compartido del clúster. Este paso hace que el archivo esté disponible en todos los nodos del clúster de DC/OS.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Carga de la imagen a ACR

Ahora desde una máquina de desarrollo, o cualquier otro sistema con Docker instalado, cree una imagen y cárguela en Azure Container Registry.

Cree un contenedor a partir de la imagen de Ubuntu.

```azurecli-interactive
docker run ubuntu --name base-image
```

Ahora capture el contenedor en una nueva imagen. El nombre de la imagen debe incluir el nombre `loginServer` del registro de contenedor con un formato de `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Inicie sesión en Azure Container Registry. Reemplace el nombre con el nombre loginServer, el nombre de usuario con el nombre del registro de contenedor, y la contraseña con una de las contraseñas proporcionadas.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Finalmente, cargue la imagen en el registro de ACR. En este ejemplo, se carga una imagen llamada dcos-demo.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Ejecución de una imagen desde ACR

Para utilizar una imagen del registro ACR, cree un archivo de nombres *acrDemo.json* y copie el siguiente texto en él. Reemplace el nombre de la imagen con el nombre loginServer del contenedor de registro y el nombre de la imagen. Por ejemplo, `loginServer/imageName`. Tome nota de la propiedad `uris`. Esta propiedad contiene la ubicación del archivo de autenticación del registro de contenedor, que en este caso es el recurso compartido de archivos de Azure que está montado en cada nodo del clúster de DC/OS.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Implemente la aplicación con la CLI de DC/OC.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, configuró DC/OS para usar Azure Container Registry, incluidas las siguientes tareas:

> [!div class="checklist"]
> * Implementación de Azure Container Registry (en caso de ser necesaria)
> * Configuración de la autenticación de ACR en un clúster de DC/OS
> * Carga de una imagen en Azure Container Registry
> * Ejecución de una imagen de contenedor de Azure Container Registry
