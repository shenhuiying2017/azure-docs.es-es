<properties 
	pageTitle="Información detallada de planes de hospedaje de Sitios web Azure - Guía de características de Windows Azure" 
	description="Obtenga información acerca de cómo funcionan los planes de hospedaje web para sitios web de Azure y cómo benefician a su experiencia de administración." 
	services="web-sites" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="byvinyal, yochayk"/>

# Información detallada sobre planes de hospedaje web de Sitios web Azure#

Los planes de hospedaje web (WHP) representan un conjunto de características y capacidades que puede compartir entre los sitios web.  Los planes de hospedaje web son compatibles con los 4 niveles de precios de Sitios web Azure (gratis, compartido, básico y estándar), donde cada uno de ellos tiene sus propias funcionalidades y capacidad.  Los sitios en la misma suscripción, el mismo grupo de recursos y la misma ubicación geográfica pueden compartir un plan de hospedaje web. Todos los sitios que comparten un plan de hospedaje web pueden aprovechar todas las capacidades y características definidas por el nivel de dicho plan. Todos los sitios web asociados a un plan de hospedaje web dado se ejecutan en recursos definidos por dicho plan. Por ejemplo, si el plan de hospedaje web está configurado para utilizar dos máquinas virtuales "pequeñas", todos los sitios asociados a ese plan de hospedaje web se ejecutarán en ambas máquinas virtuales. Como siempre con Sitios web Azure, las máquinas virtuales en las que se ejecutan los sitios son totalmente administradas y están altamente disponibles.

En este artículo exploraremos las características clave, como por ejemplo el nivel y la escala de un plan de hospedaje web y el papel que juegan mientras administra sus sitios web. 

## Sitios web, planes de hospedaje web y grupos de recursos##

Un sitio web solamente se puede asociar a un plan de hospedaje web en un momento determinado. Un plan de hospedaje web se asocia a un grupo de recursos. Un grupo de recursos es un nuevo concepto en Azure que sirve como límite del ciclo de vida para cada recurso contenido en él. Los grupos de recursos le permiten administrar todos los componentes de una aplicación conjuntamente. 

Puede tener varios planes de hospedaje web en un grupo de recursos y cada plan de hospedaje tendrá su propio conjunto de características y capacidades que utilizarán los sitios asociados.  En la siguiente imagen se ilustra esta relación:


![Resource Groups and Web Hosting Plans](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)


La capacidad de tener varios planes de hospedaje web en un solo grupo de recursos permite asignar diferentes sitios a distintos recursos, principalmente máquinas virtuales que ejecutan sus sitios web. Por ejemplo, esta capacidad permite la separación de recursos entre sitios de desarrollo, prueba y producción, donde podría estar interesado en asignar un plan de hospedaje web con su propio conjunto de recursos dedicado para sus sitios de producción y un segundo plan de hospedaje web para sus sitios de desarrollo y prueba. 

Al tener varios planes de hospedaje web en un solo grupo de recursos, puede definir una aplicación que se extienda entre regiones. Por ejemplo, un sitio web altamente escalable en dos regiones incluirá dos planes de hospedaje web, uno por cada región, y un sitio web asociado con cada plan de hospedaje web. En tal situación, todos los sitios se asociarán a un solo grupo de recursos, que define una aplicación.  Al tener una sola vista de un grupo de recursos con varios planes de hospedaje web y varios sitios, la administración, el control y la visión del mantenimiento de los sitios web resultan más sencillos. Además de la administración de recursos de sitios web y de los sitios correspondientes para una aplicación dada, puede asociar cualquier recurso de Azure relacionado, como por ejemplo bases de datos SQL de Azure y proyectos de equipo. 

## ¿Cuándo debo crear un nuevo grupo de recursos y cuándo un nuevo plan de hospedaje web?##

