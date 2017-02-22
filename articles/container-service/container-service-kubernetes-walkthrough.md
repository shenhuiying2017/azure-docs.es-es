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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Motor de Microsoft Azure Container Service: tutorial de Kubernetes

## <a name="prerequisites"></a>Requisitos previos
En este tutorial se da por supuesto que tiene la [herramienta de línea de comandos "CLI de Azure"](https://github.com/azure/azure-cli#installation) instalada y que ha creado la [clave pública SSH](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) en `~/.ssh/id_rsa.pub`.

## <a name="overview"></a>Información general

Las instrucciones siguientes crearán un clúster de Kubernetes con un nodo maestro y dos nodos de trabajo.
El maestro ofrece servicio a la API de REST de Kubernetes.  Los nodos de trabajado se agrupan en un conjunto de disponibilidad de Azure y ejecutan sus contenedores. Todas las máquinas virtuales están en la misma red privada virtual y son totalmente accesibles entre sí.

> [!NOTE]
> La compatibilidad con Kubernetes en Azure Container Service está actualmente en versión preliminar.
>

La siguiente imagen muestra la arquitectura de un clúster del servicio de contenedor con uno maestro y dos agentes:

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Creación del clúster de Kubernetes

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Para crear el clúster, primero debe crear un grupo de recursos en una ubicación específica. Puede hacer esto con:
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Crear un clúster
Una vez que tenga un grupo de recursos, puede crear un clúster en ese grupo con:
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> La herramienta CLI de Azure cargará `~/.ssh/id_rsa.pub` en las máquinas virtuales Linux.
>

Una vez completado este comando, debe tener un clúster de Kubernetes en funcionamiento.

### <a name="configure-kubectl"></a>Configuración de kubectl
`kubectl` es el cliente de línea de comandos Kubernetes.  Si no lo tiene aún instalado, puede hacerlo con:

```console
az acs kubernetes install-cli
```

Una vez que `kubectl` esté instalado, si se ejecuta el comando siguiente se descargará la configuración maestra del clúster de Kubernetes en el archivo de ~/.kube/config
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

En este momento debería estar listo para tener acceso al clúster desde el equipo. Intente ejecutar:
```console
kubectl get nodes
```

Y compruebe que puede ver las máquinas en el clúster.

## <a name="create-your-first-kubernetes-service"></a>Creación de su primer servicio Kubernetes

Después de completar este tutorial sabrá:
 * implementar una aplicación de Docker y exponerla a todo el mundo,
 * Cómo usar `kubectl exec` para ejecutar comandos en un contenedor. 
 * y obtener acceso al panel de Kubernetes.

### <a name="start-a-simple-container"></a>Iniciar un contenedor simple
Puede ejecutar un contenedor simple (en este caso el servidor web de `nginx`) mediante la ejecución de:

```console
kubectl run nginx --image nginx
```

Este comando inicia el contenedor de Docker nginx en un pod en uno de los nodos.

Puede ejecutar
```console
kubectl get pods
```

para ver el contenedor en ejecución.

### <a name="expose-the-service-to-the-world"></a>Publicar el servicio al mundo
Para publicar el servicio al mundo.  Cree un Kubernetes `Service` de tipo `LoadBalancer`:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Esto hará que Kubernetes cree una instancia de Azure Load Balancer con una dirección IP pública. El cambio tarda aproximadamente 2-3 minutos en propagarse al equilibrador de carga.

Para ver como el servicio cambia de "pendiente" a una dirección IP externa, escriba:
```console
watch 'kubectl get svc'
```

  ![Imagen de cómo se ve la transición de pendiente a una dirección IP externa](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Una vez que vea la dirección IP externa, puede desplazarse hasta ella en el explorador:

  ![Imagen del acceso a nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Examinar la interfaz de usuario de Kubernetes
Para ver la interfaz web Kubernetes, puede usar:

```console
kubectl proxy
```
Esta opción ejecuta un proxy autenticado simple en localhost, lo que puede usar para ver la [interfaz de usuario de Kubernetes](http://localhost:8001/ui)

![Imagen del panel de Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sesiones remotas dentro de los contenedores
Kubernetes permite ejecutar comandos en un contenedor de Docker remoto que se ejecuta en el clúster.

```console
# Get the name of your nginx pod
kubectl get pods
```

Mediante el nombre de pod, puede ejecutar un comando remoto en el pod.  Por ejemplo:
```console
kubectl exec nginx-701339712-retbj date
```

También puede obtener una sesión totalmente interactiva mediante las marcas `-it`:

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Imagen de curl para podIP](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>Detalles
### <a name="installing-the-kubectl-configuration-file"></a>Instalación del archivo de configuración de kubectl
Cuando ejecute `az acs kubernetes get-credentials`, el archivo de configuración de kube para el acceso remoto se habrá almacenado en el directorio particular ~/.kube/config.

Si alguna vez necesita descargarlo directamente, puede usar `ssh` en Linux u OSX, o `Putty` en Windows:

#### <a name="windows"></a>Windows
Para usar pscp desde [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Asegúrese de que el certificado se expone mediante [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X o Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>Más información

### <a name="azure-container-service"></a>Azure Container Service

1. [Documentación de Azure Container Service](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Motor de código abierto de Azure Container Service](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Documentación de la comunidad de Kubernetes

1. [Campo de entrenamiento de Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/): muestra cómo implementar, escalar, actualizar y depurar aplicaciones en contenedores.
2. [Guía del usuario de Kubernetes](http://kubernetes.io/docs/user-guide/): proporciona información sobre cómo ejecutar programas en un clúster de Kubernetes existente.
3. [Ejemplos de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples): proporciona una serie de ejemplos de cómo ejecutar aplicaciones reales con Kubernetes.



<!--HONumber=Jan17_HO4-->


