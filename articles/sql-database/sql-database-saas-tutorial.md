---
title: "Implementación y exploración de la aplicación Wingtip Tickets Platform (WTP)  (aplicación SaaS de ejemplo que usa Azure SQL Database) | Microsoft Docs"
description: "Implementación y exploración de una aplicación SaaS usa Azure SQL Database"
keywords: tutorial de sql database
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ff4dc19bb6e24ea9ceeed9721cfb3a85b4d10965
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>Implementación y exploración de una aplicación SaaS multiempresa que utiliza Azure SQL Database

En este tutorial, se implementa y explora la aplicación SaaS Wingtip Tickets Platform (WTP). La aplicación utiliza un patrón de aplicación SaaS de base de datos por inquilino para dar servicio a varios inquilinos. La aplicación está diseñada para presentar las características de Azure SQL Database que permiten escenarios de SaaS y los patrones de diseño y administración de SaaS.

Cinco minutos después de hacer clic en el botón *Implementar en Azure*, tiene una aplicación de SaaS multiempresa que usa SQL Database en funcionamiento en la nube. La aplicación se implementa con tres inquilinos de ejemplo, cada uno con su propia base de datos, y todos ellos implementados en un grupo elástico de SQL. La aplicación se implementa en su suscripción de Azure, lo que le proporciona acceso completo para inspeccionar y trabajar con los componentes de la aplicación individuales.

En este tutorial, obtendrá información:

> [!div class="checklist"]

> * Acerca de cómo implementar la aplicación WTP
> * Acerca de los servidores, los grupos y las bases de datos que componen la aplicación
> * Acerca de los inquilinos que se asignan a sus datos con el *catálogo*
> * Acerca de cómo aprovisionar nuevos inquilinos
> * Acerca de cómo ver el uso de grupos para supervisar la actividad de los inquilinos
> * Acerca de cómo eliminar los recursos de ejemplo para detener la facturación relacionada con ellos

Para explorar diversos patrones de administración y diseño de SaaS, hay un [serie de tutoriales relacionados](sql-database-wtp-overview.md) disponibles que se basan en esta implementación inicial. Cuando recorra los tutoriales, profundice en los scripts que se proporcionan y examine cómo se implementan los distintos patrones de SaaS. Recorra los scripts de cada tutorial para conocer mejor cómo implementar la gran cantidad de características de SQL Database que simplifican el desarrollo de aplicaciones SaaS.

Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>Implementación de la aplicación SaaS Wingtip Tickets Platform (WTP)

Implemente Wingtip Tickets Platform en menos de cinco minutos:

1. Haga clic para realizar la información:

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Especifique los valores necesarios de los parámetros para la implementación:

    > [!IMPORTANT]
    > Para realizar la demostración, se ha eliminado intencionadamente la protección de varios firewalls de autenticación y del servidor. **Cree un nuevo grupo de recursos**, no use los grupos de recursos, servidores o grupos existentes y no utilice esta aplicación, ni los recursos que crea, para producción. Elimine este grupo de recursos cuando haya terminado con la aplicación para detener la facturación relacionada con él.

    * **Grupo de recursos**: seleccione **Crear nuevo** y especifique un **nombre** y una **contraseña**.
    * **Usuario**: algunos recursos requieren nombres que sean únicos en todas las suscripciones de Azure. Para garantizar la unicidad, especifique un valor para diferenciar los recursos que cree de los que han creado otros usuarios que implementan la aplicación Wingtip. Se recomienda usar un nombre de **usuario** corto, como sus iniciales y un número (por ejemplo, *bg1*) y, después, utilizarlo en el nombre del grupo de recursos (por ejemplo, *wingtip-bg1*). El parámetro **Usuario** solo puede contener letras, números y guiones. El primer y último carácter deben ser una letra o un número (se recomienda usar minúsculas).

     ![template](./media/sql-database-saas-tutorial/template.png)

1. **Implemente la aplicación**.

    * Haga clic si acepta los términos y condiciones.
    * Seleccione **Anclar al panel**.
    * Haga clic en **Comprar**.

