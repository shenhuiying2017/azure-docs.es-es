---
title: "Aprovisionamiento de nuevos inquilinos en una aplicación multiinquilino que usa Azure SQL Database | Microsoft Docs"
description: "Obtenga información sobre cómo aprovisionar y catalogar nuevos inquilinos en una aplicación SaaS de multiinquilino de Azure SQL Database"
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
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: b82623f63681daff502f1e23d052da7480dda942
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Aprenda a aprovisionar y registrar nuevos inquilinos en el catálogo

En este tutorial aprenderá a aprovisionar y catalogar patrones SaaS, y cómo se implementan en la aplicación Wingtip Tickets SaaS Database per Tenant. Creará e inicializará nuevas bases de datos de inquilinos y las registrará en el catálogo de inquilinos de la aplicación. El catálogo es una base de datos que mantiene la asignación entre los diversos inquilinos de aplicaciones SaaS y sus datos. El catálogo desempeña un papel importante a la hora de dirigir las solicitudes de aplicación y administración a la base de datos correcta.  

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Aprovisionar un nuevo inquilino único, incluidos todos los pasos de su implementación
> * Aprovisionar un lote de inquilinos adicionales


Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Se ha implementado la aplicación Wingtip Tickets SaaS Database per Tenant. Para implementarla en menos de cinco minutos, consulte el artículo de [implementación y exploración de la aplicación Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introducción al patrón de catálogo de SaaS

En una aplicación SaaS multiinquilino con copia de seguridad en base de datos, es importante saber dónde se almacena la información de cada inquilino. En el patrón de catálogo de SaaS, una base de datos de catálogos se usa para mantener la asignación entre cada inquilino y la base de datos en la que se almacenan sus datos. La aplicación Wingtip Tickets SaaS Database per Tenant emplea un modelo de un único inquilino por base de datos, pero el patrón básico de almacenamiento de la asignación de inquilino a base de datos en un catálogo se aplica cada vez que los datos del inquilino se distribuyen entre varias bases de datos, tanto si se usa una base de datos de un único inquilino o multiinquilino.

A cada inquilino se le asigna una clave que les identifica en el catálogo y que, a su vez, se asigna a la ubicación de la base de datos adecuada. En la aplicación de SaaS Wingtip Tickets, la clave se forma a partir de un hash del nombre del inquilino. Esto permite utilizar la parte del nombre del inquilino de la dirección URL de la aplicación para generar la clave. También se podrían utilizar otros esquemas de claves de inquilino.  

El catálogo permite cambiar el nombre o la ubicación de la base de datos con un mínimo impacto en la aplicación.  En un modelo de base de datos multiinquilino, esto también permite mover a un inquilino entre bases de datos.  El catálogo también puede usarse para indicar si un inquilino o base de datos está sin conexión debido a operaciones de mantenimiento u otras acciones. Todo esto se trata en el [tutorial Restauración de un solo inquilino](saas-dbpertenant-restore-single-tenant.md).

Además, el catálogo, que es en efecto una base de datos de administración de una aplicación SaaS, puede almacenar inquilinos adicionales o metadatos de base de datos tales como el nivel o edición de una base de datos, la versión del esquema, el plan de servicios o los contratos de nivel de servicio ofrecidos a los inquilinos, y cualquier otra información que facilite la administración de la aplicación, el soporte al cliente y los procesos DevOps.  

Además de la aplicación de SaaS, el catálogo puede habilitar herramientas de base de datos.  En el ejemplo de Wingtip Tickets SaaS Database per Tenant, el catálogo se usa para habilitar consultas entre inquilinos. Esto se detalla en el [tutorial de análisis ad hoc](saas-tenancy-adhoc-analytics.md). La administración de trabajos entre bases de datos se describe en los tutoriales sobre [administración de esquemas](saas-tenancy-schema-management.md) y [análisis de inquilinos](saas-tenancy-tenant-analytics.md). 

En los ejemplos de SaaS de Wingtip Tickets, el catálogo se implementa mediante las características de administración de particiones de la [biblioteca de cliente de Elastic Database (EDCL)](sql-database-elastic-database-client-library.md). La EDCL está disponible en Java y .NET Framework. La EDCL permite que una aplicación pueda crear, administrar y usar un mapa de particiones con copia de seguridad en base de datos. Un mapa de particiones contiene una lista de particiones (bases de datos) y la asignación entre las claves (inquilinos) y las particiones.  Las funciones de EDCL se pueden usar a partir de aplicaciones o scripts de PowerShell durante el aprovisionamiento del inquilino para crear entradas en el mapa de particiones, y a partir de aplicaciones para conectarse eficazmente a la base de datos correcta. La EDCL almacena en caché la información de conexión para minimizar el tráfico en la base de datos de catálogo y acelerar la aplicación.  

> [!IMPORTANT]
> Los datos de asignación son accesibles en la base de datos de catálogo, pero *no los edite*. Para editar datos de asignación, utilice únicamente las API de la biblioteca de cliente de Elastic Database. No se admite la manipulación directa de los datos de asignación, que entraña el riesgo de dañar el catálogo.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introducción al patrón de aprovisionamiento de SaaS

Durante la incorporación de un nuevo inquilino a una aplicación SaaS que usa el modelo de base de datos de un solo inquilino, se debe aprovisionar una nueva base de datos de inquilino.  Esta se debe crear en la ubicación y nivel de servicio adecuados, inicializar con el esquema y los datos de referencia apropiados y, a continuación, registrar en el catálogo con la clave de inquilino correcta.  

Se pueden usar diferentes enfoques para el aprovisionamiento de la base de datos, entre los cuales se incluyen la ejecución de scripts de SQL, la implementación de un archivo BACPAC o la copia de una base de datos de plantillas.  

El método de aprovisionamiento utilizado debe estar incluido en su estrategia general de administración de esquemas, que debe garantizar que las nuevas bases de datos se aprovisionan con los esquemas más recientes.  Todo ello se describe en el [tutorial de administración de esquemas](saas-tenancy-schema-management.md).  

La aplicación Wingtip Tickets SaaS Database per Tenant aprovisiona nuevos inquilinos mediante la copia de una base de datos de plantillas llamada _basetenantdb_, que está implementada en el servidor de catálogo.  El aprovisionamiento se puede integrar en la aplicación como parte de la experiencia de registro y se puede admitir sin conexión mediante scripts. En este tutorial se describe el aprovisionamiento con PowerShell. Los scripts de aprovisionamiento copian la base de datos basetenantdb para crear una nueva base de datos de inquilinos en un grupo elástico y, a continuación, la inicializan con información específica del inquilino y la registra en el mapa de particiones del catálogo.  En la aplicación Wingtip Tickets SaaS Database per Tenant, se asignan nombres a las bases de datos de inquilinos según el nombre del inquilino, si bien esta no es una parte crítica del patrón. El uso del catálogo permite que se asigne cualquier nombre a las bases de datos de inquilinos. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip Tickets SaaS Database Per Tenant

Los scripts y el código fuente de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts SaaS de Wingtip Tickets.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Tutorial detallado de aprovisionamiento y catalogación

Para entender cómo la aplicación Wingtip implementa el aprovisionamiento de un nuevo inquilino, agregue un punto de interrupción y recorra el flujo de trabajo mientras aprovisiona un inquilino:

1. En _PowerShell ISE_, abra ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ y defina los siguientes parámetros:
   * **$TenantName** = el nombre de la nueva ubicación (por ejemplo, *Bushwillow Blues*).
   * **$VenueType** = uno de los tipos predefinidos de ubicación: *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic o soccer.
   * **$DemoScenario** = **1**, para *aprovisionar un único inquilino*.

1. Agregue un punto de interrupción colocando el cursor en cualquier lugar de la línea 48, que dice: *New-Tenant `* y presione **F9**.

   ![punto de interrupción](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. Presione **F5** para ejecutar el script.

1. Después de que la ejecución del script se detiene el punto de interrupción, presione **F11** para depurar paso a paso el código por instrucciones.

   ![depuración](media/saas-dbpertenant-provision-and-catalog/debug.png)



Siga paso a paso la ejecución del script mediante las opciones del menú **Depurar**, **F10** y **F11**, para omitir las funciones llamadas o acceder a ellas. Para obtener más información sobre cómo depurar scripts de PowerShell, consulte [Sugerencias para trabajar con scripts de PowerShell y depurarlos](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Lo que se indica a continuación no son pasos que se deban seguir de forma explícita, sino una explicación del flujo de trabajo que se debe seguir al depurar el script:

1. **Importe el módulo SubscriptionManagement.psm1** que contiene funciones para iniciar sesión en Azure y seleccionar la suscripción de Azure con la que está trabajando.
1. **Importe el módulo CatalogAndDatabaseManagement.psm1** que proporciona un catálogo y una abstracción de nivel de inquilino sobre las funciones de [administración de particiones](sql-database-elastic-scale-shard-map-management.md). Este es un módulo importante que encapsula gran parte del patrón de catálogo y que merece la pena explorar.
1. **Obtenga los detalles de configuración**. Vaya a Get-Configuration (con F11) y vea cómo se especifica la configuración de la aplicación. Los nombres de recursos y otros valores específicos de la aplicación se definen aquí, pero no cambie ninguno de estos valores hasta que esté familiarizado con los scripts.
1. **Obtenga el objeto de catálogo**. Vaya a Get-Catalog que crea y devuelve un objeto de catálogo que se usa en el script de nivel superior.  Esta función utiliza las funciones de administración de particiones que se importan desde **AzureShardManagement.psm1**. El objeto de catálogo se compone de lo siguiente:
   * $catalogServerFullyQualifiedName se construye con la raíz estándar junto con su nombre de usuario: _catalog-\<usuario\>.database.windows.net_.
   * $catalogDatabaseName se obtiene de la configuración: *tenantcatalog*.
   * El objeto $shardMapManager se inicializa desde la base de datos del catálogo.
   * El objeto $shardMap se inicializa desde el mapa de particiones *tenantcatalog* de la base de datos del catálogo.
   Se generará y devolverá un objeto de catálogo, que se usará en el script de nivel superior.
1. **Calcule la nueva clave de inquilino**. Para crear la clave de inquilino a partir del nombre del inquilino se utiliza una función hash.
1. **Compruebe si ya existe la clave de inquilino**. Se comprobará en el catálogo que la clave está disponible.
1. **La base de datos de inquilino se aprovisiona con New-TenantDatabase**. Use **F11** para acceder y ver cómo la base de datos se aprovisiona mediante una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    El nombre de la base de datos se construye a partir del nombre del inquilino para dejar claro qué partición pertenece a cada inquilino (también podrían emplearse fácilmente otras estrategias para asignar un nombre a la base de datos). Se usa una plantilla de Resource Manager para crear una base de datos de inquilinos mediante la copia de una base de datos de plantillas (basetenantdb) en el servidor de catálogo. Un método alternativo podría consistir en crear una base de datos vacía y, a continuación, inicializarla mediante la importación de un archivo BACPAC o la ejecución de un script de inicialización de una ubicación conocida.  

    La plantilla de Resource Manager se encuentra en la carpeta ...\Learning Modules\Common: *tenantdatabasecopytemplate.json*.

1. Una vez creada la base de datos de inquilino, se vuelve a **inicializar con el nombre de la ubicación (inquilino) y el tipo de ubicación**. Aquí también podría realizarse otro tipo de inicialización.

1. La **base de datos de inquilino se registra en el catálogo** con *Add-TenantDatabaseToCatalog* mediante la clave de inquilino. Use **F11** para acceder a los detalles:

    * La base de datos de catálogo se agrega a la asignación de particiones (la lista de bases de datos conocidas).
    * Se crea la asignación que vincula el valor de clave a la partición.
    * Se agregan metadatos adicionales sobre el inquilino (nombre de la ubicación) a la tabla Tenants del catálogo.  Esta tabla no forma parte del esquema de ShardManagement y la EDCL no la instala.  Esta tabla muestra cómo la base de datos de catálogo puede ampliarse para admitir los datos adicionales específicos de la aplicación.   


Después de que se complete el aprovisionamiento, la ejecución vuelve al script *Demo-ProvisionAndCatalog* original que abre la página de **eventos** para el nuevo inquilino en el explorador:

   ![events](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionamiento de un lote de inquilinos

Este ejercicio aprovisiona un lote de 17 inquilinos. Se recomienda que aprovisione este lote de inquilinos antes de completar otros tutoriales de Wingtip Tickets SaaS Database per Tenant, de manera que haya un mayor número de bases de datos con las que trabajar.

1. En *PowerShell ISE*, abra ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* y cambie el parámetro *$DemoScenario* a 3:
   * **$DemoScenario** = **3**, para *aprovisionar un lote de inquilinos*.
1. Presione **F5** para ejecutar el script.

El script implementa un lote de inquilinos adicionales Este script usa una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla el lote y, a continuación, delega el aprovisionamiento de cada base de datos a una plantilla vinculada. Esto permite que Azure Resource Manager negocie el proceso de aprovisionamiento para su script. Las plantillas aprovisionan bases de datos en paralelo cuando sea posible y controlan los reintentos si es necesario, optimizando así el proceso general. El script es idempotente, por lo que si se produce un error o se detiene por alguna razón, vuelva a ejecutarlo.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Compruebe que el lote de inquilinos se haya implementado correctamente.

* En [Azure Portal](https://portal.azure.com), vaya a la lista de servidores y abra el servidor *tenants1*. Haga clic en **Bases de datos SQL** y compruebe que el lote de 17 bases de datos adicionales aparece ahora en la lista:

   ![lista de base de datos](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Otros patrones de aprovisionamiento

Entre los patrones de aprovisionamiento que no se describen en este tutorial se incluyen los siguientes:

**Aprovisionamiento previo de bases de datos.** El patrón previamente aprovisionamiento aprovecha del hecho de que las bases de datos en un grupo elástico no suponen costes adicionales. La facturación es para el grupo elástico, no para las bases de datos, y las bases de datos inactivas no consumen ningún recurso. Al aprovisionar previamente bases de datos en un grupo y asignarlas posteriormente cuando sea necesario, puede reducirse considerablemente el tiempo de incorporación del inquilino. El número de bases de datos previamente aprovisionadas puede ajustarse según sea necesario para mantener un búfer adecuado para la velocidad de aprovisionamiento prevista.

**Aprovisionamiento automático** En el patrón de aprovisionamiento automático, un servicio de aprovisionamiento aprovisiona servidores, grupos y bases de datos automáticamente según sea necesario (incluido el aprovisionamiento previo de bases de datos en grupos elásticos si se desea). Además, si se retiran y eliminan bases de datos, los huecos que dejan en los grupos elásticos pueden ocuparse aprovisionando el servicio. Este servicio puede ser simple o complejo; por ejemplo, puede controlarse el aprovisionamiento en varias regiones geográficas y configurarse automáticamente la replicación geográfica para la recuperación ante desastres. Con el patrón de aprovisionamiento automático, un script o aplicación cliente envía una solicitud de aprovisionamiento a una cola para que el servicio de aprovisionamiento la procese y, a continuación, sonde el servicio para determinar si se ha completado el procesamiento. Si se utiliza el aprovisionamiento previo, se podrían administrar las solicitudes rápidamente y el servicio aprovisionar una base de datos de reemplazo en segundo plano.



## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]

> * Aprovisionar un nuevo inquilino único
> * Aprovisionar un lote de inquilinos adicionales
> * Acceder a los detalles de cómo aprovisionar inquilinos y registrarlos en el catálogo.

Pruebe el [Tutorial de supervisión del rendimiento](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionales

* Otros [tutoriales basados en la implementación de la aplicación Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de cliente de base de datos elástica](sql-database-elastic-database-client-library.md)
* [Cómo depurar scripts en ISE de Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
