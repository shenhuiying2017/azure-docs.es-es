---
title: "Aprovisionamiento y catalogación de nuevos inquilinos (ejemplo de aplicación SaaS que usa Azure SQL Database) | Microsoft Docs"
description: "Aprovisionamiento y catalogación de nuevos inquilinos"
keywords: tutorial de base de datos sql
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
ms.openlocfilehash: 4eeada941f8615fa04624bc725efcb44f05d56c7
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Aprovisionamiento y registro de nuevos inquilinos en el catálogo

En este tutorial, se aprovisionan nuevos inquilinos en la aplicación SaaS Wingtip Tickets Platform (WTP). Creará inquilinos y bases de datos de inquilinos, y registrará los inquilinos en el catálogo. El *catálogo* es una base de datos que mantiene la asignación entre los diversos inquilinos de aplicaciones SaaS y sus datos. Utilice estos scripts para explorar los patrones de aprovisionamiento y catálogo empleados y cómo se implementa el registro de nuevos inquilinos en el catálogo. El catálogo desempeña un papel importante para dirigir las solicitudes de aplicación a las bases de datos correctas.

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Aprovisionar un nuevo inquilino único
> * Aprovisionar un lote de inquilinos adicionales
> * Acceder a los detalles de cómo aprovisionar nuevos inquilinos y registrarlos en el catálogo.


Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* La aplicación WTP está implementada. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [Implementación y exploración de la aplicación SaaS WTP](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introducción al patrón de catálogo de SaaS

En una aplicación SaaS multiinquilino con copia de seguridad en base de datos, es importante saber dónde se almacena la información de cada inquilino. En el patrón de catálogo de SaaS, una base de datos de catálogo se usa para mantener la asignación entre los inquilinos y la ubicación de almacenamiento de sus datos. La aplicación WTP utiliza una arquitectura de base de datos de inquilino único, pero el patrón básico de almacenamiento de la asignación de inquilino a base de datos en un catálogo es aplicable tanto si se usa una base de datos de inquilino único como multiinquilino.

A cada inquilino se le asigna una clave que distingue sus datos en el catálogo. En la aplicación WTP, la clave se forma a partir de un hash del nombre del inquilino. Este patrón permite utilizar la parte del nombre del inquilino de la dirección URL de la aplicación para generar la clave y recuperar una conexión del inquilino específica. Podrían utilizarse otros esquemas de identificación sin afectar al patrón general.

El catálogo de la aplicación WTP se implementa mediante la tecnología de administración de particiones en la [biblioteca de cliente de Elastic Database (EDCL)](sql-database-elastic-database-client-library.md). EDCL es responsable de crear y administrar un _catálogo_ con copia de seguridad en base de datos donde se mantiene un _mapa de particiones_. El catálogo contiene la asignación entre las claves (inquilinos) y sus bases de datos (particiones).

> [!IMPORTANT]
> Los datos de asignación son accesibles en la base de datos de catálogo, pero *no los edite*. Para editar datos de asignación, utilice únicamente las API de la biblioteca de cliente de Elastic Database. No se admite la manipulación directa de los datos de asignación, que entraña el riesgo de dañar el catálogo.

La aplicación SaaS Wingtip aprovisiona nuevos inquilinos copiando una base de datos *golden*.

## <a name="get-the-wingtip-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip

Los scripts de Wingtip Tickets y el código fuente de la aplicación están disponibles en el repositorio de github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Los archivos de los scripts se encuentran en la [carpeta Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Descargue la carpeta **Learning Modules** en el equipo local, conservando su estructura de carpetas.

## <a name="provision-a-new-tenant"></a>Aprovisionamiento de un nuevo inquilino

Si ya ha creado un inquilino en el primer tutorial de WTP, puede pasar directamente a la siguiente sección: [Aprovisionamiento de un lote de inquilinos](#provision-a-batch-of-tenants).

Ejecute el script *Demo-ProvisionAndCatalog* para crear un inquilino rápidamente y registrarlo en el catálogo:

1. Abra **Demo-ProvisionAndCatalog.ps1** en PowerShell ISE y establezca los siguientes valores:
   * **$TenantName** = el nombre de la nueva ubicación (por ejemplo, *Bushwillow Blues*). 
   * **$VenueType** = uno de los tipos predefinidos de ubicación: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic o soccer.
   * **$DemoScenario** = 1. Deje este valor establecido en _1_ para **aprovisionar un único inquilino**.

1. Presione **F5** y ejecute el script.

Una vez completado el script, se aprovisiona el nuevo inquilino y su aplicación de *eventos* se abre en el explorador:

![Nuevo inquilino](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionamiento de un lote de inquilinos

Este ejercicio aprovisiona un lote de inquilinos adicionales. Se recomienda hacer este ejercicio antes de completar los demás tutoriales de WTP.

1. Abra ...\\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1* en *PowerShell ISE* y establezca el siguiente valor:
   * **$DemoScenario** = **3**. Establezca este valor en **3** para **aprovisionar un lote de inquilinos**.
1. Presione **F5** y ejecute el script.

El script implementa un lote de inquilinos adicionales Este script usa una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla el lote y, a continuación, delega el aprovisionamiento de cada base de datos a una plantilla vinculada. Esto permite que Azure Resource Manager negocie el proceso de aprovisionamiento para su script. Las plantillas aprovisionan bases de datos en paralelo cuando sea posible y controlan los reintentos si es necesario, optimizando así el proceso general. El script es idempotente, por lo que si se interrumpe, ejecútelo de nuevo.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Compruebe que el lote de inquilinos se haya implementado correctamente.

* Abra el servidor *tenants1* en [Azure Portal](https://portal.azure.com) y haga clic en **Bases de datos SQL**:

   ![lista de base de datos](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>Tutorial detallado de aprovisionamiento y catalogación

Para entender mejor cómo la aplicación Wingtip implementa el aprovisionamiento de un nuevo inquilino, vuelva a ejecutar el script *Demo-ProvisionAndCatalog* y aprovisione otro inquilino. Esta vez, agregue un punto de interrupción y siga los pasos del flujo de trabajo:

1. Abra ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ y establezca los siguientes parámetros en nuevos valores de inquilino que no existan en el catálogo actual:
   * **$TenantName** = defina un nuevo nombre (por ejemplo, *Hackberry Hitters*).
   * **$VenueType** = use uno de los tipos de ubicación predefinidos (por ejemplo, *judo*).
   * **$DemoScenario** = 1. Establezca este valor en **1** para **aprovisionar un único inquilino**.

1. Agregue un punto de interrupción colocando el cursor en cualquier lugar de la siguiente línea: *New-Tenant `* y presione **F9**.

   ![punto de interrupción](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Presione **F5** para ejecutar el script. Cuando se alcance el punto de interrupción, presione **F11** para acceder. Realice un seguimiento de la ejecución del script mediante **F10** y **F11** para omitir o acceder a las funciones llamadas. [Sugerencias para trabajar con scripts de PowerShell y depurarlos](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>Examine la implementación de aprovisionamiento y catalogación en detalle completando los pasos del script.

El script aprovisiona y cataloga nuevos inquilinos mediante los pasos siguientes:

1. **Importe el módulo SubscriptionManagement.psm1** que contiene funciones para iniciar sesión en Azure y seleccionar la suscripción de Azure con la que está trabajando.
1. **Importe el módulo CatalogAndDatabaseManagement.psm1** que proporciona un catálogo y una abstracción de nivel de inquilino sobre las funciones de [administración de particiones](sql-database-elastic-scale-shard-map-management.md). Este es un módulo importante que encapsula gran parte del patrón de catálogo y que merece la pena explorar.
1. **Obtenga los detalles de configuración**. Vaya a _Get-Configuration_ (con **F11**) y vea cómo se especifica la configuración de la aplicación. Los nombres de recursos y otros valores específicos de la aplicación se definen aquí, pero no cambie ninguno de estos valores hasta que esté familiarizado con los scripts.
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

Se utiliza una plantilla de Resource Manager para **crear una base de datos de inquilino al copiar una base de datos *golden*** (baseTenantDB) en el servidor de catálogo.  Un método alternativo podría consistir en crear una base de datos vacía y, a continuación, inicializarla mediante la importación de un archivo BACPAC.

La plantilla de Resource Manager se encuentra en la carpeta ...\\Learning Modules\\Common\\: *tenantdatabasecopytemplate.json*.

Una vez creada la base de datos de inquilino, se vuelve a inicializar con el nombre de la ubicación (inquilino) y el tipo de ubicación. Aquí también podría realizarse otro tipo de inicialización.

La base de datos de inquilino se registra en el catálogo con *Add-TenantDatabaseToCatalog* mediante la clave de inquilino. Use **F11** para acceder a los detalles:

* La base de datos de catálogo se agrega a la asignación de particiones (la lista de bases de datos conocidas).
* Se crea la asignación que vincula el valor de clave a la partición.
* Se agregan metadatos adicionales (nombre de la ubicación) sobre el inquilino.

Después de que se complete el aprovisionamiento, la ejecución vuelve al script *Demo-ProvisionAndCatalog* original y la página de **eventos** para el nuevo inquilino se abre en el explorador:

   ![events](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Otros patrones de aprovisionamiento

Entre los patrones de aprovisionamiento que no se describen en este tutorial se incluyen los siguientes:

**Aprovisionamiento previo de bases de datos.** Este patrón aprovecha del hecho de que las bases de datos de un grupo elástico no suponen un costo adicional (se factura por grupo elástico, no por las bases de datos) y que las bases de datos inactivas no consumen recursos. Al aprovisionar previamente bases de datos en un grupo y asignarlas posteriormente cuando sea necesario, puede reducirse considerablemente el tiempo de incorporación del inquilino. El número de bases de datos previamente aprovisionadas puede ajustarse según sea necesario para mantener un búfer adecuado para la velocidad de aprovisionamiento prevista.

**Aprovisionamiento automático** En este modelo, se usa un servicio de aprovisionamiento dedicado para aprovisionar servidores, grupos y bases de datos automáticamente según sea necesario (incluido el aprovisionamiento previo de bases de datos en grupos elásticos si se desea). Además, si se retiran y eliminan bases de datos, los huecos que dejan en los grupos elásticos pueden ocuparse aprovisionando el servicio como se prefiera. Este servicio puede ser simple o complejo; por ejemplo, puede controlarse el aprovisionamiento en varias regiones geográficas y configurarse automáticamente la replicación geográfica si se utiliza dicha estrategia para la recuperación ante desastres. Con el patrón de aprovisionamiento automático, un script o aplicación cliente enviaría una solicitud de aprovisionamiento a una cola para que el servicio de aprovisionamiento la procesara y, a continuación, sondearía el servicio para determinar si se ha completado el procesamiento. Si se utiliza el aprovisionamiento previo, se podrían administrar las solicitudes rápidamente con el servicio que administra el aprovisionamiento de una base de datos de reemplazo en ejecución en segundo plano.


## <a name="stopping-wingtip-saas-application-related-billing"></a>Detención de la facturación relacionada con la aplicación SaaS Wingtip

Si no va a continuar con otro tutorial, se recomienda que elimine todos los recursos para suspender la facturación. Elimine el grupo de recursos en el que se implementó la aplicación WTP y todos sus recursos se eliminarán.

* Vaya al grupo de recursos de la aplicación en el portal y elimínelo para detener la toda la facturación relacionada con esta implementación de WTP.

## <a name="tips"></a>Sugerencias

* EDCL también proporciona características importantes que permiten que las aplicaciones cliente se conecten al catálogo y lo manipulen. También puede usar EDCL para recuperar una conexión de ADO.NET para un determinado valor de clave, permitiendo que la aplicación se conecte a la base de datos correcta. El cliente almacena en caché esta información de conexión para minimizar el tráfico en la base de datos de catálogo y acelerar la aplicación.



## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]

> * Aprovisionar un nuevo inquilino único
> * Aprovisionar un lote de inquilinos adicionales
> * Acceder a los detalles de cómo aprovisionar nuevos inquilinos y registrarlos en el catálogo.

[Tutorial de supervisión del rendimiento](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación inicial de la aplicación Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Biblioteca de cliente de base de datos elástica](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Cómo depurar scripts en ISE de Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

