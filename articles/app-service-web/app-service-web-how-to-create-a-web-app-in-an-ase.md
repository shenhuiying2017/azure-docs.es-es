<properties
	pageTitle="Creación de una aplicación web en un entorno del Servicio de aplicaciones"
	description="Flujo de creación de aplicaciones web y planes del Servicio de aplicaciones examinados para un entorno del Servicio de aplicaciones"
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="09/15/2015"
	ms.author="ccompy"/>

# Creación de una aplicación web en un entorno del Servicio de aplicaciones #

Crear aplicaciones web es prácticamente lo mismo en entornos del Servicio de aplicaciones (ASE) que normalmente. Si está familiarizado con la funcionalidad del entorno del Servicio de aplicaciones, lea el documento [¿Qué es un entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md).

Para crear una aplicación web en un ASE deberá comenzar por tener un ASE. Para obtener detalles sobre la creación de un ASE, lea aquí el documento: [Creación de un entorno del Servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md).

El primer paso para crear una aplicación web es seleccionar la suscripción. Si tiene varias suscripciones, tenga en cuenta que para crear una aplicación en el ASE, debe usar la misma suscripción que usó al crear el ASE. El paso siguiente implica seleccionar o crear un grupo de recursos. Si no está familiarizado con los grupos de recursos, puede encontrar más información aquí: [Uso del Portal de vista previa de Azure para administrar los recursos de Azure][ResourceGroups]. Aparte de ayudar a administrar los recursos, los grupos de recursos son también importantes a la hora de establecer las reglas de control de acceso basado en rol (RBAC) para sus aplicaciones.

Después de seleccionar su suscripción y el grupo de recursos, debe crear o seleccionar un plan de servicio de aplicaciones (ASP). Si necesita crear un nuevo ASP en su ASE deberá proporcionar un nombre para el ASP, seleccionar el ASE que quiere en Ubicaciones y seleccionar el grupo de trabajo en el que quiera que esté el ASP. Esto se describe con más detalle a continuación. Si va a seleccionar un ASP en un ASE, el flujo de creación de la aplicación es el mismo que cuando se crea una aplicación normalmente. Es decir, se recorre el flujo de creación de aplicaciones comenzando con Nuevo -> Web + Móvil -> Aplicación web.

![][1]


Si usa un plan de servicio de aplicaciones que ya ha creado en el entorno del Servicio de aplicaciones, simplemente seleccione dicho plan, escriba el nombre de la aplicación web y seleccione Crear. Es el mismo flujo que cuando crea una aplicación web normalmente. Puede identificar los ASP en su ASE si mira la ubicación que se indica bajo el nombre del ASP.

![][5]

Cuando crea una aplicación, se encontrará en:

[*nombre del sitio*].[*nombre del entorno del Servicio de aplicaciones*].p.azurewebsites.net

en lugar de

[*nombre del sitio*].azurewebsites.net

Por ahora, el nombre de la aplicación web debe ser único en todo el Servicio de aplicaciones de Azure. Esto significa que si desea crear una aplicación web llamada "thisismywebapp", actualmente no puede haber ninguna otra aplicación web llamada "thisismywebapp" en el Servicio de aplicaciones de Azure.

### Planes del Servicio de aplicaciones ###

