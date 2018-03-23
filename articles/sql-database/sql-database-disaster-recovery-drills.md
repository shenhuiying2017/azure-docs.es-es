---
title: Maniobras de recuperación ante desastres de SQL Database | Microsoft Docs
description: Obtenga información de instrucciones y procedimientos recomendados para usar Azure SQL Database en la recuperación ante desastres.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 10/20/2016
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 10a3891c51d20a26b946847d47bb712d97fa3c51
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="performing-disaster-recovery-drill"></a>Obtención de detalles de la recuperación ante desastres
Se recomienda validar periódicamente el flujo de trabajo de preparación de la aplicación para la recuperación. Comprobar el comportamiento de la aplicación y las implicaciones de las pérdidas de datos o de las interrupciones que conlleva la conmutación por error es una buena práctica de ingeniería. También es un requisito de la mayoría de estándares del sector como parte de la certificación de continuidad del negocio.

Obtener los detalles de una recuperación ante desastres implica lo siguiente:

* Simular la interrupción del nivel de datos.
* Realizar la recuperación.
* Validar la integridad de la aplicación tras la recuperación.

Dependiendo de cómo [diseñó su aplicación para la continuidad del negocio](sql-database-business-continuity.md), el flujo de trabajo para la ejecución del proceso de obtención de detalles puede variar. En este artículo se describen los procedimientos recomendados de obtención de detalles de la recuperación ante desastres en el contexto de Azure SQL Database.

## <a name="geo-restore"></a>Restauración geográfica
Para evitar la posible pérdida de datos durante la obtención de detalles de la recuperación ante desastres, obtenga los detalles con un entorno de prueba mediante la creación de una copia del entorno de producción y utilizando dicho entorno para comprobar el flujo de trabajo de conmutación por error de la aplicación.

#### <a name="outage-simulation"></a>Simulación de interrupción
Puede simular la interrupción mediante el cambio de nombre de la base de datos de origen. Tenga en cuenta que esto puede producir un error de conectividad de la aplicación.

#### <a name="recovery"></a>Recuperación
* Realice la restauración geográfica de la base de datos en un servidor diferente, tal como se describe [aquí](sql-database-disaster-recovery.md).
* Cambie la configuración de la aplicación para conectarse a las bases de datos recuperadas y siga las directrices de la guía [Configuración de una base de datos recuperada](sql-database-disaster-recovery.md) para completar la recuperación.

#### <a name="validation"></a>Validación
* Complete la obtención de detalles mediante la comprobación de la integridad de la aplicación posterior a la recuperación (es decir, las cadenas de conexión, los inicios de sesión, la comprobación de funciones básicas u otras validaciones que formen parte de los procedimientos estándar de validación de aplicaciones).

## <a name="failover-groups"></a>Grupos de conmutación por error
En una base de datos protegida mediante grupos de conmutación por error, el ejercicio de obtención de detalles incluirá la conmutación por error planeada en el servidor secundario. La conmutación por error planeada garantiza que las bases de datos principal y secundaria del grupo de conmutación por error permanezcan sincronizadas cuando se cambian los roles. A diferencia de la conmutación por error no planeada, esta operación no provocará la pérdida de datos, por lo que la obtención de detalles se puede realizar en el entorno de producción.

#### <a name="outage-simulation"></a>Simulación de interrupción
Para simular una interrupción puede deshabilitar la aplicación web o la máquina virtual conectada a la base de datos. Esto provocará errores de conectividad de los clientes web.

#### <a name="recovery"></a>Recuperación
* Asegúrese de que la configuración de la aplicación en la región de recuperación ante desastres apunta a la base de datos secundaria anterior, que se convierte en la base de datos principal nueva y totalmente accesible.
* Inicie la [conmutación por error planeada](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) del grupo de conmutación por error desde el servidor secundario.
* Siga las instrucciones de la guía [Configurar una base de datos recuperada](sql-database-disaster-recovery.md) para completar la recuperación.

#### <a name="validation"></a>Validación
Complete la obtención de detalles mediante la comprobación de la integridad de la aplicación posterior a la recuperación (incluida la conectividad, la comprobación de funciones básicas u otras validaciones necesarias para los procedimientos estándar de validación de aplicaciones).

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre los escenarios de continuidad empresarial, consulte el artículo sobre los [escenarios de continuidad](sql-database-business-continuity.md).
* Para obtener información sobre las copias de seguridad automatizadas de Azure SQL Database, consulte [Copias de seguridad automatizadas de SQL Database](sql-database-automated-backups.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recover an Azure SQL database using automated database backups](sql-database-recovery-using-backups.md)(Recuperación de una base de datos SQL de Azure mediante copias de seguridad de base de datos automatizadas).
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa y grupos de conmutación por error](sql-database-geo-replication-overview.md).  
