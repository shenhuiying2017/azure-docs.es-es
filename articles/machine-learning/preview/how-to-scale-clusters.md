---
title: "Escalado de un clúster de Azure Container Service para Machine Learning | Microsoft Docs"
description: "Escalado de un clúster de ACS (escalado automático y escalado estático); escalado del número de nodos del clúster"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 8d709936bfba5c89091d7f26449d165bddb930de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Escalado del clúster para administrar el rendimiento del servicio web

## <a name="why-scale-the-cluster"></a>¿Por qué escalar el clúster?

El escalado del clúster de Azure Container Service (ACS) es una forma efectiva de optimizar el rendimiento del servicio y, al mismo tiempo, mantener el tamaño del clúster al mínimo para reducir costos. 

Para comprender mejor el escalado automático, observe el siguiente ejemplo de un clúster que ejecuta tres servicios web:

![Ejemplo: Tres servicios en un clúster](media/how-to-scale-clusters/three-services.png)

Los servicios tienen distintos períodos de hora punta: Service 1 (línea azul) requiere 40 pods en hora punta, Service 2 (línea naranja) requiere 38 en hora punta y Service 3 (línea gris) requiere 50 en hora punta. Si reserva la capacidad máxima necesaria para cada servicio individualmente, este clúster necesitaría al menos 40 + 38 + 50 = 128 pods en total.

Pero tenga en cuenta el uso de pod real en cualquier punto en el tiempo, representado por la línea discontinua negra en el gráfico. En este caso, el *número más alto de pods que se utilizan en cualquier momento* es 64, que tiene lugar a las 20:00 cuando Service 3 se encuentran en hora punta. En este momento, Service 3 utiliza 50 pods, pero Service 2 y Service 1 usan solo 9 y 5 pods respectivamente. Recuerde que se trata del *uso máximo* para este clúster. Esto significa que en ningún momento el clúster usa más de 64 pods, la mitad el requisito calculado de 128 pods para los tres servicios escalados de forma independiente para el uso máximo.

Mediante la reasignación de pods en el clúster, es decir, mediante el reescalado, para satisfacer la demanda actual de cada servicio en lugar de simplemente requerir recursos suficientes para los períodos de hora punta de todos los servicios, puede reducir el tamaño del clúster. En este sencillo ejemplo, el escalado automático reduce el número necesario de pods de 128 a 64, lo que recorta el tamaño del clúster requerido a la mitad.

El escalado del número de pods es una operación relativamente rápida que requiere menos de un minuto, por lo que la capacidad de respuesta del servicio no resulta gravemente afectada.

> [!NOTE]
> El escalado de un clúster no le ayudará con los problemas de latencia de solicitud. Para fines de operacionalización, el escalado vertical debe aumentar el número de operaciones correctas y disminuir los errores de servicios no disponibles. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Escalado de servicios web en el clúster de ACS

La opción de configuración de clúster de la CLI de Administración de modelos configura de forma predeterminada dos agentes y uno pod en su entorno. También instala al CLI de Kubernetes.

Puede escalar los servicios web que haya implementado en ACS ajustando lo siguiente:

* El número de nodos de agente del clúster
* El número de réplicas de pod de Kubernetes que se ejecutan en nodos de agente

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Escalado del número de nodos del clúster

