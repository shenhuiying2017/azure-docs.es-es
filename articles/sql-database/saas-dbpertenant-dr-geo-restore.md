---
title: Recuperación ante desastres para aplicaciones SaaS mediante copias de seguridad con redundancia geográfica de Azure SQL Database | Microsoft Docs
description: Obtenga información sobre cómo usar las copias de seguridad con redundancia geográfica de Azure SQL Database para recuperar una aplicación SaaS multiinquilino en caso de una interrupción.
keywords: tutorial de SQL Database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: a677e6eb583e293f83df824804aa4cd6f8f5d778
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="recover-a-multi-tenant-saas-application-using-geo-restore-from-database-backups"></a>Recuperación de una aplicación SaaS multiinquilino mediante la restauración geográfica a partir de copias de seguridad de base de datos

En este tutorial, veremos un escenario completo de recuperación ante desastres para una aplicación SaaS multiinquilino implementada con el modelo de una base de datos por inquilino. Se usa [_restauración geográfica_](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups) para recuperar el catálogo y las bases de datos de inquilino a partir de copias de seguridad con redundancia geográfica mantenidas automáticamente en una región de recuperación alternativa. Después de que se resuelva la interrupción, use la [_replicación geográfica_](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) para repatriar las bases de datos cambiadas a su región original.

