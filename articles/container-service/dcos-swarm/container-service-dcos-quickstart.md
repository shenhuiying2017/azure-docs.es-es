---
title: "Guía de inicio rápido de Azure Container Service - Implementación del clúster de DC/OS | Microsoft Docs"
description: "Guía de inicio rápido de Azure Container Service - Implementación del clúster de DC/OS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8070d224fe6281e61f67483d4f1dd905a2ab99eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-dcos-cluster"></a>Implementación de un clúster de DC/OS

DC/OS proporciona una plataforma distribuida para ejecutar aplicaciones modernas y en contenedores. Con Azure Container Service, el aprovisionamiento de un clúster de DC/OS listo para producción se realiza de forma rápida y sencilla. En este tutorial de inicio rápido se describen los pasos básicos necesarios para implementar un clúster de DC/OS y ejecutar una carga de trabajo básica.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 

Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Creación del clúster de DC/OS

Cree un clúster de DC/OS con el comando [az acs create](/cli/azure/acs#create).

En el ejemplo siguiente se crea un clúster de DC/OS denominado *myDCOSCluster* y las claves SSH si aún no existen. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

En algunos casos, como con las versiones de evaluación de tiempo limitado, una suscripción a Azure tiene un acceso limitado a los recursos de Azure. Si se produce un error en la implementación debido a que los núcleos disponibles son limitados, reduzca el número de agentes predeterminado agregando `--agent-count 1` al comando [az acs create](/cli/azure/acs#create). 

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

Para probar el túnel SSH, vaya a `http://localhost`. Si se ha usado un puerto distinto al puerto 80, ajuste la ubicación para que coincida. 

Si el túnel SSH se creó correctamente, se devuelve el portal de DC/OS.

![Interfaz de usuario de DCOS](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Instalación de la CLI de DC/OS

La interfaz de la línea de comandos de DC/OS se utiliza para administrar un clúster de DC/OS desde la línea de comandos. Instale la CLI de DC/OS con el comando [az acs dcos install-cli](/azure/acs/dcos#install-cli). Si usa Azure CloudShell, la CLI de DC/OS ya estará instalada. 

Si está ejecutando la CLI de Azure en macOS o Linux, es posible que tenga que ejecutar el comando con sudo.

```azurecli
az acs dcos install-cli
```

Antes de usar la CLI con el clúster, debe configurarse para usar el túnel SSH. Para ello, ejecute el comando siguiente, ajustando el puerto si es necesario.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Ejecución de una aplicación

El mecanismo de programación predeterminado para un clúster de ACS DC/OS es Marathon. Marathon se usa para iniciar una aplicación y administrar el estado de la aplicación en el clúster de DC/OS. Para programar una aplicación mediante Marathon, cree un archivo denominado *marathon-app.json* y copie el siguiente contenido en él. 

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
dcos marathon app add marathon-app.json
```

Para ver el estado de implementación de la aplicación, ejecute el siguiente comando.

```azurecli
dcos marathon app list
```

Cuando el valor de la columna **WAITING** cambie de *True* a *False*, se habrá completado la implementación de la aplicación.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Obtenga la dirección IP pública de los agentes de clúster de DC/OS.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Esta dirección dirige al sitio de NGINX predeterminado.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Eliminación del clúster de DC/OS

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el clúster de DC/OS y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado un clúster de DC/OS y ha ejecutado un contenedor de Docker simple en el clúster. Para más información sobre Azure Container Service, continúe con el tutorial de ACS.

> [!div class="nextstepaction"]
> [Administración de un clúster de ACS DC/OS](container-service-dcos-manage-tutorial.md)