El siguiente comando establece el recuento de nodos de agente en el clúster:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Esta operación tardará algunos minutos en completarse. Para más información sobre el escalado del número de nodos del clúster, consulte [Escalado de nodos de agente en un clúster de Container Service](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Escalado del número de réplicas de pod de Kubernetes en un clúster
 
Puede escalar el número de réplicas de pod asignado al clúster mediante la CLI de Azure Machine Learning o el [panel de Kubernetes] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Para más información sobre pods de réplica de Kubernetes, consulte la documentación de [pods de Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/).

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Escalado de un clúster con la CLI de Azure Machine Learning

Hay dos formas de escalar un clúster mediante la CLI:

- Autoscale
- Escalado estático

El escalado automático está activo de forma predeterminada cuando se crea el servicio y en la mayoría de los casos es el método de escalado preferido.

##### <a name="autoscale"></a>Autoscale

El siguiente comando habilita el escalado automático y establece el número mínimo y máximo de réplicas para el servicio.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Por ejemplo, si se establece `autoscale-min-replicas` en cinco, se crearán cinco réplicas. Para llegar a un número óptimo para el servicio web, establezca el número de valores como en diez y supervise el número de 503 mensajes de error. Después, ajuste el número en consecuencia.


| Nombre de parámetro | Tipo | Descripción |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | boolean | Especifica si el escalado automático está habilitado. Valor predeterminado: true |
| `autoscale-min-replicas` | integer | Especifica el número mínimo de pods. Debe ser 0 o superior. Valor predeterminado: 1 |
| `autoscale-max-replicas` | integer | Especifica el número máximo de pods. Debe ser 1 o superior. Si autoscale-max-replicas es menor que autoscale-min-replicas, autoscale-max-replicas se omitirá. Valor predeterminado: 10 |
| `autoscale-refresh-period-seconds` | integer | Especifica la duración en segundos entre actualizaciones de escalado automático. Valor predeterminado: 1 |
| `autoscale-target-utilization` | integer | Especifica el porcentaje de utilización que el escalado automático tiene como objetivo, entre 1 y 100. Valor predeterminado: 70 |

El escalado automático funciona para garantizar las dos condiciones siguientes:

1. Se cumple la utilización de destino
2. El escalado nunca supera los valores de configuración máximo y mínimo

Los servicios en un clúster compiten por los recursos del clúster. Un servicio escalado automáticamente aumentará su uso de recursos de clúster a medida que sus solicitudes por segundo (RPS) aumentan y liberará lentamente los recursos a medida que dichas solicitudes se reducen. Los recursos de clúster se adquirirán bajo demanda siempre que existan para que el servicio los adquiera.

Para más información sobre el uso de los parámetros de escalado automático, consulte la documentación [Referencia de la interfaz de línea de comandos de Administración de modelos](model-management-cli-reference.md).

##### <a name="static-scale"></a>Escalado estático

En general, el escalado estático debería evitarse, ya que no permite la reducción del tamaño de clúster del escalado automático. Aun así, en algunas situaciones, el escalado estático podría ser aconsejable. Por ejemplo, cuando un clúster está dedicado a un servicio único, el escalado automático no proporciona ninguna ventaja; todos los recursos de clúster se deben asignar a ese servicio.

Para escalar un cluster estáticamente, el escalado automático debe estar desactivado. Deshabilite el escalado automático con el siguiente comando:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Después de desactivar el escalado automático, el siguiente comando escala directamente el número de réplicas para un servicio.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Para más información sobre el escalado del número de nodos del clúster, consulte Escalado de nodos de agente en un clúster de Container Service.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Escalado del número de réplicas mediante el panel de Kubernetes

En la línea de comandos, escriba:

```
kubectl proxy
```

En Windows, la ubicación de instalación de Kubernetes no se agrega automáticamente a la ruta de acceso. En primer lugar, navegue a la carpeta de instalación:

```
c:\users\<user name>\bin
```

Cuando ejecute el comando, debería ver el siguiente mensaje informativo:

```
Starting to serve on 127.0.0.1:8001
```

Si el puerto ya está en uso, verá un mensaje similar al siguiente ejemplo:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Puede especificar un número de puerto alternativo mediante el parámetro *--port* .

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Una vez que ha iniciado el servidor de panel, abra un explorador y escriba la siguiente dirección URL:

```
127.0.0.1:<port number>/ui
```

En la pantalla principal del panel, haga clic en **Implementaciones** en la barra de navegación izquierda. Si el panel de navegación no se muestra, seleccione este icono ![Menú que consta de tres líneas horizontales cortas](media/how-to-scale-clusters/icon-hamburger.png) en la parte superior izquierda.

Busque la implementación que desea modificar y haga clic en este icono ![Icono de menú que consta de tres puntos verticales](media/how-to-scale-clusters/icon-kebab.png) situado a la derecha y, luego, haga clic en **Ver/Editar YAML**.

En la pantalla Editar implementación, busque el nodo *spec*, modifique el valor *replicas* y haga clic en **Actualizar**.