![geo-restore-architecture](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

La restauración geográfica es la solución de recuperación ante desastres de menor costo para SQL Database.  Sin embargo, la restauración a partir de copias de seguridad con redundancia geográfica puede dar lugar a una pérdida de datos de hasta una hora y puede tardar un tiempo considerable, dependiendo del tamaño de cada base de datos. **Para recuperar las aplicaciones con los menores RPO y RTO posibles, use la replicación geográfica en lugar de la restauración geográfica**.

En este tutorial se exploran los flujos de trabajo de la restauración y la repatriación. Aprenderá a:
> [!div class="checklist"]

>* Sincronizar la información de configuración de bases de datos y grupos elásticos con el catálogo de inquilino
>* Configurar un entorno de imagen reflejada en una región de "recuperación", que incluye aplicaciones, servidores y grupos    
>* Recuperar el catálogo y las bases de datos de inquilino mediante _restauración geográfica_
>* Repatriar el catálogo de inquilino y las bases de datos de inquilino modificadas mediante _replicación geográfica_ después de que se resuelva la interrupción
>* Actualizar el catálogo cuando cada base de datos se restaure (o repatríe) para realizar el seguimiento de la ubicación actual de la copia activa de la base de datos de cada inquilino
>* Asegurarse de que la aplicación y la base de datos de inquilino siempre están colocadas en la misma región de Azure para reducir la latencia  
 

Antes de iniciar este tutorial, asegúrese de que se cumplen estos requisitos previos:
* La aplicación SaaS Wingtip Tickets, con una base de datos por inquilino, está implementada. Para implementarla en menos de cinco minutos, consulte el artículo de [implementación y exploración de la aplicación Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md).  
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introducción al patrón de recuperación por restauración geográfica

La recuperación ante desastres es una consideración importante para muchas aplicaciones, ya sea por motivos de cumplimiento o por continuidad empresarial. En caso de una interrupción del servicio prolongada, un plan de recuperación ante desastres bien preparado puede minimizar la interrupción del negocio. Un plan de recuperación ante desastres por restauración geográfica debe cumplir varios objetivos:
 * Reservar toda la capacidad necesaria en la región de recuperación elegida tan pronto como sea posible para asegurarse de que está disponible para restaurar las bases de datos de inquilino.
 * Establecer un entorno de recuperación de imagen reflejada que refleje la configuración original de grupos y bases de datos 
 * Debe ser posible cancelar el proceso de restauración antes de que termine si la región original vuelve a estar en línea.
 * Habilitar rápidamente el aprovisionamiento de inquilinos para que la incorporación de nuevos inquilinos se pueda reiniciar tan pronto como sea posible  
 * Estar optimizado para restaurar los inquilinos en orden de prioridad
 * Estar optimizado para que los inquilinos vuelvan a estar en línea tan pronto como sea posible mediante la realización de los pasos en paralelo cuando sea conveniente
 * Ser resistente a errores, contar con capacidad de reinicio y ser idempotente
 * Repatriar las bases de datos a su región original con un impacto mínimo en los inquilinos cuando se resuelva la interrupción.  

> [!Note]
> La aplicación se recupera en la _región emparejada_ de la región en la que se implementa la aplicación. Para más información, vea [Regiones emparejadas de Azure](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).   



En este tutorial, estos desafíos se abordan con las características de Azure SQL Database y la plataforma de Azure:

* [Plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), para reservar toda la capacidad necesaria tan pronto como sea posible. Las plantillas de Azure Resource Manager se utilizan para aprovisionar una imagen reflejada de los servidores originales y los grupos elásticos en la región de recuperación. También se crean un servidor y un grupo independientes para el aprovisionamiento de nuevos inquilinos. 
* [Biblioteca cliente de Elastic Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) (EDCL), para crear y mantener un catálogo de bases de datos de inquilino.  El catálogo se ha ampliado para incluir información de configuración de grupos y bases de datos actualizada periódicamente.
* [Características de recuperación de administración de particiones](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) de la EDCL para mantener las entradas de ubicación de base de datos en el catálogo durante la recuperación y repatriación.  
* [Restauración geográfica](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), para recuperar el catálogo y las bases de datos de inquilino a partir de copias de seguridad con redundancia geográfica mantenidas automáticamente. 
* [Operaciones de restauración asincrónica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) enviadas en orden de prioridad de inquilino, que el sistema pone en cola para cada grupo y se procesan por lotes de modo que el grupo no se sobrecarga. Estas operaciones se pueden cancelar antes de la ejecución o durante esta en caso de que sea necesario.    
* [Replicación geográfica](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), para repatriar las bases de datos a la región original después de la interrupción. El uso de la replicación geográfica garantiza que no haya pérdida de datos y el impacto sea mínimo en el inquilino.
* [Alias DNS de SQL Server](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), para permitir que el proceso de sincronización de catálogo se conecte al catálogo de activo, independientemente de su ubicación.  

## <a name="get-the-disaster-recovery--scripts"></a>Obtención de scripts de recuperación ante desastres 

Los scripts de recuperación ante desastres de este tutorial están disponibles en el [repositorio de GitHub SaaS Wingtip Tickets, con una base de datos por inquilino](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts de administración de Wingtip Tickets.
> [!IMPORTANT]
> Al igual que todos los scripts de administración de Wingtip vales, los scripts de recuperación ante desastres tienen una calidad de muestra y no para usarse en producción.   

## <a name="review-the-healthy-state-of-the-application"></a>Revisión del estado correcto de la aplicación
Antes de comenzar el proceso de recuperación, revise el estado correcto normal de la aplicación.
1. En el explorador web, abra el centro de eventos de Wingtip Tickets: http://events.wingtip-dpt.&lt;usuario&gt;trafficmanager.net (reemplace &lt;usuario&gt; por el valor de usuario de la implementación).
    * Desplácese hasta la parte inferior de la página y observe el nombre y la ubicación del servidor de catálogo en el pie de página.  La ubicación es la región en la que se implementó la aplicación.    
        SUGERENCIA: Mantenga el puntero del mouse sobre la ubicación para ampliar la pantalla.

    ![Estado correcto del centro de eventos en la región original](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

1. Haga clic en el inquilino Contoso Concert Hall y abra su página de eventos.
    * En el pie de página, observe el nombre del servidor de inquilinos. La ubicación será la misma que la ubicación del servidor del catálogo.

    ![Región original de Contoso Concert Hall](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)    
1. En [Azure Portal](https://portal.azure.com), revise y abra el grupo de recursos en el que se implementó la aplicación.
    * Tenga en cuenta los recursos y la región donde se implementan los componentes de servicio de aplicaciones y los servidores SQL Database.

## <a name="sync-tenant-configuration-into-catalog"></a>Sincronización de la configuración del inquilino en el catálogo

En esta tarea, se inicia un proceso para sincronizar la configuración de los servidores, los grupos elásticos y las bases de datos con el catálogo de inquilino.  Esta información se utiliza posteriormente para configurar un entorno de imagen reflejada en la región de recuperación.

> [!IMPORTANT]
> Para simplificar, el proceso de sincronización y otros procesos de recuperación y repatriación de larga ejecución se implementan en estos ejemplos como trabajos locales de Powershell o sesiones que se ejecutan en el inicio de sesión del usuario de cliente. Los tokens de autenticación que se emiten al iniciar sesión expiran después de varias horas y luego se producirá un error de los trabajos. En un escenario de producción, los procesos de larga ejecución se deben implementar como servicios de Azure confiables de algún tipo, que se ejecutan en una entidad de servicio. Consulte [Uso de Azure PowerShell para crear una entidad de servicio con un certificado](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. En _PowerShell ISE_, abra el archivo ...\Learning Modules\UserConfig.psm1. Reemplace `<resourcegroup>` y `<user>` en las líneas 10 y 11 con el valor usado al implementar la aplicación.  Guarde el archivo.

2. En *PowerShell ISE*, abra el script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.
    *  Durante este tutorial, ejecutará cada uno de los escenarios en este script de PowerShell, por lo que debe mantener este archivo abierto.

3. Configure las opciones siguientes:
    * **$DemoScenario = 1**, iniciar un trabajo en segundo plano que sincroniza la información de configuración del servidor de inquilino y del grupo con el catálogo

3. Presione **F5** para ejecutar el script de sincronización. 
    *  Esta información se utiliza posteriormente para asegurarse de que la recuperación crea una imagen reflejada de los servidores, los grupos y las bases de datos en la región de recuperación.  
![Proceso de sincronización](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Deje la ventana de PowerShell ejecutándose en segundo plano y continúe con el resto del tutorial.

> [!Note]
> El proceso de sincronización conecta con el catálogo mediante un alias DNS. Este alias se modifica durante la restauración y la repatriación para apuntar al catálogo activo. El proceso de sincronización mantiene actualizado el catálogo con los cambios de configuración de las bases de datos o los grupos que se han realizado en la región de recuperación.  Durante la repatriación, estos cambios se aplican a los recursos equivalentes en la región original.

## <a name="geo-restore-recovery-process-overview"></a>Introducción al proceso de recuperación con restauración geográfica

El proceso de recuperación de restauración geográfica implementa la aplicación y restaura las bases de datos a partir de copias de seguridad en la región de recuperación.

El proceso de recuperación hace lo siguiente:

1. Deshabilita el punto de conexión de Traffic Manager de la aplicación web en la región original. La deshabilitación del punto de conexión impide que los usuarios se conecten a la aplicación en un estado no válido si la región original pasa a estar en línea durante la recuperación.

1. Aprovisiona un servidor de catálogo de recuperación en la región de recuperación, restaura geográficamente la base de datos de catálogo y actualiza el alias _activecatalog_ para que apunte al servidor de catálogo restaurado.  
    * El cambio del alias de catálogo garantiza que el proceso de sincronización de catálogo siempre se sincroniza con el catálogo activo.

1. Marca todos los inquilinos existentes en el catálogo de recuperación como sin conexión para evitar el acceso a bases de datos de inquilino antes de que se restauren.

1. Aprovisiona una instancia de la aplicación en la región de recuperación y la configura para utilizar el catálogo restaurado en dicha región.
    * Para mantener la latencia al mínimo, la aplicación de ejemplo se diseñada para que siempre se conecte a una base de datos de inquilino en la misma región.

1. Aprovisiona un servidor y un grupo elástico en los que se aprovisionarán nuevos inquilinos. La creación de estos recursos garantiza que el aprovisionamiento de nuevos inquilinos no interfiere en la recuperación de los inquilinos existentes.

1. Actualiza el alias del nuevo inquilino para que apunte al servidor para nuevas bases de datos de inquilino en la región de recuperación. El cambio de este alias garantiza que las bases de datos para los inquilinos nuevos se aprovisionan en la región de recuperación.
        
1. Aprovisiona los servidores y grupos elásticos en la región de recuperación para restaurar las bases de datos de inquilino. Estos servidores y los grupos son una imagen reflejada de la configuración de la región original.  El aprovisionamiento por adelantado reserva la capacidad necesaria para restaurar todas las bases de datos.
    * Una interrupción en una región puede provocar una presión considerable en los recursos disponibles en la región emparejada.  Si confía en la restauración geográfica para recuperación ante desastres, se recomienda la reserva de recursos rápidamente. Considere el uso de la replicación geográfica si es fundamental que una aplicación se deba recuperar en una región específica. 

1. Habilita el punto de conexión de Traffic Manager de la aplicación web en la región de recuperación. La habilitación de este punto de conexión permite a la aplicación aprovisionar a nuevos inquilinos. En esta fase, los inquilinos existentes siguen estando sin conexión.

1. Envía lotes de las solicitudes para restaurar las bases de datos en orden de prioridad. 
    * Los lotes se organizan de forma que las bases de datos se restauran en paralelo en todos los grupos.  
    * Las solicitudes de restauración se envían de forma asincrónica para que se envíen rápidamente y se pongan en cola para su ejecución en cada grupo.
    * Dado que las solicitudes de restauración se procesan en paralelo en todos los grupos, es mejor distribuir a los inquilinos importantes entre muchos grupos. 

1. Supervisa el servicio de base de datos SQL para determinar cuándo se restauran las bases de datos. Una vez que se restaura una base de datos de inquilino, se marca como en línea en el catálogo y se registra una suma de rowversion para dicha base de datos. 
    * La aplicación puede acceder a las bases de datos de inquilino tan pronto como se marcan como en línea en el catálogo.
    * Una suma de valores rowversion en la base de datos de inquilino se almacena en el catálogo. Esta suma actúa como una huella digital que permite al proceso de repatriación determinar si la base de datos se ha actualizado en la región de recuperación.      

## <a name="run-the-recovery-script"></a>Ejecución del script de recuperación

> [!IMPORTANT]
> Este tutorial restaura las bases de datos a partir de copias de seguridad con redundancia geográfica. Aunque estas copias de seguridad normalmente están disponibles en 10 minutos, puede transcurrir hasta una hora para que estén disponibles. El script se pausará hasta que estén disponibles.  ¡Es hora de tomar un café!

Ahora suponga que hay una interrupción en la región en la que la aplicación se implementa y ejecuta el script de recuperación:

1. En *PowerShell ISE*, en el script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 establezca los siguientes valores:
    * **$DemoScenario = 2**, recuperar la aplicación en una región de recuperación mediante la restauración a partir de copias de seguridad con redundancia geográfica

1. Presione **F5** para ejecutar el script.  
    * El script se abre en una nueva ventana de PowerShell y luego inicia un conjunto de trabajos de PowerShell que se ejecutan en paralelo.  Estos trabajos restauran los servidores, los grupos y las bases de datos en la región de recuperación. 
    * La región de recuperación es la _región emparejada_ asociada a la región de Azure en la que implementó la aplicación. Para más información, vea [Regiones emparejadas de Azure](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions). 

1. Supervise el estado del proceso de recuperación en la ventana de PowerShell.

    ![proceso de recuperación](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

>Para explorar el código para los trabajos de recuperación, revise los scripts de PowerShell en la carpeta ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Revisión del estado de aplicación durante la recuperación
Mientras que el punto de conexión de la aplicación esté deshabilitado en Traffic Manager, la aplicación no estará disponible. El catálogo se restaura y todos los inquilinos se marcan como sin conexión.  A continuación, se habilita el punto de conexión de la aplicación en la región de recuperación y la aplicación vuelve a estar en línea. Aunque la aplicación esté disponible, los inquilinos aparecen sin conexión en el centro de eventos hasta que sus bases de datos se restauran. Es importante diseñar la aplicación para controlar las bases de datos de inquilino sin conexión.

1. Después de recuperar la base de datos de catálogo pero antes de que los inquilinos vuelvan a estar en línea, actualice el centro de eventos de Wingtip Tickets en el explorador web.
    * En el pie de página, observe que el nombre del servidor de catálogo tiene ahora un sufijo _-recovery_ y se encuentra en la región de recuperación.
    * Observe que los inquilinos que aún no se han restaurado se marcan como sin conexión y no se pueden seleccionar.   
 
    ![proceso de recuperación](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Si abre directamente la página de eventos de un inquilino mientras el inquilino está sin conexión, la página muestra una notificación "inquilino sin conexión". Por ejemplo, si Contoso Concert Hall está sin conexión, intente abrir el proceso de recuperación http://events.wingtip-dpt.&lt;usuario&gt;.trafficmanager.net/contosoconcerthall![](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png).

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Aprovisionamiento de un nuevo inquilino en la región de recuperación
Incluso antes de que se restauren las bases de datos de inquilino, puede aprovisionar nuevos inquilinos en la región de recuperación. Más adelante, las nuevas bases de datos de inquilino de la región de recuperación se repatriarán con las bases de datos recuperadas.   

1. En *PowerShell ISE*, en el script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 establezca la siguiente propiedad:
    * **$DemoScenario = 3**, aprovisionar un nuevo inquilino en la región de recuperación

1. Presione **F5** para ejecutar el script. 

1. La página de eventos de Hawthorn Hall se abre en el explorador cuando el aprovisionamiento finaliza. 
    * Tenga en cuenta que la base de datos de Hawthorn Hall se encuentra en la región de recuperación.
    ![Hall Hawthorn aprovisionado en la región de recuperación](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

1. En el explorador, actualice la página del centro de eventos de Wingtip Tickets y verá a Hawthorn Hall incluido. 
    * Si ha aprovisionado a Hawthorn Hall sin esperar a la restauración de los otros inquilinos, es posible que estos todavía estén sin conexión.

## <a name="review-the-recovered-state-of-the-application"></a>Revisión del estado de recuperación de la aplicación

Cuando finaliza el proceso de recuperación, la aplicación y todos los inquilinos están totalmente funcionales en la región de recuperación. 

1. Una vez que la presentación de la ventana de la consola de PowerShell indica que se han recuperado todos los inquilinos, actualice el centro de eventos.  Todos los inquilinos aparecerán en línea, incluido el nuevo inquilino, Hawthorn Hall.

    ![inquilinos nuevos y recuperados en el centro de eventos](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

1. Haga clic en Contoso Concert Hall y abra su página de eventos.
    * En el pie de página, tenga en cuenta que la base de datos se encuentra en el servidor de recuperación ubicado en la región de recuperación.

    ![Contoso en la región de recuperación](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

1. En [Azure Portal](https://portal.azure.com), abra la lista de grupos de recursos.  
    * Observe el grupo de recursos que ha implementado, además del grupo de recursos de recuperación, con el sufijo _-recovery_.  El grupo de recursos de recuperación contiene todos los recursos creados durante el proceso de recuperación, además de nuevos recursos creados durante la interrupción.  

1. Abra el grupo de recursos de recuperación y observe los siguientes elementos:
    * Las versiones de recuperación de los servidores de catálogo y tenants1 con el sufijo _-recovery_.  El catálogo y las bases de datos de inquilino restaurados en estos servidores tienen los nombres utilizados en la región original.

    * El servidor SQL _tenants2-dpt-&lt;user&gt;-recovery_.  Este servidor se utiliza para el aprovisionamiento de nuevos inquilinos durante la interrupción.
    *   La instancia de App Service denominada _events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt_;, que es la instancia de recuperación de la aplicación de eventos. 

    ![Contoso en la región de recuperación](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png)   
    
1. Abra el servidor SQL _tenants2-dpt-&lt;user&gt;-recovery_.  Tenga en cuenta que contiene la base de datos _hawthornhall_ y el grupo elástico _Pool1_.  La base de datos _hawthornhall_ está configurada como una base de datos elástica en el grupo elástico _Pool1_.

## <a name="change-tenant-data"></a>Cambio de datos de inquilino 
En esta tarea, va a actualizar una de las bases de datos de inquilino restaurada. El proceso de repatriación copiará las bases de datos restauradas que se han cambiado en la región original. 

1. En el explorador, busque la lista de eventos para Contoso Concert Hall, desplácese a través de los eventos y tenga en cuenta el último evento _Seriously Strauss_.

1. En *PowerShell ISE*, en el script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 establezca el siguiente valor:
    * **$DemoScenario = 4**, eliminar un evento de un inquilino en la región de recuperación
1. Presione **F5** para ejecutar el script.
1. Actualice la página de eventos de Contoso Concert Hall (http://events.wingtip-dpt.&lt; usuario&gt;.trafficmanager.net/contosoconcerthall) y observe que el evento Seriously Strauss falta.

En este punto del tutorial, ha recuperado la aplicación, que ahora se está ejecutando en la región de recuperación.  Ha aprovisionado a un nuevo inquilino en la región de recuperación y los datos modificados de uno de los inquilinos restaurados.  

> [!Note]
> Otros tutoriales del ejemplo no están diseñados para ejecutarse con la aplicación en el estado de recuperación. Si desea explorar otros tutoriales, asegúrese de repatriar primero la aplicación.

## <a name="repatriation-process-overview"></a>Información general del proceso de repatriación

El proceso de repatriación revierte la aplicación y sus bases de datos a su región original después de que se resuelva una interrupción.

![Repatriación con restauración geográfica](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 


El proceso:

1. Detiene cualquier actividad de restauración en curso y cancela las solicitudes de restauración de base de datos pendientes o sobre la marcha.

1. Se reactiva en las bases de datos de inquilino de la región original que no se han cambiado desde la interrupción.  Esto incluye las bases de datos que aún no se han recuperado y las bases de datos que se recuperaron pero que no se modificaron posteriormente. Las bases de datos reactivadas están exactamente igual que estaban la última vez que los inquilinos accedieron a ellas.

1. Aprovisiona una imagen reflejada del nuevo servidor de inquilinos y el grupo elástico en la región original. Una vez completada esta acción, el alias del nuevo inquilino se actualiza para apuntar a este servidor. La actualización del alias provoca la incorporación de un nuevo inquilino en la región original en lugar de la región de recuperación.

1. Al usar la replicación geográfica, el catálogo se mueve a la región original desde la región de recuperación.

1. Actualiza la configuración del grupo en la región original para que sea coherente con los cambios realizados en la región de recuperación durante la interrupción.

1. Crea los servidores y grupos necesarios para hospedar cualquier base de datos nueva creada durante la interrupción.

1. El uso de la replicación geográfica, repatría las bases de datos de inquilino restauradas que se han actualizado después de la restauración y todas las nuevas bases de datos de inquilino aprovisionadas durante la interrupción. 

1. Limpia los recursos creados en la región de recuperación durante el proceso de restauración.

Para limitar el número de bases de datos de inquilino que necesitan repatriarse, los pasos 1 a 3 se realizan de inmediato.  

El paso 4 solo se realiza si se ha modificado el catálogo en la región de recuperación durante la interrupción. El catálogo se actualiza si se crean nuevos inquilinos o si se modifica la configuración de la base de datos o del grupo en la región de recuperación.

Es importante que la interrupción que el paso 7 cause a los inquilinos sea mínima y no se pierdan datos. Para lograr este objetivo, el proceso usa _replicación geográfica_.

Antes de que cada base de datos se replique geográficamente, se elimina la base de datos correspondiente de la región original. Después, la base de datos de la región de recuperación se replica geográficamente creando una réplica secundaria en la región original. Una vez completada la replicación, el inquilino se marca como sin conexión en el catálogo, lo que rompe todas las conexiones con la base de datos en la región de recuperación. Después, la base de datos conmuta por error, lo que provoca que las transacciones pendientes se procesen en la réplica secundaria, evitando así la pérdida de datos. En la conmutación por error, los roles de base de datos se invierten.  La réplica secundaria de la región original se convierte en la base de datos principal de lectura y escritura, mientras que la base de datos de la región de recuperación se convierte en una réplica secundaria de solo lectura. La entrada del inquilino en el catálogo se actualiza para hacer referencia a la base de datos en la región original y el inquilino se marca como en línea.  En este punto, la repatriación de la base de datos está completada. 

Las aplicaciones se deben escribir con lógica de reintento para garantizar que se vuelven a conectar automáticamente cuando las conexiones se rompen.  Cuando se vuelven a conectar usando el catálogo a la conexión rota, se conectan a la base de datos repatriada en la región original. Aunque la breve desconexión no suele apreciarse, puede optar por repatriar las bases de datos fuera del horario comercial. 

Una vez repatriada una base de datos, se puede eliminar la base de datos secundaria de la región de recuperación. Después, la base de datos de la región original vuelve a basarse en la restauración geográfica para protección de recuperación ante desastres.

En el paso 8, se eliminan los recursos de la región de recuperación, incluidos los grupos y servidores de recuperación.

## <a name="run-the-repatriation-script"></a>Ejecución del script de repatriación
Ahora imaginemos que la interrupción se resuelve y ejecuta el script de repatriación.

Si ha seguido el tutorial, el script reactiva inmediatamente Fabrikam Jazz Club y Dogwood Dojo en la región original porque no han cambiado. Luego repatría el nuevo inquilino, Hawthorn Hall, y Contoso Concert Hall porque se ha modificado. El script también repatría el catálogo, que se actualizó cuando se aprovisionó Hawthorn Hall.
  
1. En *PowerShell ISE*, en el script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1,

1. verifique que el proceso de sincronización del catálogo aún se está ejecutando en su instancia de PowerShell.  Si es necesario, reinícielo estableciendo:
    * **$DemoScenario = 1**, iniciar la sincronización de la información de configuración del servidor de inquilino, el grupo y la base de datos en el catálogo
    * Presione **F5** para ejecutar el script.
1.  Luego, para iniciar el proceso de repatriación, establezca:
    * **$DemoScenario = 5**, repatriar la aplicación en su región original
    * Presione **F5** para ejecutar el script de recuperación en una nueva ventana de PowerShell.  La repatriación tardará varios minutos y se puede supervisar en la ventana de PowerShell.
1. Mientras se ejecuta el script, actualice la página del centro de eventos (http://events.wingtip-dpt.&lt;usuario&gt;.trafficmanager.net)
    * Tenga en cuenta que todos los inquilinos están en línea y accesible a través de este proceso.
1. Haga clic en Fabrikam Jazz Club para abrirlo. Si no modificó este inquilino, observe en el pie de página que el servidor ya se ha revertido al servidor original.
1. Abra o actualice la página de eventos de Contoso Concert Hall y observe en el pie de página que la base de datos todavía está en el servidor _-recovery_ inicialmente.  
1. Actualice la página de eventos de Contoso Concert Hall cuando finalice el proceso de repatriación y observe que la base de datos se encuentra ahora en su región original.
1. Actualice el centro de eventos nuevamente, abra Hawthorn Hall y observe que su base de datos también se encuentra en la región original. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Limpieza de los recursos de la región de recuperación después de la repatriación
Una vez completada la repatriación, es seguro eliminar los recursos de la región de recuperación. 

> [!IMPORTANT]
> Elimine estos recursos rápidamente para detener toda la facturación provocada por los mismos.

El proceso de restauración crea todos los recursos de recuperación en un grupo de recursos de recuperación. El proceso de limpieza eliminará este grupo de recursos y quitará todas las referencias a los recursos del catálogo. 

1. En *PowerShell ISE*, en el script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, establezca:
    * **$DemoScenario = 6**, eliminar recursos obsoletos de la región de recuperación

1. Presione **F5** para ejecutar el script.

Después de limpiar los scripts, la aplicación vuelve donde comenzó.  En este momento, puede volver a ejecutar el script o probar otros tutoriales.

## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Diseño de la aplicación para asegurar que esta y la base de datos están ubicadas en el mismo lugar 
La aplicación está diseñada para conectarse siempre desde una instancia en la misma región que la base de datos de inquilino. Este diseño reduce la latencia entre la aplicación y la base de datos. Esta optimización supone que la interacción entre la aplicación y la base de datos es más activa que la interacción entre el usuario y la aplicación.  

Las bases de datos de inquilino pueden distribuirse en regiones de recuperación y originales durante algún tiempo durante la repatriación.  Para cada base de datos, la aplicación busca la región en la que se encuentra la base de datos mediante una búsqueda de DNS en el nombre del servidor del inquilino. En la base de datos SQL, el nombre del servidor es un alias. El nombre del servidor con alias contiene el nombre de la región. Si la aplicación no se encuentra en la misma región que la base de datos, se redirige a la instancia en la misma región que el servidor de base de datos.  El redireccionamiento a la instancia en la misma región que la base de datos minimiza la latencia entre la aplicación y la base de datos.  

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:
> [!div class="checklist"]

>* Utilizar el catálogo de inquilino para mantener actualizada la información de configuración periódicamente, lo que permite crear un entorno de recuperación de imagen reflejada en otra región
>* Usar la restauración geográfica para recuperar las bases de datos SQL de Azure en la región de recuperación
>* Actualizar el catálogo de inquilino para reflejar las ubicaciones de la base de datos de inquilino restaurada  
>* Utilizar un alias de DNS para habilitar una aplicación para conectarse al catálogo de inquilino sin reconfiguración
>* Utilizar la replicación geográfica para repatriar las bases de datos recuperadas a su región original después de que se resuelva una interrupción

Ahora, pruebe la [recuperación ante desastres para una aplicación SaaS de varios inquilinos mediante la replicación geográfica](saas-dbpertenant-dr-geo-replication.md) para aprender a reducir drásticamente el tiempo necesario para recuperar una aplicación multiinquilino a gran escala mediante la replicación geográfica.

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la aplicación SaaS de Wingtip](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