Cuando cree un nuevo sitio web, debe pensar en crear un nuevo grupo de recursos cuando dicho sitio representa una nueva aplicación web. En este caso, crear un nuevo grupo de recursos, un plan de hospedaje web asociado y unos sitios web es la elección correcta.  Cuando cree un nuevo sitio web de este tipo a través de la nueva vista previa del portal de Azure, utilizando la galería o la opción de nuevo sitio web + SQL, el portal creará un nuevo grupo de recursos y un plan de hospedaje web de forma predeterminada para su nuevo sitio. Si lo necesita, no obstante, puede sobrescribir esta opción predeterminada.


![Creating a new Web Hosting Plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)


Siempre puede agregar un nuevo sitio web o cualquier otro recurso a un grupo de recursos existente. Cuando cree un nuevo sitio web a partir del contexto de un grupo de recursos existente, el asistente para nuevo sitio web establecerá como predeterminado el recurso existente y el plan de hospedaje web. Aquí también puede sobrescribir estas opciones predeterminadas según estime oportuno. Cuando agregue un nuevo sitio web a un grupo de recursos existentes, podrá optar por agregar el sitio a un plan de hospedaje web existente (se trata de la opción predeterminada en la nueva vista previa del portal de Azure), o puede crear un nuevo plan de hospedaje web al que agregar el sitio.

La creación de un nuevo plan de hospedaje permite asignar un nuevo conjunto de recursos para sus sitios web y le proporciona un mayor control sobre la asignación de recursos, ya que cada plan de hospedaje web obtiene su propio conjunto de máquinas virtuales. Dado que puede mover sitios web entre planes de hospedaje web, suponiendo que dichos planes se encuentren en las mismas regiones, la decisión de crear o no un nuevo plan de hospedaje web es lo menos importante. Si un sitio web determinado comienza a consumir demasiados recursos o simplemente necesita separar unos pocos sitios web, puede crear un nuevo plan de hospedaje web y mover sus sitios web a él.

Si desea crear un nuevo sitio web en una región diferente y dicha región no tiene un plan de hospedaje web existente, tendrá que crear un nuevo plan de hospedaje web en dicha región para poder tener un sitio web asociado a él. 

Una cosa importante a tener en cuenta es que no puede mover planes de hospedaje web o sitios web entre grupos de recursos. Asimismo, no puede mover un sitio web entre dos planes de hospedaje web que se encuentren en dos regiones separadas. 

## Grupo de recursos existente en el portal de vista previa de Azure##

Si ya tiene sitios web existentes en Sitios web Azure, observará que todos nuestros sitios web se muestran en el portal de vista previa de Azure. Puede ver todo el sitio web como una lista plana haciendo clic en el botón **examinar** en el panel de navegación izquierdo y seleccionando **Sitios web**:


![See all your website as a flat list](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)


También puede ver todos los grupos de recursos que se han creado haciendo clic en el botón **examinar** en el panel de navegación izquierdo y seleccionando **Grupos de recursos**:


![See all the resource groups that have been created](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)


También observará que tiene un grupo de recursos predeterminada generado automáticamente en cada región que ya tiene sitios web. El nombre del grupo de recursos generados automáticamente para los sitios web es *Web-predeterminada-<NOMBRE DE LA UBICACIÓN>*en el que el nombre de la ubicación representa una región de Azure (por ejemplo *Web-predeterminada-WestUS*). En cada grupo de recursos encontrará todos los sitios existentes para la región del grupo. Cada sitio creado en el pasado y que se cree en el futuro en el portal de Azure completo o en el portal de vista previa de Azure, estará disponible en ambos portales. 

Dado que cada sitio web tiene que asociarse a un plan de hospedaje web, hemos creado planes de hospedaje web predeterminados para sus sitios existentes conforme a la siguiente convención, en cada región:

* Todos los sitios web de nivel **Gratis** se asocian a un plan de hospedaje web **predeterminado** y su nivel de precios se establece en **Gratis**. 

