---
title: Equilibrio de carga de contenedores de Kubernetes en Azure | Microsoft Docs
description: "Conéctese externamente y equilibre la carga entre varios contenedores en un clúster de Kubernetes en Azure Container Service."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Contenedores, microservicios, Kubernetes, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 545ec23bc720dc1a17ce3d084642e96c2397d482
ms.openlocfilehash: e61638db3c6c3acdc58f374b94b28d855161cf59


---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>Equilibrio de carga de contenedores en un clúster de Kubernetes en Azure Container Service 
En este artículo se detalla el equilibrio de carga en un clúster de Kubernetes en Azure Container Service. El equilibrio de carga proporciona una dirección IP accesible desde el exterior para el servicio y distribuye el tráfico de red entre los pod que se ejecutan en VM del agente.

Puede configurar un servicio de Kubernetes para usar [Azure Load Balancer](../load-balancer/load-balancer-overview.md) para administrar el tráfico de red externo (TCP o UDP). Con la configuración adicional, son posibles el equilibrio de carga y el enrutamiento del tráfico HTTP o HTTPS, así como escenarios más avanzados.

## <a name="prerequisites"></a>Requisitos previos
* [Implementar un clúster de Kubernetes](container-service-kubernetes-walkthrough.md) en Azure Container Service
* [Conectar el cliente](container-service-connect.md) al clúster

## <a name="azure-load-balancer"></a>Azure Load Balancer

De forma predeterminada, un clúster de Kubernetes implementado en Azure Container Service incluye Azure Load Balancer accesible desde Internet para las VM del agente. (Se configura un recurso de equilibrador de carga independiente para las VM maestras). Azure Load Balancer es un equilibrador de carga de nivel 4 (TCP y UDP).

Al crear un servicio de Kubernetes, puede configurar automáticamente Azure Load Balancer para permitir el acceso al servicio. Para configurar el equilibrador de carga, establezca el servicio `type` en `LoadBalancer`. El equilibrador de carga crea una regla para asignar una dirección IP y el número de puerto públicos de tráfico de servicio entrante a las direcciones IP y los números de puerto privados de los pod en las VM del agente (y viceversa para el tráfico de respuesta). 

 A continuación encontrará dos ejemplos que muestran cómo establecer el servicio de Kubernetes `type` en `LoadBalancer`. (Después de probar los ejemplos, elimine las implementaciones si ya no las necesita).

### <a name="example-use-the-kubectl-expose-command"></a>Ejemplo: Usar el comando `kubectl expose` 
El [tutorial de Kubernetes](container-service-kubernetes-walkthrough.md) incluye un ejemplo de cómo exponer un servicio con el comando `kubectl expose` y su marca `--type=LoadBalancer`. Estos son los pasos que se deben seguir:

