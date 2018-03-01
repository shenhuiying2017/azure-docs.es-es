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
ms.openlocfilehash: 46471073f88247510f45d6c4152afa43be6e1aaa
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Restauración de un solo inquilino con una aplicación SaaS de base de datos por inquilino

El modelo de base de datos por inquilino facilita la restauración de un inquilino único a un momento dado anterior sin afectar a otros inquilinos.

En este tutorial, conocerá dos patrones de recuperación de datos:

> [!div class="checklist"]

> * Restauración de una base de datos en una base de datos en paralelo (lado a lado)
> * Restauración de una base de datos en contexto, reemplazando la base de datos existente


|||
|:--|:--|
| **Restauración en una base de datos en paralelo** | Este patrón se puede usar para la revisión, la auditoría, el cumplimiento, etc. para permitir que un inquilino inspeccione los datos desde un punto anterior.  La base de datos actual del inquilino sigue en línea y sin modificaciones. |
| **Restauración en contexto** | Este patrón se usa habitualmente para recuperar un inquilino a un punto anterior, después de que un inquilino elimina o daña datos de manera accidental. La base de datos original se deja sin conexión y se reemplaza por la base de datos restaurada. |
|||

Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* Se implementa la aplicación SaaS de Wingtip. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [implementación y exploración de la aplicación SaaS de Wingtip](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introducción a los patrones de restauración de inquilino de SaaS

Hay dos patrones simples para restaurar los datos de un inquilino individual. Debido a que las bases de datos de inquilino están aisladas entre sí, restaurar un inquilino no afecta los datos de los demás inquilinos.  La característica de restauración a un momento dado (PITR) de SQL Database se usa en ambos patrones.  Esta característica siempre crea una base de datos nueva.   

En el primer patrón, **restauración en paralelo**, se crea una base de datos en paralelo junto con la base de datos actual del inquilino. A continuación, el inquilino recibe acceso de solo lectura a la base de datos restaurada. Los datos restaurados se pueden revisar y potencialmente usar para sobrescribir los valores de datos actuales. Del diseñador de la aplicación depende determinar cómo el inquilino accede a la base de datos restaurada y las opciones de recuperación que se proporcionan. En algunos escenarios, es posible que todo lo que se necesite sea simplemente permitir que el inquilino revise los datos en un punto anterior. 

El segundo patrón, **restauración en contexto**, resulta útil si se perdieron o dañaron datos y el inquilino quiere revertir a un punto anterior.  El inquilino queda sin conexión mientras se restaura la base de datos. La base de datos original se elimina y se cambia el nombre de la base de datos restaurada. La cadena de copia de seguridad de la base de datos original sigue siendo accesible después de la eliminación, lo que le permite restaurar la base de datos a un momento dado anterior si es necesario.

Si la base de datos usa la [replicación geográfica](sql-database-geo-replication-overview.md) y la restauración en paralelo, se recomienda copiar los datos requeridos desde la copia restaurada a la base de datos original. Si reemplaza la base de datos original por la restaurada, debe volver a configurar y sincronizar la replicación geográfica.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip Tickets SaaS Database Per Tenant

Los scripts y el código fuente de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts SaaS de Wingtip Tickets.

## <a name="before-you-start"></a>Antes de comenzar

Cuando se crea una base de datos, puede tardar entre 10 y 15 minutos hasta que la primera copia de seguridad completa esté disponible para restaurar desde ahí.  Si acaba de instalar la aplicación, es posible que deba esperar unos minutos antes de probar este escenario.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulación de la eliminación accidental de los datos de un inquilino

Para mostrar estos escenarios de recuperación, primero elimine de manera *"accidental"* un evento en una de las bases de datos de inquilino. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Apertura de la aplicación Eventos para revisar los eventos actuales

1. Abra *Events Hub* (http://events.wtp.&lt;user&gt;.trafficmanager.net) y haga clic en **Contoso Concert Hall**:

   ![events hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Desplace la lista de eventos y anote el último evento de la lista:

   ![último evento](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>Eliminación "accidental" del último evento

1. Abra ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1* en *PowerShell ISE* y establezca el valor siguiente:
   * **$DemoScenario** = **1**, eliminación del último evento (sin venta de entradas).
1. Presione **F5** para ejecutar el script y eliminar el último evento. Debería ver el mensaje de confirmación siguiente:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Se abre la página de eventos de Contoso. Desplácese hacia abajo y compruebe que el evento desapareció. Si todavía está en la lista, haga clic para actualizar y compruebe que ya no aparece.

   ![último evento](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restauración de una base de datos de inquilino en paralelo con la base de datos de producción

En este ejercicio se restaura la base de datos de Contoso Concert Hall a un momento específico antes de que se eliminara el evento. En este escenario se supone que solo desea revisar los datos eliminados en una base de datos en paralelo.

 El script *Restore-TenantInParallel.ps1* crea una base de datos de inquilino en paralelo denominada *ContosoConcertHall\_old*, con una entrada de catálogo en paralelo. Este patrón de restauración es más adecuado para recuperarse de una pérdida de datos sin importancia o si necesita revisar los datos con fines de cumplimiento o auditoría. También es el enfoque que se recomienda cuando se usa la [replicación geográfica](sql-database-geo-replication-overview.md).

1. Complete la sección en que se [simula que un usuario elimina datos de manera accidental](#simulate-a-tenant-accidentally-deleting-data).
1. En *PowerShell ISE*, abra ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
1. Establezca **$DemoScenario** = **2**, *Restore tenant in parallel* (Restaurar el inquilino en paralelo).
1. Presione **F5** para ejecutar el script.

El script restaura la base de datos de inquilino a un momento dado antes de que eliminara el evento. La base de datos se restaura a una base de datos nueva denominada _ContosoConcertHall\_old_. Los metadatos de catálogo que existen en esta base de datos restaurada se eliminar y, luego, la base de datos se agrega al catálogo mediante una clave construida a partir del nombre *ContosoConcertHall\_old*.

El script de demostración abre la página de eventos de esta base de datos de inquilino en el explorador. En la dirección URL: ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```, tenga en cuenta que esta página muestra los datos de la base de datos restaurada, donde se agrega *_old* al nombre.

Desplace los eventos que aparecen en el explorador y confirmar que se restauró el evento que se eliminó en la sección anterior.

Tenga en cuenta que exponer al inquilino restaurado como un inquilino adicional, con su propia aplicación de eventos, puede que no sea la forma en que proporcionaría acceso de inquilino a los datos restaurados, pero sí sirve para ilustrar el patrón de restauración. En realidad, es probable que proporcione acceso de solo lectura a los datos antiguos y que retenga esta base de datos restaurada durante un período definido. En el ejemplo, puede eliminar la entrada de inquilino restaurado una vez que finalice mediante la ejecución del escenario _Remove restored tenant_ (Quitar el inquilino restaurado).

1. Establezca **$DemoScenario** = **4**, *Remove restored tenant* (Quitar el inquilino restaurado)
1. **Ejecute****con****F5**
1. La entrada *ContosoConcertHall\_old* se eliminó del catálogo. Continúe y cierre la página de eventos de este inquilino en el explorador.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restauración de un inquilino en contexto, reemplazando la base de datos de inquilino existente

En este ejercicio se restaura el inquilino de Contoso Concert Hall a un momento antes de que se eliminara el evento. El script *Restore-TenantInPlace* restaura una base de datos de inquilino a una base de datos nueva y elimina la original.  Este patrón de restauración es más adecuado para la recuperación después de daños graves en los datos, porque puede haber una mayor pérdida de datos a la que debe adecuarse el inquilino.

1. Abra el archivo **Demo-RestoreTenant.ps1** en PowerShell ISE
1. Establezca **$DemoScenario** = **5**, *Restore tenant in place* (Restaurar el inquilino en contexto).
1. Ejecute con **F5**.

El script restaura la base de datos de inquilino a un punto antes de que se eliminara el evento. En primer lugar, deja sin conexión al inquilino de Contoso Concert Hall para evitar futuras actualizaciones. Luego, se crea una base de datos en paralelo mediante la restauración a partir del punto de restauración.  Luego, la base de datos restaurada recibe una marca de tiempo como nombre para garantizar que el nombre de la base de datos no entre en conflicto con el nombre de la base de datos de inquilino existente. A continuación, se elimina la base de datos de inquilino anterior y el nombre de la base de datos restaurada se cambia al nombre de la base de datos original. Por último, Contoso Concert Hall se vuelve a conectar para permitir que la aplicación tenga acceso a la base de datos restaurada.

La base de datos se restauró correctamente a un momento dado antes de que se eliminara el evento. Se abrirá la página de eventos, donde debe confirmar que se restauró el último evento.

Tenga en cuenta que una vez que restaure la base de datos, tardará entre 10 y 15 minutos antes de que la primera copia de seguridad completa esté disponible para volver a realizar la restauración. 

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]

> * Restauración de una base de datos en una base de datos en paralelo (lado a lado)
> * Restauración de una base de datos en contexto

[Administración de un esquema de base de datos de inquilino](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la aplicación SaaS de Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Introducción a la continuidad empresarial con Azure SQL Database](sql-database-business-continuity.md)
* [Más información sobre las copias de seguridad de SQL Database](sql-database-automated-backups.md)
