---
title: Limitaciones en Azure Database for PostgreSQL
description: "En este artículo se describen las limitaciones de Azure Database for PostgreSQL como el número de conexiones o las opciones de motor de almacenamiento."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ba05308039e9743dd207333476e61a45c0ca166a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limitaciones en Azure Database for PostgreSQL
El servicio Azure Database for PostgreSQL se encuentra en versión preliminar pública. En las secciones siguientes se describen los límites de capacidad y funcionales en el servicio de base de datos.

## <a name="pricing-tier-maximums"></a>Máximos de planes de tarifa
Azure Database for PostgreSQL tiene varios planes de tarifa entre los que puede elegir al crear un servidor. Para más información, consulte el artículo de [planes de tarifa de Azure Database for PostgreSQL](concepts-pricing-tiers.md).  

Hay un número máximo de conexiones, unidades de proceso y almacenamiento en cada plan de tarifa, y son los siguientes: 

|Nivel de precios| Generación de procesos| Núcleos virtuales| Conexiones máximas |
|---|---|---|---|
|Básica| Gen 4| 1| 50 |
|Básica| Gen 4| 2| 100 |
|Básica| Gen 5| 1| 50 |
|Básica| Gen 5| 2| 100 |
|Uso general| Gen 4| 2| 150|
|Uso general| Gen 4| 4| 250|
|Uso general| Gen 4| 8| 480|
|Uso general| Gen 4| 16| 950|
|Uso general| Gen 4| 32| 1.500|
|Uso general| Gen 5| 2| 150|
|Uso general| Gen 5| 4| 250|
|Uso general| Gen 5| 8| 480|
|Uso general| Gen 5| 16| 950|
|Uso general| Gen 5| 32| 1.500|
|Memoria optimizada| Gen 5| 2| 150|
|Memoria optimizada| Gen 5| 4| 250|
|Memoria optimizada| Gen 5| 8| 480|
|Memoria optimizada| Gen 5| 16| 950|
|Memoria optimizada| Gen 5| 32| 1900|

Si las conexiones superan el límite, puede que reciba el error siguiente:
> FATAL:  sorry, too many clients already

El sistema de Azure requiere cinco conexiones para supervisar el servidor de Azure Database for PostgreSQL. 

## <a name="functional-limitations"></a>Limitaciones funcionales
### <a name="scale-operations"></a>Operaciones de escalado
1.  El escalado dinámico de servidores entre planes de tarifa no se admite en este momento. Es decir, no se admite el cambio entre los planes Básico, Uso general y Memoria optimizada.
2.  La reducción del tamaño de almacenamiento del servidor no se admite actualmente.

### <a name="server-version-upgrades"></a>Actualizaciones de la versión de servidor
- La migración automatizada entre las principales versiones del motor de base de datos no se admite en este momento.

### <a name="subscription-management"></a>Administración de suscripciones
- El movimiento dinámico de servidores entre grupos de suscripciones y recursos no se admite en este momento.

### <a name="point-in-time-restore-pitr"></a>Restauración a un momento dado (PITR)
1.  Al usar la característica PITR, el nuevo servidor se crea con la misma configuración que el servidor en el que se basa.
2.  La restauración a un servidor que se ha eliminado no se admite en este momento.

## <a name="next-steps"></a>pasos siguientes
- Comprenda lo que [hay disponible en cada plan de tarifa](concepts-pricing-tiers.md).
- Conozca las [versiones de base de datos de PostgreSQL admitidas](concepts-supported-versions.md).
- Revise [cómo hacer una copia de seguridad de un servidor y restaurarlo en Azure Database for PostgreSQL mediante Azure Portal](howto-restore-server-portal.md).
