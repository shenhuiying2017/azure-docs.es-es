---
title: "Tutorial de inicio rápido: clúster de Azure Docker Swarm para Linux | Microsoft Docs"
description: "Aprenda rápidamente a crear un clúster de Docker Swarm para contenedores de Linux en Azure Container Service con la CLI de Azure."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, Docker, Swarm
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 876135d62d548e155f4ebefd8bbd9d9cca8b87d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-docker-swarm-cluster"></a>Implementación del clúster de Docker Swarm

En este tutorial de inicio rápido, se implementa un clúster de Docker Swarm mediante la CLI de Azure. A continuación, se ejecuta e implementa en el clúster una aplicación de varios contenedores que consta de un front-end web y una instancia de Redis. Una vez finalizado el proceso, la aplicación es accesible a través de Internet.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar este tutorial de inicio rápido, es necesario ejecutar la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un grupo lógico en el que se implementan y se administran los recursos de Azure.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Salida:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Creación de un clúster de Docker Swarm

Cree un clúster de Docker Swarm en Azure Container Service con el comando [az acs create](/cli/azure/acs#create). 

En el ejemplo siguiente, se crea un clúster denominado *mySwarmCluster* con un nodo maestro de Linux y tres nodos de agente de Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

En algunos casos, como con las versiones de evaluación de tiempo limitado, una suscripción a Azure tiene un acceso limitado a los recursos de Azure. Si se produce un error en la implementación debido a que los núcleos disponibles son limitados, reduzca el número de agentes predeterminado agregando `--agent-count 1` al comando [az acs create](/cli/azure/acs#create). 

Después de varios minutos, el comando se completa y devuelve información en formato json sobre el clúster.

## <a name="connect-to-the-cluster"></a>Conexión al clúster

A lo largo de este tutorial de inicio rápido, necesitará la dirección IP del maestro de Docker Swarm y del grupo de agentes de Docker. Ejecute el comando siguiente para ejecutar ambas direcciones IP.


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

Salida:

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Cree un túnel SSH al maestro de Swarm. Reemplace `IPAddress` por la dirección IP del maestro de Swarm.

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

Establezca la variable de entorno `DOCKER_HOST`. De esta forma podrá ejecutar comandos docker en Docker Swarm sin tener que especificar el nombre del host.

```bash
export DOCKER_HOST=:2375
```

Ahora está listo para ejecutar servicios de Docker en Docker Swarm.


## <a name="run-the-application"></a>Ejecución de la aplicación

Cree un archivo llamado `docker-compose.yaml` y copie en él el siguiente contenido.

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Ejecute el comando siguiente para crear el servicio Azure Vote.

```bash
docker-compose up -d
```

Salida:

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:redis-v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:redis-v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:redis-v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>Prueba de la aplicación

Busque la dirección IP del grupo de agentes de Swarm para probar la aplicación Azure Vote.

![Imagen de la exploración hasta Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Eliminación de clúster
Cuando un clúster ya no se necesite, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el servicio de contenedor y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obtención del código

En este tutorial de inicio rápido, se han usado imágenes de un contenedor creado previamente para crear un servicio de Docker. El código de la aplicación relacionado, Dockerfile, y el archivo de Compose están disponibles en GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial de inicio rápido, implementará un clúster de Docker Swarm y, en él, una aplicación de varios contenedores.

Para aprender sobre la integración de Docker Swarm con Visual Studio Team Services, consulte el artículo sobre CI/CD con Docker Swarm y VSTS.

> [!div class="nextstepaction"]
> [Integración y entrega continuas con Docker Swarm y VSTS](./container-service-docker-swarm-setup-ci-cd.md)