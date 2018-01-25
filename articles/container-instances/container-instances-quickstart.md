---
title: "Guía de inicio rápido: creación del primer contenedor de Azure Container Instances"
description: Implemente y empiece a usar Azure Container Instances
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 4c7f48c993d66dd79538fd73ccaed1355c2e8cdd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Creación del primer contenedor en Azure Container Instances
Con Azure Container Instances es muy fácil crear y administrar contenedores de Docker en Azure sin tener que aprovisionar máquinas virtuales ni recurrir a un servicio de nivel superior. En esta guía de inicio rápido, crearemos un contenedor en Azure y lo expondremos en Internet con una dirección IP pública. Esta operación se completa en un solo comando. En pocos segundos, veremos lo siguiente en el explorador:

![Aplicación implementada mediante Azure Container Instances vista en el explorador][aci-app-browser]

Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita][azure-account] antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Puede usar Azure Cloud Shell o una instalación local de la CLI de Azure para completar esta guía de inicio rápido. Si decide instalar y usar la CLI en un entorno local, para esta guía de inicio rápido es preciso que ejecute la versión 2.0.21 de la CLI de Azure o una versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install Azure CLI 2.0][azure-cli-install] (Instalación de la CLI de Azure 2.0).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Azure Container Instances son recursos de Azure y se deben colocar en un grupo de recursos de Azure, una colección lógica en la que se implementan y administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create][az-group-create].

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Crear un contenedor

Para crear un contenedor debe especificar un nombre, una imagen de Docker y un grupo de recursos de Azure para el comando [az container create][az-container-create]. Dicho contenedor se puede exponer opcionalmente a Internet con una dirección IP pública. En esta guía de inicio rápido, se implementa un contenedor que hospeda una aplicación web pequeña escrita en [Node.js][node-js].

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --ip-address public --ports 80
```

En pocos segundos, debería obtener una respuesta a su solicitud. Inicialmente, el contenedor está en un estado de **creación**, pero debería iniciarse en pocos segundos. Para comprobar el estado, use el comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

En la parte inferior de la salida, verá el estado de aprovisionamiento del contenedor y su dirección IP:

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "location:": "eastus",
    "name": "mycontainer",
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Una vez que el contenedor pasa al estado **Correcto**, se puede acceder a él desde el explorador con la dirección IP proporcionada.

![Aplicación implementada mediante Azure Container Instances vista en el explorador][aci-app-browser]

## <a name="pull-the-container-logs"></a>Extracción de los registros del contenedor

Puede extraer los registros del contenedor que creó con el comando [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Salida:

```bash
listening on port 80
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://52.224.178.107/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
```

## <a name="delete-the-container"></a>Eliminación del contenedor

Cuando haya terminado con el contenedor, puede eliminarlo con el comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Para comprobar que se ha eliminado el contenedor, ejecute el comando [az container list](/cli/azure/container#az_container_list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

El contenedor **mycontainer** no debe aparecer en la salida del comando. Si no cuenta con ningún otro contenedor en el grupo de recursos, no se muestra ninguna salida.

## <a name="next-steps"></a>pasos siguientes

Todo el código del contenedor utilizado en esta guía de inicio rápido está disponible [en GitHub][app-github-repo], junto con su Dockerfile. Si desea intentar compilarlo usted mismo e implementarlo en Azure Container Instances mediante Azure Container Registry, vaya al tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Para probar opciones para ejecutar contenedores en un sistema de orquestación en Azure, consulte los inicios rápidos de [Service Fabric][service-fabric] o [Azure Container Service (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
