---
title: "Aprovisionamiento de nuevos inquilinos en una aplicación multiinquilino que usa Azure SQL Database | Microsoft Docs"
description: "Aprenda cómo realizar el aprovisionamiento y catalogación de nuevos inquilinos en la aplicación SaaS de Wingtip"
keywords: tutorial de base de datos sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: f6beb62246aaf59bfd81467f07d347913a20677b
ms.contentlocale: es-es
ms.lasthandoff: 06/14/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Aprovisionamiento y registro de nuevos inquilinos en el catálogo

En este tutorial, se aprovisionan nuevos inquilinos en la aplicación SaaS de Wingtip. Creará inquilinos mediante el aprovisionamiento de bases de datos de inquilinos, y los registrará en el catálogo. El *catálogo* es una base de datos que mantiene la asignación entre los inquilinos de una aplicación SaaS y sus datos.

Utilice estos scripts para explorar los patrones de aprovisionamiento y catálogo empleados y cómo se implementa el registro de nuevos inquilinos en el catálogo. El catálogo desempeña un papel importante para dirigir las solicitudes de aplicación a las bases de datos correctas.

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Aprovisionar un nuevo inquilino único
> * Aprovisionar un lote de inquilinos adicionales
> * Acceder a los detalles de cómo aprovisionar inquilinos y registrarlos en el catálogo.


Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Se implementa la aplicación SaaS de Wingtip. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [implementación y exploración de la aplicación SaaS de Wingtip](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introducción al patrón de catálogo de SaaS

En una aplicación SaaS multiinquilino con copia de seguridad en base de datos, es importante saber dónde se almacena la información de cada inquilino. En el patrón de catálogo de SaaS, una base de datos de catálogo se usa para mantener la asignación entre los inquilinos y la ubicación de almacenamiento de sus datos. La aplicación SaaS de Wingtip utiliza una arquitectura inquilino único por base de datos, pero el patrón básico de almacenamiento de la asignación de inquilino a base de datos en un catálogo es aplicable tanto si se usa una base de datos de inquilino único como multiinquilino.

A cada inquilino se le asigna una clave que distingue sus datos en el catálogo. En la aplicación SaaS de Wingtip, la clave se forma a partir de un hash del nombre del inquilino. Este patrón permite utilizar la parte del nombre del inquilino de la dirección URL de la aplicación para generar la clave y recuperar una conexión del inquilino específica. Podrían utilizarse otros esquemas de identificación sin afectar al patrón general.

El catálogo de la aplicación se implementa mediante la tecnología de administración de particiones en la [biblioteca de cliente de Elastic Database (EDCL)](sql-database-elastic-database-client-library.md). EDCL es responsable de crear y administrar un *catálogo* con copia de seguridad en base de datos donde se mantiene un *mapa de particiones*. El catálogo contiene la asignación entre las claves (inquilinos) y sus particiones (bases de datos).

> [!IMPORTANT]
> Los datos de asignación son accesibles en la base de datos de catálogo, pero *no los edite*. Para editar datos de asignación, utilice únicamente las API de la biblioteca de cliente de Elastic Database. No se admite la manipulación directa de los datos de asignación, que entraña el riesgo de dañar el catálogo.



## <a name="get-the-wingtip-application-scripts"></a>Obtener scripts de la aplicación Wingtip

Los scripts SaaS de Wingtip y el código fuente de la aplicación están disponibles en el repositorio de GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Pasos para descargar los scripts SaaS de Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="provision-one-new-tenant"></a>Aprovisionamiento de un nuevo inquilino

Si ya ha creado un inquilino en el [primer tutorial sobre SaaS de Wingtip](sql-database-saas-tutorial.md), puede pasar directamente a la siguiente sección y [aprovisionar un lote de inquilinos](#provision-a-batch-of-tenants).

Ejecute el script *Demo-ProvisionAndCatalog* para crear un inquilino rápidamente y registrarlo en el catálogo:

1. Abra **Demo-ProvisionAndCatalog.ps1** en PowerShell ISE y establezca los siguientes valores:
   * **$TenantName** = el nombre de la nueva ubicación (por ejemplo, *Bushwillow Blues*).
   * **$VenueType** = uno de los tipos predefinidos de ubicación: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic o soccer.
   * **$DemoScenario** = 1. Deje este valor establecido en _1_ para **aprovisionar un único inquilino**.

1. Presione **F5** y ejecute el script.

Una vez completado el script, se aprovisiona el nuevo inquilino y su aplicación de *eventos* se abre en el explorador:

![Nuevo inquilino](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionamiento de un lote de inquilinos

Este ejercicio aprovisiona un lote de inquilinos adicionales. Se recomienda que aprovisione un lote de inquilinos antes de completar otros tutoriales de SaaS de Wingtip, de manera que haya un mayor número de bases de datos con el que trabajar.

1. Abra ...\\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1* en *PowerShell ISE* y establezca el siguiente valor:
   * **$DemoScenario** = **3**. Establezca este valor en **3** para **aprovisionar un lote de inquilinos**.
1. Presione **F5** y ejecute el script.

El script implementa un lote de inquilinos adicionales Este script usa una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla el lote y, a continuación, delega el aprovisionamiento de cada base de datos a una plantilla vinculada. Esto permite que Azure Resource Manager negocie el proceso de aprovisionamiento para su script. Las plantillas aprovisionan bases de datos en paralelo cuando sea posible y controlan los reintentos si es necesario, optimizando así el proceso general. El script es idempotente, por lo que si se produce un error o se detiene por alguna razón, vuelva a ejecutarlo.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Compruebe que el lote de inquilinos se haya implementado correctamente.

* Abra el servidor *tenants1* en [Azure Portal](https://portal.azure.com) y haga clic en **Bases de datos SQL**:

   ![lista de base de datos](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-details"></a>Detalles del aprovisionamiento y registro de inquilinos en el catálogo

Para entender mejor cómo la aplicación Wingtip implementa el aprovisionamiento de un nuevo inquilino, vuelva a ejecutar el script *Demo-ProvisionAndCatalog* y aprovisione otro inquilino. Esta vez, agregue un punto de interrupción y siga los pasos del flujo de trabajo:

1. Abra ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ y establezca los siguientes parámetros:
   * **$TenantName** = los nombres de inquilino deben ser únicos. Por lo tanto, establezca un nombre distinto a los inquilinos existentes (por ejemplo, *Hackberry Hitters*).
   * **$VenueType** = use uno de los tipos de ubicación predefinidos (por ejemplo, *judo*).
   * **$DemoScenario** = 1. Establezca este valor en **1** para **aprovisionar un único inquilino**.

1. Agregue un punto de interrupción colocando el cursor en cualquier lugar de la siguiente línea: *New-Tenant `* y presione **F9**.

   ![punto de interrupción](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Presione **F5** para ejecutar el script. Cuando se alcance el punto de interrupción, presione **F11** para acceder. Realice un seguimiento de la ejecución del script mediante **F10** y **F11** para omitir o acceder a las funciones llamadas. [Sugerencias para trabajar con scripts de PowerShell y depurarlos](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>Examine la implementación de aprovisionamiento y catalogación en detalle completando los pasos del script.

El script aprovisiona y cataloga nuevos inquilinos mediante los pasos siguientes:

1. **Importe el módulo SubscriptionManagement.psm1** que contiene funciones para iniciar sesión en Azure y seleccionar la suscripción de Azure con la que está trabajando.
1. **Importe el módulo CatalogAndDatabaseManagement.psm1** que proporciona un catálogo y una abstracción de nivel de inquilino sobre las funciones de [administración de particiones](sql-database-elastic-scale-shard-map-management.md). Este es un módulo importante que encapsula gran parte del patrón de catálogo y que merece la pena explorar.
1. **Obtenga los detalles de configuración**. Vaya a *Get-Configuration* (con **F11**) y vea cómo se especifica la configuración de la aplicación. Los nombres de recursos y otros valores específicos de la aplicación se definen aquí, pero no cambie ninguno de estos valores hasta que esté familiarizado con los scripts.
1. **Obtenga el objeto de catálogo**. Vaya a *Get-Catalog* para ver cómo se inicializa el catálogo mediante las funciones de administración de particiones que se importan desde **AzureShardManagement.psm1**. El catálogo se compone de los siguientes objetos:
   * $catalogServerFullyQualifiedName se construye con la raíz estándar junto con su nombre de usuario: _catalog-\<usuario\>.database.windows.net_.
   * $catalogDatabaseName se obtiene de la configuración: *tenantcatalog*.
   * El objeto $shardMapManager se inicializa desde la base de datos del catálogo.
   * El objeto $shardMap se inicializa desde el mapa de particiones *tenantcatalog* de la base de datos del catálogo.
   Se generará y devolverá un objeto de catálogo, que se usará en el script de nivel superior.
1. **Calcule la nueva clave de inquilino**. Para crear la clave de inquilino a partir del nombre del inquilino se utiliza una función hash.
1. **Compruebe si ya existe la clave de inquilino**. Se comprobará en el catálogo que la clave está disponible.
1. **La base de datos de inquilino se aprovisiona con New-TenantDatabase**. Use **F11** para acceder y ver cómo la base de datos se aprovisiona mediante una plantilla de Resource Manager.

El nombre de la base de datos se construye a partir del nombre del inquilino para dejar claro qué partición pertenece a cada inquilino (también podrían emplearse fácilmente otras estrategias para asignar un nombre a la base de datos).

Se utiliza una plantilla de Resource Manager para crear una base de datos de inquilino al copiar una base de datos *golden* (basetenantdb) en el servidor de catálogo.  Un método alternativo podría consistir en crear una base de datos vacía y, a continuación, inicializarla mediante la importación de un archivo BACPAC.

La plantilla de Resource Manager se encuentra en la carpeta ...\\Learning Modules\\Common\\: *tenantdatabasecopytemplate.json*.

Una vez creada la base de datos de inquilino, se vuelve a inicializar con el nombre de la ubicación (inquilino) y el tipo de ubicación. Aquí también podría realizarse otro tipo de inicialización.

La base de datos de inquilino se registra en el catálogo con *Add-TenantDatabaseToCatalog* mediante la clave de inquilino. Use **F11** para acceder a los detalles:

* La base de datos de catálogo se agrega a la asignación de particiones (la lista de bases de datos conocidas).
* Se crea la asignación que vincula el valor de clave (inquilino) a la partición (base de datos).
* Se agregan metadatos adicionales sobre el inquilino.

Después de que se complete el aprovisionamiento, la ejecución vuelve al script *Demo-ProvisionAndCatalog* original y la página de **eventos** para el nuevo inquilino se abre en el explorador:

   ![events](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Otros patrones de aprovisionamiento

Entre los patrones de aprovisionamiento que no se describen en este tutorial se incluyen los siguientes:

**Aprovisionamiento previo de bases de datos.** El patrón previamente aprovisionamiento aprovecha del hecho de que las bases de datos en un grupo elástico no suponen costes adicionales. La facturación es para el grupo elástico, no para las bases de datos, y las bases de datos inactivas no consumen ningún recurso. Al aprovisionar previamente bases de datos en un grupo y asignarlas posteriormente cuando sea necesario, puede reducirse considerablemente el tiempo de incorporación del inquilino. El número de bases de datos previamente aprovisionadas puede ajustarse según sea necesario para mantener un búfer adecuado para la velocidad de aprovisionamiento prevista.

**Aprovisionamiento automático** En el patrón de aprovisionamiento automático se usa un servicio de aprovisionamiento dedicado para aprovisionar servidores, grupos y bases de datos automáticamente según sea necesario (incluido el aprovisionamiento previo de bases de datos en grupos elásticos si se desea). Además, si se retiran y eliminan bases de datos, los huecos que dejan en los grupos elásticos pueden ocuparse aprovisionando el servicio como se prefiera. Este servicio puede ser simple o complejo; por ejemplo, puede controlarse el aprovisionamiento en varias regiones geográficas y configurarse automáticamente la replicación geográfica si se utiliza dicha estrategia para la recuperación ante desastres. Con el patrón de aprovisionamiento automático, un script o aplicación cliente enviaría una solicitud de aprovisionamiento a una cola para que el servicio de aprovisionamiento la procesara y, a continuación, sondearía el servicio para determinar si se ha completado el procesamiento. Si se utiliza el aprovisionamiento previo, se podrían administrar las solicitudes rápidamente con el servicio que administra el aprovisionamiento de una base de datos de reemplazo en ejecución en segundo plano.



## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]

> * Aprovisionar un nuevo inquilino único
> * Aprovisionar un lote de inquilinos adicionales
> * Acceder a los detalles de cómo aprovisionar inquilinos y registrarlos en el catálogo.

Pruebe el [Tutorial de supervisión del rendimiento](sql-database-saas-tutorial-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionales

* Otros [tutoriales basados en la aplicación SaaS de Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de cliente de base de datos elástica](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Cómo depurar scripts en ISE de Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

