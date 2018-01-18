---
title: Limitaciones en Azure Database for PostgreSQL | Microsoft Docs
description: Describe las limitaciones en Azure Database for PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: 6dbed1a834d74047178a9f996683d65520047e66
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limitaciones en Azure Database for PostgreSQL
El servicio Azure Database for PostgreSQL se encuentra en versión preliminar pública. En las secciones siguientes se describen los límites de capacidad y funcionales en el servicio de base de datos.

## <a name="service-tier-maximums"></a>Máximos de nivel de servicio
Azure Database for PostgreSQL tiene varios niveles de servicio entre los que puede elegir al crear un servidor. Para obtener más información, consulte la [descripción sobre los elementos disponibles en cada nivel de servicio](concepts-service-tiers.md).  

Hay un número máximo de conexiones, unidades de proceso y almacenamiento en cada nivel de servicio mientras el servicio esté en versión preliminar, y son los siguientes: 

| | |
| :------------------------- | :---------------- |
| **Conexiones máximas**        |                   |
| 50 unidades de proceso básicas     | 55 conexiones    |
| 100 unidades de proceso básicas    | 105 conexiones   |
| 100 unidades de proceso estándar | 150 conexiones   |
| 200 unidades de proceso estándar | 250 conexiones   |
| 400 unidades de proceso estándar | 480 conexiones   |
| 800 unidades de proceso estándar | 950 conexiones   |
| **Unidades de proceso máximas**      |                   |
| Nivel de servicio Básico         | 100 unidades de proceso |
| Nivel de servicio Estándar      | 800 unidades de proceso |
| **Almacenamiento máximo**            |                   |
| Nivel de servicio Básico         | 1 TB              |
| Nivel de servicio Estándar      | 1 TB              |

El sistema de Azure requiere cinco conexiones para supervisar el servidor de Azure Database for PostgreSQL. Cuando se alcanzan demasiadas conexiones, puede recibir el error siguiente:
> FATAL:  sorry, too many clients already


## <a name="preview-functional-limitations"></a>Limitaciones funcionales de la versión preliminar
### <a name="scale-operations"></a>Operaciones de escalado
1.  El escalado dinámico de servidores entre niveles de servicio no se admite en este momento. Es decir, el cambio entre los niveles de servicio Básico y Estándar.
2.  El aumento dinámico bajo demanda del almacenamiento en un servidor creado previamente no se admite en este momento.
3.  La reducción del tamaño de almacenamiento del servidor no se admite.

### <a name="server-version-upgrades"></a>Actualizaciones de la versión de servidor
- La migración automatizada entre las principales versiones del motor de base de datos no se admite en este momento.

### <a name="subscription-management"></a>Administración de suscripciones
- El movimiento dinámico de servidores creados previamente entre grupo de suscripciones y recursos no se admite en este momento.

### <a name="point-in-time-restore"></a>Restauración a un momento dado
1.  La restauración a un nivel de servicio o unidades de proceso y tamaño de almacenamiento diferente no se admite.
2.  La restauración a un servidor que se ha quitado no se admite en este momento.

## <a name="next-steps"></a>pasos siguientes
- Comprenda lo que [hay disponible en cada plan de tarifa](concepts-service-tiers.md).
- Conozca las [versiones de base de datos de PostgreSQL admitidas](concepts-supported-versions.md).
- Revise [cómo hacer una copia de seguridad de un servidor y restaurarlo en Azure Database for PostgreSQL mediante Azure Portal](howto-restore-server-portal.md)