* Todos los sitios web de nivel **Compartido** se asocian a un plan de hospedaje web **predeterminado** y su nivel de precios se establece en **Compartido**.

* Todos los sitios web de nivel **Estándar** se asocian a un plan de hospedaje web predeterminado y su nivel de precios se establece en **Estándar**. 

El nombre de este plan de hospedaje web es **DefaultServerFarm**. Este nombre se eligió para compatibilidad con una API heredada. El nombre **ServerFarm** puede ser un poco engañoso, ya que se refiere a un **plan de hospedaje web**, pero es importante tener en cuenta que es un nombre de un plan de hospedaje web y no una entidad propia. 

## P+F sobre el plan de hospedaje web##

**Pregunta**: ¿Cómo puedo crear un plan de hospedaje web?

**Respuesta**: Un plan de hospedaje web es un contenedor y, como tal, no lo puede crear vacío. Sin embargo, un plan de hospedaje web se crea explícitamente durante la creación.

Para hacer esto usando la UI en la nueva **vista previa del portal de Azure**, haga clic en **NUEVO** y seleccione **Sitio web**; se abrirá el cuadro de creación de sitios web. En la primera imagen a continuación puede ver el icono **NUEVO** en la parte inferior izquierda y en la segunda imagen puede ver la hoja de creación **Sitio web**, la hoja **Plan de hospedaje web** y la hoja **Nivel de precios**:


![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png)


![Website, Web Hosting Plan and pricing tier blades](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png)


