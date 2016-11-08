---
title: Rendimiento y precio de grupos elásticos de Base de datos SQL
description: Información sobre precios de grupos de bases de datos elásticas.
services: sql-database
documentationcenter: ''
author: srinia
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 05/27/2016
ms.author: srinia
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# Información de precios y facturación de grupos de bases de datos elásticas
Los grupos de bases de datos elásticos se facturan según la características siguientes:

* Los grupos elásticos se facturan desde su creación, incluso si no contiene ninguna base de datos.
* Los grupos elásticos se facturan por horas. Se trata de la misma frecuencia de medición que la de los niveles de rendimiento de las bases de datos únicas.
* Si se cambia el tamaño de un grupo elástico a una nueva cantidad de eDTU, el grupo no se factura según la nueva cantidad de eDTU hasta que la operación de cambio de tamaño se complete. Esto sigue el mismo patrón que el cambio de nivel de rendimiento de las bases de datos independientes.
* El precio de un grupo elástico se basa en el número de eDTU del grupo. El precio de un grupo elástico es independiente del número y el uso de las bases de datos elásticas de dentro de él.
* El precio se calcula por (número de eDTU de grupo) x (precio unitario por eDTU).

El precio unitario de eDTU de un grupo elástico es mayor que el precio unitario de eDTU para una base de datos independiente del mismo nivel de servicio. Para obtener información detallada, vea [Precios de bases de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).

Para comprender los niveles de servicio y de eDTU, consulte [Opciones de base de datos SQL y el rendimiento](sql-database-service-tiers.md).

## Pasos siguientes
* [Creación de un grupo de bases de datos elásticas](sql-database-elastic-pool-create-portal.md)
* [Monitor, manage, and size an elastic database pool (Supervisión,administración y cambio de tamaño de un grupo de bases de datos elásticas)](sql-database-elastic-pool-manage-portal.md)
* [Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio](sql-database-service-tiers.md)
* [PowerShell script for identifying databases suitable for an elastic database pool (Script de PowerShell para identificar bases de datos adecuadas para un grupo de bases de datos elásticas)](sql-database-elastic-pool-database-assessment-powershell.md)

<!---HONumber=AcomDC_0601_2016-->