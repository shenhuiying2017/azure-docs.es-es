<properties 
	pageTitle="Escalado de una aplicación web en el Servicio de aplicaciones de Azure" 
	description="Aprenda a ampliar y escalar horizontalmente una aplicación web en el Servicio de aplicaciones de Azure, incluido el escalado automático." 
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
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Escalado de una aplicación web en el Servicio de aplicaciones de Azure #

Para aumentar el rendimiento y la capacidad de las aplicaciones web de Microsoft Azure, puede usar el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para escalar el plan del [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) del modo **Gratis** al modo **Compartido**, **Básico**, **Estándar** o **Premium**. 

El escalado de las aplicaciones web de Azure implica dos acciones relacionadas: cambiar el modo del plan del Servicio de aplicaciones a un nivel de servicio más alto y configurar ciertas opciones de configuración después de pasar al nivel de servicio superior. En este artículo se incluyen ambos temas. Los niveles de servicio superiores como los modos **Estándar** y **Premium** ofrecen una mayor robustez y flexibilidad a la hora de determinar cómo se utilizan los recursos en Azure.

El cambio de modos y la configuración de estos se realiza fácilmente en la pestaña Escalar del portal de administración. Puede ampliar o escalar horizontalmente según sea necesario. Estos cambios tardan solo unos segundos en aplicarse y afectan a todas las aplicaciones web del plan del Servicio de aplicaciones. No requieren que el código se cambie o que las aplicaciones tengan que volver a implementarse.

Para obtener información acerca de los planes del Servicio de aplicaciones, consulte [¿Qué es un plan del Servicio de aplicaciones?](web-sites-web-hosting-plan-overview.md) e [Información detallada sobre planes del Servicio de aplicaciones de Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).. Para obtener información acerca de los precios y características de los planes del Servicio de aplicaciones individuales, consulte [Detalles de precios de Servicio de aplicaciones](/pricing/details/web-sites/).  

