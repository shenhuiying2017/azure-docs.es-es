---
title: "Conmutación por error y escalado de una aplicación de contenedores de Azure Service Fabric | Microsoft Docs"
description: "Obtenga información sobre cómo se administra la conmutación por error en una aplicación de contenedores de Azure Service Fabric.  Asimismo, obtenga información sobre cómo escalar los contenedores y servicios en ejecución en un clúster."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, Containers, microservicios, Service Fabric, Azure
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 21dd9dfbc90c26236c43e2c334305ca97f63d361
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Demostración de la conmutación por error y el escalado de servicios de contenedor con Service Fabric

Este tutorial es la tercera parte de una serie. En este tutorial, obtiene información sobre cómo se administra la conmutación por error en aplicaciones contenedoras de Service Fabric. Además, obtiene información sobre cómo escalar contenedores. En este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Obtener información sobre la conmutación por error del contenedor en un clúster de Service Fabric  
> * Escalar los contenedores de front-end web en una aplicación

## <a name="prerequisites"></a>Requisitos previos
La aplicación de [Parte 2](service-fabric-tutorial-package-containers.md) se ejecuta en un clúster de Service Fabric activo.

## <a name="fail-over-a-container-in-a-cluster"></a>Conmutación por error de un contenedor en un clúster
Service Fabric se asegura de que sus instancias de contenedor se mueven automáticamente a otros nodos del clúster, en caso de error. También puede descargar manualmente un nodo de contenedores y moverlos correctamente a otros nodos del clúster. Tiene varias formas de escalar sus servicios. En este ejemplo, usamos Service Fabric Explorer.

Para conmutar por error el contenedor de front-end, siga estos pasos:

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Haga clic en el nodo **fabric:/TestContainer/azurevotefront** en la vista de árbol y expanda el nodo de partición (representado por un GUID). Observe el nombre de nodo de la vista de árbol, que le muestra los nodos en los que el contenedor se ejecuta actualmente (por ejemplo, `_nodetype_1`)
3. Expanda el nodo **Nodos** en la vista de árbol. Haga clic en el botón de puntos suspensivos (tres puntos) situado junto al nodo, que ejecuta el contenedor.
1. Elija **Reiniciar** para reiniciar ese nodo y confirmar la acción de reinicio. El reinicio hace que el contenedor conmute por error a otro nodo del clúster.

![noderestart][noderestart]

Observe cómo cambia actualmente el nombre de nodo que indica dónde se ejecutan los contenedores de front-end por otro nodo del clúster. Transcurridos unos instantes, debería poder ir a la aplicación de nuevo y ver la aplicación ahora en ejecución en un nodo diferente.

## <a name="scale-containers-and-services-in-a-cluster"></a>Escalar contenedores y servicios en un clúster
Se pueden escalar contenedores de Service Fabric en un clúster para adaptarse a la carga en los servicios. Para escalar un contenedor, cambie el número de instancias que se ejecutan en el clúster.

Para escalar el front-end web, siga estos pasos:

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Haga clic en el botón de puntos suspensivos (tres puntos) situado junto al nodo **fabric:/TestContainer/azurevotefront** en la vista de árbol y elija **Scale Service** (Escalar servicio).

![sfxscale][sfxscale]

Ahora puede escalar el número de instancias del front-end web.

3. Cambie el número a **2** y haga clic en **Scale Service** (Escalar servicio).
4. Haga clic en el nodo **fabric:/TestContainer/azurevotefront** en la vista de árbol y expanda el nodo de partición (representado por un GUID).

![sfxscaledone][sfxscaledone]

Ahora puede ver que el servicio tiene dos instancias. En la vista de árbol, consulta en qué nodos se ejecutan las instancias.

Mediante esta sencilla tarea de administración, hemos duplicado los recursos disponibles para el servicio front-end para procesar la carga de usuarios. Es importante entender que no hacen falta varias instancias de un servicio para que se ejecute de forma confiable. Si se produce un error en un servicio, Service Fabric se asegurará de que se ejecute una nueva instancia de servicio en el clúster.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se demostró la conmutación por error del contenedor, así como el escalado de una aplicación. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Obtener información sobre la conmutación por error del contenedor en un clúster de Service Fabric  
> * Escalar los contenedores de front-end web en una aplicación

En esta serie de tutoriales, aprendió lo siguiente: 
> [!div class="checklist"]
> * Creación de imágenes de contenedor
> * Inserción de imágenes de contenedor en Azure Container Registry
> * Empaquetado de Containers para Service Fabric mediante Yeoman
> * Creación y ejecución de una aplicación de Service Fabric con Containers
> * Cómo se administran la conmutación por error y el escalado en Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png
