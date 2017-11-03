---
title: "Tutorial de la aplicación SaaS multiinquilino con Azure SQL Database | Microsoft Docs"
description: "Implemente y explore la aplicación SaaS de Wingtip multiinquilino, en la que se muestran los modelos de SaaS con Azure SQL Database."
keywords: tutorial de base de datos sql
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
ms.date: 07/26/2017
ms.author: sstein
ms.openlocfilehash: f624485d32901abb5f0d01c86fc30ddaaae261a3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-and-explore-a-multi-tenant-application-that-uses-azure-sql-database---wingtip-saas"></a>Implementación y exploración de una aplicación SaaS multiinquilino que usa Azure SQL Database

En este tutorial, se implementa y explora la aplicación SaaS de Wingtip. La aplicación utiliza un patrón de aplicación SaaS de base de datos por inquilino para dar servicio a varios inquilinos. La aplicación está diseñada para presentar las características de Azure SQL Database que simplifican escenarios de SaaS.

Cinco minutos después de hacer clic en el botón *Implementar en Azure*, tiene una aplicación de SaaS multiempresa que usa SQL Database en funcionamiento en la nube. La aplicación se implementa con tres inquilinos de ejemplo, cada uno con su propia base de datos, y todos ellos implementados en un grupo elástico de SQL. La aplicación se implementa en su suscripción de Azure, lo que le proporciona acceso completo para explorar y trabajar con los componentes de la aplicación individuales. El código fuente y los scripts de administración de la aplicación están disponibles en el repositorio de GitHub WingtipSaaS.


En este tutorial, obtendrá información:

> [!div class="checklist"]

> * Cómo implementar la aplicación SaaS de Wingtip
> * Dónde obtener el código fuente de la aplicación y los scripts de administración
> * Acerca de los servidores, los grupos y las bases de datos que componen la aplicación
> * Cómo se asignan los inquilinos a sus datos con el *catálogo*
> * Cómo aprovisionar un nuevo inquilino
> * Cómo supervisar la actividad de los inquilinos en la aplicación

Para explorar diversos patrones de administración y diseño de SaaS, hay un [serie de tutoriales relacionados](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials) disponibles que se basan en esta implementación inicial. Cuando recorra los tutoriales, profundice en los scripts que se proporcionan y examine cómo se implementan los distintos patrones de SaaS. Recorra los scripts de cada tutorial para conocer mejor cómo implementar la gran cantidad de características de SQL Database que simplifican el desarrollo de aplicaciones SaaS.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-saas-application"></a>Implementar la aplicación SaaS de Wingtip

Implemente la aplicación SaaS de Wingtip:

1. Al hacer clic en el botón **Implementar en Azure** se abre Azure Portal por la plantilla de implementación SaaS de Wingtip. La plantilla requiere dos valores de parámetro; un nombre para un nuevo grupo de recursos y un nombre de usuario que distingue esta implementación de otras implementaciones de la aplicación SaaS de Wingtip. El paso siguiente proporciona detalles para establecer estos valores.

   Asegúrese de anotar los valores exactos que use, ya que necesitará escribirlos más adelante en un archivo de configuración.

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Especifique los valores necesarios de los parámetros para la implementación:

    > [!IMPORTANT]
    > Para realizar la demostración, se ha eliminado intencionadamente la protección de varios firewalls de autenticación y del servidor. **Cree un nuevo grupo de recursos** y no use grupos de recursos, servidores o grupos existentes. No use esta aplicación, ni ninguno de los recursos que se crean, para producción. Elimine este grupo de recursos cuando haya terminado con la aplicación para detener la facturación relacionada con él.

    * **Grupo de recursos**: seleccione **Crear nuevo** y proporcione un **nombre** para el nuevo grupo de recursos. Seleccione una **ubicación** en la lista desplegable.
    * **Usuario**: algunos recursos requieren nombres que sean globalmente únicos. Para garantizar la unicidad, cada vez que implemente la aplicación, especifique un valor para diferenciar los recursos que cree de los que han creado otras implementaciones de la aplicación Wingtip. Se recomienda usar un nombre de **usuario** corto, como sus iniciales y un número (por ejemplo, *bg1*) y, después, utilizarlo en el nombre del grupo de recursos (por ejemplo, *wingtip-bg1*). El parámetro **Usuario** solo puede contener letras, números y guiones (sin espacios). El primer y último carácter deben ser una letra o un número (se recomienda usar minúsculas).


