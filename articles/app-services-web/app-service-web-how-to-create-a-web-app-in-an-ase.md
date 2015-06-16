<properties 
	pageTitle="Creación de una aplicación web en un entorno del Servicio de aplicaciones" 
	description="Flujo de creación de aplicaciones web y planes del Servicio de aplicaciones examinados para un entorno del Servicio de aplicaciones" 
	services="app-services\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Creación de una aplicación web en un entorno del Servicio de aplicaciones #

Crear aplicaciones web es prácticamente lo mismo en entornos del Servicio de aplicaciones (ASE) que normalmente. Si está familiarizado con la funcionalidad del entorno del Servicio de aplicaciones, lea el documento [¿Qué es un entorno del Servicio de aplicaciones][WhatisASE].

Para crear una aplicación web en un ASE deberá comenzar por tener un ASE. Para obtener detalles sobre la creación de un ASE lea el documento [Creación de un entorno del Servicio de aplicaciones][HowtoCreateASE].

El primer paso para crear una aplicación web es crear o seleccionar un plan del Servicio de aplicaciones (ASP). La creación de un ASP en un ASE se inicia de la misma forma que normalmente, es decir, siguiendo el flujo de creación de aplicaciones web seleccionando Nuevo -> Web + Mobile -> Aplicación web.

![][1]


Si utiliza un plan del Servicio de aplicaciones que ya ha creado en el entorno del Servicio de aplicaciones, seleccione dicho plan, escriba el nombre de la aplicación web y seleccione Crear. Es el mismo flujo que cuando crea una aplicación web normalmente. La principal diferencia en este caso es que la aplicación web se alcanzará en:

[*nombre del sitio*].[*nombre del entorno del Servicio de aplicaciones *]. p.azurewebsites.net
 
en lugar de

[*nombre del sitio*].azurewebsites.net

Por ahora, el nombre de la aplicación web debe ser único en todo el Servicio de aplicaciones de Azure. Esto significa que si desea crear una aplicación web llamada "thisismywebapp", actualmente no puede haber ninguna otra aplicación web llamada "thisismywebapp" en el Servicio de aplicaciones de Azure.

### Planes del Servicio de aplicaciones ###

Los planes del Servicio de aplicaciones son un conjunto administrado de sus aplicaciones web. Cuando se selecciona el precio, el precio que se cobra se aplica al Plan del Servicio de aplicaciones y no a las aplicaciones individuales. Para escalar verticalmente el número de instancias de una aplicación web escale verticalmente las instancias de su ASP, lo que afecta a todas las aplicaciones web de ese plan. Algunas características como las ranuras de sitio o la integración de la red virtual también tienen restricciones de cantidad dentro del plan. Aquí puede obtener más información acerca de los planes del Servicio de aplicaciones: [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure][Appserviceplans].

Si va a crear un nuevo plan del Servicio de aplicaciones, existen algunas diferencias para crear un ASP en un entorno del Servicio de aplicaciones. Entre otras cosas, las opciones de trabajo son diferentes, ya que no hay ningún trabajo compartido en un entorno del Servicio de aplicaciones. Los trabajos que tiene que usar son los que el administrador ha asignado en el entorno del Servicio de aplicaciones. Esto significa que para crear un nuevo ASP, necesitará tener más trabajos asignados al entorno del Servicio de aplicaciones que el número total de instancias en todos sus ASP en su entorno del Servicio de aplicaciones. Si no tiene suficiente trabajos en su entorno del Servicio de aplicaciones para crear su ASP, deberá trabajar con el administrador de su entorno del Servicio de aplicaciones para agregarlos.

Otra diferencia con los ASP hospedados del Servicio de aplicaciones es la ausencia de selección de precios. Cuando tiene un entorno del Servicio de aplicaciones, paga recursos de proceso utilizados por el sistema y se le cobran cargos adicionales por los ASP de ese entorno. Normalmente, cuando se crea un ASP selecciona un plan de precios que determina su facturación. Un entorno del Servicio de aplicaciones es esencialmente una ubicación privada donde puede crear contenido. Se paga por el entorno y no pro hospedar el contenido.

### Selección del entorno del Servicio de aplicaciones ###

Dado que un entorno del Servicio de aplicaciones es esencialmente una ubicación de implementación privada, empiece seleccionando el ASE que desee usar mediante el selector de ubicación.

![][2]

Después de la selección la IU actualizará y reemplazará el selector de planes de precios mediante un selector de grupos de trabajo. La ubicación muestra el nombre del sistema ASE y la región en la que se encuentra. En la dirección URL, el nombre de dominio para el ASE reemplaza .azurewebsites.net (normalmente presente) por el nombre del entorno del Servicio de aplicaciones.

![][3]

### Selección del grupo de trabajo ###

Normalmente, en el Servicio de aplicaciones de Azure y fuera de un entorno del Servicio de aplicaciones, hay 3 tamaños que están disponibles con la selección de un plan de precio dedicado. De manera similar, los clientes que poseen un ASE pueden definir hasta 3 grupos de trabajos y especificar el tamaño de la máquina virtual que se usa para ese grupo de trabajo. En lugar de seleccionar un plan de precios para su ASP, seleccione lo que se llama un grupo de trabajo.

La IU de selección de grupo de trabajo muestra el tamaño de las máquinas virtuales usadas para ese grupo de trabajo debajo del nombre. La cantidad disponible se refiere al número de máquinas virtuales que están disponibles para su uso en ese grupo. Realmente, el grupo total puede tener más máquinas virtuales que este número, pero este valor se refiere simplemente a cuántas no están en uso. Si necesita ajustar el entorno del Servicio de aplicaciones para agregar más recursos de proceso, vea el documento [Configuración del entorno del Servicio de aplicaciones][HowtoConfigureASE].

![][4]

En este ejemplo solamente puede ver dos grupos de trabajo disponibles. Eso es porque el administrador de ASE solamente asigna máquinas virtuales en esos dos grupos de trabajo. El tercero se mostraría cuando haya máquinas virtuales asignadas a él.

### Después de la creación de la aplicación web ###

Existen algunas consideraciones para ejecutar aplicaciones web y administrar ASP en un ASE que deben tenerse en cuenta.

Como se indicó anteriormente, el propietario del ASE es responsable del tamaño del sistema y, por tanto, también es responsable de garantizar que haya capacidad suficiente para hospedar los ASP deseados. Si no hay trabajos disponibles, no podrá crear su ASP. Esto también se cumple para escalar verticalmente la aplicación web. Si necesita más instancias, el administrador del entorno del Servicio de aplicaciones debe agregar más trabajos.

Después de crear la aplicación web y el ASP, es una buena idea para escalarla verticalmente. En un ASE siempre debe tener al menos 2 instancias de su ASP para proporcionar tolerancia a errores para las aplicaciones. El escalado de un ASP en un ASE es el mismo que a través de la IU del ASP. Para obtener más detalles sobre el escalado, lea el documento [Escalado de una aplicación web en un entorno del Servicio de aplicaciones][HowtoScale].

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment-in-an-ase/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
<!--HONumber=52-->
 