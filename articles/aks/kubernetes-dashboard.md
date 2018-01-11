---
title: "Administración de un clúster de Azure Kubernetes con una interfaz de usuario web"
description: Uso del panel de Kubernetes en AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ca828dab7bdb47e41596be2717598cfe828953ca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Panel de Kubernetes con Azure Container Service (AKS)

La CLI de Azure puede utilizarse para iniciar el panel de Kubernetes. Este documento le guía por el inicio del panel de Kubernetes con la CLI de Azure y también a través de algunas operaciones básicas del panel. Para obtener más información sobre el panel de Kubernetes, vea [Kubernetes Web UI Dashboard][kubernetes-dashboard] (Panel de la interfaz de usuario web de Kubernetes).

## <a name="before-you-begin"></a>Antes de empezar

En los pasos que se detallan en este documento se asume que se ha creado un clúster de AKS y que ha establecido una conexión kubectl con dicho clúster. Si necesita estos elementos, vea la [Guía de inicio rápido de AKS][aks-quickstart].

También es preciso que esté instalada y configurada la versión 2.0.21 de la CLI de Azure. Para saber qué versión tiene, ejecute el comando az --version. Si necesita instalarla o actualizarla, consulte [Install Azure CLI][install-azure-cli] (Instalación de la CLI de Azure).

## <a name="start-kubernetes-dashboard"></a>Inicio del panel de Kubernetes

Utilice el comando `az aks browse` para iniciar el panel de Kubernetes. Cuando ejecute este comando, reemplace el nombre de clúster y de grupo de recursos.

```azurecli
az aks browse --resource-group myResourceGroup --name myK8SCluster
```

Este comando crea a un proxy entre el sistema de desarrollo y la API de Kubernetes y abre un explorador web en el panel de Kubernetes.

## <a name="run-an-application"></a>Ejecución de una aplicación

En el panel de Kubernetes, haga clic en el botón **Create** (Crear) de la ventana derecha superior. Asigne el nombre de la implementación `nginx` y escriba `nginx:latest` para el nombre de las imágenes. En **Service** (Servicio), seleccione **External** (Externo) y escriba `80` para el puerto y el puerto de destino.

Cuando esté listo, haga clic en **Deploy** (Implementar) para crear la implementación.

![Cuadro de diálogo de creación del servicio de Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Visualización de la aplicación

El estado de la aplicación puede verse en el panel de Kubernetes. Una vez que se ejecuta la aplicación, cada componente tiene una casilla verde junto a ella.

![Pod de Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Para más información sobre los pods de aplicación, haga clic en **Pod** en el menú izquierdo y, luego, seleccione el pod **NGINX**. Aquí puede ver información específica del pod como el consumo de recursos.

![Recursos de Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Para buscar la dirección IP de la aplicación, haga clic en **Services** (Servicios) en el menú izquierdo y, luego, seleccione el servicio **NGINX**.

![vista de nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Edición de aplicación

Además de crear y visualizar las aplicaciones, el panel de Kubernetes puede utilizarse para editar y actualizar las implementaciones de aplicaciones.

Para editar una implementación, haga clic en **Deployments** (Implementaciones) en el menú izquierdo y, luego, seleccione la implementación **NGINX**. Por último, seleccione **Edit** (Editar) en la barra de navegación superior derecha.

![Edición de Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Busque el valor `spec.replica`, que debe ser 1, y cambie este valor a 3. Si lo hace, el número de réplicas de la implementación NGINX aumenta de 1 a 3.

Seleccione **Update** (Actualizar) cuando esté listo.

![Edición de Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el panel de Kubernetes, vea la documentación de Kubernetes.

> [!div class="nextstepaction"]
> [Kubernetes Web UI Dashboard][kubernetes-dashboard] (Panel de la interfaz de usuario web de Kubernetes)

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli