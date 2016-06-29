<properties
   pageTitle="Continuidad empresarial en la nube: restauración a un momento dado (Base de datos SQL) | Microsoft Azure"
   description="Obtenga información acerca de la restauración a un momento dado, que le permite revertir una Base de datos SQL de Azure a un momento dado anterior (hasta 35 días)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="sstein"/>

# Información general: restauración a un momento dado de Base de datos SQL

> [AZURE.SELECTOR]
- [Información general](sql-database-point-in-time-restore.md)
- [Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Gracias a la restauración a un momento dado, podrá restaurar una base de datos a un momento dado anterior mediante las [copias automatizadas de Base de datos SQL](sql-database-automated-backups.md). Puede restaurar a un momento dado anterior mediante el [Portal de Azure](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) o la [API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

La base de datos se puede restaurar a cualquier nivel de rendimiento o grupo elástico. Debe asegurarse de tener suficiente cuota de DTU en el servidor o grupo, teniendo en cuenta que la restauración crea una nueva base de datos y que el nivel de servicio y el de rendimiento de la base de datos restaurada puede ser diferente del estado actual de la base de datos activa. Una vez finalizada la restauración, la base de datos restaurada es una base de datos normal, totalmente accesible en línea, que se cobra a las tasas normales en función de su nivel de rendimiento y servicio. Si va a restaurar la base de datos con fines de recuperación, puede tratar la base de datos como un sustituto de la base de datos original, o utilizarla para recuperar los datos y después actualizar la base de datos original. Si la base de datos restaurada está pensada como un sustituto de la base de datos original, debe comprobar que el nivel de rendimiento y el de servicio son adecuados y escalar la base de datos si es necesario. Puede cambiar el nombre de la base de datos original y después asignar a la base de datos restaurada el nombre original mediante el comando ALTER DATABASE en T-SQL. Si va a recuperar datos de la base de datos restaurada, debe escribir por separado y ejecutar los scripts de recuperación de datos que necesite. Aunque la operación de restauración puede tardar mucho tiempo en completar la base de datos, será visible en la lista de la base de datos completa. Si elimina la base de datos durante la restauración, se cancelará la operación y no se le cobrará.

## Tiempo de recuperación para una restauración a un momento dado

El tiempo necesario para restaurar una base de datos depende de muchos factores, incluido el tamaño de la base de datos, el momento seleccionado y la cantidad de actividad que debe reproducirse para reconstruir el estado en el momento seleccionado. Para una base de datos muy grande o activa, la restauración puede tardar varias horas. La restauración de una base de datos siempre crea una nueva base de datos en el mismo servidor que la base de datos original, por lo que se debe proporcionar un nuevo nombre a la base de datos restaurada.

## Copia de restauración y restauración frente a Copia, exportación e importación

La restauración a un momento dado es el enfoque recomendado para la recuperación de bases de datos de niveles Básico, Estándar y Premium de la pérdida accidental de datos o daños. La copia de seguridad y restauración es de menor costo (no hay ningún cargo para copias de seguridad a menos que sean excesivas, mientras que se le cobra por la copia de base de datos necesaria para garantizar una exportación coherente transaccional y para almacenar el archivo BACPAC), sin administración (las copias de seguridad son automáticas, mientras que el usuario debe administrar o programar exportaciones) y tiene un RPO mejor (puede restaurar a un momento específico con mucha más granularidad (un minuto), lo que resulta práctico para la copia, exportación e importación) y un mejor tiempo de recuperación (la restauración a partir de copias de seguridad suele ser mucho más rápida que la de importación, lo que implica los pasos para crear el esquema, deshabilitar índices, cargar datos y habilitar índices para cada tabla individualmente). Tenga en cuenta que la copia y exportación sigue siendo la solución recomendada para el archivado a largo plazo, más allá del período de retención.


## Resumen

Las copias de seguridad automáticas y la restauración a un momento dado protegen las bases de datos de daños accidentales en los datos o su eliminación. Esta solución sin administración y sin costo está disponible con todas las bases de datos SQL. La copia de seguridad y restauración ofrece una mejora significativa sobre la solución de copia, exportación o importación para las necesidades de recuperación a corto plazo. Le recomendamos usar la restauración a un momento dado como parte de su estrategia de continuidad del negocio y utilizar la exportación únicamente cuando sea necesario para fines de migración de datos o de archivado a largo plazo.


## Pasos siguientes

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Restauración a un momento dado mediante el Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [Restauración a un momento dado mediante](sql-database-point-in-time-restore-powershell.md)
- [Restauración a un momento dado mediante la API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)


## Recursos adicionales

- [Restauración de una base de datos eliminada](sql-database-restore-deleted-database.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->