> [AZURE.NOTE] Antes de cambiar una aplicación web del modo **Gratis** al modo **Básico**, **Estándar** o **Premium**, primero debe quitar los límites de gasto vigentes para la suscripción al Servicio de aplicaciones de Azure. Para ver o cambiar opciones para la suscripción al Servicio de aplicaciones de Microsoft Azure, consulte [Suscribirse a Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Escalado al modo Compartido o Básico
<!-- ===================================== -->

1. En el explorador, abra el [Portal de Azure][portal].
	
2. En la hoja de la aplicación web, haga clic en **Todas las configuraciones**, en **Escalar** y en **Actualizar desde un plan Gratis para agregar instancias y obtener un mejor rendimiento**.
	
	![Choose Plan][ChooseWHP]
	
4. En la hoja **Elegir el nivel de precios**, elija el modo **Compartido** o **Básico** y, a continuación, haga clic en **Seleccionar**.
	
	La pestaña **Notificaciones** parpadeará en verde **CORRECTO** una vez completada la operación. 
	
5. Deslice la barra de **Instancia** de izquierda a derecha para aumentar el número de instancias y, a continuación, haga clic en **Guardar** en la barra de comandos. La opción del tamaño de instancia no está disponible en el modo **Compartido**. Para obtener más información sobre los tamaños de instancias, consulte [Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure][vmsizes].
	
	![Instance size for Basic mode][ChooseBasicInstances]
	
	La pestaña **Notificaciones** parpadeará en verde **CORRECTO** una vez completada la operación. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Escalado al modo Estándar o Premium
<!-- ================================= -->

> [AZURE.NOTE] Antes de cambiar un plan del Servicio de aplicaciones previsto al modo **Estándar** o **Premium**, debe quitar los límites de gasto vigentes para la suscripción al Servicio de aplicaciones de Microsoft Azure. De lo contrario, se arriesga a que su aplicación web no esté disponible si alcanza los límites antes de la finalización del período de facturación. Para ver o cambiar opciones para la suscripción al Servicio de aplicaciones de Microsoft Azure, consulte [Suscribirse a Microsoft Azure][azuresubscriptions].

1. Para escalar al modo **Estándar** o **Premium**, siga los mismos pasos iniciales que al escalar a **Compartido** o **Básico**, a continuación, elija un modo **Estándar** o **Premium** en **Elegir el nivel de precios** y haga clic en **Seleccionar**. 
	
	La pestaña **Notificaciones** parpadeará en verde **CORRECTO** una vez completada la operación y se habilitará el **Modo de escalado automático**.
	
	![Scale in Standard or Premium Mode][ScaleStandard]
	
	También puede deslizar la barra de **Instancia** para escalar manualmente a más instancias, igual que en el modo **Básico**, tal como se mostró anteriormente. No obstante, a continuación, aprenderá a usar el **Modo de escalado automático**. 
	
2. En el **Modo de escalado automático**, seleccione **Rendimiento** para ajustar la escaña automáticamente en función de las métricas de rendimiento.
	
	![Autoscale Mode set to Performance][Autoscale]
	
3. En **Intervalo de instancias**, mueva los dos controles deslizantes para definir el número mínimo y máximo de instancias que se van a escalar automáticamente para el plan del Servicio de aplicaciones. Para este tutorial, mueva el control deslizante máximo a **6** instancias.
	
4. Haga clic en **Guardar** en la barra de comandos.
	
4. En **Métricas de destino**, haga clic en **>** para configurar reglas de escalado automático para la métrica predeterminada.  
	
	![Set Target Metrics][SetTargetMetrics]
	
	Puede configurar reglas de escalado automático para distintas métricas de rendimiento, incluidos CPU, memoria, cola de disco, cola HTTP y flujo de datos. A continuación, configurará el escalado automático para el porcentaje de CPU que hace lo siguiente:
	
	- Ampliar en 1 instancia si la CPU está por encima del 70% en los últimos 10 minutos
	- Ampliar en 3 instancias si la CPU está por encima del 90% en los últimos 5 minutos
	- Reducir en 1 instancia si la CPU está por debajo del 50% en los últimos 30 minutos 
	
	
4. Deje la opción desplegable **Métrica** en **Porcentaje de CPU**.
	
5. En **Reglas de ampliación**, configure la primera regla estableciendo **Condición** en **Mayor que**, **Umbral** en **70** (%), **En los últimos** en **10** (minutos), **Ampliar en** en **1** (instancia) y **Espera** en **10** (minutos). 
	
	![Set First Autoscale Rule][SetFirstRule]
	
	>[AZURE.NOTE] La opción **Espera** especifica el tiempo que tiene que esperar esta regla después de una acción de escalado anterior para volver a escalar.
	
6. En **Agregar regla de ampliación**, configure la segunda regla estableciendo **Condición** en **Mayor que**, **Umbral** en **90** (%), **En los últimos** en **1** (minutos), **Ampliar en** en **3** (instancia) y **Espera** en **1** (minutos).
	
	![Set Second Autoscale Rule][SetSecondRule]
	
5. En **Reglas de reducción**, configure la tercera regla estableciendo **Condición** en **Menor que**, **Umbral** en **50** (%), **En los últimos** en **30** (minutos), **Reducir en** en **1** (instancia) y **Espera** en **60** (minutos). 
	
	![Set Third Autoscale Rule][SetThirdRule]
	
7. Haga clic en **Guardar** en la barra de comandos. La regla de escalado automático debe estar reflejada ahora en la hoja **Escalar**. 
	
	![Set Autoscale Rule Result][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Escalado de una base de datos SQL Server conectada a la aplicación web
Si tiene una o más bases de datos de SQL Server vinculadas a la aplicación web (independientemente del modo del plan del Servicio de aplicaciones), puede escalarlas rápidamente en función de sus necesidades.

1. Para escalar una de las bases de datos vinculadas, abra la hoja de la aplicación web en el [Portal de Azure][portal]. En la opción contraíble **Aspectos básicos**, haga clic en el vínculo **Grupo de recursos**. A continuación, en la parte **Resumen** de la hoja del grupo de recursos, haga clic en una de las bases de datos vinculadas.

	![Linked database][ResourceGroup]
	
2. En la hoja Base de datos SQL vinculada, haga clic en la parte **Nivel de precios**, seleccione uno de los niveles en función de sus requisitos de rendimiento y haga clic en **Seleccionar**. 
	
	![Scale your SQL Database][ScaleDatabase]
	
3. También puede configurar la replicación geográfica para aumentar las capacidades de alta disponibilidad y recuperación ante desastres de la base de datos SQL. Para ello, haga clic en la parte **Replicación geográfica**.
	
	![Set up geo-replication for SQL Database][GeoReplication]

<a name="devfeatures"></a>
## Características del desarrollador
Según el modo de aplicación web, se encuentran disponibles las siguientes características orientadas al desarrollador:

### Valor de bits ###

- Los modos **Básico**, **Estándar** y **Premium** son compatibles con aplicaciones de 32 y 64 bits.
- Los modos **Gratis** y **Compartido** solo son compatibles con aplicaciones de 32 bits.

### Compatibilidad con el depurador ###

- La compatibilidad con el depurador está disponible para los modos **Gratis**, **Compartido** y **Básico** en 1 conexión simultánea por cada plan del Servicio de aplicaciones.
- La compatibilidad con el depurador está disponible para los modos **Estándar** y **Premium** en 5 conexiones simultáneas por cada plan del Servicio de aplicaciones.

<a name="OtherFeatures"></a>
## Otras características

### Supervisión de extremos web ###

- La supervisión de extremos web está disponible en los modos **Básico**, **Estándar** y **Premium**. Para obtener más información sobre la supervisión de extremos web, consulte [Supervisión de aplicaciones web](web-sites-monitor.md).

- Para obtener información detallada sobre las características restantes en los planes del Servicio de aplicaciones, también el precio y las características de interés para todos los usuarios (incluidos los desarrolladores), consulte [Detalles de precios de Servicio de aplicaciones](/pricing/details/web-sites/).

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de registrarse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

<a name="Next Steps"></a>	
## Pasos siguientes

- Para comenzar con Azure, consulte [Evaluación gratuita de Microsoft Azure](/pricing/free-trial/).
- Para obtener información sobre el precio, soporte técnico y contrato de nivel de servicio, consulte los siguientes vínculos.
	
	[Detalles de precios de Transferencias de datos](/pricing/details/data-transfers/)
	
	[Planes de soporte técnico de Microsoft Azure](/support/plans/)
	
	[Contratos de nivel de servicio](/support/legal/sla/)
	
	[Detalles de precios de Base de datos SQL](/pricing/details/sql-database/)
	
	[Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure][vmsizes]
	
	[Detalles de precios de Servicio de aplicaciones](/pricing/details/web-sites/)
	
	[Detalles de precios de Servicio de aplicaciones: Conexiones SSL](/pricing/details/web-sites/#ssl-connections)

- Para obtener información sobre los procedimiento recomendados del Servicio de aplicaciones de Azure, incluida la creación de una arquitectura resistente y escalable, consulte [Prácticas recomendadas: Aplicaciones web del Servicio de aplicaciones de Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vídeos sobre cómo escalar aplicaciones web:
	
	- [Cuándo escalar sitios web de Azure (When to Scale Azure Websites): con Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Autoescalado de sitios web de Azure mediante CPU o programación (Auto Scaling Azure Websites, CPU or Scheduled): con Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Escalado de sitios web de Azure (How Azure Websites Scale): con Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## ¿Qué ha cambiado?
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal antiguo al nuevo, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

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