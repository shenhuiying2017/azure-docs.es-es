---
title: Administración de contenedores del servicio Contenedor de Azure a través de la interfaz de usuario web | Microsoft Docs
description: Implemente contenedores en un clúster del servicio Contenedor de Azure mediante la interfaz de usuario web de Marathon.
services: container-service
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Mesos, Azure

ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: nepeters

---
# Administración de contenedores a través de la interfaz de usuario web
DC/OS proporciona un entorno para implementar y escalar cargas de trabajo agrupadas, al tiempo que reduce el hardware subyacente. Por encima de DC/OS hay un marco que administra la programación y ejecución de cargas de trabajo de proceso.

Aunque hay marcos de trabajo disponibles para muchas cargas de trabajo conocidas, en este documento se detalla cómo crear y escalar implementaciones de contenedores con Marathon. Antes de trabajar con estos ejemplos, necesitará un clúster de DC/OS configurado en el servicio Contenedor de Azure. También debe tener conectividad remota con este clúster. Para más información sobre estos aspectos, consulte los siguientes artículos:

* [Implementación de un clúster del servicio Contenedor de Azure](container-service-deployment.md)
* [Conexión a un clúster del servicio Contenedor de Azure](container-service-connect.md)

## Exploración de la interfaz de usuario de DC/OS
Con un túnel Secure Shell (SSH) establecido, vaya a http://localhost/. Con ello, se cargará la interfaz de usuario web de DC/OS y aparecerá información acerca del clúster, como los recursos usados, los agentes activos y los servicios en ejecución.

![Interfaz de usuario de DC/OS](media/dcos/dcos2.png)

## Exploración de la interfaz de usuario de Marathon
Para ver la interfaz de usuario de Marathon, vaya a http://localhost/Marathon. En esta pantalla puede iniciar un nuevo contenedor u otra aplicación en el clúster de DC/OS del servicio Contenedor de Azure. También puede ver información acerca de cómo ejecutar contenedores y aplicaciones.

![Interfaz de usuario de Marathon](media/dcos/dcos3.png)

## Implementación de un contenedor con formato Docker
Para implementar un nuevo contenedor mediante Marathon, haga clic en el botón **Create Application** (Crear aplicación) y escriba la siguiente información en el formulario:

| Campo | Valor |
| --- | --- |
| ID |nginx |
| Imagen |nginx |
| Red |Bridged |
| Puerto de host |80 |
| Protocol |TCP |

![Nueva interfaz de usuario de la aplicación: General](media/dcos/dcos4.png)

![Nueva interfaz de usuario de la aplicación: Contenedor de Docker](media/dcos/dcos5.png)

![Nueva interfaz de usuario de la aplicación: Detección de servicios y puertos](media/dcos/dcos6.png)

Si desea asignar estáticamente el puerto del contenedor a un puerto en el agente, debe utilizar el modo JSON. Para ello, cambie el Asistente para nuevas aplicaciones al **modo JSON** mediante el botón de alternancia. A continuación, escriba lo siguiente en la sección `portMappings` de la definición de la aplicación. En este ejemplo se enlaza el puerto 80 del contenedor al puerto 80 del agente DC/OS. Puede volver a cambiar el modo JSON del Asistente después de realizar este cambio.

```none
"hostPort": 80,
```

![Nueva interfaz de usuario de la aplicación: Ejemplo con el puerto 80](media/dcos/dcos13.png)

El clúster de DC/OS se implementa con un conjunto de agentes públicos y privados. Para que el clúster pueda acceder a aplicaciones de Internet, debe implementar las aplicaciones en un agente público. Para ello, seleccione la pestaña **Optional** (Opcional) del Asistente para nuevas aplicaciones y escriba **slave\_public** en **Accepted Resource Roles** (Roles de recursos aceptados).

![Nueva interfaz de usuario de la aplicación: Configuración del agente público](media/dcos/dcos14.png)

De nuevo en la página principal de Marathon, puede ver el estado de implementación para el contenedor.

![Página principal de la interfaz de usuario de Marathon: Estado de la implementación del contenedor](media/dcos/dcos7.png)

Al volver a la interfaz de usuario web de DC/OS (http://localhost/), verá que se ejecuta una tarea, en este caso un contenedor con formato Docker, en el clúster de DC/OS.

![Interfaz de usuario web de DC/OS: Tarea que se ejecuta en el clúster](media/dcos/dcos8.png)

También puede ver el nodo del clúster en el que se está ejecutando la tarea.

![Interfaz de usuario web de DC/OS: nodo de clúster de la tarea](media/dcos/dcos9.png)

## Escalado de los contenedores
La interfaz de usuario de Marathon se puede utilizar para escalar el recuento de instancias de un contenedor. Para ello, navegue a la página de **Marathon**, seleccione el contenedor que desea escalar y haga clic en el botón **Scale** (Escalar). En el cuadro de diálogo **Scale Application** (Escalar aplicación), escriba el número de instancias del contenedor que desea y seleccione **Scale Application** (Escalar aplicación).

![Interfaz de usuario de Marathon: Cuadro de diálogo Scale Application (Escalar aplicación)](media/dcos/dcos10.png)

Una vez que finaliza la operación de escalado, verá varias instancias de la misma tarea distribuidas entre los agentes de DC/OS.

![Panel de interfaz de usuario web de DC/OS: Tarea distribuida entre varios agentes](media/dcos/dcos11.png)

![Interfaz de usuario web de DC/OS: Nodos](media/dcos/dcos12.png)

## Pasos siguientes
* [Trabajo con la API de DC/OS y Marathon](container-service-mesos-marathon-rest.md)

Profundice en Azure Container Service con Mesos

> [AZURE.VIDEO] azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]
> 
> 

<!---HONumber=AcomDC_0921_2016-->