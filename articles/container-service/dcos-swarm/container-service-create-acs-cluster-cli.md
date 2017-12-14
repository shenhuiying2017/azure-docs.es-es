---
title: "Implementación de un clúster de contenedores de Docker: CLI de Azure"
description: "Implementación de una solución Kubernetes, DC/OS o Docker Swarm en Azure Container Service mediante la CLI de Azure 2.0"
services: container-service
author: sauryadas
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 40d5ea0e7abce165659219db8842ab64ac75fda7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Implementación de una solución de hospedaje de contenedor de Docker mediante la CLI de Azure 2.0

Use los comandos `az acs` de la CLI de Azure 2.0 para crear y administrar clústeres en Azure Container Service. También se puede implementar un clúster de Azure Container Service mediante [Azure Portal](container-service-deployment.md) o las API de Azure Container Service.

Para obtener ayuda sobre los comandos `az acs`, use el parámetro `-h` con cualquier comando. Por ejemplo: `az acs create -h`.



## <a name="prerequisites"></a>Requisitos previos
Para crear un clúster de Azure Container Service mediante la CLI de Azure 2.0, debe:
* tener una cuenta de Azure ([obtenga aquí una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/))
* tener instalada y configurada la [CLI de Azure 2.0](/cli/azure/install-az-cli2)

## <a name="get-started"></a>Primeros pasos 
### <a name="log-in-to-your-account"></a>Inicie sesión en su cuenta.
```azurecli
az login 
```

Siga las indicaciones para iniciar sesión forma interactiva. Para ver otros métodos de inicio de sesión, consulte [Get started with Azure CLI 2.0](/cli/azure/get-started-with-az-cli2) (Introducción a la CLI de Azure 2.0).

### <a name="set-your-azure-subscription"></a>Establecimiento de una suscripción a Azure

Si tiene más de una suscripción a Azure, establezca la predeterminada. Por ejemplo:

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Se recomienda crear un grupo de recursos para cada clúster. Especifique una región de Azure en la que Azure Container Service esté [disponible](https://azure.microsoft.com/en-us/regions/services/). Por ejemplo:

```azurecli
az group create -n acsrg1 -l "westus"
```
La salida es similar a la siguiente:

![Crear un grupo de recursos](./media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Creación de un clúster de Azure Container Service

Para crear un clúster, use `az acs create`.
El nombre del clúster y el nombre del grupo de recursos creado en el paso anterior son parámetros obligatorios. 

Se establecen los valores predeterminados de otras entradas (vea la siguiente pantalla), a menos que se sobrescriban con sus respectivos modificadores. Por ejemplo, de manera predeterminada el orquestado se establece en DC/OS. Y si no se especifica ninguno, se crea un prefijo de nombre DNS basado en el nombre del clúster.

![Uso de az acs create](./media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>Uso rápido de `acs create` con los valores predeterminados
Si tiene un archivo de clave pública RSA de SSH `id_rsa.pub` en la ubicación predeterminada (o ha creado uno para [OS X y Linux](../../virtual-machines/linux/mac-create-ssh-keys.md), o [Windows](../../virtual-machines/linux/ssh-from-windows.md)), use un comando como al siguiente:

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
Si no tiene una clave pública SSH, use este segundo comando. Este comando con el modificador `--generate-ssh-keys` crea uno automáticamente.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

Después de escribir el comando, espere unos 10 minutos a que se cree el clúster. La salida del comando incluye los nombres de dominio completo (FQDN) de los nodos maestro y de agente y un comando SSH para conectarse con el primer maestro. Este es un resultado abreviado:

![Imagen del comando create de ACS](./media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> El [tutorial de Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md) muestra cómo utilizar `az acs create` con valores predeterminados para crear un clúster de Kubernetes.
>

## <a name="manage-acs-clusters"></a>Administración de clústeres de ACS

Uso otros comandos `az acs` para administrar el clúster. Estos son algunos ejemplos.

### <a name="list-clusters-under-a-subscription"></a>Lista de clústeres de una suscripción

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>Lista de clústeres de un grupo de recursos

```azurecli
az acs list -g acsrg1 --output table
```

![acs list](./media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>Visualización de los detalles de un clúster del servicio de contenedor

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![acs show](./media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>Escalado del clúster
Se permite la reducción y el escalado horizontales de nodos de agente. El parámetro `new-agent-count` es el nuevo número de agentes en el clúster de ACS.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![acs scale](./media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Eliminación de un clúster del servicio de contenedor
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
Este comando no elimina todos los recursos (red y almacenamiento) creados al crear el servicio de contenedor. Para eliminar todos los recursos con facilidad, se recomienda implementar cada clúster en un grupo de recursos distinto. Después, elimine el grupo de recursos cuando ya no se necesita el clúster.

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene un clúster funcionando, consulte los siguientes documentos para obtener información detallada sobre conexión y administración:

* [Conexión a un clúster del servicio Contenedor de Azure](../container-service-connect.md)
* [Administración de contenedores con la API de REST](container-service-mesos-marathon-rest.md)
* [Administración de contenedores con Docker Swarm](container-service-docker-swarm.md)
* [Trabajo con Azure Container Service y Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md)