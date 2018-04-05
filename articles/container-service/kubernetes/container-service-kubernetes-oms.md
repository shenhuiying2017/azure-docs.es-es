---
title: 'Supervisión de un clúster de Kubernetes en Azure: Operations Management'
description: Supervisión de un clúster de Kubernetes en Azure Container Service con Log Analytics
services: container-service
author: bburns
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: efe4b3a1a63fa1986682a2fdde1a20221dc5d93a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-an-azure-container-service-cluster-with-log-analytics"></a>Supervisión de un clúster de Azure Container Service con Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>requisitos previos
En este tutorial se da por supuesto que ha [creado un clúster de Kubernetes con Azure Container Service](container-service-kubernetes-walkthrough.md).

También se da por supuesto que tiene la CLI de Azure `az` y las herramientas de `kubectl` instaladas.

Puede probar si tiene la herramienta `az` instalada mediante la ejecución de:

```console
$ az --version
```

Si no tiene la herramienta `az` instalada, se ofrecen instrucciones [aquí](https://github.com/azure/azure-cli#installation).  
Como alternativa, puede usar [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que tiene la `az` CLI de Azure y `kubectl` herramientas ya instaladas.  

Puede probar si tiene la herramienta `kubectl` instalada mediante la ejecución de:

```console
$ kubectl version
```

Si no tiene la herramienta `kubectl` instalada, puede ejecutar:
```console
$ az acs kubernetes install-cli
```

Para probar si tiene claves de kubernetes instaladas en la herramienta de kubectl, puede ejecutar:
```console
$ kubectl get nodes
```

Si aparecen los errores anteriores de comando, debe instalar las claves de clúster de kubernetes en la herramienta de kubectl. Para ello, use el siguiente comando:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Supervisión de contenedores con Log Analytics

Log Analytics es la solución de administración de TI basada en la nube de Microsoft que lo ayuda a administrar y proteger su infraestructura local y en la nube. La solución de contenedor es una solución de Log Analytics, que le permite ver el inventario de contenedor, el rendimiento y los registros en una sola ubicación. Puede auditar contenedores y solucionar problemas de los mismos mediante la visualización de los registros en una ubicación centralizada, así como encontrar contenedores ruidosos con un consumo excesivo.

![](media/container-service-monitoring-oms/image1.png)

Para más información sobre la solución de contenedor, consulte [Solución Containers (versión preliminar) en Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalación de Log Analytics en Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obtención de la clave y el identificador de área de trabajo
Para que el agente OMS se comunique con el servicio tiene que configurarse con un identificador de área de trabajo y una clave de área de trabajo. Para obtener el identificador y la clave del área de trabajo que necesita para crear una cuenta en <https://mms.microsoft.com>.
Siga los pasos para crear una cuenta. Una vez que haya terminado de crear la cuenta, tendrá que obtener su identificador y su clave haciendo clic en **Configuración**, luego en **Connected Sources** (Orígenes conectados) y, por último, en **Servidores Linux**, tal y como se muestra a continuación.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Instalación del agente OMS con un DaemonSet
Kubernetes utiliza DaemonSets para ejecutar una única instancia de un contenedor en cada host del clúster.
Son perfectos para ejecutar agentes de supervisión.

Este es el [archivo DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Guárdelo en un archivo denominado `oms-daemonset.yaml` y reemplace los valores de marcador de posición de `WSID` y `KEY` con el identificador y la clave del área de trabajo en el archivo.

Cuando haya agregado la clave y el identificador de área de trabajo a la configuración de DaemonSet, puede instalar el agente de OMS en el clúster con la herramienta de línea de comandos `kubectl`:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-oms-agent-using-a-kubernetes-secret"></a>Instalación del agente de OMS con un Secreto de Kubernetes
Para proteger el identificador y la clave del área de trabajo de Log Analytics, puede usar un Secreto de Kubernetes como parte del archivo DaemonSet YAML.

 - Copie el script, el archivo de plantilla secreto y el archivo DaemonSet YAML (desde el [repositorio](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) y asegúrese de que estén en el mismo directorio. 
      - Script de generación de secretos: secret-gen.sh
      - Plantilla de secretos: secret-template.yaml
   - Archivo DaemonSet YAML: omsagent-ds-secrets.yaml
 - Ejecute el script. El script le pedirá el identificador del área de trabajo y la clave principal de Log Analytics. Inserte esos datos y el script creará un archivo yaml secreto para que pueda ejecutarlo.   
   ```
   #> sudo bash ./secret-gen.sh 
   ```

   - Ejecute lo siguiente para crear el pod de secretos: ``` kubectl create -f omsagentsecret.yaml ```
 
   - Para comprobarlo, ejecute lo siguiente: 

   ``` 
   root@ubuntu16-13db:~# kubectl get secrets
   NAME                  TYPE                                  DATA      AGE
   default-token-gvl91   kubernetes.io/service-account-token   3         50d
   omsagent-secret       Opaque                                2         1d
   root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
   Name:           omsagent-secret
   Namespace:      default
   Labels:         <none>
   Annotations:    <none>

   Type:   Opaque

   Data
   ====
   WSID:   36 bytes
   KEY:    88 bytes 
   ```
 
  - Ejecute ``` kubectl create -f omsagent-ds-secrets.yaml ``` para crear el daemon-set de omsagent

### <a name="conclusion"></a>Conclusión
Eso es todo. Pasados unos minutos, debería ver los datos que fluyen en el panel OMS.
