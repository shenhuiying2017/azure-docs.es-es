---
title: Uso de la interfaz de usuario web de Kubernetes en Azure Container Service | Microsoft Docs
description: Uso de la interfaz de usuario web de Kubernetes en Azure Container Service
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
ms.sourcegitcommit: bcc2d3468c8a560105aa2c2feb0d969ec3cccdcb
ms.openlocfilehash: 9ae6e606bf215d9f53f725899b8ac7c466b781f1


---

# <a name="microsoft-azure-container-service-engine---using-the-kubernetes-web-ui"></a>Motor de Microsoft Azure Container Service - Uso de la interfaz de usuario web de Kubernetes

## <a name="prerequisites"></a>Requisitos previos
En este tutorial se da por supuesto que ha [creado un clúster de Kubernetes con Azure Container Service](container-service-kubernetes-walkthrough.md).

> [!NOTE]
> La compatibilidad con Kubernetes en Azure Container Service está actualmente en versión preliminar.
>

También se da por supuesto que tiene herramientas de kubectl y de la CLI de Azure instaladas.

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

## <a name="overview"></a>Información general

### <a name="connect-to-the-web-ui"></a>Conexión a la interfaz de usuario web
Puede iniciar la interfaz de usuario web de Kubernetes ejecutando:

```console
$ az acs kubernetes browse
```

Debería abrirse un explorador web configurado para comunicarse con un proxy seguro que conecta la máquina local con la interfaz de usuario web de Kubernetes.

### <a name="create-and-expose-a-service"></a>Creación y exposición de un servicio
En la interfaz de usuario web de Kubernetes, debería ver un botón 'Crear' en la ventana derecha superior.

![Interfaz de usuario de creación de Kubernetes](media/k8s/create.png)

Se debería abrir un cuadro de diálogo en el que puede comenzar a crear la aplicación.
Utilice el nombre `hello-nginx`. Use el contenedor [ `nginx` de Docker](https://hub.docker.com/_/nginx/) e implemente las tres réplicas de este servicio web.

![Cuadro de diálogo de creación de pod de Kubernetes](media/k8s/nginx.png)

Después, cree un servicio de Kubernetes "externo" para equilibrar la carga del tráfico en nuestras tres réplicas.  Seleccione 'Externo' y escriba el puerto 80.

![Cuadro de diálogo de creación del servicio de Kubernetes](media/k8s/service.png)

Por último, pulse el botón 'Implementar' para implementar estos contenedores y servicios.

![Implementación de Kubernetes](media/k8s/deploy.png)

### <a name="view-your-containers"></a>Visualización de los contenedores
Después de presionar 'Implementar', la interfaz de usuario muestra una vista de su servicio tal y como se implementa:

![Estado de Kubernetes](media/k8s/status.png)

Puede ver el estado de cada objeto de Kubernetes en el círculo en el lado izquierdo de la interfaz de usuario. Si es un círculo parcialmente completo, el objeto se sigue implementando. Cuando un objeto está totalmente implementado, muestra una marca de verificación verde:

![Implementación de Kubernetes](media/k8s/deployed.png)

Una vez que todo funciona, puede hacer clic en uno de los pod para ver detalles sobre la ejecución del servicio web

![Pod de Kubernetes](media/k8s/pods.png)

En la vista específica del pod, puede ver información acerca de los contenedores en el pod, así como los recursos de CPU y memoria utilizados por los contenedores:

![Recursos de Kubernetes](media/k8s/resources.png)

Si no ve los recursos, es posible que tenga que esperar unos minutos para que los datos de supervisión de propaguen.

También puede hacer clic en el vínculo 'Registros' para ver los registros para el contenedor:

![Registros de Kubernetes](media/k8s/logs.png)

### <a name="viewing-your-service"></a>Visualización del servicio
Además de ejecutar los contenedores, la interfaz de usuario de Kubernetes ha creado una referencia externa `Service` que aprovisiona un equilibrador de carga para proporcionar tráfico a los contenedores en el clúster.

Puede hacer clic en 'Servicios' en el panel de navegación izquierdo para ver todos los servicios (actualmente debe ser solo uno)

![Servicios de Kubernetes](media/k8s/service-deployed.png)

En esa vista, podrá ver una dirección IP externa asignada a su servicio.
Si hace clic en esa dirección IP, debería ver el contenedor de nginx que se ejecuta detrás del equilibrador de carga.

![vista de nginx](media/k8s/nginx-page.png)

### <a name="resizing-your-service"></a>Cambio del tamaño del servicio
Además de ver los objetos en la interfaz de usuario, también puede editar y actualizar los objetos de la API de Kubernetes.

En primer lugar, vaya a `Deployment` para su servicio y haga clic en las 'Implementaciones' en el panel de navegación izquierdo.

Una vez que esté en esa vista, haga clic en ReplicaSet y, luego, haga clic en el botón 'Editar' en la barra de navegación superior:

![Edición de Kubernetes](media/k8s/edit.png)

Edite el campo `spec.replicas` para que sea '2' y presione 'Actualizar'.

Esto hará que el número de réplicas descienda a dos mediante la eliminación de uno de los pod.

 




<!--HONumber=Dec16_HO3-->


