<properties
   pageTitle="Administración de contenedores del servicio Contenedor de Azure a través de la interfaz de usuario web | Microsoft Azure"
   description="Implemente contenedores en un clúster del servicio Contenedor de Azure mediante la interfaz de usuario web de Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, microservicios, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>

# Administración de contenedores a través de la interfaz de usuario web

DC/OS proporciona un entorno para implementar y escalar cargas de trabajo agrupadas, al tiempo que reduce el hardware subyacente. Por encima de DC/OS hay un marco que administra la programación y ejecución de cargas de trabajo de proceso.

Aunque hay marcos de trabajo disponibles para muchas cargas de trabajo conocidas, en este documento se detalla cómo crear y escalar implementaciones de contenedores con Marathon. Antes de trabajar con estos ejemplos, necesitará un clúster de DC/OS configurado en el servicio Contenedor de Azure. También debe tener conectividad remota con este clúster. Para más información sobre estos aspectos, consulte los siguientes artículos:

- [Implementación de un clúster del servicio Contenedor de Azure](./container-service-deployment.md)
- [Conexión a un clúster del servicio Contenedor de Azure](./container-service-connect.md)

## Exploración de la interfaz de usuario de DC/OS

Con un túnel Secure Shell (SSH) establecido, vaya a http://localhost/. Así se cargará la interfaz de usuario web de DC/OS y mostrar información acerca del clúster, como los recursos usados, los agentes activos y los servicios en ejecución que se pueden ver.

![](media/dcos/dcos2.png)

## Exploración de la interfaz de usuario de Marathon

Para ver la interfaz de usuario de Marathon, vaya a http://localhost/Marathon. En esta pantalla puede iniciar un nuevo contenedor u otra aplicación en el clúster de DC/OS del servicio Contenedor de Azure. También puede ver información acerca de cómo ejecutar contenedores y aplicaciones.

![](media/dcos/dcos3.png)

## Implementación de un contenedor con formato Docker

Para implementar un nuevo contenedor mediante Marathon, haga clic en el botón **Create Application** (Crear aplicación) y escriba la siguiente información en el formulario. Cuando esté listo, haga clic en **Create Application** (Crear aplicación).

Campo | Valor
----------------|-----------
ID | nginx
Imagen | nginx
Red | Bridged
Puerto de host | 80
Protocolo | TCP

![](media/dcos/dcos4.png)

![](media/dcos/dcos5.png)

![](media/dcos/dcos6.png)

Si desea asignar estáticamente el puerto del contenedor a un puerto en el agente, debe realizarse mediante el "modo de JSON". Para ello, cambie al Asistente para nuevas aplicaciones al modo de JSON mediante el botón de alternancia y, después, escriba lo siguiente en la sección 'portMappings' de la definición de la aplicación. En este ejemplo enlace el puerto 80 del contenedor al puerto 80 del agente DC/OS. Este asistente puede cambiar del modo de JSON una vez que se haya realizado este cambio.

```none
“hostPort”: 80,
```

![](media/dcos/dcos13.png)

El clúster de DC/OS se implementa con un conjunto de agentes públicos y privados. Para acceder a la aplicación desde Internet, debe implementarse en un agente público. Para ello, seleccione la pestaña "Optional" (Opcional) del Asistente para nuevas aplicaciones y escriba "slave\_public" en "Accepted Resource Roles" (Roles de recursos aceptados).

![](media/dcos/dcos14.png)

De nuevo en la página principal de Marathon, puede ver el estado de implementación para el contenedor.

![](media/dcos/dcos7.png)

Si vuelve a la aplicación DC/OS (http://localhost/)), verá que se está ejecutando una tarea, en este caso un contenedor con formato Docker, en el clúster de DC/OS.

![](media/dcos/dcos8.png)

También puede ver el nodo del clúster en el que se está ejecutando la tarea.

![](media/dcos/dcos9.png)

## Escalado de los contenedores

La interfaz de usuario de Marathon se puede utilizar para escalar el recuento de instancias de un contenedor. Para ello, navegue a la página de Marathon, seleccione el contenedor que desea escalar y haga clic en el botón **Scale** (Escalar). En el cuadro de diálogo **Scale Application** (Escalar aplicación), escriba el número de instancias del contenedor que desea y seleccione **Scale Application** (Escalar aplicación).

![](media/dcos/dcos10.png)

Una vez que se completa la operación de escalado, verá varias instancias de la misma tarea distribuidas entre los agentes de DC/OS.

![](media/dcos/dcos11.png)

![](media/dcos/dcos12.png)

## Pasos siguientes

[Trabajo con la API de DC/OS y Marathon](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0427_2016-->