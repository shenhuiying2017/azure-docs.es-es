---
title: "Tutorial de la aplicación de SaaS de base de datos por inquilino: Azure SQL Database | Microsoft Docs"
description: "Implemente y descubra la aplicación SaaS Wingtip Tickets multiinquilino, en la que se muestra el patrón de base de datos por inquilino y otros modelos SaaS con Azure SQL Database."
keywords: tutorial de SQL Database
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: 5342b5290fab9826a2b38cd7ada63a6736c77601
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Implementación y exploración de una aplicación SaaS multiinquilino que usa el patrón de base de datos por inquilino con Azure SQL Database

En este tutorial, se implementa y explora la aplicación de *base de datos por inquilino* de SaaS Wingtip Tickets (Wingtip). La aplicación utiliza un patrón de base de datos por inquilino para almacenar los datos de varios inquilinos. La aplicación está diseñada para presentar las características de Azure SQL Database que simplifican la habilitación de escenarios de SaaS.

Cinco minutos después de hacer clic en el botón azul con la etiqueta **Implementar en Azure**, tiene una aplicación de SaaS multiinquilino. La aplicación incluye una instancia de Azure SQL Database que se ejecuta en la nube de Microsoft Azure. La aplicación se implementa con tres inquilinos de ejemplo, cada uno con su propia base de datos. Todas las bases de datos se implementan en un *grupo elástico* de SQL. La aplicación se implementa en la suscripción de Azure. Tiene acceso completo para explorar y trabajar con los componentes individuales de la aplicación. El código fuente C# y los scripts de administración de la aplicación están disponibles en el [repositorio de GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].

En este tutorial, obtendrá información:

> [!div class="checklist"]
> - Cómo implementar la aplicación SaaS de Wingtip.
> - Dónde obtener el código fuente de la aplicación y los scripts de administración.
> - Acerca de los servidores, los grupos y las bases de datos que componen la aplicación.
> - Cómo se asignan los inquilinos a sus datos con el *catálogo*.
> - Cómo aprovisionar un nuevo inquilino.
> - Cómo supervisar la actividad de los inquilinos en la aplicación.

Una [serie de tutoriales relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) ofrece la posibilidad de explorar varios patrones de diseño y administración de SaaS. Los tutoriales se basan en esta implementación inicial.
Al usar los tutoriales y examinar los scripts que se proporcionan, se observa cómo se implementan los distintos patrones de SaaS.
Los scripts muestran cómo las características de SQL Database simplifican el desarrollo de aplicaciones de SaaS.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implementación de la aplicación SaaS Wingtip Tickets

Implemente la aplicación:

1. Elija y recuerde los valores que necesitará para los siguientes parámetros:

    - **Usuario**: elija un valor breve, como sus iniciales, seguidas de un dígito. Por ejemplo, *af1*. El parámetro Usuario solo puede contener letras, dígitos y guiones (sin espacios). El primer y el último carácter deben ser una letra o un dígito. Es recomendable usar solo letras en minúsculas.
    - **Grupo de recursos**: cada vez que implemente la aplicación Wingtip, debe elegir un nombre único diferente para el nuevo grupo de recursos. Es recomendable anexar el nombre de usuario a un nombre base para el grupo de recursos. Por ejemplo, *af1 wingtip* podría ser el nombre de un grupo de recursos de ejemplo. De nuevo, es recomendable que todas las letras estén en minúsculas.

2. Haga clic en el botón azul **Implementar en Azure** para abrir la plantilla de implementación de base de datos por inquilino de la aplicación de SaaS Wingtip Tickets en Azure Portal.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

3. En la plantilla, escriba valores para los parámetros requeridos:

    > [!IMPORTANT]
    > Para realizar la demostración, se ha eliminado intencionadamente la protección de varios firewalls de autenticación y del servidor. Es recomendable *crear un nuevo grupo de recursos*. No use grupos de recursos, servidores o grupos existentes. No use esta aplicación, los scripts ni ninguno de los recursos de implementación para producción. Elimine este grupo de recursos cuando haya terminado con la aplicación para detener la facturación relacionada con él.

    - **Grupo de recursos**: seleccione **Crear nuevo** y proporcione el **Nombre** que eligió antes para el grupo de recursos. 
    - **Ubicación** : seleccione una **ubicación** de la lista desplegable.
    - **Usuario**: use el valor de nombre de usuario que eligió antes.

4. Implemente la aplicación.

    - Haga clic para aceptar los términos y condiciones.
    - Haga clic en **Comprar**.

