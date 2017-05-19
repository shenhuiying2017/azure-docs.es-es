---
title: "Clúster de Azure Kubernetes para Windows | Microsoft Docs"
description: "Implementación y primeros pasos para un clúster de Kubernetes para contenedores de Windows en Azure Container Service"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Introducción a los contenedores de Kubernetes y Windows en Container Service


En este artículo se muestra cómo crear un clúster de Kubernetes en Azure Container Service que contenga nodos de Windows para ejecutar contenedores de Windows. Para comenzar, use los comandos `az acs` de la CLI 2.0 de Azure para crear el clúster de Kubernetes en Azure Container Service. A continuación, use la herramienta de la línea de comandos `kubectl` de Kubernetes para comenzar a trabajar con contenedores de Windows creados a partir de imágenes de Docker. 

> [!NOTE]
> La compatibilidad de contenedores de Windows con Kubernetes en Azure Container Service está en versión preliminar. 
>



En la imagen siguiente se muestra la arquitectura de un clúster de Kubernetes en Azure Container Service con un nodo maestro de Linux y dos nodos agentes de Windows. 

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* El maestro de Linux atiende a la API de REST de Kubernetes y es accesible mediante SSH en el puerto 22 o `kubectl` en el puerto 443. 
* Los nodos agentes de Windows están agrupados en un conjunto de disponibilidad de Azure y ejecutan los contenedores. Se puede acceder a los nodos de Windows por un túnel SSH de RDP a través del nodo maestro. Las reglas de equilibrio de carga de Azure se agregan dinámicamente al clúster en función de los servicios expuestos.



Todas las máquinas virtuales están en la misma red privada virtual y son totalmente accesibles entre sí. Todas las máquinas virtuales ejecutan kubelet, Docker y un proxy.

