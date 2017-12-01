---
title: "Aprovisionamiento y catalogación de inquilinos nuevos en una aplicación SaaS con una base de datos SQL de Azure SQL Database multiinquilino | Microsoft Docs"
description: "Obtenga información sobre cómo aprovisionar y catalogar nuevos inquilinos en una aplicación SaaS de multiinquilino de Azure SQL Database"
keywords: tutorial de SQL Database
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: ec753027c8ce8040cbc574279a44eb24590fcb05
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>Aprovisionamiento y catalogación de inquilinos nuevos una aplicación SaaS con una base de datos SQL multiinquilino con particiones

En este tutorial, obtendrá información sobre los patrones para aprovisionar y catalogar los inquilinos cuando trabaje con un modelo de base de datos multiinquilino con particiones. 

Un esquema multiinquilino, que incluye un identificador de inquilino en la clave principal de las tablas que contienen los datos de inquilino, permite almacenar varios inquilinos en una sola base de datos. Para admitir grandes cantidades de inquilinos, los datos de inquilino se distribuyen entre varias particiones o bases de datos. Los datos de un inquilino cualquiera siempre están completamente contenidos en una única base de datos.  Se usa un catálogo para contener la asignación de inquilinos a las bases de datos.   

También puede elegir rellenar algunas bases de datos solo con un inquilino. Las bases de datos que contienen varios inquilinos privilegian un menor costo por individuo a costa del aislamiento del inquilino.  Las bases de datos que solo contienen un inquilino privilegian el aislamiento sobre el bajo costo.  Las bases de datos con varios inquilinos e inquilinos únicos se pueden combinar en la misma aplicación SaaS para optimizar el costo o el aislamiento de cada inquilino. Los inquilinos pueden tener su propia base de datos cuando se aprovisionan o se pueden mover más adelante a ella.

   ![Aplicación de base de datos multiinquilino con particiones con catálogo de inquilinos](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>Patrón de un catálogo de inquilino

Si los datos de inquilino se distribuyen entre varias bases de datos, es importante saber dónde se almacenan los datos de cada inquilino. Una base de datos de catálogo contiene la asignación entre un inquilino y la base de datos donde se almacenan sus datos.

Cada inquilino se identifica con una clave en el catálogo. En la aplicación de SaaS Wingtip Tickets, la clave de inquilino se forma a partir de un hash del nombre del inquilino. Esto permite que la aplicación extraiga el nombre del inquilino de la dirección URL de una página web y lo usa para conectarse a la base de datos correspondiente. También se pueden usar otros esquemas de claves de inquilino.

Usar un catálogo permite cambiar el nombre o la ubicación de una base de datos de inquilino después del aprovisionamiento sin interrumpir la aplicación.  En un modelo de base de datos multiinquilino, esto permite mover un inquilino entre bases de datos.  El catálogo también se puede usar para indicar a una aplicación si un inquilino está sin conexión debido a operaciones de mantenimiento u otras acciones.

El catálogo se puede extender para almacenar metadatos de base de datos o inquilino adicionales, como el nivel o la edición de una base de datos, la versión del esquema de la base de datos, el nombre del inquilino y el plan de servicio o SLA, además de otra información para permitir la administración de la aplicación el soporte al cliente y los procesos de DevOps.  

El catálogo también se puede usar para habilitar la generación de informes entre inquilinos, la administración de esquemas y la extracción de datos con fines de análisis. 

### <a name="elastic-database-client-library"></a>Biblioteca de cliente de Elastic Database 
En las aplicaciones de SaaS de Wingtip Tickets, el catálogo se implementa en la base de datos *tenantcatalog* mediante las características de administración de particiones de la [biblioteca de cliente de Elastic Database (EDCL)](sql-database-elastic-database-client-library.md). La biblioteca permite que una aplicación pueda crear, administrar y usar un "mapa de particiones" con copia de seguridad en base de datos. Un mapa de particiones contiene una lista de particiones (bases de datos) y la asignación entre las claves (identificadores de inquilinos) y las particiones.  Las funciones de EDCL se pueden usar a partir de aplicaciones o scripts de PowerShell durante el aprovisionamiento del inquilino para crear las entradas en el mapa de particiones y, después, para conectarse eficazmente a la base de datos correcta. La biblioteca almacena en caché la información de conexión para minimizar el tráfico en la base de datos de catálogo y acelerar la conexión. 

> [!IMPORTANT]
> Los datos de asignación son accesibles en la base de datos de catálogo, pero *no los edite*. Para editar datos de asignación, utilice únicamente las API de la biblioteca de cliente de Elastic Database. No se admite la manipulación directa de los datos de asignación, que entraña el riesgo de dañar el catálogo.


## <a name="tenant-provisioning-pattern"></a>Patrón de aprovisionamiento de inquilinos

Cuando se aprovisiona un inquilino nuevo en el modelo de base de datos multiinquilino con particiones, primero se debe determinar si el inquilino se aprovisionará en una base de datos compartida o dada su propia base de datos. Si se trata de una base de datos compartida, se debe determinar si hay espacio en una base de datos existente o si se requiere una base de datos nueva. Si se necesita una base de datos nueva, se debe aprovisionar en el nivel de servicio y la ubicación correspondiente, inicializar con los datos de referencia y el esquema adecuados y, luego, registrar en el catálogo. Por último, se puede agregar la asignación de inquilino para hacer referencia a la partición correspondiente.

Para aprovisionar la base de datos, ejecute los scripts SQL, implemente un elemento bacpac o copie una base de datos de plantilla. Las aplicaciones de SaaS de Wingtip Tickets copian una base de datos de plantilla para crear bases de datos de inquilino nuevas.

El enfoque de aprovisionamiento de la base de datos debe estar incluido en la estrategia general de administración de esquemas, que necesita garantizar que las nuevas bases de datos se aprovisionan con los esquemas más recientes.  Todo ello se describe con más detalle en el [tutorial de administración de esquemas](saas-tenancy-schema-management.md).  

Los scripts de aprovisionamiento de inquilino que aparecen en este tutorial incluyen el aprovisionamiento de un inquilino en una base de datos existente compartida con otros inquilinos y el aprovisionamiento de un inquilino en su propia base de datos. Luego, los datos de inquilino se inicializan y registran en el mapa de particiones del catálogo. En la aplicación de ejemplo, a las bases de datos que contienen varios inquilinos se les asigna un nombre genérico, como *tenants1*, *tenants2*, etc. mientras que las bases de datos que contienen un solo inquilino reciben el nombre del inquilino. Las convenciones de nomenclatura específicas que se usan en el ejemplo no son una parte fundamental del patrón, porque el uso de un catálogo permite asignar cualquier nombre a la base de datos.  

## <a name="provision-and-catalog-tutorial"></a>Tutorial de aprovisionamiento y catalogación

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Aprovisionar un inquilino en una base de datos multiinquilino
> * Aprovisionar un inquilino en una base de datos de un único inquilino
> * Aprovisionar un lote de inquilinos en bases de datos multiinquilino y en bases de datos de un único inquilino
> * Registrar una asignación de inquilino y base de datos en un catálogo

Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* La aplicación SaaS de base de datos multiinquilino Wingtip Tickets está implementada. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [implementación y exploración de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="get-the-wingtip-tickets-management-scripts"></a>Obtención de los scripts de administración de Wingtip Tickets

Los scripts de administración y el código fuente de la aplicación están disponibles en el repositorio [WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) de GitHub. <!--See [Steps to download the Wingtip SaaS scripts](saas-tenancy-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).-->


## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>Aprovisionamiento de un inquilino en una base de datos compartida con otros inquilinos

Para comprender cómo la aplicación Wingtip Tickets implementa el aprovisionamiento de un inquilino nuevo en una base de datos compartida, agregue un punto de interrupción y recorra el flujo de trabajo:

1. En _PowerShell ISE_, abra ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ y establezca los parámetros siguientes:
   * **$TenantName** = **Bushwillow Blues**, el nombre de la ubicación nueva.
   * **$VenueType** = **blues**, uno de los tipos de ubicación predefinidos: *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (en minúsculas, sin espacio).
   * **$Scenario** = **1**, para el *aprovisionamiento de un inquilino en una base de datos compartida con otros inquilinos*.

1. Agregue un punto de interrupción colocando el cursor en cualquier lugar de la línea 38, que dice: *New-Tenant `* y presione **F9**.

   ![punto de interrupción](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. Presione **F5** para ejecutar el script.

1. Después de que la ejecución del script se detiene en el punto de interrupción, presione **F11** para depurar paso a paso el código por instrucciones.

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

Siga paso a paso la ejecución del script mediante las opciones del menú **Depurar**, **F10** y **F11**, para omitir las funciones llamadas o acceder a ellas. Para obtener más información sobre cómo depurar scripts de PowerShell, consulte [Sugerencias para trabajar con scripts de PowerShell y depurarlos](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


A continuación se muestran los elementos clave del flujo de trabajo de aprovisionamiento que recorre:

* **Calcule la nueva clave de inquilino**. Para crear la clave de inquilino a partir del nombre del inquilino se utiliza una función hash.
* **Compruebe si ya existe la clave de inquilino**. El catálogo se comprueba para garantizar que todavía no se registró la clave.
* **Inicialice el inquilino en la base de datos de inquilino predeterminada**. La base de datos de inquilino se actualiza para agregar la información del inquilino nuevo.  
* **Registre el inquilino en el catálogo**. La asignación entre la clave de inquilino nueva y la base de datos tenants1 existente se agrega al catálogo. 
* **Agregue el nombre del inquilino a una tabla de extensión de catálogo**. El nombre de la ubicación se agrega a la tabla Inquilinos del catálogo.  Esto muestra cómo la base de datos de catálogo puede ampliarse para admitir los datos adicionales específicos de la aplicación.
* **Abra la página Eventos para el inquilino nuevo**. La página de eventos de *Bushwillow Blues* se abre en el explorador:

   ![events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>Aprovisionamiento de un inquilino en su propia base de datos

Ahora recorra los procesos para crear un inquilino en su propia base de datos:

1. Todavía en ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_, establezca los parámetros siguientes:
   * **$TenantName** = **Sequoia Soccer**, el nombre de la ubicación nueva.
   * **$VenueType** = **soccer**, uno de los tipos de ubicación predefinidos: *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (en minúsculas, sin espacio).
   * **$Scenario** = **2**, para el *aprovisionamiento de un inquilino en una base de datos compartida con otros inquilinos*.

1. Agregue un punto de interrupción nuevo colocando el cursor en cualquier lugar de la línea 57, que dice: *&&nbsp;$PSScriptRoot\New-TenantAndDatabase `*, y presione **F9**.

   ![punto de interrupción](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. Presione **F5** para ejecutar el script.

1. Después de que la ejecución del script se detiene el punto de interrupción, presione **F11** para depurar paso a paso el código por instrucciones.  Use **F10** y **F11** para omitir las funciones para hacer seguimiento de la ejecución o acceder a ellas.

A continuación se muestran los elementos clave del flujo de trabajo que recorre mientras se hace seguimiento del script:

* **Calcule la nueva clave de inquilino**. Para crear la clave de inquilino a partir del nombre del inquilino se utiliza una función hash.
* **Compruebe si ya existe la clave de inquilino**. El catálogo se comprueba para garantizar que todavía no se registró la clave.
* **Cree una base de datos de inquilino nueva**. La base de datos se crea al copiar la base de datos *basetenantdb* con una plantilla de Resource Manager.  El nombre de la base de datos nueva se basa en el nombre del inquilino.
* **Agregue la base de datos al catálogo**. La base de datos de inquilino nueva se registra como una partición en el catálogo.
* **Inicialice el inquilino en la base de datos de inquilino predeterminada**. La base de datos de inquilino se actualiza para agregar la información del inquilino nuevo.  
* **Registre el inquilino en el catálogo** La asignación entre la clave de inquilino nueva y la base de datos *sequoiasoccer* se agrega al catálogo.
* **El nombre del inquilino se agrega al catálogo**. El nombre de la ubicación se agrega a la tabla de extensión Inquilinos del catálogo.
* **Abra la página Eventos para el inquilino nuevo**. La página de eventos de *Sequoia Soccer* se abre en el explorador:

   ![events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionamiento de un lote de inquilinos

Este ejercicio aprovisiona un lote de 17 inquilinos. Se recomienda que aprovisione este lote de inquilinos antes de comenzar con otros tutoriales de Wingtip Tickets, de manera que haya más bases de datos con las cuales trabajar.

1. En *PowerShell ISE*, abra ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* y cambie el parámetro *$Scenario* a 3:
   * **$Scenario** = **3**, para el *aprovisionamiento de un lote de inquilinos en una base de datos compartida*.
1. Presione **F5** y ejecute el script.


### <a name="verify-the-deployed-set-of-tenants"></a>Comprobación del conjunto de inquilinos implementado 
En esta fase, se tiene una combinación de inquilinos implementados en una base de datos compartida y los inquilinos implementados en sus propias bases de datos. Se puede usar Azure Portal para inspeccionar las bases de datos creadas:  

* En [Azure Portal](https://portal.azure.com), abra el servidor **tenants1-mt-\<USER\>** mediante la navegación a la lista de servidores SQL Server.  La lista de **bases de datos SQL** debe incluir la base **tenants1** compartida y las bases de datos de los inquilinos que están en su propia base de datos:

   ![lista de base de datos](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Mientras Azure Portal muestra las bases de datos de inquilino, no le permite ver los inquilinos *dentro* de la base de datos compartida. Puede ver la lista completa de inquilinos en la página del centro de eventos de Wingtip Tickets y examinando el catálogo:   

1. Abra la página del centro de eventos en el explorador (http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

   La lista completa de los inquilinos y su base de datos correspondiente está disponible en el catálogo. Se proporciona una vista SQL en la base de datos tenantcatalog que combina el nombre de inquilino almacenado en la tabla Inquilinos con el nombre de base de datos en las tablas de administración de particiones. Esta vista muestra de manera correcta el valor de extender los metadatos almacenados en el catálogo.

2. En *SQL Server Management Studio (SSMS)*, está conectado al servidor de inquilinos en **tenants1-mt.\<USER\>.database.windows.net**, con el inicio de sesión: **developer** y la contraseña: **P@ssword1**

    ![Cuadro de diálogo de conexión SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. En el *Explorador de objetos*, examine las vistas de la base de datos *tenantcatalog*.
2. Haga clic con el botón derecho en la vista *TenantsExtended* y elija **Select Top 1000 Rows** (Seleccionar las 1000 primeras filas). Observe la asignación entre el nombre de inquilino y la base de datos para los distintos inquilinos.

    ![Vista ExtendedTenants en SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Otros patrones de aprovisionamiento

Otros patrones de aprovisionamiento interesantes incluyen:

**Aprovisionamiento previo de las bases de datos en grupos elásticos.** El patrón de aprovisionamiento previo aprovecha el hecho de que, cuando se usan los grupos elásticos, la facturación se hace en función del grupo y no de las bases de datos. Por lo tanto, las bases de datos se pueden agregar a un grupo elástico antes de que sea necesario incurrir en costos adicionales. Al aprovisionar previamente las bases de datos de un grupo y asignarlas cuando sea necesario, se puede reducir considerablemente el tiempo para aprovisionar un inquilino en una base de datos. El número de bases de datos previamente aprovisionadas puede ajustarse según sea necesario para mantener un búfer adecuado para la velocidad de aprovisionamiento prevista.

**Aprovisionamiento automático** En el patrón de aprovisionamiento automático se usa un servicio de aprovisionamiento dedicado para aprovisionar servidores, grupos y bases de datos automáticamente según sea necesario (incluido el aprovisionamiento previo de bases de datos en grupos elásticos). Además, si se retiran y eliminan bases de datos, los espacios que esto crea en los grupos elásticos pueden ocuparse aprovisionando el servicio como se prefiera. Este servicio puede ser simple o complejo; por ejemplo, puede controlarse el aprovisionamiento en varias regiones geográficas y configurarse automáticamente la replicación geográfica para la recuperación ante desastres. Con el patrón de aprovisionamiento automático, un script o aplicación cliente enviaría una solicitud de aprovisionamiento a una cola para que un servicio de aprovisionamiento la procesara y, a continuación, sondearía para determinar si se ha completado el procesamiento. Si se usa el aprovisionamiento previo, se podrían administrar las solicitudes rápidamente mientras que otro servicio administraría el aprovisionamiento de una base de datos de reemplazo en segundo plano.



## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]

> * Aprovisionar un único inquilino nuevo en una base de datos multiinquilino compartida y su propia base de datos
> * Aprovisionar un lote de inquilinos adicionales
> * Recorrer los detalles sobre cómo aprovisionar inquilinos y registrarlos en el catálogo

Pruebe el [Tutorial de supervisión del rendimiento](saas-multitenantdb-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionales

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Biblioteca de cliente de base de datos elástica](sql-database-elastic-database-client-library.md)
* [Cómo depurar scripts en ISE de Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
