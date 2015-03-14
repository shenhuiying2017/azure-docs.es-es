<properties 
	pageTitle="¿Qué es un plan de hospedaje web?" 
	description="Información general del plan de hospedaje web" 
	services="web-sites" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="adamab"/>

# ¿Qué es un plan de hospedaje web? #

Los planes de hospedaje web representan un conjunto de características y capacidades que puede compartir entre los sitios web.  Los planes de hospedaje web son compatibles con algunos niveles de precios (por ejemplo, gratis, compartido, básico y estándar), donde cada uno de ellos tiene sus propias funcionalidades.  Los sitios en la misma suscripción, el mismo grupo de recursos y la misma ubicación geográfica pueden compartir un plan de hospedaje web.

## Características en los planes de hospedaje web ##

Cada nivel de precio (por ejemplo, Gratis, Compartido, Básico y Estándar) tiene su propio conjunto de características.  [Vaya a este sitio](http://go.microsoft.com/fwlink/?LinkID=394421) para conocer la información más reciente sobre características y precios.

Estas son algunas sugerencias útiles respecto de los planes de hospedaje web y sus características:

- Puede cambiar el nivel de precio de un plan de hospedaje web en cualquier momento sin tiempo de inactividad.
- Los sitios en la misma suscripción, ubicación y grupo de recursos pueden compartir un plan de hospedaje web. 
- Características como el escalado automático funcionan dirigidas a un plan de hospedaje web.  Si desea escalar de manera automática sitios individuales, debe dedicar un plan de hospedaje web a ese sitio.

[Obtenga más información acerca de la administración de planes de hospedaje web](http://go.microsoft.com/fwlink/?LinkID=394411).

## Planes de hospedaje web y capacidad ##

Los planes de hospedaje web en los niveles Gratis y Compartido proporcionan sitios con una infraestructura compartida, lo que significa que los sitios comparten recursos con los sitios de otros clientes.  

Los planes de hospedaje web en los niveles Básico y Estándar proporcionan sitios con una infraestructura dedicada, lo que significa que solo el sitio o los sitios que asocie con este plan se ejecutarán en esos recursos.  En este nivel puede configurar el plan de hospedaje web para usar una o más instancias de máquina virtual.  Hay compatibilidad para instancias pequeñas, medianas y grandes.  Estas máquinas virtuales se administran en su lugar, lo que significa que nunca tendrá que preocuparse sobre las actualizaciones de los sistemas operativos o aspectos similares.  

Una nota acerca del nivel Compartido (vista previa).  En todos los niveles, excepto en el compartido, paga un precio por el plan de hospedaje web según el nivel y la capacidad elegida, y no hay cargo adicional por cada sitio que use el plan. Los planes de hospedaje web compartidos son distintos.  Debido a la naturaleza de la infraestructura compartida, cada sitio del plan se cobra por separado.  

### Planes de hospedaje web y el nuevo portal de vista previa de Azure ##

El nuevo portal de vista previa de Azure le permite asociar sus sitios web nuevos o existentes con planes de hospedaje web.  De hecho, todos los sitios web existentes se han asignado automáticamente a planes de hospedaje web según su suscripción, ubicación geográfica y nivel de precios actual.  

Cuando se crea un sitio nuevo, el portal le preguntará con qué plan de hospedaje web desea asociarlo.  Puede crear un nuevo plan de hospedaje web o seleccionar uno existente.  Para utilizar un plan existente, el sitio nuevo debe residir en la misma suscripción, ubicación geográfica y grupo de recursos que el plan existente.  Cuando se crea un sitio web vacío, Azure definirá el último plan de hospedaje web utilizado como valor predeterminado para aprovechar al máximo los planes que ya ha creado.  Cuando cree un sitio web con una base de datos, no estará disponible la opción para volver a utilizar un plan existente.

Puede ver todos los planes de hospedaje web en todas sus suscripciones si utiliza el botón **Examinar** en la barra de menú de la izquierda y, a continuación, hace clic en **Todo** en la esquina superior derecha del panel de actividades que aparece en pantalla.

![][BrowseEverythingScreenshot]
![][BrowseWebHostingPlansScreenshot]

También puede ver con qué plan de hospedaje web está asociado cada sitio web si mira la representación gráfica del grupo de recursos que aparece en la parte superior del cuadro del sitio web.

![][WebHostingPlanResourceMapScreenshot]

Si hace clic en el plan, aparece un cuadro que le permite administrar el plan de hospedaje web.  [Obtenga más información acerca de la administración de planes de hospedaje web](http://go.microsoft.com/fwlink/?LinkID=394411).

![][WebHostingPlanBladeScreenshot]
	

### Pasos siguientes ###

Para comenzar con Azure, puede usar una [evaluación gratuita de Microsoft Azure](http://azure.microsoft.com/pricing/free-trial/).

<!-- Images. -->
[BrowseEverythingScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-everything.png
[WebHostingPlanResourceMapScreenshot]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-resource-map.png
[WebHostingPlanBladeScreenshot]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-blade.png
[BrowseWebHostingPlansScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-web-hosting-plans.png

<!--HONumber=46--> 