1. Supervise el estado de implementación, para lo que debe hacer clic en **Notificaciones** (el icono de la campana de la derecha del cuadro de búsqueda). La implementación de la aplicación WTP tarda aproximadamente cuatro minutos en realizarse.

   ![implementación correcta](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>Exploración de la aplicación

La aplicación presenta lugares, como salas de concierto, clubs de jazz o clubes deportivos, que alojan eventos. Los lugares se registran como clientes (o inquilinos) de Wingtip Tickets Platform (WTP), lo que facilita la enumeración de eventos y la venta de entradas. Cada lugar obtiene una aplicación web personalizada que administra y enumera sus eventos y vende las entradas, que independiente y está aislada de otros inquilinos. Tras los bastidores, cada inquilino obtiene una base de datos de SQL Database que se implementa en un grupo elástico de SQL.

Un **centro de eventos** central proporciona una lista de las direcciones URL de los inquilinos específicas para la implementación. Todas estas direcciones incluyen un valor específico de *Usuario* y siguen este formato: http://events.wtp.&lt; USUARIO&gt;.trafficmanager.net/*fabrikamjazzclub*. 

1. Abra el _centro de eventos_: http://events.wtp.&lt;USUARIO&gt;.trafficmanager.net (sustitúyalo por el nombre de usuario):

    ![centro de eventos](media/sql-database-saas-tutorial/events-hub.png)

1. Haga clic en **Fabrikam Jazz Club** en el *centro de eventos*.

   ![Eventos](./media/sql-database-saas-tutorial/fabrikam.png)

1. Haga clic en **Tickets** (Entradas) y vea cómo se realiza la compra de entradas de un evento.

La aplicación WTP usa [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para controlar la distribución del tráfico entrante. Las páginas de eventos, que son específicas del inquilino, requieren que los nombres de los inquilinos se incluyan en las direcciones URL. La aplicación de eventos analiza el nombre de inquilino de la dirección URL y lo utiliza para crear una clave para acceder a un catálogo mediante la [*administración del mapa de particiones*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). El catálogo asigna la clave a la ubicación de base de datos del inquilino. El **centro de eventos** usa metadatos extendidos en el catálogo para recuperar el nombre del inquilino asociado a cada base de datos.

En un entorno de producción, lo habitual sería crear un registro DNS CNAME que [*apunte a un dominio de empresa de Internet*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) para el perfil de Traffic Manager.

## <a name="get-the-wingtip-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip

Los scripts de Wingtip Tickets y el código fuente de la aplicación están disponibles en el repositorio de github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Los archivos de los scripts se encuentran en la [carpeta Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Descargue la carpeta **Learning Modules** en el equipo local, conservando su estructura de carpetas.

## <a name="provision-a-new-tenant"></a>Aprovisionamiento de un nuevo inquilino

La implementación inicial crea tres inquilinos de ejemplo, pero para este tutorial necesitamos crear más. En este paso se crea rápidamente un inquilino nuevo. Más adelante, tendrá que profundizar en los detalles del aprovisionamiento de nuevos inquilinos, en el [tutorial de aprovisionamiento en el catálogo](sql-database-saas-tutorial-provision-and-catalog.md), donde puede ver lo sencillo que sería implementar un componente de registro en la aplicación y aprovisionar automáticamente los inquilinos como clientes que se suscriben.

### <a name="initialize-the-user-config-file-for-your-deployment"></a>Inicialización del archivo de configuración de usuario en la implementación

Al ser el primer script que va a ejecutar después de la implementación inicial de la aplicación WTP, debe actualizar el archivo de configuración de usuario. Actualice las variables con los valores concretos de su implementación.

   1. Abra ...\\Learning Modules\\*UserConfig.psm1* en *PowerShell ISE*
   1. Modifique _$userConfig.ResourceGroupName_ y especifique el _grupo de recursos_ que estableció cuando implementó la aplicación.
   1. Modifique _$userConfig.Name_ y a especifique el nombre de _usuario_ que estableció cuando implementó la aplicación.

### <a name="provision-the-new-tenant"></a>Aprovisionamiento del nuevo inquilino

1. Abra ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* en *PowerShell ISE*.
1. Presione **F5** para ejecutar el script (deje por ahora los valores predeterminados del script).

El nuevo inquilino se registra en el catálogo. Se crea su base de datos y se agrega a un grupo elástico de SQL. Si el aprovisionamiento se ha realizado correctamente, el sitio de venta de entradas del nuevo inquilino aparece en el explorador:

![Nuevo inquilino](./media/sql-database-saas-tutorial/red-maple-racing.png)

Actualice el centro de eventos y compruebe que el nuevo inquilino está en la lista.

## <a name="start-generating-load-on-the-tenant-databases"></a>Empiece a generar carga en las bases de datos de los inquilinos

Ahora que tenemos varias bases de datos de los inquilinos, es preciso ponerlas en funcionamiento. Se proporciona un script de PowerShell que simula una carga de trabajo que se ejecuta en todas las bases de datos de los inquilinos. De forma predeterminada, la carga dura 60 minutos. Wingtip Tickets es una aplicación SaaS y, en el mundo real, la carga que tiene una aplicación SaaS habitualmente es esporádica e impredecible. Para realizar la simulación, el generador de carga crea una carga aleatoria que distribuye entre todos los inquilinos. El patrón tarda varios minutos en crearse, por lo que se recomienda dejar que el generador se ejecute durante unos 5-10 minutos antes de intentar supervisar la carga en las secciones siguientes.

1. Abra ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.psm1* en **PowerShell ISE**
1. Presione **F5** para ejecutar el script e iniciar el generador de carga (por ahora deje los valores predeterminados de los parámetros).

> [!IMPORTANT]
> El generador de carga se ejecuta como una serie de trabajos en la sesión local de PowerShell, así que debe mantener dicha sesión abierta. Si cierra PowerShell o la pestaña en la que se inició el generador de carga, o pone el equipo en suspensión los trabajos se detendrán.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explore los servidores, los grupos y las bases de datos de los inquilinos

Ahora que ha explorado la aplicación, ha aprovisionado un nuevo inquilino y ha empezado a ejecutar una carga en la colección de inquilinos, echemos un vistazo a algunos de los recursos que se han implementado.

1. En [Azure Portal](http://portal.azure.com), abra el servidor **catalog-&lt;USUARIO&gt;**. El servidor de catálogo contiene dos bases de datos: **tenantcatalog** y **basetenantdb** (una base de datos *golden* se copia para crear nuevos inquilinos).

   ![bases de datos](./media/sql-database-saas-tutorial/databases.png)

1. Abra el servidor **tenants1 -&lt;USUARIO&gt;** que contiene las bases de datos de los inquilinos. Tenga en cuenta que todas las bases de datos de inquilinos son _elásticas estándar_ en un conjunto estándar de 50 eDTU. Observe también hay una base de datos de _Red Maple Racing_, el inquilino que aprovisionó anteriormente.

   ![servidor](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Supervisión del grupo

Si el generador de carga lleva varios minutos en ejecución, debería haber suficientes datos disponibles para empezar a examinar algunas de las funcionalidades de supervisión integradas en los grupos y las bases de datos.

1. Vaya al servidor *tenants1 -&lt;USUARIO&gt;*y haga clic en **Pool1** para ver el uso de recursos del grupo:

   ![supervisar grupo](./media/sql-database-saas-tutorial/monitor-pool.png)

Estos dos gráficos ilustran es el grado de idoneidad de los grupos elásticos y de SQL Database para las cargas de trabajo de una aplicación SaaS. Cuatro bases de datos que generen un máximo de 40 eDTU cada una se admiten fácilmente en un grupo de 50 eDTU. Si cada una de ellas se ha aprovisionado como una base de datos independiente, necesitaría ser del tipo S2 (50 DTU) para admitir dicha generación, pero el costo de cuatro bases de datos S2 independiente casi triplicaría el precio del grupo. Además, de esta forma el grupo aún tiene gran cantidad de espacio disponible para muchas más bases de datos. En el mundo real, los clientes están ejecutando hasta 500 bases de datos en grupos de 200 eDTU. Para más información consulte el [tutorial de supervisión del rendimiento](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="deleting-the-resources-created-with-this-tutorial"></a>Eliminación de los recursos creados en este tutorial

Cuando haya acabado de explorar y trabajar con la aplicación WTP, vaya al grupo de recursos de la aplicación en el portal y elimínelo para detener la toda la facturación relacionada con esta implementación. Si ha usado alguno de los uno de los tutoriales que lo acompaña, también se eliminarán todos los recursos que estos hayan creado.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha obtenido información:

> [!div class="checklist"]

> * Acerca de cómo implementar la aplicación WTP
> * Acerca de los servidores, los grupos y las bases de datos que componen la aplicación
> * Acerca de los inquilinos que se asignan a sus datos con el *catálogo*
> * Acerca de cómo aprovisionar nuevos inquilinos
> * Acerca de cómo ver el uso de grupos para supervisar la actividad de los inquilinos
> * Acerca de cómo eliminar los recursos de ejemplo para detener la facturación relacionada con ellos

Ahora pruebe el [tutorial de aprovisionamiento del catálogo](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación inicial de la aplicación Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* Para obtener información acerca de los grupos elásticos, consulte [*¿Qué son los grupos elásticos de Azure SQL?*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Para obtener información acerca de los trabajos elásticos, consulte [*Administración de bases de datos escaladas horizontalmente en la nube*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* Para obtener información acerca de aplicaciones SaaS multiempresa, consulte [*Modelos de diseño para las aplicaciones SaaS multiempresa y Azure SQL Database*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