Para más información de contexto, consulte la [introducción a Azure Container Service](container-service-intro.md) y la [documentación de Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Requisitos previos
Para crear un clúster de Azure Container Service mediante la CLI de Azure 2.0, debe:
* tener una cuenta de Azure ([obtenga aquí una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/))
* haber instalado la [CLI 2.0 de Azure](/cli/azure/install-az-cli2) y haber iniciado sesión en ella.

También necesitará los siguientes elementos para el clúster de Kubernetes. Puede prepararlos con antelación, o bien usar las opciones del comando `az acs create` para generarlos automáticamente durante la implementación del clúster. 

* **Clave pública SSH de RSA**: si desea crear claves RSA de Shell seguro (SSH), consulte la guía de [macOS y Linux](../virtual-machines/linux/mac-create-ssh-keys.md) o de [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **Identificador y secreto de cliente de la entidad de servicio**: para ver los pasos para crear una entidad de servicio de Azure Active Directory e información adicional, consulte [Acerca de la entidad de servicio para un clúster de Kubernetes](container-service-kubernetes-service-principal.md).

El ejemplo de comando en este artículo genera automáticamente las claves SSH y la entidad de servicio.
  
## <a name="create-your-kubernetes-cluster"></a>Creación del clúster de Kubernetes

Estos son los comandos de la CLI 2.0 de Azure para crear el clúster. 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un grupo de recursos en una ubicación donde Azure Container Service esté [disponible](https://azure.microsoft.com/regions/services/). El comando siguiente crea un grupo de recursos denominado *myKubernetesResourceGroup* en la ubicación *westus*:

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Creación de un clúster de Kubernetes con nodos de agente de Windows

Cree un clúster de Kubernetes en el grupo de recursos mediante el comando `az acs create` con `--orchestrator-type=kubernetes` y la opción del agente `--windows`. Para conocer la sintaxis del comando, consulte la `az acs create` [ayuda](/cli/azure/acs#create).

El comando siguiente crea un clúster de Container Service llamado *myKubernetesClusterName*, con un prefijo DNS *myPrefix* para el nodo de administración y las credenciales especificadas para conectar con los nodos de Windows. Esta versión del comando genera automáticamente las claves RSA de SSH y la entidad de servicio para el clúster de Kubernetes.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

Tras varios minutos, el comando se completa y debe tener un clúster de Kubernetes en funcionamiento.

> [!IMPORTANT]
> Si su cuenta no tiene permisos para crear la entidad de servicio de Azure AD, el comando genera un error similar a `Insufficient privileges to complete the operation.`. Para más información, consulte [Acerca de la entidad de servicio para un clúster de Kubernetes](container-service-kubernetes-service-principal.md). 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>Conexión al clúster con kubectl

Para conectarse al clúster de Kubernetes desde el equipo cliente, use [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), el cliente de la línea de comandos de Kubernetes. 

Si aún no tiene instalado `kubectl` localmente, puede instalarlo con `az acs kubernetes install-cli`. (También puede descargarlo desde el [sitio de Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/)).

**Linux o macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> De forma predeterminada, este comando instala el archivo binario `kubectl` en `/usr/local/bin/kubectl` en un sistema Linux o macOS, o bien en `C:\Program Files (x86)\kubectl.exe` en Windows. Para especificar otra ruta de instalación, use el parámetro `--install-location`.
>
> Una vez instalado `kubectl`, asegúrese de que su directorio esté en la ruta del sistema o agréguela a la ruta. 


A continuación, ejecute el siguiente comando para descargar la configuración del clúster maestro de Kubernetes en el archivo local `~/.kube/config`:

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

En este momento está listo para acceder al clúster desde su máquina: Intente ejecutar:

```bash
kubectl get nodes
```

Compruebe que puede ver una lista de las máquinas del clúster.

![Nodos que se ejecutan en un clúster de Kubernetes](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>Creación de su primer servicio Kubernetes

Después de crear el clúster y conectar con `kubectl`, intente iniciar una aplicación de Windows desde un contenedor de Docker y exponerla a Internet. En este ejemplo básico se usa un archivo JSON para especificar un contenedor de Microsoft Internet Information Server (IIS) y luego se crea mediante `kubctl apply`. 

1. Cree un archivo denominado `iis.json` y copie lo siguiente: Este archivo indica a Kubernetes que ejecute IIS en Windows Server 2016 Server Core, mediante una imagen pública de [Docker Hub](https://hub.docker.com/r/microsoft/iis/). El contenedor usa el puerto 80, pero inicialmente solo es accesible dentro de la red de clústeres.

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. Para iniciar la aplicación, escriba:  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. Para realizar el seguimiento de la implementación del contenedor, escriba:  
  ```bash
  kubectl get pods
  ```
  Mientras se implementa el contenedor, el estado es `ContainerCreating`. 

  ![Contenedor de IIS en estado ContainerCreating](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  Debido al tamaño de la imagen de IIS, el contenedor puede tardar varios minutos en pasar al estado `Running`.

  ![Contenedor de IIS en estado de ejecución](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. Para exponer el contenedor al mundo, escriba el siguiente comando:

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  Con este comando, Kubernetes crea una regla de Azure Load Balancer con una dirección IP pública. El cambio tarda unos minutos en propagarse al equilibrador de carga. Para más información, consulte [Equilibrio de carga de contenedores en un clúster de Kubernetes en Azure Container Service](container-service-kubernetes-load-balancing.md).

5. Ejecute el siguiente comando para ver el estado del servicio.

  ```bash
  kubectl get svc
  ```

  La dirección IP aparece inicialmente como `pending`:

  ![Dirección IP externa pendiente](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  A los pocos minutos, se establece la dirección IP:
  
  ![Dirección IP externa para IIS](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. Cuando la dirección IP externa esté disponible, podrá buscarla en su explorador:

  ![Imagen de exploración hasta IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. Para eliminar el pod de IIS, escriba:

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>Pasos siguientes

* Para usar la IU de Kubernetes, ejecute el comando `kubectl proxy`. A continuación, vaya a http://localhost:8001/ui.

* Para conocer los pasos para crear un sitio web de IIS personalizado y ejecutarlo en un contenedor de Windows, consulte las instrucciones en [Docker Hub](https://hub.docker.com/r/microsoft/iis/).

* Para acceder a los nodos de Windows mediante un túnel SSH de RDP hasta el nodo maestro con PuTTy, consulte la [documentación del motor de ACS](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master). 

