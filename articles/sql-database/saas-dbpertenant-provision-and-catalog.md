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
ms.openlocfilehash: 79b3743054f73914c6755a3c9b102b613b1944f2
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Aprenda a aprovisionar y registrar nuevos inquilinos en el catálogo

En este tutorial aprenderá a aprovisionar y catalogar patrones SaaS, y cómo se implementan en la aplicación Wingtip Tickets SaaS Database per Tenant. Creará e inicializará nuevas bases de datos de inquilinos y las registrará en el catálogo de inquilinos de la aplicación. El catálogo es una base de datos que mantiene la asignación entre los diversos inquilinos de aplicaciones SaaS y sus datos. El catálogo desempeña un papel importante a la hora de dirigir las solicitudes de aplicación y administración a la base de datos correcta.  

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Aprovisionar un nuevo inquilino único
> * Aprovisionar un lote de inquilinos adicionales


Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* Aplicación Wingtip Tickets SaaS Database Per Tenant implementada. Para implementarla en menos de cinco minutos, consulte el artículo de [implementación y exploración de la aplicación Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introducción al patrón de catálogo de SaaS

En una aplicación SaaS multiinquilino con copia de seguridad en base de datos, es importante saber dónde se almacena la información de cada inquilino. En el patrón de catálogo de SaaS, una base de datos de catálogos se usa para mantener la asignación entre cada inquilino y la base de datos en la que se almacenan sus datos. Este patrón se aplica cada vez que los datos de inquilino se distribuyen entre varias bases de datos.

Cada inquilino se identifica como una clave en el catálogo que se asigna a la ubicación de su base de datos. En la aplicación de Wingtip Tickets, la clave se forma a partir de un hash del nombre del inquilino. Esto permite que la aplicación construya la clave a partir del nombre de inquilino que se incluye en la dirección URL de la aplicación. También se podrían utilizar otros esquemas de claves de inquilino.  

El catálogo permite cambiar el nombre o la ubicación de la base de datos con un mínimo impacto en la aplicación.  En un modelo de base de datos multiinquilino, esto también permite mover a un inquilino entre bases de datos.  El catálogo también puede usarse para indicar si un inquilino o base de datos está sin conexión debido a operaciones de mantenimiento u otras acciones. Todo esto se trata en el [tutorial Restauración de un solo inquilino](saas-dbpertenant-restore-single-tenant.md).

El catálogo también puede almacenar metadatos adicionales de inquilino o base de datos, como la versión del esquema, el plan de servicio o los SLA que se ofrecen a los inquilinos, además de otro tipo de información que permite la administración de aplicaciones, la asistencia al cliente o DevOps.  

Además de la aplicación de SaaS, el catálogo puede habilitar herramientas de base de datos.  En el ejemplo de Wingtip Tickets SaaS Database Per Tenant, el catálogo se usa para habilitar consultas entre inquilinos. Esto se detalla en el [tutorial de notificaciones ad hoc](saas-tenancy-cross-tenant-reporting.md). La administración de trabajos entre bases de datos se describe en los tutoriales sobre [administración de esquemas](saas-tenancy-schema-management.md) y [análisis de inquilinos](saas-tenancy-tenant-analytics.md). 

En los ejemplos de SaaS de Wingtip Tickets, el catálogo se implementa mediante las características de administración de particiones de la [biblioteca de cliente de Elastic Database (EDCL)](sql-database-elastic-database-client-library.md). La EDCL está disponible en Java y .NET Framework. La EDCL permite que una aplicación pueda crear, administrar y usar un mapa de particiones con copia de seguridad en base de datos. Un mapa de particiones contiene una lista de particiones (bases de datos) y la asignación entre las claves (inquilinos) y las particiones.  Las funciones de EDCL se usan durante el aprovisionamiento del inquilino para crear las entradas en el mapa de particiones y, luego, en el entorno de tiempo de ejecución mediante las aplicaciones para conectarse a la base de datos correcta. La EDCL almacena en caché la información de conexión para minimizar el tráfico en la base de datos de catálogo y acelerar la aplicación.  

> [!IMPORTANT]
> Los datos de asignación son accesibles en la base de datos de catálogo, pero *no los edite*. Para editar datos de asignación, utilice únicamente las API de la biblioteca de cliente de Elastic Database. No se admite la manipulación directa de los datos de asignación, que entraña el riesgo de dañar el catálogo.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introducción al patrón de aprovisionamiento de SaaS

Durante la incorporación de un nuevo inquilino a una aplicación SaaS que usa el modelo de base de datos de un solo inquilino, se debe aprovisionar una nueva base de datos de inquilino.  La base de datos se debe crear en la ubicación y nivel de servicio adecuados, inicializar con el esquema y los datos de referencia apropiados y registrar en el catálogo con la clave de inquilino correcta.  

Se pueden usar diferentes enfoques para el aprovisionamiento de la base de datos, entre los cuales se incluyen la ejecución de scripts de SQL, la implementación de un archivo BACPAC o la copia de una base de datos de plantillas.  

El aprovisionamiento de la base de datos debe formar parte de la estrategia de administración de esquemas, que necesita garantizar que las bases de datos nuevas se aprovisionan con el esquema más reciente. Este requisito se explora en el [tutorial de administración de esquemas](saas-tenancy-schema-management.md).  

La aplicación Wingtip Tickets de base de datos por inquilino aprovisiona nuevos inquilinos mediante la copia de una base de datos de plantilla denominada _basetenantdb_, que está implementada en el servidor de catálogo.  El aprovisionamiento se puede integrar en la aplicación como parte de la experiencia de registro y se puede admitir sin conexión mediante scripts. En este tutorial se describe el aprovisionamiento con PowerShell. Los scripts de aprovisionamiento copian la base de datos _basetenantdb_ para crear una base de datos de inquilino nueva en un grupo elástico y, a continuación, la inicializan con información específica del inquilino y la registra en el mapa de particiones del catálogo.  Las bases de datos de inquilino reciben nombres basados en el nombre de inquilino, pero este esquema de nomenclatura no es una parte fundamental del patrón porque, debido a que el catálogo asigna la clave de inquilino al nombre de la base de datos, se puede usar cualquier convención de nomenclatura. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip Tickets SaaS Database Per Tenant

Los scripts de SaaS de Wingtip Tickets y el código fuente de la aplicación están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts SaaS de Wingtip Tickets.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Tutorial detallado de aprovisionamiento y catalogación

Para entender cómo la aplicación Wingtip Tickets implementa el aprovisionamiento de un nuevo inquilino, agregue un punto de interrupción y recorra el flujo de trabajo mientras aprovisiona un inquilino:

1. En _PowerShell ISE_, abra ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ y defina los siguientes parámetros:
   * **$TenantName** = el nombre de la nueva ubicación (por ejemplo, *Bushwillow Blues*).
   * **$VenueType** = uno de los tipos predefinidos de ubicación: _blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer_.
   * **$DemoScenario** = **1**, para *aprovisionar un único inquilino*.

1. Agregue un punto de interrupción colocando el cursor en cualquier lugar de la línea, que dice: *New-Tenant `* y presione **F9**.

   ![punto de interrupción](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. Presione **F5** para ejecutar el script.

1. Después de que la ejecución del script se detiene el punto de interrupción, presione **F11** para depurar paso a paso el código por instrucciones.

   ![depuración](media/saas-dbpertenant-provision-and-catalog/debug.png)



Siga paso a paso la ejecución del script mediante las opciones del menú **Depurar**, **F10** y **F11**, para omitir las funciones llamadas o acceder a ellas. Para obtener más información sobre cómo depurar scripts de PowerShell, consulte [Sugerencias para trabajar con scripts de PowerShell y depurarlos](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Lo que se indica a continuación no son pasos que se deban seguir de forma explícita, sino una explicación del flujo de trabajo que se debe seguir al depurar el script:

1. **Importe el módulo SubscriptionManagement.psm1** que contiene funciones para iniciar sesión en Azure y seleccionar la suscripción de Azure con la que está trabajando.
1. **Importe el módulo CatalogAndDatabaseManagement.psm1** que proporciona un catálogo y una abstracción de nivel de inquilino sobre las funciones de [administración de particiones](sql-database-elastic-scale-shard-map-management.md). En este módulo se encapsula gran parte del patrón del catálogo y merece la pena explorarlo.
1. **Obtenga los detalles de configuración**. Vaya a Get-Configuration (con F11) y vea cómo se especifica la configuración de la aplicación. Los nombres de recursos y otros valores específicos de la aplicación se definen aquí, pero no cambie estos valores hasta que esté familiarizado con los scripts.
1. **Obtenga el objeto de catálogo**. Vaya a Get-Catalog que crea y devuelve un objeto de catálogo que se usa en el script de nivel superior.  Esta función utiliza las funciones de administración de particiones que se importan desde **AzureShardManagement.psm1**. El objeto de catálogo se compone de los elementos siguientes:
   * $catalogServerFullyQualifiedName se construye con la raíz estándar junto con su nombre de usuario: _catalog-\<usuario\>.database.windows.net_.
   * $catalogDatabaseName se obtiene de la configuración: *tenantcatalog*.
   * El objeto $shardMapManager se inicializa desde la base de datos del catálogo.
   * El objeto $shardMap se inicializa desde el mapa de particiones _tenantcatalog_ de la base de datos del catálogo.
   Se generará y devolverá un objeto de catálogo, que se usará en el script de nivel superior.
1. **Calcule la nueva clave de inquilino**. Para crear la clave de inquilino a partir del nombre del inquilino se utiliza una función hash.
1. **Compruebe si ya existe la clave de inquilino**. Se comprobará en el catálogo que la clave está disponible.
1. **La base de datos de inquilino se aprovisiona con New-TenantDatabase**. Use **F11** para acceder y ver cómo la base de datos se aprovisiona mediante una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    El nombre de la base de datos se construye a partir del nombre del inquilino para dejar claro qué partición pertenece a cada inquilino, a pesar de que se pueden usar otras convenciones de nomenclatura para la base de datos.
    Una plantilla de Resource Manager crea una base de datos de inquilino mediante la copia de una base de datos de plantilla (_basetenantdb_) en el servidor de catálogo. Como alternativa, podría crear una base de datos e inicializarla mediante la importación de un archivo BACPAC o ejecutar un script de inicialización desde una ubicación conocida.

    La plantilla de Resource Manager se encuentra en la carpeta ...\Learning Modules\Common: *tenantdatabasecopytemplate.json*.

1. Una vez creada la base de datos de inquilino, se vuelve a **inicializar con el nombre de la ubicación (inquilino) y el tipo de ubicación**. Aquí también podría realizarse otro tipo de inicialización.

1. La **base de datos de inquilino se registra en el catálogo** con *Add-TenantDatabaseToCatalog* mediante la clave de inquilino. Use **F11** para acceder a los detalles:

    * La base de datos de catálogo se agrega a la asignación de particiones (la lista de bases de datos conocidas).
    * Se crea la asignación que vincula el valor de clave a la partición.
    * Se agregan metadatos adicionales sobre el inquilino (nombre de la ubicación) a la tabla *Tenants* del catálogo.  Esta tabla no forma parte del esquema de ShardManagement y la EDCL no la instala.  Esta tabla muestra cómo la base de datos de catálogo puede ampliarse para admitir los datos adicionales específicos de la aplicación.   


Después de que se complete el aprovisionamiento, la ejecución vuelve al script *Demo-ProvisionAndCatalog* original que abre la página de **eventos** para el nuevo inquilino en el explorador:

   ![events](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionamiento de un lote de inquilinos

Este ejercicio aprovisiona un lote de 17 inquilinos. Se recomienda que aprovisione este lote de inquilinos antes de completar otros tutoriales de Wingtip Tickets SaaS Database per Tenant, de manera que haya un mayor número de bases de datos con las que trabajar.

1. En *PowerShell ISE*, abra ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* y cambie el parámetro *$DemoScenario* a 3:
   * **$DemoScenario** = **3**, para *aprovisionar un lote de inquilinos*.
1. Presione **F5** para ejecutar el script.

El script implementa un lote de inquilinos adicionales Este script usa una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla el lote y que delega el aprovisionamiento de cada base de datos a una plantilla vinculada. Esto permite que Azure Resource Manager negocie el proceso de aprovisionamiento para su script. Las plantillas aprovisionan las bases de datos en paralelo y, en caso de ser necesario, controlan los reintentos. El script es idempotente, por lo que si se produce un error o se detiene por alguna razón, vuelva a ejecutarlo.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Compruebe que el lote de inquilinos se haya implementado correctamente.

* En [Azure Portal](https://portal.azure.com), vaya a la lista de servidores y abra el servidor *tenants1*. Haga clic en **Bases de datos SQL** y compruebe que el lote de 17 bases de datos adicionales aparece ahora en la lista:

   ![lista de base de datos](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Otros patrones de aprovisionamiento

Entre los patrones de aprovisionamiento que no se describen en este tutorial se incluyen los siguientes:

**Aprovisionamiento previo de bases de datos.** El patrón previamente aprovisionamiento aprovecha del hecho de que las bases de datos en un grupo elástico no suponen costes adicionales. La facturación es para el grupo elástico, no para las bases de datos, y las bases de datos inactivas no consumen ningún recurso. Al aprovisionar previamente bases de datos en un grupo y asignarlas posteriormente cuando sea necesario, puede reducirse considerablemente el tiempo de incorporación del inquilino. El número de bases de datos previamente aprovisionadas puede ajustarse según sea necesario para mantener un búfer adecuado para la velocidad de aprovisionamiento prevista.

**Aprovisionamiento automático** En el patrón de aprovisionamiento automático, un servicio de aprovisionamiento aprovisiona servidores, grupos y bases de datos automáticamente según sea necesario (incluido el aprovisionamiento previo de bases de datos en grupos elásticos si se desea). Además, si se retiran y eliminan bases de datos, los huecos que dejan en los grupos elásticos pueden ocuparse aprovisionando el servicio. Este servicio puede ser simple o complejo; por ejemplo, puede controlarse el aprovisionamiento en varias regiones geográficas y configurarse automáticamente la replicación geográfica para la recuperación ante desastres. Con el patrón de aprovisionamiento automático, un script o aplicación cliente envía una solicitud de aprovisionamiento a una cola para que el servicio de aprovisionamiento la procese y, a continuación, sonde el servicio para determinar si se ha completado el procesamiento. Si se utiliza el aprovisionamiento previo, se podrían administrar las solicitudes rápidamente y el servicio aprovisionar una base de datos de reemplazo en segundo plano.


## <a name="next-steps"></a>pasos siguientes

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
