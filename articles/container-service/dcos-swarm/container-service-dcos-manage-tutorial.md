---
title: Tutorial de Azure Container Service - Administrar DC/OS
description: Tutorial de Azure Container Service - Administrar DC/OS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 07/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0c58bd764cf0fdacd55675f8343c6e7481a11823
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Tutorial de Azure Container Service - Administrar DC/OS

DC/OS proporciona una plataforma distribuida para ejecutar aplicaciones modernas y en contenedores. Con Azure Container Service, el aprovisionamiento de un clúster de DC/OS listo para producción se realiza de forma rápida y sencilla. En esta guía de inicio rápido se describen los pasos básicos necesarios para implementar un clúster de DC/OS y ejecutar una carga de trabajo básica.

> [!div class="checklist"]
> * Creación de un clúster de ACS DC/OS
> * Conexión al clúster
> * Instalación de la CLI de DC/OS
> * Implementación de una aplicación en el clúster
> * Escalado de una aplicación en el clúster
> * Escalado de los nodos del clúster de DC/OS
> * Administración básica de DC/OS
> * Eliminación del clúster de DC/OS

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Creación del clúster de DC/OS

En primer lugar, cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

A continuación, cree un clúster de DC/OS con el comando [az acs create](/cli/azure/acs#create).

En el ejemplo siguiente se crea un clúster de DC/OS denominado *myDCOSCluster* y las claves SSH si aún no existen. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Después de varios minutos, el comando se completa y muestra la información sobre la implementación.

## <a name="connect-to-dcos-cluster"></a>Conexión al clúster de DC/OS

Una vez creado un clúster de DC/OS, es accesible a través de un túnel SSH. Ejecute el comando siguiente para devolver la dirección IP pública del patrón de DC/OS. Esta dirección IP se almacena en una variable y se utiliza en el paso siguiente.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Para crear el túnel SSH, ejecute el siguiente comando y siga las instrucciones en pantalla. Si el puerto 80 ya está en uso, se produce un error en el comando. Actualice el puerto de túnel a uno que no esté en uso, como `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>Instalación de la CLI de DC/OS

Instale la CLI de DC/OS con el comando [az acs dcos install-cli](/azure/acs/dcos#install-cli). Si usa Azure CloudShell, la CLI de DC/OS ya estará instalada. Si está ejecutando la CLI de Azure en macOS o Linux, es posible que tenga que ejecutar el comando con sudo.

```azurecli
az acs dcos install-cli
```

Antes de usar la CLI con el clúster, debe configurarse para usar el túnel SSH. Para ello, ejecute el comando siguiente, ajustando el puerto si es necesario.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Ejecución de una aplicación

El mecanismo de programación predeterminado para un clúster de ACS DC/OS es Marathon. Marathon se usa para iniciar una aplicación y administrar el estado de la aplicación en el clúster de DC/OS. Para programar una aplicación mediante Marathon, cree un archivo denominado **marathon-app.json** y copie el siguiente contenido en él. 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Ejecute el comando siguiente para programar la aplicación que ejecutar en el clúster de DC/OS.

```azurecli
dcos marathon app add marathon-app.json
```

Para ver el estado de implementación de la aplicación, ejecute el siguiente comando.

```azurecli
dcos marathon app list
```

Cuando el valor de la columna **TASKS** cambie de *0/1* a *1/1*, se habrá completado la implementación de la aplicación.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Escalado de la aplicación de Marathon

En el ejemplo anterior, se creó una aplicación de instancia única. Para actualizar esta implementación para que haya disponibles tres instancias de la aplicación, abra el archivo **marathon-app.json** y actualice la propiedad de instancias a 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Actualice la aplicación mediante el comando `dcos marathon app update`.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Para ver el estado de implementación de la aplicación, ejecute el siguiente comando.

```azurecli
dcos marathon app list
```

Cuando el valor de la columna **TASKS** cambie de *1/3* a *3/1*, se habrá completado la implementación de la aplicación.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Ejecución de una aplicación accesible a través de Internet

El clúster de ACS DC/OS consta de dos conjuntos de nodos: uno público, que es accesible en Internet, y otro privado, que no es accesible en Internet. El conjunto predeterminado es el de nodos privados, que se utilizó en el último ejemplo.

Para que una aplicación sea accesible en Internet, impleméntela en el conjunto de nodos públicos. Para ello, asigne al objeto `acceptedResourceRoles` un valor de `slave_public`.

Cree un archivo denominado **nginx-public.json** y copie en él el contenido siguiente.

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Ejecute el comando siguiente para programar la aplicación que ejecutar en el clúster de DC/OS.

```azurecli 
dcos marathon app add nginx-public.json
```

Obtenga la dirección IP pública de los agentes de clúster públicos de DC/OS.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Esta dirección dirige al sitio de NGINX predeterminado.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Escalado del clúster de DC/OS

En los ejemplos anteriores, una aplicación se ha escalado a varias instancias. También se puede escalar la infraestructura de DC/OS para proporcionar más o menos capacidad de proceso. Esto se realiza con el comando [az acs scale](). 

Para ver el recuento actual de agentes de DC/OS, use el comando [az acs show](/cli/azure/acs#show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Para aumentar el total a 5, utilice el comando [az acs scale](/cli/azure/acs#scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Eliminación del clúster de DC/OS

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el clúster de DC/OS y todos los recursos relacionados.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido sobre tareas básicas de administración de DC/OS, incluidas las siguientes. 

> [!div class="checklist"]
> * Creación de un clúster de ACS DC/OS
> * Conexión al clúster
> * Instalación de la CLI de DC/OS
> * Implementación de una aplicación en el clúster
> * Escalado de una aplicación en el clúster
> * Escalado de los nodos del clúster de DC/OS
> * Eliminación del clúster de DC/OS

Vaya al siguiente tutorial para obtener información acerca de la aplicación de equilibrio de carga de DC/OS en Azure. 

> [!div class="nextstepaction"]
> [Aplicaciones de equilibrio de carga](container-service-load-balancing.md)