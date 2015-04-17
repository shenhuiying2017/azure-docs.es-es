<properties 
	pageTitle="Escalación una aplicación web en Servicios de aplicaciones de Azure" 
	description="Aprenda a escalar vertical y horizontalmente una aplicación web en Servicios de aplicaciones de Azure, incluido el escalado automático." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="cephalin"/>

# Escalación una aplicación web en Servicios de aplicaciones de Azure #

Para aumentar el rendimiento para aplicaciones web en Microsoft Azure y para habilitar una mayor funcionalidad para satisfacer sus necesidades empresariales, puede usar el Portal de administración de Azure para escalar su plan de Servicios de aplicaciones del modo gratuito a compartido, básico, estándar y Premium (vista previa). 

La escalación vertical en aplicaciones web de Azure implica dos acciones relacionadas: una es el cambio del modo del plan de Servicios de aplicaciones a un mayor nivel de servicio y la otra la configuración de determinados valores una vez que haya cambiado a un mayor nivel de servicio. En este artículo se incluyen ambos temas. Los niveles de servicio más altos como el modo estándar ofrecen una mayor robustez y flexibilidad en la determinación de cómo se usan los recursos en Azure.

El cambio de modos y la configuración de estos se realiza fácilmente en la pestaña Scale del portal de administración. Puede escalar o reducir verticalmente según sea necesario. Estos cambios tardan solo segundos en aplicarse y afectan a todas las aplicaciones web en el plan de servicio. No requieren que el código se cambie o que las aplicaciones tengan que volver a implementarse.

