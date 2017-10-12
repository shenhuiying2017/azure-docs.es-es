---
title: "Preguntas más frecuentes sobre Azure SQL Database | Microsoft Docs"
description: "Respuestas a las preguntas más comunes que los clientes preguntan sobre las bases de datos en la nube y Base de datos SQL de Azure, el sistema de administración de bases de datos relacionales (RDBMS) de Microsoft y bases de datos como un servicio en la nube."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sashan;carlrab
ms.openlocfilehash: 6ed02ead07c50b9a49e8868756b6f957d7b49b99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sql-database-faq"></a>Preguntas más frecuentes sobre la Base de datos SQL

## <a name="what-is-the-current-version-of-sql-database"></a>¿Cuál es la versión actual de SQL Database?
La versión actual de SQL Database es V12. Se ha retirado la versión V11.

## <a name="what-is-the-sla-for-sql-database"></a>¿Qué es el Acuerdo de Nivel de Servicio para SQL Database?
Garantizamos que al menos el 99,99 % del tiempo los clientes tendrán conectividad entre su base de datos única o elástica de Microsoft Azure SQL Database Básico, Estándar o Premium y nuestra puerta de enlace de Internet. Para más información, consulte [Acuerdo de Nivel de Servicio](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>¿Cómo se puede restablecer la contraseña para el administrador del servidor?
En [Azure Portal](https://portal.azure.com) haga clic en **Servidores SQL Server**, seleccione el servidor en la lista y luego haga clic en **Restablecer contraseña**.

## <a name="how-do-i-manage-databases-and-logins"></a>¿Cómo se administran las bases de datos e inicios de sesión?
Consulte [Administrar bases de datos e inicios de sesión](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>¿Cómo asegurarse de que solo se permiten las direcciones IP autorizadas para acceder al servidor?
Vea [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>¿Cómo aparece el uso que hago de la Base de datos SQL en la factura?
SQL Database se factura con tarifas por horas predecibles basadas en el nivel de servicio + nivel de rendimiento para bases de datos únicas o en eDTU por grupo elástico. El uso real se procesa y se prorratea por horas, por lo que su factura podría mostrar fracciones de una hora. Por ejemplo, si una base de datos existe durante 12 horas al mes, la factura mostrará un uso de 0,5 días. Además, los niveles de servicio y de nivel de rendimiento, así como las eDTU por grupo se desglosan en la factura para que pueda ver más fácilmente el número de días que usó la base de datos para cada elemento, por mes.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>¿Qué ocurre si una base de datos única está activa durante menos de una hora o usa un nivel de servicio mayor durante menos de una hora?
Se le cobrará por cada hora que una base de datos exista con el mayor nivel de servicio + nivel de rendimiento aplicable durante esa hora, independientemente del uso o de si la base de datos estuvo activa durante menos de una hora. Por ejemplo, si crea una base de datos única y la elimina a los cinco minutos, se le efectuará un cargo de una hora por usar la base de datos. 

Ejemplos

* Si crea una base de datos Básica y la actualiza inmediatamente a Estándar S1, se le cobrará la tarifa Estándar S1 por la primera hora.
* Si actualiza una base de datos de Básica a Premium a las 10:00 p.m. y la actualización finaliza a la 1:35 a.m. del día siguiente, se le cobra según la tarifa de Premium a partir de 1:00 a.m. 
* Si degrada una base de datos de Premium a Básica a las 11:00 a.m. y dicha operación finaliza a las 2:15 p.m., la base de datos se cobra según la tarifa Premium hasta las 3:00 p.m. y después según la tarifa Básica.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>¿Cómo se muestra el uso del grupo elástico en la factura y qué ocurre al cambiar a eDTU por grupo?
Los cargos por grupo elástico se muestran en la factura como DTU elásticas (eDTU) con los incrementos que se indican en eDTU por grupo en la [página de precios](https://azure.microsoft.com/pricing/details/sql-database/). No se realizan cargos por base de datos para los grupos elásticos. Se le cobrará por cada hora que un grupo exista en la eDTU más alta, independientemente del uso o de si el grupo estuvo activo durante menos de una hora. 

Ejemplos

* Si crea un grupo elástico Estándar con 200 eDTU a las 11:18, al agregar cinco bases de datos al grupo se le cobrarán 200 eDTU por la hora completa, empezando a las 11:00. durante el resto del día.
* El día 2, a las 05:05 h, la base de datos 1 empieza a consumir 50 eDTU y se mantiene constante durante todo el día. Las bases de datos 2 a 5 fluctúan entre 0 y 80 eDTU. Durante el día se agregan otras cinco bases de datos con un consumo variable de eDTU durante todo el día. El día 2 es un día completo por el que se facturan 200 eDTU. 
* El día 3, a las 5 a.m. agrega otras 15 bases de datos. El uso de las bases de datos aumenta a lo largo del día hasta el punto en que decide aumentar las eDTU para el grupo de 200 a 400 a las 20:05 h. Los cargos en el nivel de 200 eDTU estaban en vigor hasta las 20:00 h y aumenta a 400 eDTU para las cuatro horas restantes. 

## <a name="elastic-pool-billing-and-pricing-information"></a>Información de precios y facturación de grupos elásticos
Los grupos elásticos se facturan según las características siguientes:

* Los grupos elásticos se facturan desde su creación, incluso si no contiene ninguna base de datos.
* Los grupos elásticos se facturan por horas. Se trata de la misma frecuencia de medición que la de los niveles de rendimiento de las bases de datos únicas.
* Si se cambia el tamaño de un grupo elástico a un nuevo número de eDTU, el grupo no se factura según la nueva cantidad de eDTU hasta que la operación de cambio de tamaño se complete. Esto sigue el mismo patrón que el cambio de nivel de rendimiento de las bases de datos únicas.
* El precio de un grupo elástico se basa en el número de eDTU del grupo. El precio de un grupo elástico es independiente del número y el uso de las bases de datos elásticas de dentro de él.
* El precio se calcula por (número de eDTU de grupo) x (precio unitario por eDTU).

El precio unitario de eDTU de un grupo elástico es mayor que el precio unitario de eDTU para una base de datos única del mismo nivel de servicio. Para obtener información detallada, vea [Precios de bases de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/). 

Para comprender los niveles de servicio y de eDTU, consulte [Opciones de base de datos SQL y el rendimiento](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>¿Cómo se muestra en mi factura el uso de la replicación geográfica activa en un grupo elástico?
A diferencia de las bases de datos únicas, el uso de [replicación geográfica activa](sql-database-geo-replication-overview.md) con bases de datos elásticas no afecta a la facturación.  Solo se cobrarán las eDTU aprovisionadas para cada uno de los grupos (grupo principal y grupo secundario).

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>¿Cómo afectará a mi factura el uso de la característica de auditoría?
La auditoría está integrada en el servicio de SQL Database sin coste adicional y está disponible para las bases de datos de los niveles Básico, Estándar, Premium y Premium RS. Sin embargo, para almacenar los registros de auditoría, la característica de auditoría usa una cuenta de Almacenamiento de Azure y se aplican tarifas por tablas y colas de Almacenamiento de Azure en función del tamaño de su registro de auditoría.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>¿Cómo puedo encontrar el nivel de rendimiento y de nivel de servicio adecuado para las bases de datos únicas y los grupos elásticos?
Dispone de algunas herramientas. 

* Para las bases de datos locales, use el [Asesor de ajuste de tamaño de DTU](http://dtucalculator.azurewebsites.net/) a fin de recomendar las bases de datos y las DTU necesarias y evalúe varias bases de datos para grupos elásticos.
* En caso de que una base de datos única se beneficie de estar en un grupo, el motor inteligente de Azure recomienda un grupo elástico si ve un patrón de uso histórico que lo garantiza. Vea [Supervisión y administración de un grupo elástico con Azure Portal](sql-database-elastic-pool-manage-portal.md). Para obtener información detallada sobre cómo realizar el cálculo por su cuenta, vea [Consideraciones de precio y rendimiento para un grupo elástico](sql-database-elastic-pool.md).
* Para ver si necesita ajustar una base de datos única hacia arriba o hacia abajo, consulte la [guía de rendimiento para bases de datos únicas](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>¿Con qué frecuencia se puede cambiar el nivel de servicio o el nivel de rendimiento de una base de datos única?
Puede cambiar el nivel de servicio (entre Básico, Estándar, Premium y Premium RS) o el nivel de rendimiento dentro de un nivel de servicio (por ejemplo, de S1 a S2) siempre que quiera. En cuanto a las versiones anteriores de las bases de datos, puede cambiar el nivel de servicio o el nivel de rendimiento un total de cuatro veces en un período de 24 horas.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>¿Con qué frecuencia se pueden ajustar las eDTU por cada grupo?
Tan a menudo como desee.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>¿Cuánto tiempo se tarda en cambiar el nivel de servicio o el nivel de rendimiento de una base de datos única o en mover una base de datos dentro y fuera de un grupo elástico?
Para cambiar el nivel de servicio de una base de datos e incorporarla y retirarla de un grupo es necesario copiar la base de datos en la plataforma como operación en segundo plano. Cambiar el nivel de servicio puede tardar desde solo unos minutos hasta varias horas, según el tamaño de las bases de datos. En ambos casos, las bases de datos permanecen en línea y disponibles durante el traslado. Para más información sobre cómo cambiar las bases de datos únicas, consulte [Cambiar el nivel de servicio de una base de datos](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>¿Cuándo se deben usar bases de datos elásticas y cuándo una base de datos única?
En general, los grupos elásticos están diseñados para un [patrón de aplicación de software como servicio (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md) típico, donde hay una base de datos por cliente o inquilino. Adquirir bases de datos individuales y aprovisionarlas en exceso para cubrir todas las variaciones y la demanda máxima no resulta rentable. Con los grupos, el rendimiento colectivo del grupo y el escalado horizontal y reducción vertical de las bases de datos se administra automáticamente. El motor inteligente de Azure recomendará un grupo para las bases de datos si un patrón de uso lo garantiza. Para más información, consulte la [guía de grupos elásticos](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>¿Qué significa tener hasta un 200 % del almacenamiento de base de datos aprovisionado máximo para almacenar copias de seguridad?
El almacenamiento de copia de seguridad es el almacenamiento asociado a las copias de seguridad de base de datos automatizadas que se usan para la [restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore) y la [restauración geográfica](sql-database-recovery-using-backups.md#geo-restore). La Base de datos SQL de Microsoft Azure le proporciona hasta un 200 % de almacenamiento de base de datos aprovisionado máximo, para que pueda almacenar copias de seguridad sin coste adicional. Por ejemplo, si tiene una instancia de base de datos de tipo Estándar con un tamaño de base de datos aprovisionado de 250 GB, se le proporcionarán 500 GB para almacenar sus copias de seguridad sin coste adicional. Si su base de datos excede el tamaño del almacenamiento de copias de seguridad suministrado, puede elegir reducir el período de retención poniéndose en contacto con el Servicio técnico de Azure o pagar el almacenamiento de copias de seguridad extra; si se decide por la segunda opción, esta se facturará según la tarifa de almacenamiento de redundancia geográfica con acceso de lectura (RA-GRS) estándar. Para obtener más información sobre la facturación RA-GRS, consulte los Detalles de los precios de almacenamiento.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Me cambio de Web/Business a los nuevos niveles de servicio, ¿qué necesito saber?
Las bases de datos Web y Business de Azure se han retirado. Los niveles Básico, Estándar, Premium y Premium RS son los que reemplazan las bases de datos web y profesionales. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>¿Qué es un retraso de replicación esperado al replicar geográficamente una base de datos entre dos regiones dentro de la misma ubicación geográfica de Azure?
Actualmente admitimos un RPO de cinco segundos, mientras que el retraso de replicación ha sido inferior a este valor durante el hospedaje de la base de datos secundaria geográficamente en la región emparejada recomendada de Azure y en el mismo nivel de servicio.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>¿Qué es un retraso de replicación esperado cuando la base de datos secundaria geográficamente se crea en la misma región que la base de datos principal?
Según los datos empíricos, no hay demasiada diferencia entre el retraso de replicación dentro de una región y entre regiones cuando se utiliza la región emparejada recomendada de Azure. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Si hay un error de red entre dos regiones, ¿cómo funciona la lógica de reintento cuando está configurada la replicación geográfica?
Si se produce una desconexión, intentamos volver a establecer las conexiones cada 10 segundos.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>¿Qué puedo hacer para tener la seguridad de que se replica un cambio importante en la base de datos principal?
La base de datos secundaria geográficamente es una réplica asincrónica y no intentaremos mantenerla sincronizada completamente con la principal. No obstante, proporcionamos un método para forzar la sincronización a fin de garantizar la replicación de cambios importantes (por ejemplo, actualizaciones de contraseña). La sincronización forzada afecta al rendimiento al bloquear el subproceso de llamada hasta que todas las transacciones confirmadas se replican. Para más información, consulte [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>¿Qué herramientas están disponibles para supervisar el retraso de replicación entre la base de datos principal y la base de datos secundaria geográficamente?
Exponemos el retraso de replicación en tiempo real entre la base de datos principal y la base de datos secundaria geográficamente mediante una DMV. Para más información, consulte [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover una base de datos a un servidor diferente en la misma suscripción
* En [Azure Portal](https://portal.azure.com), haga clic en **Bases de datos SQL**, seleccione una base de datos de la lista y haga clic en **Copiar**. Para más información, consulte [Copia de una Base de datos SQL de Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover una base de datos de una suscripción a otra
* En [Azure Portal](https://portal.azure.com), haga clic en **Servidores SQL Server** y, en la lista, seleccione el servidor que hospeda la base de datos. Haga clic en **Mover**y seleccione los recursos que va a mover y la suscripción a la que los va a mover.
