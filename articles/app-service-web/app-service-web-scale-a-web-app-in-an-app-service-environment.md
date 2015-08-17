<properties 
	pageTitle="Escalado de una aplicación web en un entorno del Servicio de aplicaciones" 
	description="Escalado de una aplicación web en un entorno del Servicio de aplicaciones" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Escalado de aplicaciones web en un entorno del Servicio de aplicaciones #

A alto nivel, los entornos del Servicio de aplicaciones son esencialmente implementaciones personales del Servicio de aplicaciones de Azure en la red virtual y solo puede se pueden administrar mediante su suscripción. Ofrecen nuevas capacidades de red porque se encuentran en la red virtual y también se pueden escalar más allá de lo que normalmente está disponible en los entornos del Servicio de aplicaciones de Azure. Si necesita obtener más información sobre lo que es un entorno del Servicio de aplicaciones (ASE), vea [¿Qué es un entorno del Servicio de aplicaciones][WhatisASE]. Para ver detalles sobre la creación de un entorno del Servicio de aplicaciones o una aplicación web en un entorno del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones][HowtoCreateASE] y [Creación de una aplicación web en un entorno del Servicio de aplicaciones][CreateWebappinASE].

Como recordatorio rápido, al cambiar un atributo de escala para una aplicación web, normalmente lo está cambiando en el nivel del plan del Servicio de aplicaciones. Para obtener detalles sobre el escalado planes del Servicio de aplicaciones o solamente detalles sobre planes del Servicio de aplicaciones fuera de los entornos del Servicio de aplicaciones, vea [Escalado de una aplicación web en el Servicio de aplicaciones de Azure][ScaleWebapp] e [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure][Appserviceplans].

El escalado de una aplicación web en un entorno del Servicio de aplicaciones es muy similar a escalar aplicaciones web normalmente. En el Servicio de aplicaciones de Azure hay normalmente tres cosas que puede escalar:

- plan de precios
- tamaño de trabajo (para instancias dedicadas)
- número de instancias.

En un entorno del Servicio de aplicaciones no es necesario seleccionar o cambiar el plan de precios. En términos de capacidades ya está en un nivel de capacidad de precios Premium. En un entorno del Servicio de aplicaciones tampoco hay trabajos compartidos. Son todos trabajos dedicados. En lugar de tamaños fijos, el administrador de ASE puede asignar el tamaño del recurso de proceso para que lo use cada grupo de trabajo. Eso significa que puede tener el grupo de trabajo 1 con recursos de proceso P4 y el grupo de trabajo 2 con recursos de proceso P1, si lo desea. No tienen que estar en orden de tamaño. Para obtener información sobre los tamaños y sus precios consulte el documento [Precios de Servicio de aplicaciones de Azure][AppServicePricing]. Esto deja las opciones de escalado para aplicaciones web y planes del Servicio de aplicaciones en un entorno del Servicio de aplicaciones para que sean:

- selección de grupo de trabajo
- número de instancias

El cambio de cualquier elemento se realiza a través de la IU adecuada mostrada con su plan del Servicio de aplicaciones.

![][1]

### Escalado del número de instancias ###

Cuando crea la aplicación web en un entorno del Servicio de aplicaciones por primera vez, debe escalarlo hasta al menos 2 instancias para proporcionar tolerancia a errores.

Si su ASE tiene suficiente capacidad, entonces esto es bastante sencillo. Vaya a su plan del Servicio de aplicaciones que contiene los sitios que desea escalar verticalmente y seleccione Escalar. Se abrirá la IU donde simplemente tiene que deslizar el indicador de instancia hasta el valor deseado y guardar.

![][2]

No podrá ampliar su plan de Servicio de aplicaciones más allá de la cantidad de recursos de proceso disponibles en el grupo de trabajo en el que se encuentra dicho plan. Si necesita más, tendrá que agregar más recursos de proceso al grupo de trabajo en el que los necesita a través del administrador de ASE. Para obtener información sobre la reconfiguración de su ASE lea esta información: [Configuración de un entorno del Servicio de aplicaciones][HowtoConfigureASE].
 

### Selección del grupo de trabajo ###

A la selección del grupo de trabajo se tiene acceso desde la IU del plan del Servicio de aplicaciones. Abra el plan del Servicio de aplicaciones que desea escalar y seleccione un grupo de trabajo. Verá todos los grupos de trabajo que ha configurado en el entorno del Servicio de aplicaciones. Si solamente tiene un grupo de trabajo, solo se mostrará ese grupo en la lista. Para cambiar el plan del Servicio de aplicaciones en el que está el grupo de trabajo, simplemente seleccione el grupo de trabajo al que desea mover su plan de Servicio de aplicaciones.

![][3]

Antes de hacer esto es importante asegurarse de que tendrá la capacidad adecuada para su plan del Servicio de aplicaciones. En la lista de grupos de trabajo, no solo se muestra el nombre del grupo de trabajo, sino que también puede ver cuántos trabajos están disponibles en ese grupo de trabajo. Asegúrese de que hay suficientes instancias disponibles para contener su plan de Servicio de aplicaciones. Si necesita más recursos de proceso en el grupo de trabajo al que desea moverse, tendrá que agregarlos a través del administrador de ASE.

Al mover una aplicación web de un grupo de trabajo se reiniciarán sus aplicaciones web. Esto puede causar un pequeño tiempo de inactividad en la aplicación dependiendo de cuánto tarda en reiniciarse.

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones][HowtoCreateASE].

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleasp.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleinstances.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scalepool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=August15_HO6-->