5. Para supervisar el estado de implementación, haga clic en **Notificaciones**, que es el icono de campana que aparece a la derecha del cuadro de búsqueda. La implementación de la aplicación SaaS Wingtip Tickets tarda aproximadamente cinco minutos.

   ![implementación correcta](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Descarga y desbloqueo de los scripts de administración de Wingtip Tickets

Mientras la aplicación se está implementando, descargue el código fuente y los scripts de administración.

> [!IMPORTANT]
> Es posible que Windows bloquee el contenido ejecutable (scripts, archivos DLL) cuando se descarguen y extraigan archivos ZIP desde un origen externo. Cuando extraiga los scripts de un archivo ZIP, use estos pasos para desbloquear el archivo ZIP antes de extraerlo. El desbloqueo garantiza que se puedan ejecutar los scripts.

1. Vaya al [repositorio de GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].
2. Haga clic en **Clone or download** (Clonar o descargar).
3. Haga clic en **Download ZIP** (Descargar archivo ZIP) y guarde el archivo.
4. Haga clic con el botón derecho en el archivo **WingtipTicketsSaaS-DbPerTenant-master.zip** y seleccione **Properties** (Propiedades).
5. En la pestaña **General** (General), seleccione **Unblock** (Desbloquear) y haga clic en **Apply** (Aplicar).
6. Haga clic en **Aceptar**.
7. Extraiga los archivos.

Los scripts se encuentran en la carpeta *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules*.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Actualización del archivo de configuración de usuario para esta implementación

Antes de ejecutar cualquier script, actualice los valores de *resource group* y *user* en **UserConfig.psm1**. Establezca estas variables en los valores usados durante la implementación.

1. En *PowerShell ISE*, abra ...\\Learning Modules\\*UserConfig.psm1*. 
2. Actualice *ResourceGroupName* y *Name* con los valores específicos para la implementación (solo en las líneas 10 y 11).
3. Guarde los cambios.

A estos valores se hacen referencia en casi todos los scripts.

## <a name="run-the-application"></a>Ejecución de la aplicación

La aplicación presenta lugares que albergan eventos. Entre los tipos de lugares hay salas de concierto, clubs de jazz y clubs deportivos. En Wingtip Tickets, los lugares se registran como inquilinos. El hecho de ser un inquilino ofrece a un lugar una manera sencilla de enumerar eventos y vender entradas para estos a sus clientes. Cada lugar obtiene un sitio web personalizado para mostrar sus eventos y vender entradas.

Dentro de la aplicación, cada inquilino obtiene una base de datos SQL implementada en un grupo elástico de SQL.

Una página de **Event Hubs** central proporciona una lista de vínculos a los inquilinos en su implementación.

1. Abra el *centro de Event Hubs* en el explorador web: http://events.wingtip-dpt.&lt;USER&gt;.trafficmanager.net (sustituya &lt;USER&gt; por el valor de usuario de la implementación).

    ![events hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Haga clic en **Fabrikam Jazz Club** en el *centro de eventos*.

    ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Administrador de tráfico de Azure

La aplicación Wingtip usa [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) para controlar la distribución de solicitudes entrantes. La dirección URL para acceder al centro de eventos de un inquilino debe tener el formato siguiente:

- http://events.wingtip-dpt.&lt;USUARIO&gt;.trafficmanager.net/fabrikamjazzclub

En la tabla siguiente se explican los elementos del formato anterior.

| Elemento de URL | Descripción |
| :------- | :---------- |
| http://events.wingtip-dpt | Elementos de eventos de la aplicación Wingtip.<br /><br />la parte ***-dpt*** distingue la implementación de *base de datos por inquilino* de Wingtip de otras implementaciones ligeramente distintas. Por ejemplo, otros artículos de documentación ofrecen Wingtip para elementos *independientes* (*-sa*) o para *BD de varios inquilinos*. |
| .*&lt;USUARIO&gt;* | *af1* en nuestro ejemplo. |
| .trafficmanager.NET/ | Azure Traffic Manager, URL base. |
| fabrikamjazzclub | Para el inquilino denominado *Fabrikam Jazz Club*. |
| &nbsp; | &nbsp; |

1. La aplicación de eventos analiza el nombre del inquilino a partir de la dirección URL.
2. El nombre del inquilino se usa para crear una clave.
3. La clave se usa para acceder al catálogo y obtener la ubicación de la base de datos del inquilino.
    - El catálogo se implementa mediante la *administración de asignaciones de particiones*.
4. El *centro de eventos* usa metadatos extendidos en el catálogo para obtener una lista de direcciones URL de eventos.

En un entorno de producción, lo habitual sería crear un registro de DNS CNAME que [*apunte a un dominio de Internet de empresa*](../traffic-manager/traffic-manager-point-internet-domain.md) para el perfil de Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Empiece a generar carga en las bases de datos de los inquilinos

Ahora que la aplicación se ha implementado, vamos a ponerla en marcha.
El script de PowerShell *Demo-LoadGenerator* inicia una carga de trabajo que se ejecuta en las bases de datos de todos los inquilinos.
La carga real en muchas aplicaciones de SaaS es esporádica e imprevisible.
Para simular este tipo de carga, el generador produce una carga con picos o ráfagas de actividad en cada inquilino.
Las ráfagas tienen lugar a intervalos aleatorios.
El modelo de carga tarda varios minutos en emerger. De modo que es mejor permitir que el generador se ejecute durante al menos tres o cuatro minutos antes de supervisar la carga.

1. En *PowerShell ISE*, abra el script ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Presione **F5** para ejecutar el script e iniciar el generador de carga. (Deje los valores predeterminados de parámetro por ahora.)

No vuelva a usar la misma instancia de PowerShell ISE para nada más, excepto, quizás, para ejecutar de nuevo *Demo-LoadGenerator.ps1*. Si tiene que ejecutar otros scripts de PowerShell, inicie otra instancia de PowerShell ISE.

#### <a name="rerun-with-different-parameters"></a>Repetir la ejecución con otros parámetros

Para volver a ejecutar la prueba de carga con otros parámetros, siga estos pasos:

1. Detenga *LoadGenerator.ps1*.
    - Use **Ctrl + C** o haga clic en el botón **Detener**.
    - Esta detención no detiene ni afecta a los trabajos en segundo plano incompletos que siguen en ejecución.

2. Vuelva a ejecutar *Demo-LoadGenerator.ps1*.
    - Esta nueva ejecución detiene, en primer lugar, cualquier trabajo en segundo plano que pueda seguir ejecutando *sp_CpuLoadGenerator*.

También puede finalizar la instancia de PowerShell ISE, lo que detiene cualquier trabajo en segundo plano. A continuación, inicie una nueva instancia de PowerShell ISE y vuelva a ejecutar *Demo-LoadGenerator.ps1*.

#### <a name="monitor-the-background-jobs"></a>Supervisar los trabajos en segundo plano

Si quiere controlar y supervisar los trabajos en segundo plano, puede usar los cmdlets siguientes:

- Get-Job
- Receive-Job
- Stop-Job

#### <a name="demo-loadgeneratorps1-actions"></a>Acciones de Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* imita una carga de trabajo activa de transacciones de cliente. Los pasos siguientes describen la secuencia de acciones que inicia *Demo-LoadGenerator.ps1*:

1. *Demo-LoadGenerator.ps1* inicia *LoadGenerator.ps1* en primer plano.
    - Ambos archivos. ps1 se almacenan en las carpetas *Learning Modules\\Utilities\\*.

2. *LoadGenerator.ps1* recorre en bucle todas las bases de datos de inquilino registradas en el catálogo.

3. Para cada base de datos de inquilino, *LoadGenerator.ps1* inicia una ejecución del procedimiento almacenado Transact-SQL denominado *sp_CpuLoadGenerator*.
    - Las ejecuciones se inician en segundo plano mediante una llamada al cmdlet *Invoke-SqlAzureWithRetry*.
    - *sp_CpuLoadGenerator* emite una instrucción SELECT de SQL en bucle durante un tiempo predeterminado de 60 segundos. El intervalo de tiempo entre las emisiones de SELECT varía en función de los valores de los parámetros.
    - Este archivo .sql está almacenado en *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Para cada base de datos de inquilino, *LoadGenerator.ps1* también inicia el cmdlet *Start-Job*.
    - *Start-Job* imita una carga de trabajo de ventas de vales.

5. *LoadGenerator.ps1* continúa ejecutándose y supervisando los inquilinos nuevos que se aprovisionan.

&nbsp;

Antes de continuar con la siguiente sección, deje el generador de carga en ejecución en el estado de invocación de trabajo.

## <a name="provision-a-new-tenant"></a>Aprovisionamiento de un nuevo inquilino

La implementación inicial crea tres inquilinos de ejemplo. Ahora creará otro inquilino para ver cómo afecta esto a la aplicación implementada. En la aplicación Wingtip, se explica el flujo de trabajo para aprovisionar nuevos inquilinos en el [tutorial de catalogación y aprovisionamiento](saas-dbpertenant-provision-and-catalog.md). En esta fase, se crea a un nuevo inquilino, lo que lleva menos de un minuto.

1. En *PowerShell ISE*, abra ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
2. Presione **F5** para ejecutar el script. (Deje los valores predeterminados por ahora.)

   > [!NOTE]
   > Muchos scripts SaaS de Wingtip usan *$PSScriptRoot* para permitir navegar por las carpetas para llamar a funciones en otros scripts. Esta variable solo se evalúa cuando se ejecuta el script completo presionando **F5**.  Resaltar y ejecutar una selección con **F8** puede provocar errores. Para ejecutar los scripts, presione **F5**.

La base de datos de inquilino nueva se:

- Crea en un grupo elástico de SQL.
- Inicializa.
- Registra en el catálogo.

Una vez completado el aprovisionamiento correctamente, el sitio *Events* del nuevo inquilino aparece en el explorador:

![Nuevo inquilino](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Actualice el *centro de eventos* para que el nuevo inquilino aparezca en la lista.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explore los servidores, los grupos y las bases de datos de los inquilinos

Ahora que ha empezado a ejecutar una carga en la colección de inquilinos, veamos algunos de los recursos que se han implementado:

1. En [Azure Portal](http://portal.azure.com), vaya a la lista de servidores SQL Server y abra el servidor **catalog-dpt-&lt;USUARIO&gt;**.
    - El servidor de catálogos contiene dos bases de datos, **tenantcatalog** y **basetenantdb** (una base de datos de plantilla vacía que se copia para crear nuevos inquilinos).

   ![bases de datos](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Vuelva a la lista de servidores SQL Server.

3. Abra el servidor **tenants1-dpt-&lt;USUARIO&gt;** que contiene las bases de datos de los inquilinos.

4. Observe los siguientes elementos:
    - Todas las bases de datos de inquilinos son *elásticas estándar* en un conjunto estándar de 50 eDTU.
    - La base de datos *Red Maple Racing*, que es la base de datos de inquilino que aprovisionó anteriormente.

   ![Servidor](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Supervisión del grupo

Después de ejecutar *LoadGenerator.ps1* durante varios minutos, debería haber suficientes datos disponibles para empezar a observar ciertas funciones de supervisión. Estas funciones se integran en grupos y bases de datos.

Vaya al servidor **tenants1 -&lt;USUARIO&gt;** y haga clic en **Pool1** para ver el uso de recursos del grupo. En los siguientes gráficos, el generador de carga se ejecutó durante una hora.

   ![supervisar grupo](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- El primer gráfico, con la etiqueta **Uso de recursos** muestra el uso de eDTU del grupo.
- El segundo gráfico muestra el uso de eDTU de las cinco bases de datos principales del grupo.

Estos dos gráficos ilustran que los grupos elásticos y SQL Database funcionan bien para las cargas de trabajo de una aplicación de SaaS.
Los gráficos muestran 4 bases de datos que emiten ráfagas de hasta 40 eDTU por base de datos y, aún así, un grupo de 50 eDTU las admite cómodamente. El grupo de 50 eDTU puede admitir cargas de trabajo incluso superiores.
Si se aprovisionaron como bases de datos independientes, cada una debe ser de tipo S2 (50 DTU) para admitir las ráfagas.
El costo de 4 bases de datos S2 independiente S2 sería casi 3 veces superior al del grupo.
En situaciones reales, los clientes de SQL Database están ejecutando hasta 500 bases de datos en grupos de 200 eDTU.
Para más información consulte el [tutorial de supervisión del rendimiento](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionales

- [Tutoriales adicionales que se basan en el patrón de base de datos por inquilino de la aplicación SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Para obtener información acerca de los grupos elásticos, consulte [*¿Qué son los grupos elásticos de Azure SQL?*](sql-database-elastic-pool.md)
- Para obtener información acerca de los trabajos elásticos, consulte [*Administración de bases de datos escaladas horizontalmente en la nube*](sql-database-elastic-jobs-overview.md)
- Para obtener información acerca de aplicaciones SaaS multiempresa, consulte [*Modelos de diseño para las aplicaciones SaaS multiempresa y Azure SQL Database*](saas-tenancy-app-design-patterns.md)


## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha obtenido información:

> [!div class="checklist"]
> - Implementación de la aplicación SaaS Wingtip Tickets
> - Acerca de los servidores, los grupos y las bases de datos que componen la aplicación
> - Acerca de los inquilinos que se asignan a sus datos con el *catálogo*
> - Acerca de cómo aprovisionar nuevos inquilinos
> - Acerca de cómo ver el uso de grupos para supervisar la actividad de los inquilinos
> - Acerca de cómo eliminar los recursos de ejemplo para detener la facturación relacionada con ellos

A continuación, pruebe el [tutorial de aprovisionamiento y catalogación](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

