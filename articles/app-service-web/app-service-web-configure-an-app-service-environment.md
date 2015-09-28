<properties 
	pageTitle="Configuración de un entorno del Servicio de aplicaciones" 
	description="Configuración, administración y supervisión de entornos del Servicio de aplicaciones" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="ccompy"/>

# Configuración de un entorno del Servicio de aplicaciones #

## Información general ##

Los entornos del Servicio de aplicaciones son una nueva funcionalidad de nivel Premium que se ofrece en vista previa. Ofrece nuevas funcionalidades de acceso de red y de escalado. Esta nueva funcionalidad de escalado permite colocar una instancia del Servicio de aplicaciones de Azure en la red virtual. Si no está familiarizado con la funcionalidad del entorno del Servicio de aplicaciones (ASE), lea aquí el documento [¿Qué es un entorno del Servicio de aplicaciones?]/app-service-app-service-environment-intro.md). Para obtener información sobre la creación de un ASE lea el documento [Creación de un entorno del Servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md).

A un alto nivel, un entorno del Servicio de aplicaciones consta de varios componentes principales:

- Recursos de proceso que se ejecutan en el servicio hospedado del entorno del Servicio de aplicaciones de Azure
- Almacenamiento
- Base de datos
- Una red virtual clásica "v1" con al menos una subred
- Subred con el servicio hospedado del entorno del Servicio de aplicaciones de Azure que se ejecuta en él

Para ayudar a administrar y supervisar sus entornos del Servicio de aplicaciones, puede obtener acceso a la IU para ese fin seleccionando Examinar -> Entornos del Servicio de aplicaciones en el Portal de vista previa de Azure. La versión inicial tiene lo necesario para administrar el sistema y seguirá mejorando con funcionalidades adicionales en las próximas semanas.

![][1]

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Supervisión ##

No hay muchas funcionalidades de métricas disponibles en la versión de vista previa inicial, pero en breve lo estarán. Esas funcionalidades de métricas ayudarán a los administradores del sistema a tomar decisiones sobre las operaciones y el escalado del sistema.

Incluso ahora puede enumerar en el portal todos los planes del Servicio de aplicaciones del ASE, así como todas las aplicaciones web del entorno del Servicio de aplicaciones. Para ver cualquiera de las listas vaya a Configuración y seleccione el elemento que le interese.

![][3]

En ambas listas puede ver la asignación Grupo de trabajo con el número de instancias y el tamaño del recurso de proceso que se utiliza. Los detalles sobre el rendimiento dentro de un plan del Servicio de aplicaciones individual estarán disponibles como de costumbre abriendo la IU de dicho plan.

![][4]

## Recursos de proceso ##

El Servicio de aplicaciones de Azure usa todos los recursos de proceso, el almacenamiento y la base de datos. No obstante, es el usuario quien debe decidir la cantidad y los tamaños de los recursos de proceso.

Independientemente del tamaño de los recursos de proceso, el espacio mínimo tiene 2 servidores front-end y 2 trabajos. Un entorno del Servicio de aplicaciones se puede configurar para usar hasta 55 recursos de proceso en total. De esos 55 recursos de proceso, solamente 50 se pueden usar para hospedar cargas de trabajo. El motivo de esto es doble. Hay un mínimo de 2 recursos de proceso front-end. Esto deja hasta 53 para admitir asignación de grupos de trabajo. Para proporcionar tolerancia a errores, necesita tener un recurso de proceso adicional asignado según las reglas siguientes:

- cada grupo de trabajo necesita al menos un recurso de proceso adicional que no se pueden asignar a cargas de trabajo
- cuando la cantidad de recursos de proceso de un grupo supera un determinado valor, se necesita otro recurso de proceso

Dentro de cualquier grupo de trabajo los requisitos de tolerancia a errores son que para un determinado valor de X recursos asignados a un grupo de trabajo:

- Si el valor de X está comprendido entre 2 y 20, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-1
- Si el valor de X está comprendido entre 21 y 40, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-2
- Si el valor de X está comprendido entre 41 y 53, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-3

