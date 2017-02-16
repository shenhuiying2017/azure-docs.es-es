---
title: "Supervisión de un clúster de Azure Kubernetes - Operations Management | Microsoft Docs"
description: "Supervisión de un clúster de Kubernetes en Azure Container Service utilizando Microsoft Operations Management Suite"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 2a124c42e6c90e9443475e1f46cf3e10b5d53d6a


---

# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Supervisión de un clúster de Azure Container Service con Microsoft Operations Management Suite (OMS)

## <a name="prerequisites"></a>Requisitos previos
En este tutorial se da por supuesto que ha [creado un clúster de Kubernetes con Azure Container Service](container-service-kubernetes-walkthrough.md).

También se da por supuesto que tiene la CLI de Azure `az` y las herramientas de `kubectl` instaladas.

Puede probar si tiene la herramienta `az` instalada mediante la ejecución de:

```console
$ az --version
```

Si no tiene la herramienta `az` instalada, se ofrecen instrucciones [aquí](https://github.com/azure/azure-cli#installation).

Puede probar si tiene la herramienta `kubectl` instalada mediante la ejecución de:

```console
$ kubectl version
```

Si no tiene la herramienta `kubectl` instalada, puede ejecutar:

```console
$ az acs kubernetes install-cli
```

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Supervisión de contenedores con Operations Management Suite (OMS)

Microsoft Operations Management (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura en la nube y local. La solución de contenedor es una solución de Log Analytics de OMS, que le permite ver el inventario de contenedor, el rendimiento y los registros en una sola ubicación. Puede auditar contenedores y solucionar problemas de los mismos mediante la visualización de los registros en una ubicación centralizada, así como encontrar contenedores ruidosos con un consumo excesivo.

![](media/container-service-monitoring-oms/image1.png)

Para más información sobre la solución de contenedor, consulte [Solución Containers (versión preliminar) en Log Analytics](../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Instalación de OMS en Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obtención de la clave y el identificador de área de trabajo
Para que el agente OMS se comunique con el servicio tiene que configurarse con un identificador de área de trabajo y una clave de área de trabajo. Para obtener ambos tiene que crear una cuenta OMS en <https://mms.microsoft.com>.
Siga los pasos para crear una cuenta. Una vez que haya terminado de crear la cuenta, tendrá que obtener su identificador y su clave haciendo clic en **Configuración**, luego en **Connected Sources** (Orígenes conectados) y, por último, en **Servidores Linux**, tal y como se muestra a continuación.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Instalación del agente OMS con un DaemonSet
Kubernetes utiliza DaemonSets para ejecutar una única instancia de un contenedor en cada host del clúster.
Son perfectos para ejecutar agentes de supervisión.

Este es el archivo DaemonSet YAML. Guárdelo en un archivo denominado `oms-daemonset.yaml` y reemplace los valores de marcador de posición de `WSID` y `KEY` a continuación con el identificador y la clave del área de trabajo en el archivo.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: omsagent
spec:
  template:
    metadata:
      labels:
        app: omsagent
    spec:
      containers:
      - env:
        - name: WSID
          value: <your workspace ID>
        - name: KEY
          value: <your key>
        image: microsoft/oms
        name: omsagent
        ports:
        - containerPort: 25225
          protocol: TCP
        securityContext:
          privileged: true
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
```

Cuando haya agregado la clave y el identificador de área de trabajo a la configuración de DaemonSet, puede instalar el agente de OMS en el clúster con la herramienta de línea de comandos `kubectl`:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="conclusion"></a>Conclusión
Eso es todo. Pasados unos minutos, debería ver los datos que fluyen en el panel OMS.



<!--HONumber=Jan17_HO4-->


