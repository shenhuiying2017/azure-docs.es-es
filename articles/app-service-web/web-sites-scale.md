---
title: Escalado vertical de aplicaciones en Azure | Microsoft Docs
description: "Aprenda a escalar verticalmente una aplicación del Servicio de aplicaciones de Azure para agregar capacidad y características."
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: b7ef8386e974486c2d327d0b042eeabdb9701b7e


---
# <a name="scale-up-an-app-in-azure"></a>Escalado vertical de aplicaciones en Azure
En este artículo se muestra cómo escalar aplicaciones en el Servicio de aplicaciones de Azure. Existen dos flujos de trabajo para escalar aplicaciones, el escalado vertical y el escalado horizontal, y en este artículo se explica el flujo de trabajo para escalar verticalmente.

* [Escalado vertical](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenga más CPU, memoria, espacio en disco y características adicionales como máquinas virtuales exclusivas, dominios y certificados personalizados, espacios de ensayo, autoescala y mucho más. Para escalar verticalmente, se cambia el plan de tarifa del plan del Servicio de aplicaciones al que pertenece la aplicación.
* [Escalado horizontal](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): se aumenta el número de instancias de máquina virtual que ejecutan la aplicación.
  Se puede escalar horizontalmente a un máximo de 20 instancias, según el plan de tarifa. [entornos del Servicio de aplicaciones](../app-service/app-service-app-service-environments-readme.md) del nivel **Premium** tier will further del nivelcrease your scale-out count to 50 del nivelstances. Para más información sobre el escalado horizontal, consulte [Escalado del número de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md). En ese artículo aprenderá a utilizar el escalado automático, que consiste en escalar el número de instancias automáticamente en función de las programaciones y reglas predefinidas.

La configuración de escalado tarda solo unos segundos en aplicarse y afecta a todas las aplicaciones del [plan del Servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
No hay que modificar el código ni volver a implementar la aplicación.

Para obtener información de los precios y características de planes del Servicio de aplicaciones individuales, consulte [Precios del Servicio de aplicaciones](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de cambiar un plan del Servicio de aplicaciones con el nivel **Gratis** a uno superior, primero debe quitar los [límites de gasto](https://azure.microsoft.com/pricing/spending-limits/) vigentes de la suscripción de Azure. Para ver o cambiar opciones para la suscripción a Azure App Service, consulte [Suscripciones a Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Escalado vertical del plan de tarifa
1. En el explorador, abra [Azure Portal][portal].
2. En la hoja de la aplicación, haga clic en **Toda la configuración** y en **Escalar verticalmente**.
   
    ![Desplácese para escalar verticalmente la aplicación de Azure.][ChooseWHP]
3. Elija el nivel y, después, haga clic en **Seleccionar**.
   
    La pestaña **Notificaciones** emitirá el mensaje **CORRECTO** en color verde indicando que se ha completado con éxito una vez finalizada la operación.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Recursos relacionados con el escalado
Si su aplicación depende de otros servicios, como Base de datos SQL o Almacenamiento de Azure, también puede escalar verticalmente estos recursos según sus necesidades. Debe tener en cuenta que no se escalan con el plan del Servicio de aplicaciones y que esta operación debe realizarse por separado.

1. En **Essentials**, haga clic en el vínculo **Grupo de recursos**.
   
    ![Escalado vertical de recursos relacionados con la aplicación de Azure](./media/web-sites-scale/RGEssentialsLink.png)
2. Después, en la parte **Resume**n de la hoja **Grupo de recursos**, haga clic en uno de los recursos que quiera escalar. En la captura de pantalla siguiente se muestra un recurso de Base de datos SQL y uno de Almacenamiento de Azure.
   
    ![Desplácese a la hoja del grupo de recursos para escalar verticalmente la aplicación de Azure.](./media/web-sites-scale/ResourceGroup.png)
3. Para un recurso de SQL Database, haga clic en **Configuración** > **Nivel de precios** para escalar el plan de tarifa.
   
    ![Escalado vertical del back-end de Base de datos SQL de la aplicación de Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    También puede activar la [replicación geográfica](../sql-database/sql-database-geo-replication-overview.md) de su instancia de Base de datos SQL.
   
    Para un recurso de Azure Storage, haga clic en **Configuración** > **Configuración** para escalar verticalmente las opciones de almacenamiento.
   
    ![Escalado vertical de la cuenta de Almacenamiento de Azure que utiliza la aplicación de Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>

## <a name="learn-about-developer-features"></a>Más información sobre las características del desarrollador
Según el nivel de precios, se encuentran disponibles las siguientes características orientadas al desarrollador:

### <a name="bitness"></a>Valor de bits
* Los modos **Básico**, **Estándar** y **Premium** son compatibles con aplicaciones de 32 y 64 bits.
* Los niveles de plan **Gratis** y **Compartido** solo son compatibles con aplicaciones de 32 bits.

### <a name="debugger-support"></a>Compatibilidad con el depurador
* La compatibilidad con el depurador está disponible para los modos **Gratis**, **Compartido** y **Básico** en una conexión por cada plan de App Services.
* La compatibilidad con el depurador está disponible para los modos **Estándar** y **Premium** en cinco conexiones simultáneas por cada plan de App Services.

<a name="OtherFeatures"></a>

## <a name="learn-about-other-features"></a>Más información sobre otras características
* Para obtener información detallada sobre todas las características restantes en los planes de Servicios de aplicaciones, incluido el precio y las características de interés para todos los usuarios (incluidos los desarrolladores), consulte [Detalles de precios de Servicios de aplicaciones](https://azure.microsoft.com/pricing/details/web-sites/).

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://azure.microsoft.com/try/app-service/) , donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

<a name="Next Steps"></a>

## <a name="next-steps"></a>Pasos siguientes
* Para comenzar con Azure, vea [Evaluación gratuita de Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/).
* Para obtener información sobre el precio, soporte técnico y Acuerdo de Nivel de Servicio, consulte los siguientes vínculos.
  
    [Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/)
  
    [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/)
  
    [Detalles de precios de Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure][vmsizes]
  
    [Detalles de precios del Servicio de aplicaciones](https://azure.microsoft.com/pricing/details/app-service/)
  
    [Detalles de precios de Servicios de aplicaciones: las conexiones SSL](https://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)
* Para obtener información sobre las prácticas recomendadas de Servicios de aplicaciones de Azure, incluida la creación de una arquitectura resistente y escalable, consulte [Prácticas recomendadas: Aplicaciones web del Servicio de aplicaciones de Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).
* Para ver vídeos sobre cómo escalar aplicaciones del Servicio de aplicaciones, consulte los siguientes recursos:
  
  * [Cuándo escalar sitios web de Azure: con Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Escalación automática de sitios web de Azure mediante CPU o programación: con Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
  * [Escalación de sitios web de Azure: con Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
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



<!--HONumber=Jan17_HO3-->


