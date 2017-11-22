---
title: "Tutorial de la aplicación SaaS multiinquilino con Azure SQL Database | Microsoft Docs"
description: "Implemente y explore una aplicación SaaS de inquilino único independiente que usa Azure SQL Database."
keywords: tutorial de SQL Database
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: ce0268f800dcf1900730d6ad9c476fb06320a79e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implementación y dexploración de una aplicación de inquilino único independiente que usa Azure SQL Database

En este tutorial, se implementa y explora la aplicación independiente SaaS Wingtip Tickets. La aplicación está diseñada para presentar las características de Azure SQL Database que simplifican escenarios de SaaS.

El patrón de aplicación independiente implementa un grupo de recursos de Azure que contiene una aplicación de inquilino único y una base de datos de inquilino único para cada inquilino.  Es posible aprovisionar varias instancias de la aplicación para proporcionar una solución multiinquilino.

Si bien en este tutorial se implementarán grupos de recursos para varios inquilinos en una suscripción de Azure, este patrón permite que los grupos de recursos se implementen en la suscripción a Azure de un inquilino.  Azure tiene programas para socios que permiten que un proveedor de servicios administre estos grupos de recursos en el nombre del inquilino como un administración en la suscripción del inquilino.

En la sección de implementación siguiente hay tres botones Implementar en Azure, cada uno de los cuales muestra una instancia distinta de la aplicación personalizada para un inquilino específico. Cuando se presiona cada botón, la aplicación correspondiente se implementa totalmente cinco minutos después.  Las aplicaciones se implementan en la suscripción a Azure.  Tiene acceso completo para explorar y trabajar con los componentes individuales de la aplicación.

El código fuente y los scripts de administración de la aplicación están disponibles en el repositorio [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) de GitHub.


En este tutorial, obtendrá información:

> [!div class="checklist"]

> * Implementación de la aplicación independiente SaaS Wingtip Tickets
> * Dónde obtener el código fuente de la aplicación y los scripts de administración
> * Información sobre los servidores y las bases de datos que componen la aplicación

Se publicarán más tutoriales oportunamente que le permitirán explorar una variedad de escenarios de administración en función de este patrón de aplicación.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implementación de la aplicación independiente SaaS Wingtip Tickets

Implemente la aplicación para los tres inquilinos proporcionados:

1. Haga clic en cada botón **Implementar en Azure** para abrir la plantilla de implementación en Azure Portal. Cada plantilla requiere dos valores de parámetro: un nombre para un grupo de recursos nuevo y un nombre de usuario que distingue esta implementación de otras implementaciones de la aplicación. El paso siguiente proporciona detalles para establecer estos valores.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp;&nbsp;**Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam Jazz Club**

2. Especifique los valores necesarios de los parámetros para cada implementación.

    > [!IMPORTANT]
    > Para realizar la demostración, se ha eliminado intencionadamente la protección de varios firewalls de autenticación y del servidor. **Cree un nuevo grupo de recursos** para cada implementación de aplicación.  No use un grupo de recursos existente. No use esta aplicación, ni ninguno de los recursos que se crean, para producción. Elimine todos los grupos de recursos cuando haya terminado con la aplicación para detener la facturación relacionada con ellos.

    Se recomienda usar solo letras minúsculas, números y guiones en los nombres de recurso.
    * En **Grupo de recursos**: seleccione Crear nuevo y, luego, proporcione un nombre para el grupo de recursos (distingue mayúsculas de minúsculas).
        * Se recomienda usar solo letras minúsculas en el nombre del grupo de recursos.
        * Le recomendamos anexar un guión, seguido de sus iniciales, y después un dígito, por ejemplo: _wingtip-sa-af1_.
        * Seleccione una ubicación en la lista desplegable.

    * En **Usuario**: se recomienda elegir un valor Usuario corto, como sus iniciales más un dígito, por ejemplo: _af1_.


1. **Implemente la aplicación**.

    * Haga clic para aceptar los términos y condiciones.
    * Haga clic en **Comprar**.

1. Supervise el estado de implementación de las tres implementaciones, para lo que debe hacer clic en **Notificaciones** (el icono de la campana de la derecha del cuadro de búsqueda). La implementación de la aplicación tarda aproximadamente cinco minutos.


## <a name="run-the-application"></a>Ejecución de la aplicación

La aplicación presenta lugares, como salas de concierto, clubs de jazz y clubes deportivos, que hospedan eventos. Estos lugares se registran como clientes (o inquilinos) de Wingtip Tickets, lo que facilita la enumeración de eventos y la venta de entradas. Cada lugar obtiene un sitio web personalizado que administra y enumera sus eventos y vende las entradas, que es independiente y se encuentra aislado de otros inquilinos. En segundo plano, cada inquilino obtiene una instancia de aplicación independiente y una base de datos SQL independiente.

1. Abra la página de eventos para cada uno de los tres inquilinos en pestañas independientes del explorador:

    http://events.contosoconcerthall.&lt;USUARIO&gt;.trafficmanager.net <br>
    http://events.dogwooddojo.&lt;USUARIO&gt;.trafficmanager.net<br>
    http://events.fabrikamjazzclub.&lt;USUARIO&gt;.trafficmanager.net

    (reemplace &lt;USUARIO&gt; por el valor de usuario de la implementación).

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


Para controlar la distribución de las solicitudes entrantes, la aplicación usa [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Cada aplicación específica del inquilino incluye el nombre del inquilino como parte del nombre de dominio de la dirección URL. Todas las direcciones URL de inquilino incluyen el valor específico de *usuario* y siguen este formato: http://events.&lt;venuename&gt;.&lt;USUARIO&gt;.trafficmanager.net. La dirección de la base de datos de cada inquilino se incluye en la configuración de la aplicación implementada correspondiente.

En un entorno de producción, lo habitual sería crear un registro DNS CNAME que [*apunte a un dominio de empresa de Internet*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) para la dirección URL del perfil de Traffic Manager.


## <a name="explore-the-servers-and-tenant-databases"></a>Exploración de los servidores y las bases de datos de inquilino

Veamos algunos de los recursos que se implementaron:

1. En [Azure Portal](http://portal.azure.com), navegue a la lista de los grupos de recursos.  Debería ver el grupo de recursos **wingtip-sa-catalog-&lt;USUARIO&gt;** en el que el servidor **catalog-sa-&lt;USUARIO&gt;** se implementa con la base de datos **tenantcatalog**. También debería ver los tres grupos de recursos de inquilino.

1. Abra el grupo de recursos **wingtip-sa-fabrikam-&lt;USUARIO&gt;** que contiene los recursos para la implementación Fabrikam Jazz Club.  El servidor **fabrikamjazzclub-&lt;USUARIO&gt;** contiene la base de datos **fabrikamjazzclub**.


Cada base de datos de inquilino es una base de datos _independiente_ de 50 DTU.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha obtenido información:

> [!div class="checklist"]

> * Implementación de la aplicación independiente SaaS Wingtip Tickets
> * Información sobre los servidores y las bases de datos que componen la aplicación
> * Acerca de cómo eliminar los recursos de ejemplo para detener la facturación relacionada con ellos


## <a name="additional-resources"></a>Recursos adicionales

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* Para obtener información acerca de aplicaciones SaaS multiempresa, consulte [*Modelos de diseño para las aplicaciones SaaS multiempresa y Azure SQL Database*](saas-tenancy-app-design-patterns.md)
