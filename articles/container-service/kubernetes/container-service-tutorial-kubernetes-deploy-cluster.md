---
title: "Tutorial de Azure Container Service: Implementación de clúster | Microsoft Docs"
description: "Tutorial de Azure Container Service: Implementación de clúster"
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
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 16070499b7befca26d55259d845d1dbc14110f2a
ms.contentlocale: es-es
ms.lasthandoff: 08/09/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Implementación de un clúster de Kubernetes en Azure Container Service

Kubernetes proporciona una plataforma distribuida para aplicaciones en contenedores. Con Azure Container Service, el aprovisionamiento de un clúster de Kubernetes listo para producción se realiza de forma rápida y sencilla. En este tutorial, la tercera parte de siete, se implementa un clúster de Kubernetes de Azure Container Service. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación de un clúster de ACS de Kubernetes
> * Instalación de la CLI de Kubernetes (kubectl)
> * Configuración de kubectl

En tutoriales posteriores, la aplicación Azure Vote se implementa en el clúster, se escala y se actualiza, y Operations Management Suite se configura para supervisar el clúster de Kubernetes.

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores, se creó una imagen de contenedor y se actualizó en una instancia de Azure Container Registry. Si no ha realizado estos pasos, pero desea continuar, vuelva al tutorial [Create container images to be used with Azure Container Service](./container-service-tutorial-kubernetes-prepare-app.md) (Creación de las imágenes de contenedor que se usan con Azure Container Service).

## <a name="create-kubernetes-cluster"></a>Creación de un clúster de Kubernetes

En el [tutorial anterior](./container-service-tutorial-kubernetes-prepare-acr.md), se creó un grupo de recursos denominado *myResourceGroup*. Si todavía no lo ha hecho, cree ahora este grupo de recursos.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Cree un clúster de Kubernetes en Azure Container Service con el comando [az acs create](/cli/azure/acs#create). 

En el ejemplo siguiente, se crea un clúster denominado *myK8sCluster* con un nodo maestro de Linux y tres nodos de agente de Linux.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

Después de varios minutos, el comando se completa y devuelve información en formato json sobre la implementación de ACS.

## <a name="install-the-kubectl-cli"></a>Instalación de la CLI de kubectl

Para conectarse al clúster de Kubernetes desde el equipo cliente, use [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), el cliente de la línea de comandos de Kubernetes. 

Si usa Azure CloudShell, `kubectl` ya está instalado. Si desea instalarlo de forma local, use el comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Si está ejecutando en Linux o macOS, debe ejecutar con sudo. En Windows, asegúrese de que el shell se ha ejecutado como administrador.

```azurecli-interactive 
az acs kubernetes install-cli 
```

En Windows, la instalación predeterminada es *c:\program files (x86)\kubectl.exe*. Puede que deba agregar este archivo a la ruta de acceso de Windows. 

## <a name="connect-with-kubectl"></a>Conexión con kubectl

Para configurar `kubectl` para conectarse al clúster de Kubernetes, ejecute el comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Para comprobar la conexión al clúster, ejecute el comando [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli-interactive
kubectl get nodes
```

Salida:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Al finalizar el tutorial, tendrá un clúster de ACS de Kubernetes preparado para cargas de trabajo. En los tutoriales posteriores, una aplicación de contenedores múltiples está implementada en este clúster, escalada horizontalmente, actualizada y supervisada.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se implementó un clúster de Kubernetes de Azure Container Service. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de un clúster de ACS de Kubernetes
> * Instalación de la CLI de Kubernetes (kubectl)
> * Configuración de kubectl

Avance al siguiente tutorial para aprender a ejecutar la aplicación en el clúster.

> [!div class="nextstepaction"]
> [Implementación de una aplicación en Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)

