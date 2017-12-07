---
title: "Implementación de una aplicación SaaS de base de datos multiinquilino con particiones que usa Azure SQL Database | Microsoft Docs"
description: "Implemente y explore la aplicación de base de datos multiinquilino SaaS Wingtip Tickets con particiones que muestra los patrones de SaaS con Azure SQL Database."
keywords: tutorial de SQL Database
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: billgib;MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.openlocfilehash: 1ef4355f7234bc6a534d21a57fa52b480983b99b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Implementación y exploración de una aplicación multiinquilino con particiones que usa Azure SQL Database

En este tutorial se implementa y explora una aplicación de base de datos multiinquilino SaaS de ejemplo, denominada Wingtip Tickets. La aplicación Wingtip está diseñada para presentar las características de Azure SQL Database que simplifican la implementación de los escenarios de SaaS.

Esta implementación de Wingtips usa un patrón de base de datos multiinquilino con particiones. El particionamiento se realiza por identificador del inquilino. Los datos del inquilino se distribuyen a una base de datos determinada según los valores del identificador del inquilino. Independientemente de cuántos inquilinos contiene una base de datos determinada, todas las bases de datos son multiinquilino en el sentido de que los esquemas de tabla incluyen un identificador de inquilino. 

Este patrón de base de datos permite almacenar uno o varios inquilinos en cada partición o base de datos. Para optimizar la disminución del costo, varios inquilinos pueden compartir cada base de datos. O bien puede optimizar el aislamiento, haciendo que cada base de datos solo almacene un inquilino. Puede tomar la opción de optimización por separado para cada inquilino específico. Puede tomar la opción la primera vez que se almacena el inquilino, o bien puede decidirlo más adelante. El diseño de la aplicación le permite funcionar bien de cualquier modo.

#### <a name="app-deploys-quickly"></a>La aplicación se implementa rápidamente

La sección de implementación que sigue proporciona el botón **Implementar en Azure**. Cuando se presiona el botón, la aplicación Wingtip se implementa completamente unos cinco minutos después. La aplicación Wingtip se ejecuta en la nube de Azure y usa Azure SQL Database. Wingtip se implementa en la suscripción de Azure. Tiene acceso completo para trabajar con los componentes individuales de la aplicación.

La aplicación se implementa con los datos para los tres inquilinos de ejemplo. Los inquilinos se almacenan juntos en una base de datos multiinquilino.

Cualquier usuario puede descargar el código fuente de C# y PowerShell para Wingtip Tickets desde [el repositorio de GitHub][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>En este tutorial

> [!div class="checklist"]

> - Cómo implementar la aplicación SaaS de Wingtip.
> - Dónde obtener el código fuente de la aplicación y los scripts de administración.
> - Información sobre los servidores y las bases de datos que componen la aplicación.
> - Cómo se asignan los inquilinos a sus datos con el *catálogo*.
> - Cómo aprovisionar un nuevo inquilino.
> - Cómo supervisar la actividad de los inquilinos en la aplicación.

Hay disponible una serie de tutoriales relacionados que se basan en esta implementación inicial. En los tutoriales se explora una variedad de patrones de diseño y administración de SaaS. Cuando trabaja con los tutoriales, se recomienda que recorra los scripts proporcionados para ver cómo se implementan los distintos patrones de SaaS.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

