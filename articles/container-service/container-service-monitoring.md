---
title: Supervisión de un clúster del servicio de contenedores de Azure con Datadog | Microsoft Docs
description: Supervise un clúster del servicio de contenedores de Azure con Datadog. Utilice la interfaz de usuario web de DC/OS para implementar agentes de Datadog en el clúster.
services: container-service
documentationcenter: ''
author: rbitia
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Contenedores, DC/OS, Docker Swarm, Azure

ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 07/28/2016
ms.author: t-ribhat

---
# Supervisión de un clúster del servicio de contenedores de Azure con Datadog
En este artículo, implementaremos agentes Datadog en todos los nodos de agente del clúster del servicio de contenedores de Azure. Necesita una cuenta con Datadog para esta configuración.

## Requisitos previos
[Implemente](container-service-deployment.md) y [conecte](container-service-connect.md) un clúster configurado por el servicio de contenedores de Azure. Explore la [interfaz de usuario de Marathon](container-service-mesos-marathon-ui.md). Vaya a [http://datadoghq.com](http://datadoghq.com) para configurar una cuenta de Datadog.

## Datadog
Datadog es un servicio de supervisión que recopila datos de supervisión de los contenedores dentro del clúster del servicio de contenedor de Azure. Datadog tiene un panel de integración de Docker donde puede ver las métricas específicas dentro de los contenedores. Las métricas que se recopilan de los contenedores están organizadas por CPU, memoria, red y E/S. Datadog divide las métricas en contenedores e imágenes. A continuación se muestra un ejemplo del aspecto de la interfaz de usuario para el uso de CPU.

![Interfaz de usuario de Datadog](./media/container-service-monitoring/datadog4.png)

## Configuración de una implementación de Datadog con Marathon
Estos pasos muestran cómo configurar e implementar aplicaciones de Datadog en su clúster con Marathon.

Acceda a la interfaz de usuario de DC/OS mediante [http://localhost:80/](http://localhost:80/). Una vez en la interfaz de usuario de DC/OS, vaya a "Universe" (Universo) en la parte inferior izquierda, busque "Datadog" y haga clic en "Install" (Instalar).

![Paquete de Datadog dentro de Universe (Universo) de DC/OS](./media/container-service-monitoring/datadog1.png)

Ahora, para completar la configuración, necesita una cuenta de Datadog o una cuenta de evaluación gratuita. Cuando haya iniciado sesión en el sitio web de Datadog, mire a la izquierda y vaya a Integrations (Integraciones) y, después, a API.

![Clave de API de Datadog](./media/container-service-monitoring/datadog2.png)

Después, escriba la clave de API en la configuración de Datadog dentro de Universe (Universo) en DC/OS.

![Configuración de Datadog en Universe en DC/OS](./media/container-service-monitoring/datadog3.png)

En la configuración anterior, las instancias se establecen en 10000000 para que, siempre que se agregue un nuevo nodo al clúster de Datadog, se implemente automáticamente un agente en ese nodo nuevo. Se trata de una solución provisional. Una vez instalado el paquete, debe volver al sitio web de Datadog y buscar "Dashboards" (Paneles). Desde allí, verá los paneles de integración y personalizados. El panel de integración de Docker tendrá todas las métricas de contenedor que necesita para supervisar el clúster.

<!---HONumber=AcomDC_0810_2016-->