<properties
   pageTitle="Presentación del servicio Contenedor de Azure | Microsoft Azure"
   description="El servicio Contenedor de Azure ofrece una forma de simplificar la creación, configuración y administración de un clúster de máquinas virtuales preconfiguradas para ejecutar aplicaciones en contenedor."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
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
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# Presentación del servicio Contenedor de Azure

El servicio Contenedor de Azure simplifica la creación, configuración y administración de un clúster de máquinas virtuales preconfiguradas para ejecutar aplicaciones en contenedor. Usa una configuración optimizada de herramientas de programación y orquestación de código abierto conocidas. Esto le permite usar sus conocimientos o recurrir a un importante y creciente grupo de expertos comunitarios para implementar y administrar aplicaciones basadas en contenedores en Microsoft Azure.


![El servicio Contenedor de Azure proporciona un medio para administrar aplicaciones en contenedor en varios hosts de Azure.](./media/acs-intro/acs-cluster.png)


El servicio Contenedor de Azure usa el formato de contenedor Docker para garantizar que los contenedores de su aplicación sean completamente portátiles. También es compatible con su elección de Marathon y DC/OS o Docker Swarm para que pueda escalar estas aplicaciones a miles e incluso a decenas de miles de contenedores.

Con el servicio Contenedor de Azure puede aprovechar las características empresariales de Azure sin dejar de mantener la portabilidad de aplicaciones, incluida la portabilidad en las capas de orquestación.

Uso del servicio Contenedor de Azure
-----------------------------

Nuestro objetivo con el servicio Contenedor de Azure es proporcionar un entorno de hospedaje de contenedores mediante el uso de tecnologías y herramientas de código abierto, conocidas por nuestros clientes. Con este fin, se exponen los puntos de conexión de API estándar y el orquestador que haya elegido (DC/OS o Docker Swarm). Al usar estos puntos de conexión, puede aprovechar cualquier software que se comunique con estos. Por ejemplo, en el caso del punto de conexión de Docker Swarm, puede usar la interfaz de la línea de comandos (CLI) de Docker. Para DC/OS, puede usar la CLI de DCOS.

Creación de un clúster de Docker con el servicio Contenedor de Azure
-------------------------------------------------------

Para comenzar a usar Azure Container Service, implemente un clúster del servicio de contenedor de Azure a través del portal (busque "Azure Container Service") mediante una plantilla de Azure Resource Manager ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm) o [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)), o bien con la [CLI](/documentation/articles/xplat-cli-install/). Las plantillas de inicio rápido que se proporcionan se pueden modificar para que incluyan una configuración de Azure adicional o avanzada. Para obtener más información sobre la implementación de un clúster del servicio de contenedores de Azure, consulte [Implementación de un clúster de Azure Container Service](container-service-deployment.md).

Implementación de una aplicación
------------------------

Azure Container Service permite elegir Docker Swarm o DC/OS como orquestación. El procedimiento para implementar la aplicación dependerá del orquestador que elija.

### Uso de DC/OS

DC/OS es un sistema operativo distribuido basado en el kernel de sistemas distribuidos Apache Mesos. Apache Mesos se hospeda en Apache Software Foundation e incluye algunos de los [principales nombres de TI](http://mesos.apache.org/documentation/latest/powered-by-mesos/) como usuarios y colaboradores.

![Servicio Contenedor de Azure está configurado para Swarm, que muestra agentes y patrones.](media/acs-intro/dcos.png)

DC/OS y Apache Mesos presentan una impresionante serie de características:

-   Escalabilidad demostrada

-   Patrón y esclavos replicados tolerantes a errores que usan Apache ZooKeeper

-   Compatibilidad con los contenedores en formato Docker

-   Aislamiento nativo entre las tareas con contenedores de Linux

-   Programación de varios recursos (memoria, CPU, disco y puertos)

-   API de Java, Python y C++ para desarrollar nuevas aplicaciones paralelas

-   Una IU web para ver el estado del clúster

De forma predeterminada, la instancia de DC/OS que se ejecuta en el servicio de contenedor de Azure incluye la plataforma de orquestación Marathon para cargas de trabajo de programación. Sin embargo, al implementar DC/OS de ACS, se incluye el universo de servicios de Mesosphere, que pueden agregarse al servicio, entre ellas Spark, Hadoop, Cassandra y muchos más.

![Universo de DC/OS en el servicio de contenedor de Azure](media/dcos/universe.png)

#### Uso de Marathon

Marathon es un sistema de inicialización y control de todo el clúster para servicios en cgroups o, en el caso del servicio Contenedor de Azure, contenedores en formato Docker. Marathon proporciona una IU web desde la que puede implementar aplicaciones. Puede acceder a esta desde una dirección URL semejante a `http://DNS_PREFIX.REGION.cloudapp.azure.com`, donde DNS\_PREFIX y REGION se definen en el momento de la implementación. Por supuesto, también puede proporcionar su propio nombre DNS. Para obtener más información sobre cómo ejecutar un contenedor mediante la interfaz de usuario web de Marathon, consulte [Administración de contenedores a través de la interfaz de usuario web](container-service-mesos-marathon-ui.md).

![Lista de aplicaciones de Marathon](media/dcos/marathon-applications-list.png)

Además, puede usar las API de REST para comunicarse con Marathon. Existen varias bibliotecas de cliente que están disponibles para cada herramienta. Abarcan una variedad de lenguajes y, por supuesto, puede usar el protocolo HTTP en cualquier lenguaje. Muchas herramientas de DevOps conocidas también proporcionan compatibilidad con Marathon. Esto proporciona la máxima flexibilidad a su equipo de operaciones cuando trabaja con un clúster del servicio Contenedor de Azure. Para obtener más información sobre cómo ejecutar un contenedor mediante la API de REST de Marathon, consulte [Administración de contenedores con la API de REST](container-service-mesos-marathon-rest.md).

### Uso de Docker Swarm

Docker Swarm proporciona agrupación en clústeres nativa para Docker. Como Docker Swarm proporciona servicio a la API de Docker estándar, cualquier herramienta que ya se comunique con Docker daemon podrá usar Swarm para escalar de forma transparente a varios hosts en el servicio Contenedor de Azure.

![Azure Container Service está configurado para usar DC/O, que muestra JumpBox, agentes y patrones.](media/acs-intro/acs-swarm2.png)

Entre las herramientas compatibles para administrar contenedores en un clúster Swarm se incluyen las siguientes:

-   Dokku

-   CLI de Docker y Docker Compose

-   Krane

-   Jenkins

Vídeos
------

Introducción a Azure Container Service:

> [AZURE.VIDEO azure-container-service-101]

Building Applications Using the Azure Container Service (Creación de aplicaciones mediante Azure Container Service) (Build 2016)

> [AZURE.VIDEO build-2016-building-applications-using-the-azure-container-service]

<!---HONumber=AcomDC_0914_2016-->