1. Inicie una nueva implementación de contenedor. Por ejemplo, el comando siguiente inicia una nueva implementación denominada `mynginx`. La implementación consta de tres contenedores basados en la imagen de Docker para el servidor web de Nginx.

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. Compruebe que los contenedores se están ejecutando. Por ejemplo, si realiza una consulta para los contenedores con `kubectl get pods`, verá una salida similar a la siguiente:

    ![Obtención de contenedores de Nginx](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. Para configurar el equilibrador de carga para que acepte tráfico externo en la implementación, ejecute `kubectl expose` con `--type=LoadBalancer`. El siguiente comando expone el servidor de Nginx en el puerto 80:

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. Escriba `kubectl get svc` para ver el estado de los servicios en el clúster. Mientras el equilibrador de carga configura la regla, `EXTERNAL-IP` del servicio aparece como `<pending>`. Después de unos minutos, se configura la dirección IP externa: 

    ![Configurar Azure Load Balancer](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. Compruebe que puede acceder al servicio en la dirección IP externa. Por ejemplo, abra un explorador web a la dirección IP mostrada. El explorador muestra el servidor web de Nginx ejecutándose en uno de los contenedores. O bien, ejecute el comando `curl` o `wget`. Por ejemplo:

    ```
    curl 13.82.93.130
    ```

    Debería mostrarse una salida similar a esta:

    ![Acceder a Nginx con curl](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. Para ver la configuración de Azure Load Balancer, vaya a [Azure Portal](https://portal.azure.com).

7. Busque el grupo de recursos para el clúster de servicio de contenedor y seleccione el equilibrador de carga para las VM del agente. Su nombre debe ser el mismo que el servicio de contenedor. (No elija el equilibrador de carga para los nodos maestros, aquel cuyo nombre incluye **master-lb**). 

    ![Equilibrador de carga en el grupo de recursos](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. Para ver los detalles de la configuración del equilibrador de carga, haga clic en **Reglas de equilibrio de carga** y en el nombre de la regla que se configuró.

    ![Reglas de equilibrador de carga](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>Ejemplo: Especificar `type: LoadBalancer` en el archivo de configuración de servicio

Si implementa una aplicación de contenedor de Kubernetes a partir de un [archivo de configuración de servicio](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file) YAML o JSON, especifique un equilibrador de carga externo agregando la siguiente línea a la especificación del servicio:

```YAML
 "type": "LoadBalancer"
``` 



Los pasos siguientes utilizan el [ejemplo del libro de visitas](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook) de Kubernetes. Este ejemplo es una aplicación web de varios niveles basada en imágenes de Redis y Docker PHP. Puede especificar en el archivo de configuración de servicio que el servidor PHP front-end utilice Azure Load Balancer.

1. Descargue el archivo `guestbook-all-in-one.yaml` desde [GitHub](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one). 
2. Busque `spec` para el servicio `frontend`.
3. Quite la marca de comentario de la línea `type: LoadBalancer`.

    ![Equilibrador de carga en configuración del servicio](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. Guarde el archivo e implemente la aplicación ejecutando el comando siguiente:

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. Escriba `kubectl get svc` para ver el estado de los servicios en el clúster. Mientras el equilibrador de carga configura la regla, `EXTERNAL-IP` del servicio `frontend` aparece como `<pending>`. Después de unos minutos, se configura la dirección IP externa: 

    ![Configurar Azure Load Balancer](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. Compruebe que puede acceder al servicio en la dirección IP externa. Por ejemplo, puede abrir un explorador web a la dirección IP externa del servicio.

    ![Acceder al libro de visitas externamente](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    Puede agregar entradas del libro de visitas.

7. Para ver la configuración de Azure Load Balancer, busque el recurso del equilibrador de carga para el clúster en [Azure Portal](https://portal.azure.com). Consulte los pasos del ejemplo anterior.

### <a name="considerations"></a>Consideraciones

* La creación de la regla del equilibrador de carga se realiza de forma asincrónica y la información sobre el equilibrador aprovisionado se publica en el campo `status.loadBalancer` del servicio.
* A cada servicio se le asigna automáticamente su propia dirección IP virtual en el equilibrador de carga.
* Si desea acceder al equilibrador de carga mediante un nombre DNS, trabaje con el proveedor de servicios de dominio para crear un nombre DNS para la dirección IP de la regla.

## <a name="http-or-https-traffic"></a>Tráfico HTTP o HTTPS

Para equilibrar la carga del tráfico HTTP o HTTPS para las aplicaciones web de contenedor y administrar certificados para Seguridad de la capa de transporte (TLS), puede usar el recurso [Ingress](https://kubernetes.io/docs/user-guide/ingress/) de Kubernetes. Ingress es una colección de reglas que permiten que las conexiones entrantes lleguen a los servicios de clúster. Para que un recurso Ingress funcione, el clúster de Kubernetes debe tener un [controlador Ingress](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers) ejecutándose.

Azure Container Service no implementa un controlador Ingress de Kubernetes automáticamente. Hay varias implementaciones de controlador disponibles. Actualmente, se recomienda el [controlador Ingress de Nginx](https://github.com/kubernetes/contrib/blob/master/ingress/controllers/nginx/README.md) para configurar las reglas Ingress y equilibrar la carga del tráfico HTTP y HTTPS. 

Para más información y ejemplos, consulte la [documentación del controlador Ingress de Nginx](https://github.com/kubernetes/contrib/blob/master/ingress/controllers/nginx/README.md).

> [!IMPORTANT]
> Cuando use el controlador Ingress de Nginx en Azure Container Service, debe exponer la implementación del controlador como un servicio con `type: LoadBalancer`. Esto configura Azure Load Balancer para enrutar tráfico hacia el controlador. Para más información, consulte la sección anterior.


## <a name="next-steps"></a>Pasos siguientes

* Consultar la [documentación del equilibrador de carga de Kubernetes](https://kubernetes.io/docs/user-guide/load-balancer/)
* Obtener más información sobre los controladores [Ingress de Kubernetes](https://kubernetes.io/docs/user-guide/ingress/)
* Consultar [ejemplos de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples)




<!--HONumber=Jan17_HO5-->


