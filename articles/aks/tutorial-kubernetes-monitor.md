---
title: "Tutorial de Kubernetes en Azure: supervisión de Kubernetes"
description: "Tutorial de AKS: supervisión de Kubernetes con Microsoft Operations Management Suite (OMS)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b01aa01df198ce75b2f8b66d28a2db68b1c30b87
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="monitor-azure-container-service-aks"></a>Supervisión de Azure Container Service (AKS)

La supervisión de su clúster de Kubernetes y de los contenedores es fundamental, sobre todo al ejecutar un clúster de producción, a escala, con varias aplicaciones.

En este tutorial, puede configurar la supervisión del clúster de AKS mediante la [solución Containers para Log Analytics][log-analytics-containers].

En este tutorial, la séptima parte de ocho, se tratan las siguientes tareas:

> [!div class="checklist"]
> * Configuración de la solución de supervisión de contenedores
> * Configuración de los agentes de supervisión
> * Obtener acceso a la información de supervisión en Azure Portal

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores se empaquetaba una aplicación en imágenes de contenedor, se cargaban estas imágenes en Azure Container Registry y se creó un clúster de Kubernetes.

Si no ha realizado estos pasos, pero desea continuar, vuelva al [tutorial 1: Creación de imágenes de contenedor][aks-tutorial-prepare-app].

## <a name="configure-the-monitoring-solution"></a>Configurar la solución de supervisión

En Azure Portal, seleccione **Nuevo** y busque `Container Monitoring Solution`. Una vez que se encuentre, seleccione **Crear**.

![Agregar solución](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Cree un área de trabajo de OMS o seleccione una existente. El área de trabajo de OMS le guía a través de este proceso.

Al crear el área de trabajo, seleccione **Anclar al panel** para que la recuperación resulte sencilla.

![Área de trabajo de OMS](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Cuando haya terminado, seleccione **Aceptar**. Una vez que se haya completado la validación, seleccione **Crear** para crear la solución de supervisión de contenedores.

Tras crearse el área de trabajo, se le presenta en Azure Portal.

## <a name="get-workspace-settings"></a>Obtener la configuración del área de trabajo

La clave y el id. de área de trabajo de Log Analytics son necesarios para configurar el agente de solución en los nodos de Kubernetes.

Para recuperar estos valores, seleccione **Área de trabajo de OMS** en el menú izquierdo de soluciones de contenedores. Seleccione **Configuración avanzada** y anote los valores de **WORKSPACE ID** (ID. DE ÁREA DE TRABAJO) y **PRIMARY KEY** (CLAVE PRINCIPAL).

## <a name="configure-monitoring-agents"></a>Configurar los agentes de supervisión

El siguiente archivo de manifiesto de Kubernetes se puede usar para configurar los agentes de supervisión de contenedores en un clúster de Kubernetes. Crea un recurso [DaemonSet][kubernetes-daemonset] de Kubernetes, que ejecuta un solo pod en cada nodo del clúster.

Guarde el siguiente texto en un archivo denominado `oms-daemonset.yaml` y reemplace los valores de marcador de posición de `WSID` y `KEY` con la clave y el id. de área de trabajo de Log Analytics.

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
        - mountPath: /var/log
          name: host-log
        - mountPath: /var/lib/docker/containers/
          name: container-log
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
    - name: container-hostname
      hostPath:
       path: /etc/hostname
    - name: host-log
      hostPath:
       path: /var/log
    - name: container-log
      hostPath:
       path: /var/lib/docker/containers/
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

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Una vez que se ejecutan los agentes, OMS tarda varios minutos en ingerir y procesar los datos.

## <a name="access-monitoring-data"></a>Acceso a los datos de supervisión

En Azure Portal, seleccione el área de trabajo de Log Analytics que se ha anclado al panel del portal. Haga clic en el icono **Solución de supervisión de contenedores**. Aquí encontrará información sobre el clúster de AKS y los contenedores del clúster.

![panel](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Consulte la [documentación de Azure Log Analytics][log-analytics-docs] para obtener una guía detallada para consultar y analizar los datos de supervisión.

## <a name="next-steps"></a>pasos siguientes

En este tutorial, se ha supervisado el clúster de Kubernetes con OMS. Estas son las tareas que se han tratado:

> [!div class="checklist"]
> * Configuración de la solución de supervisión de contenedores
> * Configuración de los agentes de supervisión
> * Obtener acceso a la información de supervisión en Azure Portal

Vaya al siguiente tutorial para aprender a actualizar Kubernetes a una nueva versión.

> [!div class="nextstepaction"]
> [Actualización de Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml
