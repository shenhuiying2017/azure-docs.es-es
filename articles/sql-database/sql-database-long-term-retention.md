---
title: Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo | Microsoft Docs
description: Obtenga información acerca de cómo Azure SQL Database admite almacenar copias de seguridad de bases de datos completas durante un máximo de 10 años.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 51f00984a8f0d750bdb478ae4bc8093adad8108e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo

Muchas aplicaciones tienen finalidades normativas, de conformidad u otras de carácter empresarial que requieren que se conserven copias de seguridad de las bases de datos más allá de entre los 7 y 35 días proporcionados por las [copias de seguridad automáticas](sql-database-automated-backups.md) de Azure SQL Database. Con la característica Retención a largo plazo, puede almacenar copias de seguridad completas de SQL Database en Blob Storage de [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) durante un máximo de 10 años. Así, podrá restaurar cualquier copia de seguridad como si fuera una base de datos nueva.

> [!IMPORTANT]
> La característica Retención a largo plazo se encuentra actualmente en su versión preliminar. Las copias de seguridad existentes almacenadas en el almacén de Azure Recovery Services como parte de la versión preliminar anterior de esta característica se migran al almacenamiento de SQL Azure.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>Funcionamiento de la retención a largo plazo de SQL Database

La retención a largo plazo aprovecha las [copias de seguridad automáticas de SQL Database](sql-database-automated-backups.md) creadas para la restauración a un momento dado (PITR). Puede configurar una directiva de retención a largo plazo para cada base de datos SQL y especificar con qué frecuencia debe copiar las copias de seguridad en el almacenamiento a largo plazo. Para habilitar la flexibilidad, puede definir la directiva mediante una combinación de cuatro parámetros: retención de copia de seguridad semanal (W), retención de copia de seguridad mensual (M), retención de copia de seguridad anual (Y) y la semana del año (WeekOfYear). Si especifica W, se copiará una copia de seguridad cada semana en el almacenamiento a largo plazo. Si especifica M, se copiará una copia de seguridad durante la primera semana de cada mes en el almacenamiento a largo plazo. Si especifica Y, se copiará una copia de seguridad durante la semana especificada en WeekOfYear en el almacenamiento a largo plazo. Cada copia de seguridad se mantendrá en el almacenamiento a largo plazo durante el período especificado por estos parámetros. 

Ejemplos:

-  W=0, M=0, Y=5, WeekOfYear=3

   La tercera copia de seguridad completa de cada año se conservará durante 5 años.

- W=0, M=3, Y=0

   La primera copia de seguridad completa de cada mes se conservará durante 3 meses.

- W=12, M=0, Y=0

   Cada copia de seguridad completa semanal se conservará durante 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada copia de seguridad completa semanal se conservará durante 6 semanas. Excepto la primera copia de seguridad completa de cada mes, que se conservará durante 12 meses. Excepto la copia de seguridad completa realizada en la 16ª semana del año, que se conservará durante 10 años. 

En la tabla siguiente se muestra la cadencia y caducidad de las copias de seguridad a largo plazo para la siguiente directiva:

W=12 semanas (84 días), M=12 meses (365 días), Y=10 años (3650 días), WeekOfYear=15 (semana posterior al 15 de abril)

   ![Ejemplo de LTR](./media/sql-database-long-term-retention/ltr-example.png)


 
Si tuviera que modificar la directiva anterior y establece W=0 (sin copias de seguridad semanales), la cadencia de las copias de seguridad cambiaría tal y como se muestra en la tabla anterior en función de las fechas destacadas. La cantidad de almacenamiento necesaria para mantener estas copias de seguridad se reduciría según corresponda. Nota: Las copias de LTR se crean mediante el servicio de almacenamiento de Azure, por lo que el proceso de copia no tiene ningún impacto en el rendimiento en la base de datos existente.
Para restaurar una base de datos desde el almacenamiento de LTR, puede seleccionar una copia de seguridad específica en función de su marca de tiempo.   La base de datos se puede restaurar en cualquier servidor existente en la misma suscripción que la base de datos original. 

## <a name="configure-long-term-backup-retention"></a>Configuración de la retención de copia de seguridad a largo plazo

Para obtener información sobre cómo configurar la retención a largo plazo mediante Azure Portal o mediante PowerShell, consulte [Configure long-term backup retention](sql-database-long-term-backup-retention-configure.md) (Configuración de la retención de copia de seguridad a largo plazo).

## <a name="next-steps"></a>Pasos siguientes

Dado que las copias de seguridad de bases de datos protegen los datos de eliminaciones o daños accidentales, son una parte esencial de cualquier estrategia de recuperación ante desastres y de continuidad empresarial. Para descubrir otras soluciones de continuidad empresarial de SQL Database, consulte el artículo de [información general sobre la continuidad empresarial](sql-database-business-continuity.md).
