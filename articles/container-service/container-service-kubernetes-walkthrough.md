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
ms.date: 04/05/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 5c529ae41b42d276d37e6103305e33ed04694e18
ms.lasthandoff: 04/07/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Introducción a un clúster de Kubernetes en Container Service


En este tutorial se muestra cómo utilizar los comandos de CLI de Azure 2.0 para crear un clúster de Kubernetes en Azure Container Service. Puede usar la herramienta de línea de comandos `kubectl` para empezar a trabajar con contenedores en el clúster.

La siguiente imagen muestra la arquitectura de un clúster de Container Service con un maestro y dos agentes. El maestro ofrece servicio a la API de REST de Kubernetes. Los nodos de agente se agrupan en un conjunto de disponibilidad de Azure y ejecutan los contenedores. Todas las máquinas virtuales están en la misma red privada virtual y son totalmente accesibles entre sí.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Requisitos previos
En este tutorial se supone que ha instalado y configurado la [CLI de Azure 2.0](/cli/azure/install-az-cli2). 

En los ejemplos de comandos se supone que ejecuta la CLI de Azure en un shell de bash, común en Linux y macOS. Si ejecuta la CLI de Azure en un cliente de Windows, parte de la sintaxis de scripting y los archivos puede variar, en función del shell de comandos. 

## <a name="create-your-kubernetes-cluster"></a>Creación del clúster de Kubernetes

A continuación se indican los comandos de shell usados por la CLI de Azure 2.0 para crear el clúster. 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Para crear el clúster, primero debe crear un grupo de recursos en una ubicación específica. Ejecute comandos similares al siguiente:

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Crear un clúster
Cuando tenga un grupo de recursos, puede crear un clúster en él. En el ejemplo siguiente se usa la opción `--generate-ssh-keys`, que genera los SSH archivos de claves públicas y privadas necesarios para la implementación si aún no existen en el directorio `~/.ssh/` predeterminado. 

Este comando también genera automáticamente la [entidad de servicio de Azure Active Directory](container-service-kubernetes-service-principal.md) usada por un clúster de Kubernetes en Azure.

```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```


Tras varios minutos, el comando se completa y debe tener un clúster de Kubernetes en funcionamiento.

### <a name="connect-to-the-cluster"></a>Conexión al clúster

Para conectarse al clúster de Kubernetes desde el equipo cliente se utiliza [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), el cliente de línea de comandos de Kubernetes. 

Si aún no tiene instalado `kubectl`, puede instalarlo con:

```azurecli
sudo az acs kubernetes install-cli
```
> [!TIP]
> De forma predeterminada, este comando instala el archivo binario `kubectl` en `/usr/local/bin/kubectl` en un sistema Linux o macOS, o bien en `C:\Program Files (x86)\kubectl.exe` en Windows. Para especificar otra ruta de instalación, use el parámetro `--install-location`.
>

Una vez instalado `kubectl`, asegúrese de que su directorio está en la ruta del sistema o agréguela a la ruta. 


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

### <a name="start-a-simple-container"></a>Iniciar un contenedor simple
Puede ejecutar un contenedor simple (en este caso el servidor web de Nginx) mediante la ejecución de:

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
Este comando ejecuta un proxy autenticado simple en localhost, que puede usar para ver la interfaz de usuario web de Kubernetes que se ejecuta en [http://localhost:8001/ui](http://localhost:8001/ui). Para más información, consulte [Uso de la interfaz de usuario web de Kubernetes con Azure Container Service](container-service-kubernetes-ui.md).

![Imagen del panel de Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sesiones remotas dentro de los contenedores
Kubernetes permite ejecutar comandos en un contenedor de Docker remoto que se ejecuta en el clúster.

```bash
# Get the name of your nginx pods
kubectl get pods
```

Mediante el nombre de pod, puede ejecutar un comando remoto en el pod.  Por ejemplo:

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

