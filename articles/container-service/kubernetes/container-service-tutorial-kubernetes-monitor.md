---
title: 'Tutorial de Azure Container Service: supervisión de Kubernetes'
description: 'Tutorial de Azure Container Service: supervisión de Kubernetes con Log Analytics'
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: e7d55f1579ce45a39f9b07225bc88c8ef8ff6b66
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-a-kubernetes-cluster-with-log-analytics"></a>Supervisión de un clúster de Azure Kubernetes con Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

La supervisión de su clúster de Kubernetes y de los contenedores es fundamental, sobre todo al administrar un clúster de producción a escala con varias aplicaciones. 

Puede sacar provecho de varias soluciones de supervisión de Kubernetes, tanto de Microsoft como de otros proveedores. En este tutorial, se supervisa un clúster de Kubernetes mediante la solución Containers de [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md), la solución de administración de TI en la nube de Microsoft. (La solución Containers de OMS está en versión preliminar).

Este tutorial, la séptima parte de siete, trata las siguientes tareas:

> [!div class="checklist"]
> * Obtener la configuración del área de trabajo de Log Analytics
> * Configurar agentes OMS en los nodos de Kubernetes
> * Acceder a la información de supervisión en el Portal de OMS o en Azure Portal

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores se empaquetaba una aplicación en imágenes de contenedor, se cargaban estas imágenes en Azure Container Registry y se creó un clúster de Kubernetes. 

Si no ha realizado estos pasos, pero desea continuar, vuelva al tutorial [Create container images to be used with Azure Container Service](./container-service-tutorial-kubernetes-prepare-app.md) (Creación de las imágenes de contenedor que se usan con Azure Container Service). 

## <a name="get-workspace-settings"></a>Obtener la configuración del área de trabajo

Cuando pueda acceder al [Portal de OMS](https://mms.microsoft.com), vaya a **Configuración** > **Orígenes conectados** > **Servidores Linux**. Ahí puede encontrar el *identificador del área de trabajo* y una *clave del área de trabajo* principal o secundaria. Anote de estos valores, ya que se necesitan para configurar agentes de OMS en el clúster.

## <a name="create-kubernetes-secret"></a>Creación de un secreto de Kubernetes

Almacene la configuración del área de trabajo de Log Analytics en un secreto de Kubernetes llamado `omsagent-secret` mediante el comando [kubectl create secret][kubectl-create-secret]. Actualice `WORKSPACE_ID` con el identificador del área de trabajo de Log Analytics y `WORKSPACE_KEY` con la clave de área de trabajo.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-oms-agents"></a>Configuración de agentes de OMS

Este es un archivo YAML para configurar agentes de OMS en los nodos de clúster de Linux. Crea un recurso [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) de Kubernetes, que ejecuta un solo pod idéntico en cada nodo del clúster. El recurso DaemonSet es ideal para implementar a un agente de supervisión. 

Guarde el siguiente texto en un archivo llamado `oms-daemonset.yaml` y reemplace los valores de marcador de posición de *myWorkspaceID* y *myWorkspaceKey* por la clave y el identificador del área de trabajo de Log Analytics. (en producción, estos valores se pueden codificar como secretos).

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers 
          name: containerlog-path  
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux    
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"     
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log 
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers 
```

Cree el recurso DaemonSet con el siguiente comando:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Para ver que se ha creado, ejecute:

```azurecli-interactive
kubectl get daemonset
```

La salida es similar a la siguiente:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Una vez que se ejecutan los agentes, Log Analytics tarda varios minutos en ingerir y procesar los datos.

## <a name="access-monitoring-data"></a>Acceso a los datos de supervisión

Vea y analice los datos de supervisión del contenedor con la [solución Containers](../../log-analytics/log-analytics-containers.md) en el Portal de OMS o en Azure Portal. 

Para instalar dicha solución desde el [Portal de OMS](https://mms.microsoft.com), vaya a **Galería de soluciones**. A continuación, agregue **Solución de contenedor**. Como alternativa, agregue la solución Containers de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

En el Portal de OMS, busque un icono de resumen de **Containers** en el panel. Haga clic en el icono para obtener la siguiente información: eventos de contenedor, errores, estado, inventario de la imagen y uso de la CPU y la memoria. Para obtener información más detallada, haga clic en una fila de cualquier icono o realice una [búsqueda de registros](../../log-analytics/log-analytics-log-searches.md).

![Panel de contenedores en el Portal de OMS](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

En Azure Portal, vaya a **Log Analytics** y seleccione el nombre de su área de trabajo. Para ver el icono de resumen de **Containers**, haga clic en **Soluciones** > **Containers**. Para ver los detalles, haga clic en el icono.

Consulte la [documentación de Azure Log Analytics](../../log-analytics/index.yml) para obtener una guía detallada para consultar y analizar los datos de supervisión.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se ha supervisado el clúster de Kubernetes con Log Analytics. Estas son las tareas que se han tratado:

> [!div class="checklist"]
> * Obtener la configuración del área de trabajo de Log Analytics
> * Configurar agentes OMS en los nodos de Kubernetes
> * Acceder a la información de supervisión en el Portal de OMS o en Azure Portal


Siga este vínculo para ver ejemplos de scripts del servicio Container creados previamente.

> [!div class="nextstepaction"]
> [Ejemplos de scripts de Azure Container Service](cli-samples.md)