Para este ejemplo hemos optado por crear un nuevo sitio web llamado **contosomarketing** y colocarlo en el nuevo plan de hospedaje web llamado **contoso**. El nivel de precios seleccionado para este plan de hospedaje web es **Estándar pequeño**. Para obtener más detalles sobre los niveles de precios de los planes de hospedaje web así como de las características, precios y opciones de escala proporcionados en cada uno de ellos, visite [Especificaciones de planes de hospedaje web de Sitios web Azure](http://go.microsoft.com/?linkid=9845586). 

Debe tenerse en cuenta que un plan de hospedaje web también se puede crear en el Portal de Azure existente. Esto se realiza como parte del asistente para **creación rápida** seleccionando **Crear nuevo plan de hospedaje web** desde la lista desplegable **PLAN DE HOSPEDAJE WEB**:


![Create new web hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png)


Para este ejemplo hemos creado un nuevo sitio llamado **northwind** y hemos optado por crear un nuevo plan de hospedaje web. El resultado de esta operación será un nuevo plan de hospedaje web llamado **default0** que contiene el sitio web **northwind**. Todos los planes de hospedaje web creados a través de esta experiencia siguen esta convención de nomenclatura y no se puede cambiar el nombre de los planes de hospedaje web una vez creados. Asimismo, los planes de hospedaje web creados a través de este proceso se crearán en el nivel de precios **Gratis**.

**Pregunta**: ¿Cómo asigno un sitio a un **plan de hospedaje web**?

**Respuesta**: Los sitios se asignan a un plan de hospedaje web como parte del proceso de creación del sitio. Para hacer esto usando la UI en la nueva **vista previa del portal de Azure** haga clic en **NUEVO** y seleccione **Sitio web**:


![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png)


A continuación, en la hoja de creación del sitio web, seleccione el plan de hospedaje:


![Select a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png)


Un sitio también se puede crear en un plan de hospedaje web específico usando el Portal de Azure existente. Esto se lleva a cabo como parte del asistente para **creación rápida**. Después de escribir la dirección URL del sitio web, use la lista desplegable **PLAN DE HOSPEDAJE WEB** para seleccionar un plan al que agregar el sitio:


![Select a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png)


**Pregunta**: ¿Cómo puedo mover un sitio a un plan de hospedaje web diferente?

**Respuesta**: Puede mover un sitio a un plan de hospedaje web diferente mediante el portal de vista previa de Azure. Los sitios web se pueden mover entre planes de hospedaje web en la misma región geográfica que pertenezcan al mismo grupo de recursos.

Para mover un sitio a otro plan, navegue al cuadro de sitio web del sitio que desea mover.  A continuación, haga clic en **Plan de hospedaje web**:


![Choose a new or existing web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png)


Esta operación abrirá la hoja Plan de hospedaje web. Llegados a este punto, puede elegir un plan de hospedaje web existente o crear uno nuevo. Los planes de una ubicación geográfica o grupo de recursos diferente se atenúan y no se pueden seleccionar.

Tenga en cuenta que cada plan de hospedaje web tiene su propio nivel de precios. Cuando mueve un sitio de un plan de hospedaje web de nivel **Gratis** a un plan de hospedaje web **Estándar**, el sitio web podrá aprovechar todas las características y recursos del nivel Estándar. 


**Pregunta**: ¿Cómo puedo escalar un plan de hospedaje web?

**Respuesta**: Existen dos formas de escalar un plan de hospedaje web. Una de ellas es escalar verticalmente el plan de hospedaje web y todos los sitios asociados a dicho plan. Al cambiar el nivel de precios de un plan de hospedaje web, todos los sitios de dicho plan estarán sujetos a las características y recursos definidos por ese nivel de precios. 

En la imagen siguiente puede ver la hoja **Plan de hospedaje web**, así como al hoja **Nivel de precios**. Al hacer clic en la parte **Nivel de precios** en la hoja **Plan de hospedaje web**, se expande la hoja **Nivel de precios** donde puede cambiar el nivel de precios para el plan de hospedaje web:


![The Web Hosting Plan blade and the Pricing Tier](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png)


La segunda forma de escalar un plan es escalarlo horizontalmente aumentando el número de instancias del plan de hospedaje web. En la imagen siguiente puede ver los cuadros **Plan de hospedaje web** y **Escala**. Al hacer clic en el área Escala en la hoja **Plan de hospedaje web** se expande y permite cambiar el número de instancias del plan:


![Changing the instance count of a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png)


Dado que el plan de hospedaje web de la imagen anterior está configurado para utilizar el nivel de precios **Estándar**, la opción **Escalado automático** se habilita. 

Realizar esto en el Portal de Azure completo puede hacerse en la pestaña **Escala**, tal y como se muestra a continuación:


![Changing the instance count of a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png)


**Pregunta**: ¿Cómo puedo eliminar un plan de hospedaje web?

**Respuesta**: Para eliminar un plan de hospedaje web, primero debe eliminar todos los sitios web asociados a él. Una vez eliminados todos los sitios web de un plan de hospedaje web, podrá eliminar dicho plan del cuadro Plan de hospedaje web:


![Deleting a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png)


En el portal de Azure completo, al eliminar el último sitio web de un plan de hospedaje web se eliminará el plan de hospedaje web asociado automáticamente.

**Pregunta**: ¿Cómo puedo supervisar un plan de hospedaje web?

**Respuesta**: Los planes de hospedaje web se pueden supervisar con las partes de supervisión de la hoja Plan de hospedaje web:


![Monitoring a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png)


Los controles de supervisión se pueden personalizar haciendo clic en el control y seleccionando **editar consulta**:


![Editing the monitoring controls](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png)


Las métricas expuestas son:

* Porcentaje de CPU

* Porcentaje de memoria

* Longitud de la cola de disco 

* Longitud de la cola HTTP. 

Estas métricas representan el uso medio entre instancias pertenecientes a un plan de hospedaje web. Todas estas métricas se pueden usar para configurar alertas y reglas de escalado automático.

## Resumen y conclusiones##

Los planes de hospedaje web representan un conjunto de características y capacidades que puede compartir entre los sitios web.  Un plan de hospedaje web le proporciona flexibilidad para asignar sitios específicos a un conjunto de recursos dado (máquinas virtuales) y optimizar aún más su asignación y uso de recursos de Azure para sitios web. 

<!--HONumber=47-->