Además de ser capaz de administrar la cantidad de recursos de proceso que se pueden asignar a un grupo determinado, también tiene control sobre el tamaño. Con entornos del Servicio de aplicaciones puede elegir 4 tamaños diferentes (etiqueta P1 a P4). Para obtener detalles sobre los tamaños y sus precios, consulte [Precios del Servicio de aplicaciones](../app-service/app-service-value-prop-what-is.md). Los tamaños de recurso de proceso P1 a P3 son los mismos que los que normalmente están disponibles. El recurso de proceso P4 proporciona 8 núcleos con 14 GB de RAM y solamente está disponible en un entorno del Servicio de aplicaciones.

Como se indicó anteriormente, la característica Entorno del Servicio de aplicaciones está actualmente en vista previa y, por tanto, tiene margen para crecer. Aparte de las funcionalidades de supervisión adicionales, se implementarán más características de administración a medida que los entornos del Servicio de aplicaciones se muevan a GA. Por ahora, solamente hay algunas cosas que se pueden administrar en esta interfaz:

- Número de recursos de proceso de cada grupo
- Tamaño de los recursos de proceso de cada grupo
- Número de direcciones IP disponibles

Para controlar todo esto seleccione el elemento de configuración Escala en la parte superior.

![][2]

Aquí se puede ajustar la cantidad de recursos de proceso de cada grupo y su tamaño. Sin embargo, antes de realizar cambios es importante tener en cuenta unas cuantas cosas:

- los cambios realizados pueden tardar horas en completarse dependiendo de lo grande que sea el cambio solicitado
- cuando ya hay un cambio de configuración en el entorno del Servicio de aplicaciones en curso, no se puede iniciar otro cambio
- si cambia el tamaño de los recursos de proceso usados en un grupo de trabajo, puede provocar interrupciones en las aplicaciones web que se ejecutan en ese grupo de trabajo

Agregar instancias adicionales a un grupo de trabajo no es una operación dañina y no conlleva un impacto del sistema. Sin embargo, el cambio de tamaño del recurso de proceso usado en un grupo de trabajo es muy diferente. Para evitar tiempos de inactividad en cualquier aplicación durante un cambio de tamaño en un grupo de trabajo, lo mejor es:

- emplear un grupo de trabajo no usado para mostrar las instancias necesarias en el tamaño deseado
- escalar los planes del Servicio de aplicaciones al nuevo grupo de trabajo.  
 
Esto es mucho menos problemático para las aplicaciones que se encuentran en ejecución que cambiar el tamaño de recursos de cálculo con cargas de trabajo en ejecución. Para obtener detalles sobre el escalado de aplicaciones web en un entorno del Servicio de aplicaciones vaya a [Escalado de aplicaciones web en un entorno del Servicio de aplicaciones](app-service-web-scale-a-web-app-in-an-app-service-environment.md).

## Red virtual ##

La [red virtual][virtualnetwork] y la subred están bajo el control del usuario. Los entornos del Servicio de aplicaciones tienen unos cuantos requisitos de red, pero el resto depende del usuario. Esos requisitos de ASE son:

- una red virtual clásica "v1" con al menos 512 direcciones
- una subred con al menos 8 direcciones 
- la red virtual debe ser una red virtual regional  
 
La administración de la red virtual se realiza a través de la IU de la red virtual normal.

Dado que esta funcionalidad coloca el Servicio de aplicaciones de Azure en la red virtual, significa que las aplicaciones hospedadas en su ASE ahora pueden tener acceso a los recursos disponibles a través de ExpressRoute o VPN de sitio a sitio directamente. Las aplicaciones dentro de los entornos del Servicio de aplicaciones no requieren características de red adicionales para tener acceso a los recursos disponibles a la red virtual que hospeda el entorno del Servicio de aplicaciones.

Si lo desea, ahora también puede controlar el acceso mediante grupos de seguridad de red. Esta capacidad le permite bloquear el entorno del Servicio de aplicaciones a solamente las direcciones IP a las que desea restringirlo. Para obtener más información sobre cómo hacerlo, vea el documento [Control del tráfico de entrada en un entorno del Servicio de aplicaciones](app-service-app-service-environment-control-inbound-traffic.md).

## Eliminación de un entorno del Servicio de aplicaciones ##

Si desea eliminar un entorno del Servicio de aplicaciones, simplemente utilice la acción Eliminar que se encuentra en la parte superior de la hoja Entorno del Servicio de aplicaciones. No se puede eliminar un ASE aunque tenga contenido. Asegúrese de quitar todas las aplicaciones web y los planes del Servicio de aplicaciones con el fin de eliminar el entorno del Servicio de aplicaciones.

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md).

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=Sept15_HO3-->