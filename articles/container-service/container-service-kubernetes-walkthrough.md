---
title: "Inicio rápido de clúster de Kubernetes en Azure | Microsoft Docs"
description: "Implementación e introducción a un clúster de Kubernetes en Azure Container Service"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2ec155129374c03ba7e0ecaa5d2bf29a1d3111aa
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Introducción a un clúster de Kubernetes en Container Service


En este tutorial se muestra cómo utilizar los comandos de CLI de Azure 2.0 para crear un clúster de Kubernetes en Azure Container Service. Puede usar la herramienta de línea de comandos `kubectl` para empezar a trabajar con contenedores en el clúster.

La siguiente imagen muestra la arquitectura de un clúster de Container Service con un maestro y dos agentes Linux. El maestro ofrece servicio a la API de REST de Kubernetes. Los nodos de agente se agrupan en un conjunto de disponibilidad de Azure y ejecutan los contenedores. Todas las máquinas virtuales están en la misma red privada virtual y son totalmente accesibles entre sí.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

Para más información de contexto, consulte la [introducción a Azure Container Service](container-service-intro.md) y la [documentación de Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Requisitos previos
Para crear un clúster de Azure Container Service mediante la CLI de Azure 2.0, debe:
* tener una cuenta de Azure ([obtenga aquí una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/))
* tener instalada y configurada la [CLI de Azure 2.0](/cli/azure/install-az-cli2)

Además, necesita (o puede utilizar la CLI de Azure para generar automáticamente durante la implementación del clúster):

* **Clave pública SSH de RSA**: si desea crear claves RSA de Shell seguro (SSH) por adelantado, consulte la guía de [macOS y Linux](../virtual-machines/linux/mac-create-ssh-keys.md) o de [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **Identificador y secreto de cliente de la entidad de servicio**: para ver los pasos para crear una entidad de servicio de Azure Active Directory e información adicional, consulte [Acerca de la entidad de servicio para un clúster de Kubernetes](container-service-kubernetes-service-principal.md).

 El ejemplo de comando en este artículo genera automáticamente las claves SSH y la entidad de servicio.

## <a name="create-your-kubernetes-cluster"></a>Creación del clúster de Kubernetes

A continuación se indican los comandos de shell de Bash usados por la CLI de Azure 2.0 para crear el clúster. 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Para crear el clúster, debe crear primero un grupo de recursos en una ubicación donde Azure Container Service esté [disponible](https://azure.microsoft.com/regions/services/). Ejecute comandos similares al siguiente:

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Crear un clúster
Cree un clúster de Kubernetes en el grupo de recursos mediante el comando `az acs create` con `--orchestrator-type=kubernetes`. Para conocer la sintaxis del comando, consulte la `az acs create` [ayuda](/cli/azure/acs#create).

Esta versión del comando genera automáticamente las claves RSA de SSH y la entidad de servicio para el clúster de Kubernetes.



```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```

Tras varios minutos, el comando se completa y debe tener un clúster de Kubernetes en funcionamiento.

> [!IMPORTANT]
> Si su cuenta no tiene permisos para crear la entidad de servicio de Azure AD, el comando genera un error similar a `Insufficient privileges to complete the operation.`. Para más información, consulte [Acerca de la entidad de servicio para un clúster de Kubernetes](container-service-kubernetes-service-principal.md).
> 



### <a name="connect-to-the-cluster"></a>Conexión al clúster

Para conectarse al clúster de Kubernetes desde el equipo cliente se utiliza [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), el cliente de línea de comandos de Kubernetes. 

Si aún no tiene instalado `kubectl`, puede instalarlo con `az acs kubernetes install-cli`. (También puede descargarlo desde el [sitio de Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/)).

```azurecli
sudo az acs kubernetes install-cli
```

> [!TIP]
> De forma predeterminada, este comando instala el archivo binario `kubectl` en `/usr/local/bin/kubectl` en un sistema Linux o macOS, o bien en `C:\Program Files (x86)\kubectl.exe` en Windows. Para especificar otra ruta de instalación, use el parámetro `--install-location`.
>
> Una vez instalado `kubectl`, asegúrese de que su directorio está en la ruta del sistema o agréguela a la ruta. 
>


A continuación, ejecute el siguiente comando para descargar la configuración del clúster de Kubernetes maestro en el archivo `~/.kube/config`:

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

Para obtener más opciones para instalar y configurar `kubectl`, consulte [Conexión a un clúster de Azure Container Service](container-service-connect.md).

En este momento debería estar listo para acceder al clúster desde su máquina: Intente ejecutar:

```bash
kubectl get nodes
```

Compruebe que puede ver una lista de las máquinas del clúster.

## <a name="create-your-first-kubernetes-service"></a>Creación de su primer servicio Kubernetes

Después de completar este tutorial sabrá:
* Implementar una aplicación de Docker y exponerla al mundo
* Usar `kubectl exec` para ejecutar comandos en un contenedor 
* Acceder al panel de Kubernetes

### <a name="start-a-container"></a>Inicio de un contenedor
Puede ejecutar un contenedor (en este caso el servidor web de Nginx) mediante la ejecución de:

```bash
kubectl run nginx --image nginx
```

Este comando inicia el contenedor Nginx de Docker en un pod en uno de los nodos.

Para ver el contenedor en funcionamiento, ejecute:

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Publicar el servicio al mundo
Para exponer el servicio al mundo, cree un Kubernetes `Service` de tipo `LoadBalancer`:

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Este comando hace que Kubernetes cree una regla de Azure Load Balancer con una dirección IP pública. El cambio tarda unos minutos en propagarse al equilibrador de carga. Para más información, consulte [Equilibrio de carga de contenedores en un clúster de Kubernetes en Azure Container Service](container-service-kubernetes-load-balancing.md).

Ejecute el siguiente comando para observar cómo el servicio cambia de `pending` para mostrar una dirección IP externa:

```bash
watch 'kubectl get svc'
```

  ![Imagen de cómo se ve la transición de pendiente a una dirección IP externa](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Cuando vea la dirección IP externa, puede desplazarse hasta ella en el explorador:

  ![Imagen de desplazamiento a Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Examinar la interfaz de usuario de Kubernetes
Para ver la interfaz web Kubernetes, puede usar:

```bash
kubectl proxy
```
Este comando ejecuta un proxy autenticado en localhost, que puede usar para ver la interfaz de usuario web de Kubernetes que se ejecuta en [http://localhost:8001/ui](http://localhost:8001/ui). Para más información, consulte [Uso de la interfaz de usuario web de Kubernetes con Azure Container Service](container-service-kubernetes-ui.md).

![Imagen del panel de Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sesiones remotas dentro de los contenedores
Kubernetes permite ejecutar comandos en un contenedor de Docker remoto que se ejecuta en el clúster.

```bash
# Get the name of your nginx pods
kubectl get pods
```

Mediante el nombre de pod, puede ejecutar un comando remoto en el pod. Por ejemplo:

```bash
kubectl exec <pod name> date
```

También puede obtener una sesión totalmente interactiva mediante las marcas `-it`:

```bash
kubectl exec <pod name> -it bash
```

![Sesión remota dentro de un contenedor](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Pasos siguientes

Para sacarle mayor partido a su clúster de Kubernetes, consulte los siguientes recursos:

* [Campo de entrenamiento de Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/): muestra cómo implementar, escalar, actualizar y depurar aplicaciones en contenedores.
* [Guía del usuario de Kubernetes](http://kubernetes.io/docs/user-guide/): proporciona información sobre cómo ejecutar programas en un clúster de Kubernetes existente.
* [Ejemplos de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples): proporciona ejemplos de cómo ejecutar aplicaciones reales con Kubernetes.

