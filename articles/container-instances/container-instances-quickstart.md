---
title: "Guía de inicio rápido: creación del primer contenedor de Azure Container Instances"
description: Implemente y empiece a usar Azure Container Instances
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 15e6127d419bb41f1b146aff147c43dce2233d8d
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Creación del primer contenedor en Azure Container Instances

Con Azure Container Instances es muy fácil crear y administrar contenedores de Docker en Azure sin tener que aprovisionar máquinas virtuales ni recurrir a un servicio de nivel superior. En esta guía de inicio rápido, crearemos un contenedor en Azure y lo expondremos en Internet con una dirección IP pública. Esta operación se completa en un solo comando. En pocos segundos, veremos lo siguiente en el explorador:

![Aplicación implementada mediante Azure Container Instances vista en el explorador][aci-app-browser]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.12 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Azure Container Instances son recursos de Azure y se deben colocar en un grupo de recursos de Azure, una colección lógica en la que se implementan y administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create][az-group-create].

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Crear un contenedor

Para crear un contenedor debe especificar un nombre, una imagen de Docker y un grupo de recursos de Azure para el comando [az container create][az-container-create]. Dicho contenedor se puede exponer opcionalmente a Internet con una dirección IP pública. En este caso, se va a usar un contenedor que hospeda una aplicación web muy simple escrita en [Node.js](http://nodejs.org).

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public
```

En pocos segundos, debería obtener una respuesta a su solicitud. Inicialmente, el contenedor estará en un estado de **creación**, pero debería comenzar en pocos segundos. Para comprobar el estado, use el comando [az container show][az-container-show]:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

En la parte inferior de la salida, verá el estado de aprovisionamiento del contenedor y su dirección IP:

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Una vez que el contenedor pasa al estado **Correcto**, se puede acceder a él desde el explorador con la dirección IP proporcionada.

![Aplicación implementada mediante Azure Container Instances vista en el explorador][aci-app-browser]

## <a name="pull-the-container-logs"></a>Extracción de los registros del contenedor

Puede extraer los registros del contenedor que creó con el comando [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Salida:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>Eliminación del contenedor

Cuando haya terminado con el contenedor, puede eliminarlo con el comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Todo el código del contenedor utilizado en esta guía de inicio rápido está disponible [en GitHub][app-github-repo], junto con su Dockerfile. Si desea intentar compilarlo usted mismo e implementarlo en Azure Container Instances mediante Azure Container Registry, vaya al tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Para probar opciones para ejecutar contenedores en un sistema de orquestación en Azure, consulte los inicios rápidos de [Service Fabric][service-fabric] o [Azure Container Service (AKS)][container-service].  

<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
[container-service]: ../aks/kubernetes-walkthrough.md


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png