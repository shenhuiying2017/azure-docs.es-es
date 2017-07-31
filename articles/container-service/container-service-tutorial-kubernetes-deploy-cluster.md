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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 9735d204413d4fff182adc8799074510757f83a7
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Implementación de un clúster de Kubernetes en Azure Container Service

Kubernetes proporciona una plataforma distribuida para ejecutar aplicaciones modernas y en contenedores. Con Azure Container Service, el aprovisionamiento de un clúster de Kubernetes listo para producción se realiza de forma rápida y sencilla. En esta guía de inicio rápido se describen los pasos básicos necesarios para implementar un clúster de Kubernetes y ejecutar una carga de trabajo básica. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación de un clúster de ACS de Kubernetes
> * Instalación de la CLI de Kubernetes (kubectl)
> * Configuración de kubectl

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores, las imágenes del contenedor se creaban y cargaban en una instancia de Azure Container Registry. Si no ha realizado estos pasos y quiere continuar, vuelva al [Paso 1: Creación de imágenes de contenedor](./container-service-tutorial-kubernetes-prepare-app.md). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-kubernetes-cluster"></a>Creación de un clúster de Kubernetes

En el [tutorial anterior](./container-service-tutorial-kubernetes-prepare-acr.md), se creó un grupo de recursos denominado *myResourceGroup*. Si todavía no lo ha hecho, cree ahora este grupo de recursos.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Cree un clúster de Kubernetes en Azure Container Service con el comando [az acs create](/cli/azure/acs#create). 

En el ejemplo siguiente, se crea un clúster denominado *myK8sCluster* con un nodo maestro de Linux y tres nodos de agente de Linux. Si no existen, se crean claves SSH. Para utilizar un conjunto específico de claves en una ubicación no predeterminada, utilice la opción `--ssh-key-value`.

```azurecli-interactive 
az acs create --orchestrator-type=kubernetes --resource-group myResourceGroup --name=myK8SCluster --generate-ssh-keys 
```

Después de varios minutos, el comando se completa y muestra la información sobre la implementación de ACS.

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
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8SCluster
```

Para comprobar la conexión al clúster, ejecute el comando [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
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

En competencia tutorial, tiene un clúster de ACS de Kubernetes preparado para cargas de trabajo. En los tutoriales posteriores, una aplicación de contenedores múltiples está implementada en este clúster, escalada horizontalmente, actualizada y supervisada.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se implementó un clúster de Kubernetes de Azure Container Service. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de un clúster de ACS de Kubernetes
> * Instalación de la CLI de Kubernetes (kubectl)
> * Configuración de kubectl

Avance al siguiente tutorial para aprender a ejecutar la aplicación en el clúster.

> [!div class="nextstepaction"]
> [Implementación de una aplicación en Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