Para obtener información acerca de los planes de servicio de la aplicación (anteriormente planes de hospedaje web), consulte [¿Qué es un plan de hospedaje de Servicios de aplicaciones?](web-sites-web-hosting-plan-overview.md) e [Información general detallada de planes de hospedaje de Servicios de aplicaciones de Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). Para obtener información de los precios y características de planes Servicios de aplicaciones individuales, consulte [Detalles de precios de servicio de aplicación](http://http://azure.microsoft.com/pricing/details/web-sites/).  

> [AZURE.NOTE] Antes de cambiar una aplicación web del modo **gratuito** al modo **básico** o **estándar**, primero debe quitar los límites de gasto para la suscripción de Servicios de aplicaciones de Azure. Para ver o cambiar opciones para la suscripción a Servicios de aplicaciones de Microsoft Azure, consulte [Suscripciones a Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Escalación al modo de plan de Servicios de aplicaciones compartido o básico
<!-- ===================================== -->

1. En el explorador, abra el [Portal de Azure][portal].
	
2. En la hoja de la aplicación web, haga clic en **Toda la configuración**, en **Escalar** y, a continuación, haga clic en **Actualizar desde un plan gratuito para agregar instancias y obtener un mejor rendimiento**.
	
	![Choose Hosting Plan][ChooseWHP]
	
4. En la hoja **Elegir el nivel de precios**, seleccione el modo **compartido** o **básico** y, a continuación, haga clic en **Seleccionar**.

	La ficha **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación. 
	
5. Deslice la barra **Instancia** de izquierda a derecha para aumentar el número de instancias y, a continuación, haga clic en **Guardar** en la barra de comandos. La opción del tamaño de instancia no está disponible en el modo **compartido**. Para obtener más información sobre los tamaños de instancias, consulte [Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure][vmsizes].
	
	![Instance size for Basic mode][ChooseBasicInstances]
	
	La ficha **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Escalación al modo de plan de Servicios de aplicaciones estándar
<!-- ================================= -->

> [AZURE.NOTE] Antes de cambiar un plan de Servicios de aplicaciones del modo **gratuito** a **estándar**, debe quitar las limitaciones de gasto de la suscripción a Servicios de aplicaciones de Microsoft Azure. De lo contrario, se arriesga a que su sitio no esté disponible si alcanza los límites antes de la finalización del período de facturación. Para ver o cambiar opciones para la suscripción a Servicios de aplicaciones de Microsoft Azure, consulte [Suscripciones a Microsoft Azure][azuresubscriptions].

1. Para escalar al modo **estándar**, siga los mismos pasos iniciales que cuando escalaba a **compartido** o **básico** y, a continuación, elija un modo **estándar** en **Elija el nivel de precios** y, a continuación, haga clic en **Seleccionar**. 
	
	La ficha **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación y se activará el **modo de escalado automático**.

	![Scale in Standard Mode][ScaleStandard]

	Puede deslizar la barra **Instancia** para escalar manualmente en más instancias, como en el modo **básico** como se mostró anteriormente. Sin embargo, a continuación, sabrá cómo usar el **modo de escalado automático**. 

2. En **Modo de escalado automático**, seleccione **Rendimiento** para realizar la escalación automática según las métricas de rendimiento.
	
	![Autoscale Mode set to Performance][Autoscale]
	
3. En **Rango de instancias**, mueva los dos controles deslizantes para definir el número mínimo y máximo de instancias que escalar automáticamente para el plan de Servicios de aplicaciones. Para este tutorial, mueva el control deslizante al máximo, hasta **6** instancias.

4. Haga clic en **Guardar** en la barra de comandos.

4. En **Métricas de destino**, haga clic en **>** para configurar reglas de escalación automática para la métrica predeterminada.  
	
	![Set Target Metrics][SetTargetMetrics]
	
	Puede configurar reglas de escalación automática para las métricas de rendimiento diferentes, incluyendo la CPU, la memoria, la cola de disco, la cola HTTP y el flujo de datos. A continuación, podrá configurar la escalación automática para el porcentaje de CPU que hace lo siguiente:

	- Escalación horizontal de 1 instancia si la CPU supera el 70% en los últimos 10 minutos
	- Escalación horizontal de 3 instancias si la CPU supera el 90% en los últimos 5 minutos
	- Escalación vertical de 1 instancia si la CPU es inferior al 50% en los últimos 30 minutos 


4. Deje el cuadro desplegable **Métrica** como **Porcentaje de CPU**.
	
5. En **Escalar reglas horizontalmente**, para configurar la primera regla, establezca **Condición** en **Mayor**, **Umbral** en **70** (%), **Tiempo transcurrido** en **10** (minutos), **Escalar horizontalmente** en **1** (instancia) y **Duración del enfriamiento** en **10** (minutos). 
	
	![Set First Autoscale Rule][SetFirstRule]

	>[AZURE.NOTE] La configuración de **Duración del enfriamiento** especifica el tiempo que tiene que esperar esta regla tras una acción de escalado anterior para volver a escalar.
	
6. En **Escalar reglas horizontalmente**, para configurar la segunda regla, establezca **Condición** en **Mayor**, **Umbral** en **90** (%), **Tiempo transcurrido** en **1** (minutos), **Escalar horizontalmente** en **3** (instancia) y **Duración del enfriamiento** en **1** (minutos).
	
	![Set Second Autoscale Rule][SetSecondRule]
	
5. En **Escalar reglas verticalmente**, para configurar la tercera regla, establezca **Condición** en **Menor**, **Umbral** en **50** (%), **Tiempo transcurrido** en **30** (minutos), **Escalar verticalmente** en **1** (instancia) y **Duración del enfriamiento** en **60** (minutos). 
	
	![Set Third Autoscale Rule][SetThirdRule]
	
7. Haga clic en **Guardar** en la barra de comandos. La regla de escalado automático ahora debe reflejarse en la hoja **Escala**. 
	
	![Set Autoscale Rule Result][SetRulesFinal]
	
<a name="ScalingSQLServer"></a>
##Escalación de una base de datos SQL Server conectada a su aplicación web
Si tiene una o más bases de datos de SQL Server vinculadas a la aplicación web (independientemente del modo de plan de Servicios de aplicaciones), puede escalarlas rápidamente en función de sus necesidades.

1. Para escalar una de las bases de datos vinculadas, abra la hoja de aplicaciones web en el [Portal de Azure][portal]. En el menú desplegable que se puede contraer **Essentials**, haga clic en el vínculo **Grupo de recursos**. A continuación, en la parte **Resumen** de la hoja del grupo de recursos, haga clic en una de las bases de datos vinculadas.
	
	![Linked database][ResourceGroup]
	
2. En la hoja de base de datos SQL vinculada, haga clic en la parte **Nivel de precios**, seleccione uno de los niveles según sus requisitos de rendimiento y haga clic en **Seleccionar**. 
	
	![Scale your SQL Database][ScaleDatabase]
	
3. También puede configurar la replicación geográfica para aumentar las capacidades de recuperación ante desastres y de alta disponibilidad de la base de datos SQL. Para ello, haga clic en la parte **Replicación geográfica**.

	![Set up geo-replication for SQL Database][GeoReplication]

<a name="devfeatures"></a>
## Características del desarrollador
Según el modo del plan de servicios de las aplicaciones web, se encuentran disponibles las siguientes características orientadas al desarrollador:

### Valor de bits ###

- Los modos de planes básico y estándar son compatibles con aplicaciones de 32 y 64 bits.
- Los modos de plan libre y compartido solo son compatibles con aplicaciones de 32 bits.

### Compatibilidad con el depurador ###

- Se ofrece compatibilidad con el depurador para los modos del plan de Servicio de aplicaciones gratis, compartida y básica en una conexión simultánea por aplicación.
- Se ofrece compatibilidad con el depurador para el modo de plan de Servicio de aplicaciones estándar en 5 conexiones simultáneas por aplicación.

<a name="OtherFeatures"></a>
## Otras características

### Supervisión de extremos web ###

- La supervisión de extremos web está disponible en los modos de plan de Servicios de aplicaciones básico y estándar. Para obtener más información sobre la supervisión de extremos web, consulte [Supervisión de aplicaciones web](web-sites-monitor.md).

- Para obtener información detallada sobre todas las características restantes en los planes de Servicios de aplicaciones, incluido el precio y las características de interés para todos los usuarios (incluidos los desarrolladores), consulte [Detalles de precios de Servicios de aplicaciones](http://azure.microsoft.com/pricing/details/web-sites/).

<a name="Next Steps"></a>	
## Pasos siguientes

- Para comenzar con Azure, puede usar una [evaluación gratuita de Microsoft Azure](http://azure.microsoft.com/pricing/free-trial/).

- Para obtener información sobre el precio, soporte técnico y contrato de nivel de servicio, consulte los siguientes vínculos.
	
	[Detalles de precios de Transferencias de datos](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Planes de soporte técnico de Azure](http://azure.microsoft.com/support/plans/)
	
	[Contratos de nivel de servicio](http://azure.microsoft.com/support/legal/sla/)
	
	[Detalles de precios de Base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure][vmsizes]
	
	[Detalles de precios de Servicios de aplicaciones](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[Detalles de precios de Servicios de aplicaciones: las conexiones SSL](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- Para obtener información sobre las prácticas recomendadas de Servicios de aplicaciones, incluida la creación de una arquitectura resistente y escalable, consulte [Best Practices: Sitios web de Microsoft Azure (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vídeos para la escalación de sitios web de Azure:
	
	[Cuándo escalar sitios web de Azure: con Stefan Schackow](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[Escalación automática de sitios web de Azure mediante CPU o programación: con Stefan Schackow](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)

	[Escalación de sitios web de Azure: con Stefan Schackow](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!--HONumber=49-->