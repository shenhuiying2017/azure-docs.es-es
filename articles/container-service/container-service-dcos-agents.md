<properties
   pageTitle="ACS de grupos de agentes públicos y privados | Microsoft Azure"
   description="Cómo funcionan los grupos de agentes públicos y privados con un clúster de Azure Container Service."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, microservicios, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="adegeo"/>

# Grupos de agentes

Azure Container Service divide los agentes en varios grupos. Y aunque cada máquina virtual de un grupo es idéntica, cada grupo tiene su propia subred. Por lo tanto, cada grupo puede marcarse como público o privado, lo cual afecta a la accesibilidad entre equipos del servicio de contenedores.

### Agentes privados

Los nodos de agente privado se ejecutan a través de una red no enrutable. Esta red solo es accesible desde la zona de administración o a través del enrutador de borde de la zona pública. De forma predeterminada, DC/OS inicia aplicaciones en nodos de agente privado. Consulte la [documentación de DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obtener más información acerca de la seguridad de red.

### Agentes públicos

Los nodos de agente público ejecutan servicios y aplicaciones de DC/OS a través de una red de acceso público. Consulte la [documentación de DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obtener más información acerca de la seguridad de red.

## Uso de grupos de agente

De forma predeterminada, **Marathon** implementa cualquier nueva aplicación en los nodos de agente *privado*. Tendrá que implementar explícitamente la aplicación en el nodo *público*. Lamentablemente, la implementación en el nodo público no es tan sencilla como activar una casilla de verificación Durante la creación de una nueva aplicación, vaya a la pestaña **Opcional** y escriba **slave\_public** en **Accepted Resource Roles** (Roles de recursos aceptados). Este proceso se describe [aquí](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) y en la documentación de [DC\\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## Pasos siguientes

Obtenga más información sobre cómo [administrar contenedores de DC/OS](container-service-mesos-marathon-ui.md).

<!---HONumber=AcomDC_0824_2016-->