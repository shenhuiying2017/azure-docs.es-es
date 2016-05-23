<properties
   pageTitle="Continuidad del negocio en la nube: restauración a un momento dado| Microsoft Azure"
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

# Información general: restauración a un momento dato de Base de datos SQL

La restauración a un momento dado le permite restaurar una base de datos a un momento dado anterior.

El servicio Base de datos SQL de Azure protege todas las bases de datos con una copia de seguridad automatizada que se conserva durante 7 días para el nivel Básico, 14 días para el Estándar y 35 días para el Premium. La restauración a un momento dado está diseñada para recuperar la base de datos a partir de estas copias de seguridad después de un daño o eliminación accidental.

Las copias de seguridad de la base de datos se realizan automáticamente, sin necesidad de aceptarlo ni de asumir ningún cargo adicional. Estas copias de seguridad automatizadas y la restauración a un momento dado proporcionan una forma sin costos y sin administración para proteger las bases de datos de daños de un daño o eliminación accidental, con independencia de la causa.


|Tarea (portal) | PowerShell | REST |
|:--|:--|:--|
| [Restauración de una Base de datos SQL a un momento dado anterior](sql-database-point-in-time-restore-portal.md) | [PowerShell](sql-database-point-in-time-restore-powershell.md) | [REST (createMode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |
| [Restauración de una Base de datos SQL eliminada](sql-database-restore-deleted-database-portal.md) | [PowerShell](sql-database-restore-deleted-database-powershell.md) | [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|



## Descripción de las copias de seguridad automáticas

Las bases de datos de niveles Básico, Estándar y Premium están protegidas por copias de seguridad automáticas. Las copias de seguridad completas se realizan cada semana, las copias de seguridad diferenciales, cada día y las copias de seguridad de registro, cada 5 minutos. La primera copia de seguridad completa se programa inmediatamente después de la creación de la base de datos. Normalmente tarda 30 minutos en completarse, pero puede tardar más. Si una base de datos ya es grande, por ejemplo, si se crea como resultado de una copia de base de datos o de restauración de una base de datos grande, la primera copia de seguridad completa puede tardar más tiempo en completarse. Después de la primera copia de seguridad completa, todas las copias de seguridad adicionales se programan automáticamente y se administran silenciosamente en segundo plano. El tiempo exacto de las copias de seguridad completas y diferenciales viene determinado por el sistema para equilibrar la carga general. Los archivos de copia de seguridad se almacenan en una cuenta de almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) para garantizar la disponibilidad con fines de recuperación ante desastres.


## Tiempo de recuperación para una restauración a un momento dado

El tiempo necesario para restaurar una base de datos depende de muchos factores, incluido el tamaño de la base de datos, el momento seleccionado y la cantidad de actividad que debe reproducirse para reconstruir el estado en el momento seleccionado. Para una base de datos muy grande o activa, la restauración puede tardar varias horas. La restauración de una base de datos siempre crea una nueva base de datos en el mismo servidor que la base de datos original, por lo que se debe proporcionar un nuevo nombre a la base de datos restaurada.


La base de datos se puede restaurar a cualquier nivel de rendimiento o grupo elástico. Debe asegurarse de tener suficiente cuota de DTU en el servidor o grupo, teniendo en cuenta que la restauración crea una nueva base de datos y que el nivel de servicio y el de rendimiento de la base de datos restaurada puede ser diferente del estado actual de la base de datos activa. Una vez finalizada la restauración, la base de datos restaurada es una base de datos normal, totalmente accesible en línea, que se cobra a las tasas normales en función de su nivel de rendimiento y servicio. Si va a restaurar la base de datos con fines de recuperación, puede tratar la base de datos como un sustituto de la base de datos original, o utilizarla para recuperar los datos y después actualizar la base de datos original. Si la base de datos restaurada está pensada como un sustituto de la base de datos original, debe comprobar que el nivel de rendimiento y el de servicio son adecuados y escalar la base de datos si es necesario. Puede cambiar el nombre de la base de datos original y después asignar a la base de datos restaurada el nombre original mediante el comando ALTER DATABASE en T-SQL. Si va a recuperar datos de la base de datos restaurada, debe escribir por separado y ejecutar los scripts de recuperación de datos que necesite. Aunque la operación de restauración puede tardar mucho tiempo en completar la base de datos, será visible en la lista de la base de datos completa. Si elimina la base de datos durante la restauración, se cancelará la operación y no se le cobrará.



## Restauración de una base de datos recientemente eliminada

Si elimina una base de datos de niveles Básico, Estándar o Premium, se conserva la copia de seguridad final para el período de retención normal, lo que le permite restaurar la base de datos hasta el momento de su eliminación.

Para las bases de datos eliminadas, el punto de restauración se fija en la hora de la eliminación de la base de datos. Y, como antes, al restaurar una base de datos eliminada, solo puede restaurarse en el servidor que contenía la base de datos original. Tenga en cuenta esto al eliminar un servidor, ya no podrá restaurar las bases de datos ubicadas anteriormente en ese servidor una vez eliminado.

## Copia de restauración y restauración frente a Copia, exportación e importación

La restauración a un momento dado es el enfoque recomendado para la recuperación de bases de datos de niveles Básico, Estándar y Premium de la pérdida accidental de datos o daños. La copia de seguridad y restauración es de menor costo (no hay ningún cargo para copias de seguridad a menos que sean excesivas, mientras que se le cobra por la copia de base de datos necesaria para garantizar una exportación coherente transaccional y para almacenar el archivo BACPAC), sin administración (las copias de seguridad son automáticas, mientras que el usuario debe administrar o programar exportaciones) y tiene un RPO mejor (puede restaurar a un momento específico con mucha más granularidad (un minuto), lo que resulta práctico para la copia, exportación e importación) y un mejor tiempo de recuperación (la restauración a partir de copias de seguridad suele ser mucho más rápida que la de importación, lo que implica los pasos para crear el esquema, deshabilitar índices, cargar datos y habilitar índices para cada tabla individualmente). Tenga en cuenta que la copia y exportación sigue siendo la solución recomendada para el archivado a largo plazo, más allá del período de retención.


## Resumen

Las copias de seguridad automáticas y la restauración a un momento dado protegen las bases de datos de daños accidentales en los datos o su eliminación. Esta solución sin administración y sin costo está disponible con todas las bases de datos SQL. La copia de seguridad y restauración ofrece una mejora significativa sobre la solución de copia, exportación o importación para las necesidades de recuperación a corto plazo. Le recomendamos usar la restauración a un momento dado como parte de su estrategia de continuidad del negocio y utilizar la exportación únicamente cuando sea necesario para fines de migración de datos o de archivado a largo plazo.



## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0511_2016-->