- Tiene instalada la versión más reciente de Azure PowerShell. Para más información, consulte [Introducción a Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Implementación de la aplicación Wingtip Tickets

1. Haga clic en el botón **Implementar en Azure** siguiente.
    - Se abrirá Azure Portal con la plantilla de implementación de la aplicación SaaS Wingtip Tickets. La plantilla requiere dos valores de parámetro: un nombre para un grupo de recursos nuevo y un valor de "usuario" que distingue esta implementación de otras implementaciones de la aplicación. En el paso siguiente se proporcionan los detalles para establecer estos valores de parámetro.
        - Asegúrese de anotar los valores exactos que use, porque los necesitará más adelante para un archivo de configuración.

    [![Botón para Implementar en Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. Especifique los valores necesarios de los parámetros para la implementación.

    > [!IMPORTANT]
    > Parte de la configuración del firewall del servidor y de la autenticación es poco segura de manera intencional para facilitar la demostración. Elija **Crear nuevo grupo de recursos** y no use grupos de recursos, servidores o grupos existentes. No use esta aplicación, ni ninguno de los recursos que se crean, para producción. Elimine este grupo de recursos cuando haya terminado con la aplicación para detener la facturación relacionada con él.

    Se recomienda usar solo letras minúsculas, números y guiones en los nombres de recurso.

    - En **Grupo de recursos**: seleccione **Crear nuevo** y, luego, proporcione un **nombre** para el grupo de recursos (distingue mayúsculas de minúsculas).
        - Se recomienda usar solo letras minúsculas en el nombre del grupo de recursos.
        - Le recomendamos anexar un guión, seguido de sus iniciales, y después un dígito, por ejemplo: *wingtip-af1*.
        - Seleccione una **ubicación** en la lista desplegable.
    - En **Usuario**: se recomienda elegir un valor **Usuario** corto, como sus iniciales más un dígito, por ejemplo: *af1*.

3. **Implemente la aplicación**.

    - Haga clic para aceptar los términos y condiciones.
    - Haga clic en **Comprar**.

4. Supervise el estado de implementación con un clic en **Notificaciones**, que es el icono de la campaña que aparece a la derecha del cuadro de búsqueda. La implementación de la aplicación Wingtip tarda aproximadamente cinco minutos.

   ![implementación correcta](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Descarga y desbloqueo de los scripts de administración

Durante la implementación de la aplicación, descargue el código fuente y los scripts de administración de la aplicación.

> [!IMPORTANT]
> Es posible que Windows bloquee el contenido ejecutable (scripts, archivos DLL) cuando se descarguen y extraigan archivos ZIP desde un origen externo. Cuando extraiga los scripts de un archivo ZIP, use estos pasos para desbloquear el archivo ZIP antes de extraerlo. Al desbloquear el archivo ZIP se asegura de que los scripts estén autorizados para ejecutarse.

1. Vaya al [repositorio de GitHub de WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Haga clic en **Clone or download** (Clonar o descargar).
3. Haga clic en **Download ZIP** (Descargar archivos ZIP) y guarde el archivo.
4. Haga clic con el botón derecho en el archivo **WingtipTicketsSaaS-MultiTenantDb-master.zip** y seleccione **Propiedades**.
5. En la pestaña **General**, seleccione **Desbloquear** y haga clic en **Aplicar**.
6. Haga clic en **Aceptar**.
7. Extraiga los archivos.

Los scripts se encuentra en la carpeta *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\*.

## <a name="update-the-configuration-file-for-this-deployment"></a>Actualizar el archivo de configuración para esta implementación

Antes de ejecutar cualquier script, establezca los valores *grupo de recursos* y *usuario* en **UserConfig.psm1**. Establezca estas variables en los mismos valores establecidos durante la implementación.

1. Abra ...\\Learning Modules\\*UserConfig.psm1* en *PowerShell ISE*
2. Actualice *ResourceGroupName* y *Name* con los valores específicos para la implementación (solo en las líneas 10 y 11).
3. Guarde los cambios.

Todos los scripts usan los valores establecidos en este archivo, por lo que es importante que sean precisos. Si vuelve a implementar la aplicación, asegúrese de elegir otro grupo de recursos y otro valor de usuario. Luego, actualice UserConfig con los valores nuevos.

## <a name="run-the-application"></a>Ejecución de la aplicación

La aplicación presenta lugares, como salas de concierto, clubs de jazz o clubes deportivos, que alojan eventos. Los lugares se registran como clientes (o inquilinos) de la plataforma Wingtip, lo que facilita la enumeración de eventos y la venta de entradas. Cada lugar obtiene una aplicación web personalizada que administra y enumera sus eventos y vende las entradas, que independiente y está aislada de otros inquilinos. En segundo plano, los datos de cada inquilino se almacenan de manera predeterminada en una base de datos multiinquilino con particiones.

Una instancia central de **Events Hub** proporciona una lista de los vínculos a los inquilinos en su implementación específica.

1. Abra *Events Hub* en el explorador web:
    - http://events.wingtip.&lt;USUARIO&gt;.trafficmanager.net &nbsp; *(reemplácelo por el valor de usuario de su implementación)*

    ![events hub](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Haga clic en **Fabrikam Jazz Club** en el *centro de eventos*.

   ![Eventos](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

Para controlar la distribución de las solicitudes entrantes, la aplicación usa [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Las páginas de eventos, que son específicas del inquilino, incluyen el nombre del inquilino en la dirección URL. Las direcciones URL también incluyen el valor de usuario específico y siguen este formato:

- http://events.wingtip.&lt;USUARIO&gt;.trafficmanager.net/*fabrikamjazzclub*
 
La aplicación de eventos analiza el nombre del inquilino de la dirección URL y le aplica código hash para crear una clave para acceder a un catálogo mediante la [administración del mapa de particiones](sql-database-elastic-scale-shard-map-management.md). El catálogo asigna la clave a la ubicación de base de datos del inquilino. **Events Hub** enumera todos los inquilinos que se encuentran registrados en el catálogo. **Events Hub** usa metadatos extendidos en el catálogo para recuperar el nombre del inquilino asociado con cada asignación para construir las direcciones URL.

En un entorno de producción, lo habitual sería crear un registro DNS CNAME que [apunte a un dominio de empresa de Internet](../traffic-manager/traffic-manager-point-internet-domain.md) para el perfil de Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Empiece a generar carga en las bases de datos de los inquilinos

Ahora que se implementó la aplicación, vamos a ponerla en marcha. El script *Demo-LoadGenerator* de PowerShell inicia una carga de trabajo que se ejecuta para cada inquilino. La carga real en muchas aplicaciones de SaaS normalmente es esporádica e imprevisible. Para simular este tipo de carga, el generador produce una carga distribuida entre todos los inquilinos. La carga incluye ráfagas aleatorias en cada inquilino que se producen a intervalos aleatorios. El modelo de carga tarda varios minutos en surgir, por lo que es mejor permitir que el generador se ejecute durante al menos tres o cuatro minutos antes de supervisar la carga.

1. En *PowerShell ISE*, abra el script ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Presione **F5** para ejecutar el script e iniciar el generador de carga (por ahora deje los valores predeterminados de los parámetros).

> [!IMPORTANT]
> El script *Demo-LoadGenerator.ps1* abre otra sesión de PowerShell donde se ejecuta el generador de carga. El generador de carga se ejecuta en esta sesión como una tarea en primer plano que invoca trabajos de generación de carga en segundo plano, uno para cada inquilino.

Una vez que se inicia la tarea en primer plano, se mantiene en un estado de invocación de trabajo. La tarea inicia trabajos en segundo plano adicionales para cualquier inquilino nuevo que se aprovisione posteriormente.

Cerrar la sesión de PowerShell detiene todos los trabajos.

Es posible que desee reiniciar la sesión del generador de carga para usar otros valores de parámetro. Si es así, cierre la sesión de generación de PowerShell y, luego, vuelva a ejecutar *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Aprovisionamiento de un inquilino nuevo en la base de datos con particiones

La implementación inicial incluye tres inquilinos de ejemplo en la base de datos *Tenants1*. Vamos a crear otro inquilino para ver cómo afecta esto a la aplicación implementada. En este paso, se crea rápidamente un inquilino.

1. Abra ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionTenants.ps1* en *PowerShell ISE*.
2. Presione **F5** para ejecutar el script (deje por ahora los valores predeterminados).

   > [!NOTE]
   > Muchos scripts de SaaS de Wingtip Tickets usan *$PSScriptRoot* para permitir la navegación de las carpetas, para invocar otros scripts o para importar módulos. Esta variable solo se evalúa cuando el script se ejecuta como un conjunto al presionar **F5**.  Resaltar y ejecutar una selección (**F8**) puede dar lugar a errores, por tanto, presione **F5** al ejecutar los scripts.

El nuevo inquilino Red Maple Racing se agrega a la base de datos *Tenants1* y se registra en el catálogo. El sitio de *eventos* de venta de entradas del nuevo inquilino se abre en el explorador:

![Nuevo inquilino](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Actualice el *centro de eventos* y el nuevo inquilino aparece ahora en la lista.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprovisionamiento de un inquilino nuevo en su propia base de datos

El modelo multiinquilino con particiones permite elegir entre aprovisionar un inquilino nuevo en una base de datos que contiene otros inquilinos o aprovisionar el inquilino en su propia base de datos. Un inquilino en su propia base de datos se beneficia de tener los datos aislados de los datos de otros inquilinos. El aislamiento permite administrar el rendimiento del inquilino de manera independiente de otros inquilinos. Además, es más sencillo restaurar los datos del inquilino aislado a un momento anterior. Puede decidir colocar los clientes de versiones de evaluación gratuita o los clientes habituales en una base de datos multiinquilino, y los clientes Premium en bases de datos individuales. Si crea grandes cantidades de bases de datos donde cada una contiene solo un inquilino, puede administrarlas todas de manera colectiva en un grupo elástico para optimizar los costos de los recursos.  

Ahora aprovisionamos otro inquilino, esta vez en su propia base de datos.

1. En \\Módulos de aprendizaje\\Aprovisionamiento y catálogo\*Demo-ProvisionTenants.ps1*, modifique *$TenantName* a **Salix Salsa**, *$VenueType* a **dance** y *$Scenario* a **2**.

2. Presione **F5** para volver a ejecutar el script.
    - Esta acción aprovisiona el inquilino nuevo en una base de datos independiente. La base de datos y el inquilino se registran en el catálogo. Luego, el explorador se abre en la página de eventos del inquilino.

   ![Página de eventos de Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Desplácese hasta la parte inferior de la página. En el banner verá el nombre de la base de datos en que están almacenados los datos del inquilino.

3. Actualice Events Hub y los dos inquilinos nuevos aparecerán ahora en la lista.



## <a name="explore-the-servers-and-tenant-databases"></a>Exploración de los servidores y las bases de datos de inquilino

Veamos ahora algunos de los recursos que se implementaron:

1. En [Azure portal](http://portal.azure.com), navegue a la lista de los grupos de recursos. Abra el grupo de recursos que creó cuando implementó la aplicación.

   ![resource group](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Haga clic en el servidor **catalog-mt&lt;USER&gt;**. El servidor del catálogo contiene dos bases de datos denominadas *tenantcatalog* y *basetenantdb*. La base de datos *basetenantdb* es una base de datos de plantilla vacía. Se copia para crear una base de datos de inquilino nueva, ya sea para que la usen muchos inquilinos o solo uno.

   ![Servidor de catálogo](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Vuelva al grupo de recursos y seleccione el servidor *tenants1-mt* que contiene las bases de datos de inquilino.
    - La base de datos tenants1 es una base de datos multiinquilino en la que se almacenan los tres inquilinos originales, además del primer inquilino que agregó. Está configurada como una base de datos estándar de 50 DTU.
    - La base de datos **salixsalsa** contiene el lugar de baile de Salix Salsa como el único inquilino. Está configurada como una base de datos de edición Estándar con 50 DTU como valor predeterminado.

   ![servidor de inquilinos](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Supervisión del rendimiento de la base de datos

Si el generador de carga lleva varios minutos en ejecución, debería haber suficiente telemetría para empezar a examinar algunas de las funcionalidades de supervisión de bases de datos integradas en el portal.

1. Navegue al servidor **tenants1-mt&lt;USUARIO&gt;** y haga clic en **tenants1** para ver el uso de recursos para la base de datos que tiene cuatro inquilinos en ella. Cada inquilino está sujeto a una carga pesada esporádica desde el generador de carga:

   ![supervisión de tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   El gráfico de uso de DTU ilustra bien cómo una base de datos multiinquilino puede admitir una carga de trabajo impredecible a través de varios inquilinos. En este caso, el generador de carga aplica una carga esporádica de aproximadamente 30 DTU a cada inquilino. Esta carga equivale al 60 % del uso de una base de datos de 50 DTU. Valores máximos que superan el 60 % son el resultado de la carga que se aplica a más de un inquilino al mismo tiempo.

2. Navegue al servidor **tenants1-mt&lt;USUARIO&gt;** y haga clic en la base de datos **salixsalsa** para ver el uso de recursos de esta base de datos que solo contiene un inquilino.

   ![base de datos salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

El generador de carga aplica una carga similar a cada inquilino, independientemente de la base de datos en que se encuentra cada inquilino. Con solo un inquilino en la base de datos **salixsalsa**, puede ver que la base de datos podría admitir una carga mucho mayor que la base de datos con varios inquilinos. 

> [!NOTE]
> Las cargas que crea el generador de carga solo tienen fines ilustrativos.  Los recursos asignados a bases de datos multiinquilino y de inquilino único, además de los números de inquilinos que puede hospedar en una base de datos multiinquilino, depende de los patrones de carga de trabajo reales en la aplicación.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha obtenido información:

> [!div class="checklist"]

> - Implementación de la aplicación SaaS de base de datos multiinquilino Widget Tickets
> - Información sobre los servidores y las bases de datos que componen la aplicación
> - Acerca de los inquilinos que se asignan a sus datos con el *catálogo*
> - Aprovisionamiento de inquilinos nuevos en una base de datos multiinquilino o en una base de datos de inquilino único.
> - Acerca de cómo ver el uso de grupos para supervisar la actividad de los inquilinos
> - Acerca de cómo eliminar los recursos de ejemplo para detener la facturación relacionada con ellos

Ahora pruebe el [tutorial de aprovisionamiento del catálogo](sql-database-saas-tutorial-provision-and-catalog.md).



## <a name="additional-resources"></a>Recursos adicionales

- Para obtener información acerca de aplicaciones SaaS multiempresa, consulte [*Modelos de diseño para las aplicaciones SaaS multiempresa y Azure SQL Database*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)








<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/





<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."

-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png