1. **Implemente la aplicación**.

    * Haga clic para aceptar los términos y condiciones.
    * Haga clic en **Comprar**.

1. Supervise el estado de implementación, para lo que debe hacer clic en **Notificaciones** (el icono de la campana de la derecha del cuadro de búsqueda). La implementación de la aplicación SaaS de Wingtip tarda aproximadamente cinco minutos.

   ![implementación correcta](media/sql-database-saas-tutorial/succeeded.png)

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Descargar y desbloquear los scripts SaaS de Wingtip

Mientras la aplicación se está implementando, descargue el código fuente y los scripts de administración.

> [!IMPORTANT]
> Es posible que Windows bloquee el contenido ejecutable (scripts, archivos DLL) cuando se descarguen y extraigan archivos ZIP desde un origen externo. Al extraer los scripts de un archivo ZIP, siga estos pasos para desbloquear el archivo .zip antes de extraerlo. Esto garantiza que se puedan ejecutar los scripts.

1. Vaya al [repositorio de SaaS de Wingtip en GitHub](https://github.com/Microsoft/WingtipSaaS).
1. Haga clic en **Clone or download** (Clonar o descargar).
1. Haga clic en **Download ZIP** (Descargar archivos ZIP) y guarde el archivo.
1. Haga clic con el botón derecho en el archivo **WingtipSaaS-master.zip** y seleccione **Propiedades**.
1. En la pestaña **General**, seleccione **Desbloquear** y haga clic en **Aplicar**.
1. Haga clic en **Aceptar**.
1. Extraiga los archivos.

Los scripts se encuentran en la carpeta *..\\WingtipSaaS-master\\Learning Modules*.

## <a name="update-the-configuration-file-for-this-deployment"></a>Actualizar el archivo de configuración para esta implementación

Antes de ejecutar cualquier script, establezca los valores *grupo de recursos* y *usuario* en **UserConfig.psm1**. Establezca estas variables en los valores establecidos durante la implementación.

1. Abra ...\\Learning Modules\\*UserConfig.psm1* en *PowerShell ISE*
1. Actualice *ResourceGroupName* y *Name* con los valores específicos para la implementación (solo en las líneas 10 y 11).
1. Guarde los cambios.

Al establecerlo aquí simplemente evita tener que actualizar estos valores específicos de la implementación en todos los scripts.

## <a name="run-the-application"></a>Ejecución de la aplicación

La aplicación presenta lugares, como salas de concierto, clubs de jazz o clubes deportivos, que alojan eventos. Los lugares se registran como clientes (o inquilinos) de la plataforma Wingtip, lo que facilita la enumeración de eventos y la venta de entradas. Cada lugar obtiene una aplicación web personalizada que administra y enumera sus eventos y vende las entradas, que independiente y está aislada de otros inquilinos. Entre bastidores, cada inquilino obtiene una base de datos SQL que se implementa en un grupo elástico de SQL.

Un **centro de eventos** central proporciona una lista de las direcciones URL de los inquilinos específicas para la implementación.

1. Abra el _centro de eventos_ en el explorador web: http://events.wtp.&lt;USUARIO&gt;.trafficmanager.net (reemplácelo por el nombre de usuario de la implementación):

    ![events hub](media/sql-database-saas-tutorial/events-hub.png)

1. Haga clic en **Fabrikam Jazz Club** en el *centro de eventos*.

   ![Eventos](./media/sql-database-saas-tutorial/fabrikam.png)


Para controlar la distribución de las solicitudes entrantes, la aplicación usa [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Las páginas de eventos, que son específicas del inquilino, requieren que los nombres de los inquilinos se incluyan en las direcciones URL. Todas estas direcciones incluyen un valor específico de *Usuario* y siguen este formato: http://events.wtp.&lt; USUARIO&gt;.trafficmanager.net/*fabrikamjazzclub*. La aplicación de eventos analiza el nombre de inquilino de la dirección URL y lo utiliza para crear una clave para acceder a un catálogo mediante la [*administración del mapa de particiones*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). El catálogo asigna la clave a la ubicación de base de datos del inquilino. El **centro de eventos** usa metadatos extendidos en el catálogo para recuperar el nombre del inquilino asociado a cada base de datos para proporcionar la lista de direcciones URL.

En un entorno de producción, lo habitual sería crear un registro DNS CNAME que [*apunte a un dominio de empresa de Internet*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) para el perfil de Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Empiece a generar carga en las bases de datos de los inquilinos

Ahora que la aplicación se ha implementado, vamos a ponerla en marcha. El script de PowerShell *Demo-LoadGenerator* inicia una carga de trabajo que se ejecuta en las bases de datos de todos los inquilinos. La carga real en muchas aplicaciones de SaaS normalmente es esporádica e imprevisible. Para simular este tipo de carga, el generador genera una carga que se distribuye entre todos los inquilinos, con ráfagas aleatorias en cada inquilino que se producen a intervalos aleatorios. Por este motivo, el modelo de carga tarda varios minutos en surgir, por lo que es mejor permitir que el generador se ejecute durante al menos tres o cuatro minutos antes de supervisar la carga.

1. En *PowerShell ISE*, abra el script ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
1. Presione **F5** para ejecutar el script e iniciar el generador de carga (por ahora deje los valores predeterminados de los parámetros).

> [!IMPORTANT]
> Para ejecutar otros scripts, abra una nueva ventana de PowerShell ISE. El generador de carga se ejecuta como una serie de trabajos en la sesión local de PowerShell. El script *Demo-LoadGenerator.ps1* inicia el script real del generador de carga, que se ejecuta como una tarea en primer plano además de una serie de trabajos de generación de carga en segundo plano. Se invoca un trabajo de generador de carga para cada base de datos registrada en el catálogo. Los trabajos se ejecutan en la sesión de PowerShell local, por lo que al cerrar la sesión de PowerShell se detienen todos los trabajos. Si se suspende el equipo, se pausa la generación de carga y se reanudará cuando reactive el equipo.

Una vez que el generador de carga invoca los trabajos de generación de carga para cada inquilino, la tarea en primer plano permanece en un estado de invocación de trabajo, donde inicia trabajos en segundo plano adicionales para los nuevos inquilinos que se aprovisionen posteriormente. Puede presionar *Ctrl-C* o pulsar el botón *Detener* para detener la tarea en primer plano, pero los trabajos en segundo plano existentes seguirán generando la carga en cada base de datos. Si necesita supervisar y controlar los trabajos en segundo plano, use *Get-Job*, *Receive-Job* y *Stop-Job*. Mientras se ejecuta la tarea en primer plano no se puede usar la misma sesión de PowerShell para ejecutar otros scripts. Para ejecutar otros scripts, abra una nueva ventana de PowerShell ISE.

Si quiere reiniciar la sesión del generador de carga, por ejemplo con parámetros diferentes, puede detener la tarea en primer plano y, después, volver a ejecutar el script *Demo-LoadGenerator.ps1*. Al volver a ejecutar *Demo-LoadGenerator.ps1* primero se detiene cualquier trabajo en ejecución y después se reinicia el generador, que inicia un nuevo conjunto de trabajos con los parámetros actuales.

Por ahora, deje el generador de carga en ejecución en el estado de invocación de trabajo.


## <a name="provision-a-new-tenant"></a>Aprovisionamiento de un nuevo inquilino

La implementación inicial crea tres inquilinos de ejemplo, pero vamos a crear otro inquilino para ver cómo afecta a la aplicación implementada. El flujo de trabajo de aprovisionamiento de inquilinos de SaaS de Wingtip se detalla en el [tutorial de aprovisionamiento y el catálogo](sql-database-saas-tutorial-provision-and-catalog.md). En este paso, se crea rápidamente un inquilino.

1. Abra ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* en *PowerShell ISE*.
1. Presione **F5** para ejecutar el script (deje por ahora los valores predeterminados).

   > [!NOTE]
   > Muchos scripts SaaS de Wingtip usan *$PSScriptRoot* para permitir navegar por las carpetas para llamar a funciones en otros scripts. Esta variable solo se evalúa cuando se ejecuta el script presionando **F5**.  Resaltar y ejecutar una selección (**F8**) puede dar lugar a errores, por tanto, presione **F5** al ejecutar los scripts.

La nueva base de datos de inquilinos se crea en un grupo elástico de SQL, se inicializa y se registra en el catálogo. Si el aprovisionamiento se ha realizado correctamente, el sitio de *eventos* de venta de entradas del nuevo inquilino aparece en el explorador:

![Nuevo inquilino](./media/sql-database-saas-tutorial/red-maple-racing.png)

Actualice el *centro de eventos* y el nuevo inquilino aparece ahora en la lista.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explore los servidores, los grupos y las bases de datos de los inquilinos

Ahora que ha empezado a ejecutar una carga en la colección de inquilinos, veamos algunos de los recursos que se han implementado:

1. En [Azure Portal](http://portal.azure.com), vaya a la lista de servidores SQL Server y abra el servidor **catalog-&lt;USUARIO&gt;**. El servidor de catálogo contiene dos bases de datos: **tenantcatalog** y **basetenantdb** (una base de datos *golden* vacía o una base de datos de plantilla que se copia para crear nuevos inquilinos).

   ![bases de datos](./media/sql-database-saas-tutorial/databases.png)

1. Vuelva a la lista de servidores de SQL Server y abra el servidor **tenants1&lt;&gt;USUARIO** que contiene las bases de datos de los inquilinos. Todas las bases de datos de inquilinos son _elásticas estándar_ en un conjunto estándar de 50 eDTU. Observe también hay una base de datos de _Red Maple Racing_, la base de datos de inquilino que aprovisionó anteriormente.

   ![Servidor](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Supervisión del grupo

Si el generador de carga lleva varios minutos en ejecución, debería haber suficientes datos disponibles para empezar a examinar algunas de las funcionalidades de supervisión integradas en los grupos y las bases de datos.

1. Vaya al servidor de **tenants1-&lt;USUARIO&gt;** y haga clic en **Pool1** para ver el uso de los recursos para el grupo (el generador de carga se ejecutó durante una hora en los siguientes gráficos):

   ![supervisar grupo](./media/sql-database-saas-tutorial/monitor-pool.png)

Estos dos gráficos ilustran es el grado de idoneidad de los grupos elásticos y de SQL Database para las cargas de trabajo de una aplicación SaaS. Cuatro bases de datos que generen un máximo de 40 eDTU cada una se admiten fácilmente en un grupo de 50 eDTU. Si se aprovisionaron como bases de datos independientes, cada una debe ser de tipo S2 (50 DTU) para admitir las ráfagas. El coste de cuatro bases de datos S2 independientes sería casi tres veces el precio del grupo y el grupo todavía tiene una gran cantidad de espacio disponible para muchas más bases de datos. En situaciones reales, los clientes de SQL Database están ejecutando hasta 500 bases de datos en grupos de 200 eDTU. Para más información consulte el [tutorial de supervisión del rendimiento](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha obtenido información:

> [!div class="checklist"]

> * Cómo implementar la aplicación SaaS de Wingtip
> * Acerca de los servidores, los grupos y las bases de datos que componen la aplicación
> * Acerca de los inquilinos que se asignan a sus datos con el *catálogo*
> * Acerca de cómo aprovisionar nuevos inquilinos
> * Acerca de cómo ver el uso de grupos para supervisar la actividad de los inquilinos
> * Acerca de cómo eliminar los recursos de ejemplo para detener la facturación relacionada con ellos

Ahora pruebe el [tutorial de aprovisionamiento del catálogo](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>Recursos adicionales

* Otros [tutoriales basados en la aplicación SaaS de Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* Para obtener información acerca de los grupos elásticos, consulte [*¿Qué son los grupos elásticos de Azure SQL?*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Para obtener información acerca de los trabajos elásticos, consulte [*Administración de bases de datos escaladas horizontalmente en la nube*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* Para obtener información acerca de aplicaciones SaaS multiempresa, consulte [*Modelos de diseño para las aplicaciones SaaS multiempresa y Azure SQL Database*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)
