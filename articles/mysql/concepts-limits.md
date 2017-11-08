---
title: Limitaciones en Azure Database for MySQL | Microsoft Docs
description: "Describe las limitaciones de la versión preliminar en Azure Database for MySQL."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/26/2017
ms.openlocfilehash: b3fba38cacf5b5abcdea7f0def8c1d39e653f0a8
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Limitaciones en Azure Database for MySQL (versión preliminar)
El servicio Azure Database for MySQL se encuentra en versión preliminar pública. En las secciones siguientes se describen los límites de capacidad y funcionales en el servicio de base de datos. Consulte también las [limitaciones generales](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) que se aplican al motor de base de datos MySQL.

## <a name="service-tier-maximums"></a>Máximos de nivel de servicio
Azure Database for MySQL tiene varios niveles de servicio entre los que puede elegir al crear un servidor. Para obtener más información, consulte la [descripción sobre los elementos disponibles en cada nivel de servicio](concepts-service-tiers.md).  

Hay un número máximo de conexiones, unidades de proceso y almacenamiento en cada nivel de servicio durante la versión preliminar, y son los siguientes: 

|                            |                   |
| :------------------------- | :---------------- |
| **Conexiones máximas**        |                   |
| 50 unidades de proceso básicas     | 50 conexiones    |
| 100 unidades de proceso básicas    | 100 conexiones   |
| 100 unidades de proceso estándar | 200 conexiones   |
| 200 unidades de proceso estándar | 400 conexiones   |
| 400 unidades de proceso estándar | 800 conexiones   |
| 800 unidades de proceso estándar | 1600 conexiones  |
| **Unidades de proceso máximas**      |                   |
| Nivel de servicio Básico         | 100 unidades de proceso |
| Nivel de servicio Estándar      | 800 unidades de proceso |
| **Almacenamiento máximo**            |                   |
| Nivel de servicio Básico         | 1 TB              |
| Nivel de servicio Estándar      | 1 TB              |

Cuando se alcanzan demasiadas conexiones, puede recibir el error siguiente:
> ERROR 1040 (08004): Too many connections

## <a name="preview-functional-limitations"></a>Limitaciones funcionales de la versión preliminar

### <a name="scale-operations"></a>Operaciones de escalado
- El escalado dinámico de servidores entre niveles de servicio no se admite en este momento. Es decir, el cambio entre los niveles de servicio Básico y Estándar.
- El aumento dinámico bajo demanda del almacenamiento en un servidor creado previamente no se admite en este momento.
- La reducción del tamaño de almacenamiento del servidor no se admite.

### <a name="server-version-upgrades"></a>Actualizaciones de la versión de servidor
- La migración automatizada entre las principales versiones del motor de base de datos no se admite en este momento.

### <a name="subscription-management"></a>Administración de suscripciones
- El movimiento dinámico de servidores creados previamente entre grupo de suscripciones y recursos no se admite en este momento.

### <a name="point-in-time-restore"></a>Restauración a un momento dado
- La restauración a un nivel de servicio o unidades de proceso y tamaño de almacenamiento diferente no se admite.
- La restauración a un servidor que se ha quitado no se admite en este momento.

## <a name="next-steps"></a>Pasos siguientes
- [Qué está disponible en cada nivel de servicio](concepts-service-tiers.md)
- [Versiones de base de datos de MySQL admitidas](concepts-supported-versions.md)
