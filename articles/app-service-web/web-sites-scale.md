<properties 
	pageTitle="Escalado vertical de aplicaciones en Azure" 
	description="Aprenda a escalar verticalmente una aplicación del Servicio de aplicaciones de Azure para agregar capacidad y características." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/05/2016" 
	ms.author="cephalin"/>

# Escalado vertical de aplicaciones en Azure #

En este artículo se muestra cómo escalar aplicaciones en el Servicio de aplicaciones de Azure. El escalado cuenta con dos flujos de trabajo. En este documento explicaremos el primero de ellos (el escalado vertical):

- [Escalado vertical](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenga más CPU, memoria, espacio en disco y características adicionales como máquinas virtuales exclusivas, dominios y certificados personalizados, espacios de ensayo, escalado automático y mucho más. Para escalar horizontalmente, se cambia el plan de tarifa del plan del Servicio de aplicaciones al que pertenece la aplicación.
- [Escalado horizontal](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): se aumenta el número de instancias de máquina virtual que ejecutan la aplicación. Se puede escalar horizontalmente a un máximo de 20 instancias, según el plan de tarifa. Al utilizar los [entornos del Servicio de aplicaciones](../app-service/app-service-app-service-environments-readme.md) del nivel **Premium**, se aumentará a 50 el número de instancias que pueden escalarse horizontalmente. Para obtener más información sobre el escalado horizontal, consulte [Escalado manual o automático del número de instancias](../azure-portal/insights-how-to-scale.md). En ese artículo aprenderá a utilizar el escalado automático, que consiste en escalar el número de instancias automáticamente en función de las programaciones y reglas predefinidas.

La configuración de escalado tarda solo unos segundos en aplicarse y afecta a todas las aplicaciones del [plan del Servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). No hay que modificar el código ni volver a implementar la aplicación.

Para obtener información de los precios y características de planes Servicios de aplicaciones individuales, consulte [Detalles de precios del Servicio de aplicaciones](/pricing/details/web-sites/).

> [AZURE.NOTE] Antes de cambiar un plan del Servicio de aplicaciones con el nivel **Gratis** a uno superior, primero debe quitar los [límites de gasto](/pricing/spending-limits/) vigentes de la suscripción de Azure. Para ver o cambiar opciones para la suscripción a Servicios de aplicaciones de Microsoft Azure, consulte [Suscripciones a Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## Escalado vertical del plan de tarifa

1. En el explorador, abra el [Portal de Azure][portal].
	
2. En la hoja de la aplicación, haga clic en **Toda la configuración** y, luego, en **Escalar verticalmente**.
	
	![Desplácese para escalar verticalmente la aplicación de Azure.][ChooseWHP]
	
4. Desplácese y elija el nivel; después, haga clic en **Seleccionar**.

	La ficha **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación.
	
<a name="ScalingSQLServer"></a>
##Escalación de recursos relacionados
Si su aplicación depende de otros servicios, como Base de datos SQL o Almacenamiento de Azure, también puede escalar verticalmente estos recursos según sus necesidades. Debe tener en cuenta que no se escalan con el plan del Servicio de aplicaciones y que esta operación debe realizarse por separado.

1. En **Essentials**, haga clic en el vínculo **Grupo de recursos**.

	![Escalado vertical de recursos relacionados con la aplicación de Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Después, en la parte **Resumen** de la hoja del grupo de recursos, haga clic en uno de los recursos que quiera escalar. En la captura de pantalla siguiente se muestra un recurso de Base de datos SQL y uno de Almacenamiento de Azure.

	![Desplácese a la hoja del grupo de recursos para escalar verticalmente la aplicación de Azure.](./media/web-sites-scale/ResourceGroup.png)

3. Para un recurso de Base de datos SQL, haga clic en **Configuración** > **Nivel de precios** para escalar el plan de tarifa.

	![Escalado vertical del back-end de Base de datos SQL de la aplicación de Azure](./media/web-sites-scale/ScaleDatabase.png)

	También puede activar la [replicación geográfica](../sql-database/sql-database-geo-replication-overview.md) de su base de datos SQL.

    Para un recurso de Almacenamiento de Azure, haga clic en **Configuración** > **Configuración** para escalar verticalmente las opciones de almacenamiento.

    ![Escalado vertical de la cuenta de Almacenamiento de Azure que utiliza la aplicación de Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>
## Características del desarrollador
Según el nivel de precios, se encuentran disponibles las siguientes características orientadas al desarrollador:

### Valor de bits ###

- Los modos **Básico**, **Estándar** y **Premium** son compatibles con aplicaciones de 32 y 64 bits.
- El nivel de plan **Gratis** y **Compartido** solo son compatibles con aplicaciones de 32 bits.

### Compatibilidad con el depurador ###

- La compatibilidad con el depurador está disponible para los modos **Gratis**, **Compartido** y **Básico** en 1 conexión simultánea por cada plan del Servicio de aplicaciones.
- La compatibilidad con el depurador está disponible para los modos **Estándar** y **Premium** en 5 conexiones simultáneas por cada plan del Servicio de aplicaciones.

<a name="OtherFeatures"></a>
## Otras características

- Para obtener información detallada sobre todas las características restantes en los planes de Servicios de aplicaciones, incluido el precio y las características de interés para todos los usuarios (incluidos los desarrolladores), consulte [Detalles de precios de Servicios de aplicaciones](/pricing/details/web-sites/).

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

<a name="Next Steps"></a>
## Pasos siguientes

- Para comenzar con Azure, consulte [Evaluación gratuita de Microsoft Azure](/pricing/free-trial/).
- Para obtener información sobre el precio, soporte técnico y contrato de nivel de servicio, consulte los siguientes vínculos.

	[Detalles de precios de Transferencias de datos](/pricing/details/data-transfers/)

	[Planes de soporte técnico de Azure](/support/plans/)

	[Contratos de nivel de servicio](/support/legal/sla/)

	[Detalles de precios de Base de datos SQL](/pricing/details/sql-database/)

	[Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure][vmsizes]

	[Detalles de precios del Servicio de aplicaciones](/pricing/details/app-service/)

	[Detalles de precios de Servicios de aplicaciones: las conexiones SSL](/pricing/details/web-sites/#ssl-connections)

- Para obtener información sobre las prácticas recomendadas de Servicios de aplicaciones, incluida la creación de una arquitectura resistente y escalable, consulte [Prácticas recomendadas: Aplicaciones web del Servicio de aplicaciones](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vídeos sobre cómo escalar aplicaciones del Servicio de aplicaciones:

	- [Cuándo escalar sitios web de Azure: con Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Escalación automática de sitios web de Azure mediante CPU o programación: con Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Escalación de sitios web de Azure: con Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)


<!-- LINKS -->
[vmsizes]: /pricing/details/app-service/
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
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
 

<!---HONumber=AcomDC_0706_2016-->