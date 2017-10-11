---
title: "Administración de un clúster de Azure Kubernetes con una interfaz de usuario web | Microsoft Docs"
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
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: e31f90d61fc61f17582372fe9f491a1e21f628b0
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Uso de la interfaz de usuario web de Kubernetes con Azure Container Service

## <a name="prerequisites"></a>Requisitos previos
En este tutorial se da por supuesto que ha [creado un clúster de Kubernetes con Azure Container Service](container-service-kubernetes-walkthrough.md).


También se da por supuesto que tiene la CLI de Azure 2.0 y las herramientas de `kubectl` instaladas.

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
Puede iniciar la interfaz de usuario web de Kubernetes ejecutando lo siguiente:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Debería abrirse un explorador web configurado para comunicarse con un proxy seguro que conecta la máquina local con la interfaz de usuario web de Kubernetes.

### <a name="create-and-expose-a-service"></a>Creación y exposición de un servicio
1. En la interfaz de usuario web de Kubernetes, haga clic en el botón **Create** (Crear) de la ventana derecha superior.

    ![Interfaz de usuario de creación de Kubernetes](./media/container-service-kubernetes-ui/create.png)

    Se debería abrir un cuadro de diálogo en el que puede comenzar a crear la aplicación.

2. Utilice el nombre `hello-nginx`. Use el contenedor [ `nginx` de Docker](https://hub.docker.com/_/nginx/) e implemente las tres réplicas de este servicio web.

    ![Cuadro de diálogo de creación de pod de Kubernetes](./media/container-service-kubernetes-ui/nginx.png)

3. En **Service** (Servicio), seleccione **External** (Externo) y escriba el puerto 80.

    Esta configuración equilibra la carga de tráfico a las tres réplicas.

    ![Cuadro de diálogo de creación del servicio de Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Haga clic en **Deploy** (Implementar) para implementar estos contenedores y servicios.

    ![Implementación de Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Visualización de los contenedores
Después de hacer clic en **Deploy** (Implementar), la interfaz de usuario muestra una vista de su servicio tal y como se implementa:

![Estado de Kubernetes](./media/container-service-kubernetes-ui/status.png)

Puede ver el estado de cada objeto de Kubernetes en el círculo en el lado izquierdo de la interfaz de usuario, bajo **Pods** (Pods). Si es un círculo parcialmente completo, el objeto se sigue implementando. Cuando un objeto está totalmente implementado, muestra una marca de verificación verde:

![Implementación de Kubernetes](./media/container-service-kubernetes-ui/deployed.png)

Cuando todo funcione, puede hacer clic en uno de los pod para ver detalles sobre el servicio web que se esté ejecutando.

![Pod de Kubernetes](./media/container-service-kubernetes-ui/pods.png)

En la vista específica **Pods**, puede ver información sobre los contenedores del pod, así como los recursos de CPU y memoria utilizados por los contenedores:

![Recursos de Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Si no ve los recursos, es posible que tenga que esperar unos minutos para que los datos de supervisión de propaguen.

Para ver los registros para el contenedor, haga clic en **View logs** (Ver registros).

![Registros de Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visualización del servicio
Además de ejecutar los contenedores, la interfaz de usuario de Kubernetes ha creado una referencia externa `Service` que aprovisiona un equilibrador de carga para proporcionar tráfico a los contenedores en el clúster.

En el panel de navegación izquierdo, haga clic en **Services** (Servicios) para ver todos los servicios (debería haber solo uno).

![Servicios de Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

En esa vista debe ver un punto de conexión externo (dirección IP) asignado a su servicio.
Si hace clic en esa dirección IP, debería ver el contenedor de Nginx que se ejecuta detrás del equilibrador de carga.

![vista de nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Cambio del tamaño del servicio
Además de ver los objetos en la interfaz de usuario, también puede editar y actualizar los objetos de la API de Kubernetes.

En primer lugar, vaya a **Deployments** (Implentaciones) del panel de navegación izquierdo para ver la implementación de su servicio.

Cuando esté en esa vista, haga clic en el conjunto de réplicas y, luego, haga clic en **Edit** (Editar) en la barra de navegación superior:

![Edición de Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Edite el campo `spec.replicas` para establecerlo en `2`y haga clic en **Update** (Actualizar).

Esto hará que el número de réplicas descienda a dos mediante la eliminación de uno de los pod.

 