Los planes del Servicio de aplicaciones son un conjunto administrado de sus aplicaciones web. Cuando se selecciona el precio, el precio que se cobra se aplica al Plan del Servicio de aplicaciones y no a las aplicaciones individuales. Para escalar verticalmente el número de instancias de una aplicación web escale verticalmente las instancias de su ASP, lo que afecta a todas las aplicaciones web de ese plan. Algunas características como las ranuras de sitio o la integración de la red virtual también tienen restricciones de cantidad dentro del plan. Aquí puede obtener más información acerca de los planes del Servicio de aplicaciones: [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

Si va a crear un nuevo plan del Servicio de aplicaciones, existen algunas diferencias para crear un ASP en un entorno del Servicio de aplicaciones. Entre otras cosas, las opciones de trabajo son diferentes, ya que no hay ningún trabajo compartido en un ASE. Los trabajos que tiene que usar son los que el administrador ha asignado al ASE. Esto significa que para crear un nuevo ASP, el número de trabajos asignados al grupo de trabajo del ASE debe ser superior al número total de instancias en todos los ASP que ya existen en ese grupo de trabajo. Si no tiene suficientes trabajos en su grupo de trabajo del ASE para crear su ASP, deberá trabajar con el administrador del ASE para agregarlos.

Otra diferencia con los ASP hospedados del Servicio de aplicaciones es la ausencia de selección de precios. Cuando tiene un entorno del Servicio de aplicaciones, paga recursos de proceso utilizados por el sistema y se le cobran cargos adicionales por los ASP de ese entorno. Normalmente, cuando se crea un ASP selecciona un plan de precios que determina su facturación. Un entorno del Servicio de aplicaciones es esencialmente una ubicación privada donde puede crear contenido. Se paga por el entorno y no pro hospedar el contenido.

### Creación de un ASP para el entorno del Servicio de aplicaciones ###

Para crear un ASP en un ASE, primero seleccione Crear nuevo en la interfaz de usuario de selección del ASP y proporcione un nombre para el ASP, lo mismo que haría normalmente fuera de un ASE. El siguiente paso consiste en seleccionar el ASE que quiera usar en el selector de ubicación. Como un entorno del Servicio de aplicaciones es básicamente una ubicación de implementación privada, se muestra en Ubicación.

![][2]

Después de la selección de un ASE en el selector de ubicación, se actualizará la interfaz de usuario de creación del ASP. La ubicación muestra ahora el nombre del sistema ASE y la región en la que está, y el selector de planes de tarifa se sustituye por un selector de grupos de trabajo.

![][3]

### Selección del grupo de trabajo ###

Normalmente, en el Servicio de aplicaciones de Azure y fuera de un entorno del Servicio de aplicaciones, hay tres tamaños de proceso disponibles con la selección de un plan de tarifa dedicado. De igual forma, los clientes que poseen un ASE pueden definir hasta tres grupos de trabajo y especificar el tamaño de proceso que se usa para ese grupo de trabajo. Para los inquilinos esto significa que en lugar de seleccionar un plan de tarifa con el tamaño de proceso de su ASP, se selecciona lo que se conoce como un grupo de trabajo.

La interfaz de usuario de selección del grupo de trabajo muestra el tamaño de proceso usado para ese grupo de trabajo debajo del nombre. La cantidad disponible se refiere al número de instancias de proceso que están disponibles para usarse en ese grupo. El grupo total puede tener en realidad un número de instancias superior a este, pero este valor se refiere simplemente al número que no está en uso. Si necesita ajustar el entorno del Servicio de aplicaciones para agregar más recursos de proceso, vea el documento [Configuración del entorno del Servicio de aplicaciones](app-service-web-configure-an-app-service-environment.md).

![][4]

En este ejemplo solamente puede ver dos grupos de trabajo disponibles. Eso se debe a que el administrador de ASE solamente asigna hosts en esos dos grupos de trabajo. El tercero se mostraría cuando haya máquinas virtuales asignadas a él.

### Después de la creación de la aplicación web ###

Existen algunas consideraciones para ejecutar aplicaciones web y administrar ASP en un ASE que deben tenerse en cuenta.

Como se indicó anteriormente, el propietario del ASE es responsable del tamaño del sistema y, por tanto, también es responsable de garantizar que haya capacidad suficiente para hospedar los ASP deseados. Si no hay trabajos disponibles, no podrá crear su ASP. Esto también se cumple para escalar verticalmente la aplicación web. Si necesita más instancias, el administrador del entorno del Servicio de aplicaciones debe agregar más trabajos.

Después de crear la aplicación web y el ASP, es una buena idea para escalarla verticalmente. En un ASE siempre debe tener al menos 2 instancias de su ASP para proporcionar tolerancia a errores para las aplicaciones. El escalado de un ASP en un ASE es el mismo que a través de la IU del ASP. Para obtener más detalles sobre el escalado, lea el documento [Escalado de una aplicación web en un entorno del Servicio de aplicaciones](app-service-web-scale-a-web-app-in-an-app-service-environment.md).


[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/

<!---HONumber=Oct15_HO4-->