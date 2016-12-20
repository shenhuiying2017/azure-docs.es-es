---
title: "Implementación de un clúster de Azure Container Service con Kubernetes | Microsoft Docs"
description: "Implementación de un clúster de Azure Container Service con Kubernetes"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 0989c85e4b7c73a8b7b335254e4af90ef34ddc01
ms.openlocfilehash: 8c10da9d2363f0938d821b59cc4fd58b53f9ed17


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Motor de Microsoft Azure Container Service: tutorial de Kubernetes

## <a name="deployment"></a>Implementación

Estos son los pasos para implementar un clúster de Kubernetes simple:

1. [generar la clave ssh](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#ssh-key-generation)
3. [generar la entidad de servicio](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md)
4. Haga clic en el [botón Implementar en Azure en el archivo LÉAME](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-kubernetes/README.md) y rellene los campos.

## <a name="walkthrough"></a>Tutorial

Una vez creado el clúster de Kubernetes, tendrá un grupo de recursos con:

1. Un maestro al que se puede acceder mediante SSH en el puerto 22 o kubectl en el puerto 443.

2. Un conjunto de nodos en un conjunto de disponibilidad.  Se puede acceder a los nodos mediante un maestro.  Consulte [desvío de agente](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#key-management-and-agent-forwarding-with-windows-pageant) para ver un ejemplo de cómo hacerlo.

La siguiente imagen muestra la arquitectura de un clúster del servicio de contenedor con un maestro y dos agentes:

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

En la imagen anterior, puede ver las partes siguientes:

1. **Componentes del maestro**: el maestro ejecuta el programador de Kubernetes, el servidor de API y el administrador de controladores.  El puerto 443 se expone para la administración remota con la CLI de kubectl.
2. **Nodos**: los nodos de Kubernetes se ejecutan en un conjunto de disponibilidad.  Las instancias de Azure Load Balancer se agregan dinámicamente al clúster según los servicios expuestos. 
3. **Componentes comunes**: todas las máquinas virtuales ejecutan un elemento kubelet, Docker y un servidor Proxy.
4. **Redes**: todas las máquinas virtuales se asignan a una dirección IP en la red 10.240.0.0/16.  Cada máquina virtual se asigna a una subred /24 para su CIDR de pod que habilita la IP para cada pod.  El proxy que se ejecuta en cada máquina virtual implementa la red de servicio 10.0.0.0/16.

Todas las máquinas virtuales están en la misma red privada virtual y son totalmente accesibles entre sí.

## <a name="create-your-first-kubernetes-service"></a>Creación de su primer servicio Kubernetes

Después de completar este tutorial sabrá:
 * Cómo acceder a un clúster de Kubernetes mediante SSH.
 * Cómo implementar una aplicación simple de Docker y exponerla a todo el mundo.
 * La ubicación del archivo de configuración Kube y cómo acceder al clúster de Kubernetes de forma remota.
 * Cómo usar `kubectl exec` para ejecutar comandos en un contenedor. 
 * Por último, acceder al panel de Kubernetes.

1. Después de implementar correctamente la plantilla, anote los nombres de dominio completo del maestro.
   1. Si usa Powershell o CLI, el parámetro output está en la sección de OutputsString denominada "masterFQDN".
   2. Si usa el Portal, vaya a la hoja de información general del recurso ContainerService para copiar el nombre de dominio completo del maestro:
     
   ![Imagen del escalado del docker](media/container-service-kubernetes-walkthrough/portal-kubernetes-outputs.png)

2. Haga SSH en el nombre de dominio completo del maestro obtenido en el paso 1.

3. Explore los nodos y los pods que se están ejecutando:
  1. Para ver una lista de los nodos, escriba `kubectl get nodes`.  Si desea ver todos los detalles de los nodos, agregue `-o yaml` para que sea `kubectl get nodes -o yaml`.
  2. Para ver una lista de los pods que se están ejecutando, escriba `kubectl get pods --all-namespaces`.

4. Inicie la primera imagen de Docker escribiendo `kubectl run nginx --image nginx`.  Esto iniciará el contenedor de Docker nginx en un pod en uno de los nodos.

5. Escriba `kubectl get pods -o yaml` para ver los detalles completos de la implementación de nginx. Puede ver las direcciones IP del host y del pod.  La dirección IP de pod se asigna desde el CIDR de pod en el host.  Ejecute curl para la dirección IP de pod para ver la salida de nginx; p. ej. `curl 10.244.1.4`

  ![Imagen de curl para podIP](media/container-service-kubernetes-walkthrough/kubernetes-nginx1.png)

6. El siguiente paso es exponer la implementación de nginx como un servicio Kubernetes en la red privada del servicio 10.0.0.0/16:
  1. Exponga el servicio con el comando `kubectl expose deployment nginx --port=80`.
  2. Obtenga la dirección IP de servicio `kubectl get service`.
  3. Ejecute curl para la dirección IP; p. ej. `curl 10.0.105.199`

  ![Imagen de curl para la IP del servicio](media/container-service-kubernetes-walkthrough/kubernetes-nginx2.png)

7. El paso final es exponer el servicio a todo el mundo.  Esto se realiza cambiando el tipo de servicio de `ClusterIP` a `LoadBalancer`:
  1. Edite el servicio: `kubectl edit svc/nginx`
  2. Cambie `type` de `ClusterIP` a `LoadBalancer` y guárdelo.  Esto hará que Kubernetes cree una instancia de Azure Load Balancer con una dirección IP pública.
  3. El cambio tardará unos 2 o 3 minutos.  Para ver como el servicio cambia de "pendiente" a una dirección IP externa, escriba `watch 'kubectl get svc'`.

  ![Imagen de cómo se ve la transición de pendiente a una dirección IP externa](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

  4. Una vez que vea la dirección IP externa, puede desplazarse hasta ella en el explorador:

  ![Imagen del acceso a nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  

8. El siguiente paso de este tutorial es mostrar cómo administrar de forma remota su clúster de Kubernetes.  Primero, descargue Kubectl en su máquina y colóquelo en la ruta de acceso:
  * [Kubectl para Windows](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/windows/amd64/kubectl.exe)
  * [Kubectl para OSX](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/darwin/amd64/kubectl)
  * [Linux](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/linux/amd64/kubectl)

9. El maestro de Kubernetes contiene el archivo de configuración kube para el acceso remoto en el directorio particular ~/.kube/config.  Descargue este archivo en su máquina, establezca la variable de entorno KUBECONFIG y ejecute kubectl para comprobar que puede conectarse al clúster:
  * Para Windows utilice pscp desde [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Asegúrese de que el certificado se expone mediante [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```
  * OS X o Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
10. El siguiente paso es mostrar cómo se ejecutan comandos de forma remota en un contenedor de Docker remoto:
  1. Ejecute `kubectl get pods` para mostrar el nombre de su pod nginx.
  2. Mediante el nombre de su pod, puede ejecutar un comando remoto en su pod.  P. ej. `kubectl exec nginx-701339712-retbj date`
  3. Vuelva a ejecutar una sesión remota de Bash. P. ej. `kubectl exec nginx-701339712-retbj -it bash`.  La siguiente captura de pantalla muestra estos comandos:

  ![Imagen de curl para podIP](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)

11. El paso final de este tutorial es mostrarle el panel:
  1. Ejecute `kubectl proxy` para conectarse directamente al servidor proxy.
  2. En el explorador, vaya al [panel](http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all).
  3. Mire alrededor y explore los pods y los servicios.
  ![Imagen del panel de Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

# <a name="learning-more"></a>Más información

A continuación, se recomiendan vínculos para aprender más acerca de Kubernetes:

1. [Documentación de Azure Kubernetes](https://azure.microsoft.com/en-us/documentation/services/container-service/)

## <a name="kubernetes-community-documentation"></a>Documentación de la comunidad de Kubernetes

1. [Campo de entrenamiento de Kubernetes](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html): muestra cómo implementar, escalar, actualizar y depurar aplicaciones en contenedores.
2. [Guía del usuario de Kubernetes](http://kubernetes.io/docs/user-guide/): proporciona información sobre cómo ejecutar programas en un clúster de Kubernetes existente.
3. [Ejemplos de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples): proporciona una serie de ejemplos de cómo ejecutar aplicaciones reales con Kubernetes.



<!--HONumber=Nov16_HO3-->


