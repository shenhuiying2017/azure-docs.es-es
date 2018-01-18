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
ms.date: 01/11/2018
ms.openlocfilehash: f0f9a10f987f19d8ae77a07038cffe23446856fd
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitaciones en Azure Database for MySQL
El servicio Azure Database for MySQL se encuentra en versión preliminar pública. En las siguientes secciones se describen la capacidad, la compatibilidad del motor de almacenamiento, la compatibilidad de los privilegios, la compatibilidad de las instrucciones de manipulación de datos y los límites funcionales del servicio de base de datos. Consulte también las [limitaciones generales](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) que se aplican al motor de base de datos MySQL.

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

## <a name="storage-engine-support"></a>Compatibilidad del motor de almacenamiento

### <a name="supported"></a>Compatible
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>No compatible
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Compatibilidad de los privilegios

### <a name="unsupported"></a>No compatible
- Rol DBA: muchos parámetros de servidor y valores de configuración pueden reducir por error el rendimiento del servidor o invalidar las propiedades ACID del sistema de administración de bases de datos. Por lo tanto, para mantener la integridad del servicio y SLA en un nivel de producto, no se expone el rol DBA a los clientes. La cuenta de usuario predeterminada, que se crea a la vez que las instancias de base de datos, permite a los clientes realizar la mayoría de las instrucciones DDL y DML en la instancia de base de datos administrados. 
- Privilegio SUPER: del mismo modo, los [privilegios SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) también están restringidos.

## <a name="data-manipulation-statement-support"></a>Compatibilidad de las instrucciones de manipulación de datos

### <a name="supported"></a>Compatible
- LOAD DATA INFILE: compatible, pero debe especificar el parámetro [LOCAL] que se dirige a una ruta de acceso UNC (Azure Storage montado mediante XSMB).

### <a name="unsupported"></a>No compatible
- SELECT ... INTO OUTFILE

## <a name="preview-functional-limitations"></a>Limitaciones funcionales de la versión preliminar

### <a name="scale-operations"></a>Operaciones de escalado
- El escalado dinámico de servidores entre niveles de servicio no se admite en este momento. Es decir, el cambio entre los niveles de servicio Básico y Estándar.
- El aumento dinámico bajo demanda del almacenamiento en un servidor creado previamente no se admite en este momento.
- La reducción del tamaño de almacenamiento del servidor no se admite.

### <a name="server-version-upgrades"></a>Actualizaciones de la versión de servidor
- La migración automatizada entre las principales versiones del motor de base de datos no se admite en este momento.

### <a name="point-in-time-restore"></a>Restauración a un momento dado
- La restauración a un nivel de servicio o unidades de proceso y tamaño de almacenamiento diferente no se admite.
- La restauración a un servidor que se ha eliminado no se admite en este momento.

## <a name="functional-limitations"></a>Limitaciones funcionales

### <a name="subscription-management"></a>Administración de suscripciones
- El movimiento dinámico de servidores creados previamente entre grupo de suscripciones y recursos no se admite en este momento.

## <a name="current-known-issues"></a>Problemas conocidos actualmente
- Instancia del servidor MySQL muestra una versión de servidor errónea después de establecer la conexión. Para obtener las versiones de la instancia de servidor correctas, utilice comando select version(); del símbolo del sistema de MySQL.

## <a name="next-steps"></a>pasos siguientes
- [Qué está disponible en cada nivel de servicio](concepts-service-tiers.md)
- [Versiones de base de datos de MySQL admitidas](concepts-supported-versions.md)
