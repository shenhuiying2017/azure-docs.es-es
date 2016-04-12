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

Mesos proporciona un entorno para implementar y escalar las cargas de trabajo agrupadas al tiempo que reduce el hardware subyacente. Sobre Mesos, hay un marco de trabajo que administra la programación y ejecución de cargas de trabajo de proceso.

Aunque hay marcos de trabajo disponibles para muchas cargas de trabajo conocidas, en este documento se detalla cómo crear y escalar implementaciones de contenedores con Marathon. Antes de trabajar con estos ejemplos, necesitará un clúster de Mesos configurado en el servicio Contenedor de Azure. También debe tener conectividad remota con este clúster. Para más información sobre estos aspectos, consulte los siguientes artículos:

- [Implementación de un clúster del servicio Contenedor de Azure](./container-service-deployment.md)
- [Conexión a un clúster del servicio Contenedor de Azure](./container-service-connect.md)

## Exploración de la interfaz de usuario de Mesos

Con un túnel Secure Shell (SSH) establecido, vaya a http://localhost/Mesos. Esto cargará la interfaz de usuario web de Mesos. En esa página puede recopilar información acerca del clúster de Mesos, como los agentes activados, el estado de la tarea y la disponibilidad de recursos.

![Creación de una implementación con la IU 1](media/ui1.png)

## Exploración de la interfaz de usuario de Marathon

Para ver la interfaz de usuario de Marathon, vaya a http://localhost/Marathon. Desde esta pantalla, puede iniciar un nuevo contenedor u otra aplicación en el clúster de Mesos del servicio Contenedor de Azure. También puede ver información acerca de cómo ejecutar contenedores y aplicaciones.

![Creación de una implementación con la IU 2](media/ui2.png)

## Implementación de un contenedor con formato Docker

Para utilizar Marathon para iniciar un nuevo contenedor en el clúster de Mesos, haga clic en el botón **Crear aplicación**. El formulario **Nueva aplicación** se utiliza para definir los parámetros de la aplicación o contenedor. En este ejemplo, se implementará un sencillo contenedor Nginx. Escriba la siguiente información. Cuando haya terminado, haga clic en **Crear**.

Campo | Valor
----------------|-----------
ID | nginx
Imagen | nginx
Red | Bridged
Puerto del contenedor | 80
Puerto de host | 80
Protocolo | TCP

![Creación de una implementación con la IU 3](media/ui3.png)

De nuevo en la página principal de Marathon, puede ver el estado de implementación para el contenedor.

![Creación de una implementación con la IU 4](media/ui4.png)

Si regresa a la aplicación de Mesos http://localhost/Mesos), verá que se está ejecutando una tarea, en este caso un contenedor con formato Docker, en el clúster de Mesos. También puede ver el nodo del clúster en el que se está ejecutando la tarea.

![Creación de una implementación con la IU 5](media/ui5.png)

## Escalado de los contenedores

La interfaz de usuario web de Marathon también se puede utilizar para escalar el recuento de instancias de un contenedor. Para ello, vaya a la página de Marathon, seleccione el contenedor que desea escalar y haga clic en el botón **Escalar**. En el cuadro de diálogo **Escalar aplicación**, escriba el número de instancias del contenedor que desea y seleccione **Escalar aplicación**.

![Creación de una implementación con la IU 6](media/ui6.png)

Una vez completada la operación de escalado, verá varias instancias de la misma tarea distribuidas entre agentes de Mesos.

![Creación de una implementación con la IU 7](media/ui8.png)

<!---HONumber=AcomDC_0406_2016-->