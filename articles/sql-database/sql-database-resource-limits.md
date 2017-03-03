---
title: "Límites de recursos de Azure SQL Database | Microsoft Docs"
description: "En esta página se describen algunos límites de recursos comunes para Base de datos SQL de Azure."
services: sql-database
documentationcenter: na
author: janeng
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/24/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: f2e48e290f59efb5ab6271b7b2882ca8ea8887a6
ms.openlocfilehash: 15fd64f21eb20ae412f4d0cf1930e99e2e9fc62b
ms.lasthandoff: 02/09/2017


---
# <a name="azure-sql-database-resource-limits"></a>Límites de recursos de Base de datos SQL
## <a name="overview"></a>Información general
Azure SQL Database administra los recursos disponibles para una base de datos mediante dos mecanismos diferentes: **Resources Governance** (Regulación de recursos) y **Enforcement of Limits** (Aplicación de límites). Este tema explica estas dos áreas principales de la administración de recursos.

## <a name="resource-governance"></a>Regulador de recursos
Uno de los objetivos de diseño de los niveles de servicio Básico, Estándar y Premium es que la instancia de Azure SQL Database se comporte como si la base de datos se ejecutase en su propio equipo, aislada de otras bases de datos. La regulación de recursos emula este comportamiento. Si el uso de recursos agregados alcanza el máximo de CPU disponible, memoria, E/S de registro y los recursos de E/S de datos asignados a la base de datos, el regulador de recursos pone en cola las consultas en ejecución y asignará recursos a las consultas en cola a medida que se vayan liberando.

Como en un equipo dedicado, el uso de todos los recursos disponibles, provoca una ejecución más larga de las consultas en ejecución, lo que a su vez puede provocar la finalización del tiempo de espera de los comandos en el cliente. Las aplicaciones con lógica de reintento agresivo y aplicaciones que ejecutan consultas en la base de datos con una frecuencia alta, pueden encontrar mensajes de error al intentar ejecutar nuevas consultas cuando se ha alcanzado el límite de solicitudes simultáneas.

### <a name="recommendations"></a>Recomendaciones:
Supervisar el uso de recursos y los tiempos de respuesta promedio de las consultas cuando se aproxime al uso máximo de una base de datos. Cuando encuentre latencias de consulta más elevadas generalmente tienen tres opciones:

1. Reducir el número de solicitudes entrantes a la base de datos para evitar que el tiempo de espera finalice y las solicitudes se apilen.
2. Asignar un nivel de rendimiento superior a la base de datos.
3. Optimizar las consultas para reducir el uso de recursos de cada consulta. Para obtener más información, consulte la sección de sugerencias/optimizació de consulta en el artículo de Guía de rendimiento de Base de datos SQL de Azure.

## <a name="enforcement-of-limits"></a>Aplicación de límites
Al denegar nuevas solicitudes cuando se alcanzan los límites, se aplican recursos otros recursos diferentes de la CPU, memoria, registro de E/S y datos de E/S. Cuando una base de datos alcanza el límite de tamaño máximo configurado, inserciones y actualizaciones que aumentan el tamaño de los datos no funcione correctamente, mientras selecciona y elimina continuarán funcionando. Los clientes reciben un [mensaje de error](sql-database-develop-error-messages.md) dependiendo del límite que alcance.

Por ejemplo, el número de conexiones a una base de datos SQL y el número de solicitudes simultáneas que se pueden procesar está restringido. La Base de datos SQL permite que el número de conexiones a la base de datos sea mayor que el número de solicitudes simultáneas para admitir la agrupación de conexiones. Mientras que el número de conexiones que están disponibles fácilmente puede controlarse mediante la aplicación, el número de solicitudes paralelas suele ser más difícil de calcular y controlar. Los errores se producen especialmente durante las cargas máximas cuando la aplicación envía demasiadas solicitudes o la base de datos alcanza su límite de recursos y empieza a amontonar los subprocesos de trabajo debido a consultas de ejecución más largas.

## <a name="service-tiers-and-performance-levels"></a>Niveles de servicio y niveles de rendimiento
Existen niveles de servicio y rendimiento para los grupos elásticos y bases de datos únicas.

### <a name="single-databases"></a>Bases de datos únicas
Para una base de datos única, los límites de una base de datos se definen mediante el nivel de servicio de base de datos el nivel de rendimiento. En la tabla siguiente se describen las características de las bases de datos de nivel Básico, Estándar y Premium en distintos niveles de rendimiento.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Grupos elásticos
[Grupos elásticos](sql-database-elastic-pool.md) comparten recursos entre bases de datos del grupo. En la tabla siguiente se describen las características de los grupos elásticos de nivel Básico, Estándar y Premium.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Para obtener una definición expandida de cada recurso enumerado en las tablas anteriores, consulte las descripciones en [Límites y capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Para obtener una descripción general de los niveles de servicio, consulte [Niveles de servicio y niveles de rendimiento de Base de datos SQL de Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Otros límites de Base de datos SQL
| Ámbito | Límite | Description |
| --- | --- | --- |
| Bases de datos que usan exportación automatizada por suscripción |10 |La exportación automatizada le permite crear una programación personalizada para realizar copias de seguridad de las bases de datos SQL. La versión preliminar de esta característica finalizará el 1 de marzo de 2017.  |
| Base de datos por servidor |Hasta 5000 |Se permiten hasta 5000 bases de datos por servidor en servidores V12. |
| DTU por servidor |45000 |Hay disponibles&45; 000 DTU por servidor en servidores V12 para el aprovisionamiento de las bases de datos, los grupos elásticos y los almacenes de datos. |

> [!IMPORTANT]
> La exportación automática de Azure SQL Database Automated Export se encuentra ahora en versión preliminar y se retirará el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, ya no podrá configurar la exportación automatizada en cualquier instancia de SQL Database. Los trabajos de exportación automatizada existentes continuarán funcionando hasta el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, podrá usar la [retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md) o [Azure Automation](../automation/automation-intro.md) para archivar las bases de datos SQL periódicamente mediante PowerShell de acuerdo con la programación que elija. Para obtener un script de ejemplo, puede descargar [este que está disponible en Github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 
>


## <a name="resources"></a>Recursos
[Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)

[Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](sql-database-service-tiers.md)

[Mensajes de error para los programas de cliente de base de datos SQL](sql-database-develop-error-messages.md)


