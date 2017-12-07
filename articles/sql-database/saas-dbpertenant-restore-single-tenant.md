---
title: "Restauración de una base de datos SQL de Azure en una aplicación SaaS multiinquilino | Microsoft Docs"
description: "Obtenga información sobre cómo restaurar una base de datos SQL Database de inquilino único después de la eliminación accidental de datos"
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
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: ee2bc6d8b75b92243c0550db0044895e41c9474b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>Restauración de una instancia de Azure SQL Database de un solo inquilino en una aplicación SaaS multiinquilino

La aplicación SaaS Wingtip Tickets se compila con un modelo de base de datos por inquilino, donde cada inquilino tiene su propia base de datos. Una de las ventajas de este modelo es que resulta fácil restaurar los datos de un inquilino único de manera aislada sin afectar a otros inquilinos.

En este tutorial, conocerá dos patrones de recuperación de datos:

> [!div class="checklist"]

> * Restauración de una base de datos en una base de datos en paralelo (lado a lado)
> * Restauración de una base de datos en contexto


|||
|:--|:--|
| **Restauración de una base de datos a un momento dado anterior, en una base de datos en paralelo** | El inquilino puede usar este patrón para revisión, auditoría, cumplimiento, etc. La base de datos original sigue en línea y sin modificaciones. |
| **Restauración de inquilino en contexto** | Este patrón se usa habitualmente para recuperar un inquilino a un momento dado anterior, después de que un inquilino elimina datos por accidente. La base de datos original se deja sin conexión y se reemplaza por la base de datos restaurada. |
|||

Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Se implementa la aplicación SaaS de Wingtip. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [implementación y exploración de la aplicación SaaS de Wingtip](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>Introducción al patrón de restauración de inquilino de SaaS

Para el patrón de restauración del inquilino, hay dos patrones simples para restaurar los datos de un inquilino individual. Debido a que las bases de datos de inquilino están aisladas entre sí, restaurar un inquilino no afecta los datos de los demás inquilinos.

En el primer patrón, los datos se restauran en una base de datos nueva. Luego, el inquilino recibe acceso a esta base de datos junto con sus datos de producción. Este patrón permite que un administrador de inquilino revise los datos restaurados y posiblemente los use para sobrescribir de forma selectiva los valores de datos actuales. Depende del diseñador de aplicaciones SaaS determinar qué tan sofisticadas deben ser las opciones de recuperación de datos. En algunos escenarios, puede que solo sea necesario que puedan revisar los datos en el estado que tenían en algún momento determinado. Si la base de datos usa la [replicación geográfica](sql-database-geo-replication-overview.md), se recomienda copiar los datos necesarios de la copia restaurada en la base de datos original. Si reemplaza la base de datos original por la restaurada, debe volver a configurar y sincronizar la replicación geográfica.

En el segundo patrón, que supone que el inquilino sufrió una pérdida o daños en los datos, la base de datos de producción del inquilino se restaura a un momento dado anterior. En el patrón de restauración en contexto, el inquilino se deja sin conexión durante un tiempo breve mientras la base de datos se restaura y se vuelve a poner en línea. La base de datos original se elimina, pero de todos modos se puede hacer la restauración a partir de ella si necesita volver a un momento dado incluso anterior. Una variación de este patrón podría cambiar el nombre de la base de datos en lugar de eliminar, a pesar de que cambiar el nombre no ofrece ninguna ventaja adicional en términos de la seguridad de los datos.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip Tickets SaaS Database Per Tenant

Los scripts y el código fuente de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts SaaS de Wingtip Tickets.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulación de la eliminación accidental de los datos de un inquilino

Para mostrar estos escenarios de recuperación, es necesario eliminar *accidentalmente* algunos datos en una de las bases de datos del inquilino. Aunque puede eliminar cualquier registro, en el paso siguiente la demostración se configura para que no se bloquee por infracciones a la integridad referencial. También se agregan algunos datos de compra de entradas que puede usar más adelante en los *tutoriales de Analytics de SaaS Wingtip*.

Ejecute el script del generador de entradas y cree datos adicionales. De manera intencional, el generador de entradas no compra entradas para el último evento de cada inquilino.

1. Abra ...\\Learning Modules\\Utilities\\*Demo-TicketGenerator.ps1* en *PowerShell ISE*
1. Presione **F5** para ejecutar el script y generar datos de ventas de entradas y clientes.


### <a name="open-the-events-app-to-review-the-current-events"></a>Apertura de la aplicación Eventos para revisar los eventos actuales

1. Abra *Events Hub* (http://events.wtp.&lt;user&gt;.trafficmanager.net) y haga clic en **Contoso Concert Hall**:

   ![events hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Desplace la lista de eventos y anote el último evento de la lista:

   ![último evento](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>Ejecute el escenario de demostración para eliminar accidentalmente el último evento

1. Abra ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1* en *PowerShell ISE* y establezca el valor siguiente:
   * **$DemoScenario** = **1**. Establezca el valor en **1** para eliminar eventos sin venta de entradas.
1. Presione **F5** para ejecutar el script y eliminar el último evento. Debe ver un mensaje de confirmación similar al siguiente:

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Se abre la página de eventos de Contoso. Desplácese hacia abajo y compruebe que el evento desapareció. Si todavía está en la lista, haga clic para actualizar y compruebe que ya no aparece.

   ![último evento](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restauración de una base de datos de inquilino en paralelo con la base de datos de producción

En este ejercicio se restaura la base de datos de Contoso Concert Hall a un momento específico antes de que se eliminara el evento. Una vez que el evento se eliminó en los pasos anteriores, desea recuperar y ver los datos eliminados. No es necesario restaurar la base de datos de producción con el registro eliminado, pero sí tiene que recuperar la base de datos anterior para tener acceso a los datos antiguos por otros motivos de negocio.

 El script *Restore-TenantInParallel.ps1* crea una base de datos de inquilino en paralelo y una entrada de catálogo en paralelo, ambos denominados *ContosoConcertHall\_old*. Este patrón de restauración es más adecuado para recuperarse de una pérdida de datos sin importancia o para escenarios de recuperación de cumplimiento y auditoría. También es el enfoque que se recomienda cuando se usa la [replicación geográfica](sql-database-geo-replication-overview.md).

1. Complete la sección en que se [simula que un usuario elimina datos de forma accidental](#simulate-a-tenant-accidentally-deleting-data).
1. Abra ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_ en *PowerShell ISE*.
1. Establezca **$DemoScenario** = **2**; establezca este valor en **2** para *restaurar el inquilino en paralelo*.
1. Presione **F5** para ejecutar el script.

El script restaura la base de datos de inquilino (a una base de datos en paralelo) a un momento dado antes de la eliminación del evento que realizó en la sección anterior. Crea una segunda base de datos, quita cualquier metadato de catálogo existente en esta base de datos y agrega la base de datos al catálogo en la entrada *ContosoConcertHall\_old*.

El script de demostración abre la página de eventos en el explorador. En la dirección URL: ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```, tenga en cuenta que se muestran los datos de la base de datos restaurada, donde se agrega *_old* al nombre.

Desplace los eventos que aparecen en el explorador y confirmar que se restauró el evento que se eliminó en la sección anterior.

Tenga en cuenta que exponer al inquilino restaurado como un inquilino adicional, con su propia aplicación de eventos para examinar entradas, puede que no sea la forma en que proporcionaría acceso de inquilino a los datos restaurados, pero sí sirve para ilustrar fácilmente el patrón de restauración.

En realidad, es probable que solo debería retener esta base de datos restaurada durante un período definido. Puede eliminar la entrada del inquilino restaurado una vez que finalice; para ello, debe llamar al script *Remove-RestoredTenant.ps1*.

1. Establezca **$DemoScenario** en **4** para seleccionar el escenario en que se *quita el inquilino restaurado*.
1. **Ejecute****con****F5**
1. La entrada *ContosoConcertHall\_old* se eliminó del catálogo. Continúe y cierre la página de eventos de este inquilino en el explorador.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restauración de un inquilino en contexto, reemplazando la base de datos de inquilino existente

En este ejercicio se restaura el inquilino de Contoso Concert Hall a un momento específico antes de que se eliminara el evento. El script *Restore-TenantInPlace* restaura la base de datos de inquilino actual a una base de datos nueva que señala un momento dado anterior y elimina la base de datos original. Este patrón de restauración es más adecuado para la recuperación después de daños graves en los datos, porque puede haber una mayor pérdida de datos a la que debe adecuarse el inquilino.

1. Abra el archivo **Demo-RestoreTenant.ps1** en PowerShell ISE
1. Establezca **$DemoScenario** en **5** para seleccionar el *escenario de restauración de inquilino en contexto*.
1. Ejecute con **F5**.

El script restaura la base de datos de inquilino a un punto cinco minutos antes de que se eliminara el evento. Para ello, primero deja sin conexión al inquilino Contoso Concert Hall para que no haya actualizaciones adicionales a los datos. Luego se crea una base de datos en paralelo a partir de la restauración desde un punto de restauración, con una marca de tiempo como nombre para garantizar que el nombre de la base de datos no entre en conflicto con el nombre de la base de datos de inquilino existente. A continuación, se elimina la base de datos de inquilino anterior y el nombre de la base de datos restaurada se cambia al nombre de la base de datos original. Por último, Contoso Concert Hall se vuelve a conectar para permitir que la aplicación tenga acceso a la base de datos restaurada.

La base de datos se restauró correctamente a un momento dado antes de que se eliminara el evento. Se abrirá la página de eventos, donde debe confirmar que se restauró el último evento.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]

> * Restauración de una base de datos en una base de datos en paralelo (lado a lado)
> * Restauración de una base de datos en contexto

[Administración de un esquema de base de datos de inquilino](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la aplicación SaaS de Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Introducción a la continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md)
* [Más información sobre las copias de seguridad de SQL Database](sql-database-automated-backups.md)
