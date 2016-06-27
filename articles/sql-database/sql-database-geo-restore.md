<properties
   pageTitle="Continuidad del negocio en la nube: restauración geográfica| Microsoft Azure"
   description="Obtenga información acerca de cómo la Base de datos SQL de Azure permite la continuidad del negocio en la nube y la recuperación de la base de datos, y ayuda a que las aplicaciones críticas de la nube se sigan ejecutando."
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

# Información general: restauración geográfica de Base de datos SQL

> [AZURE.SELECTOR]
- [Información general](sql-database-geo-restore.md)
- [Portal de Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

La restauración geográfica permite restaurar una base de datos SQL desde la copia de seguridad diaria más reciente y se habilita automáticamente en todos los niveles de servicio sin coste adicional. La restauración geográfica usa una copia de seguridad con redundancia geográfica como fuente y se puede usar para recuperar una base de datos, aunque no se pueda acceder a dicha base de datos o al centro de datos debido a una interrupción.

La iniciación de la restauración geográfica crea una nueva base de datos SQL que se puede crear en cualquier servidor en cualquier región de Azure.

> [AZURE.NOTE] También puede usar [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


La restauración geográfica proporciona la opción de recuperación predeterminada cuando la base de datos no está disponible debido a una incidencia en la región en la que se hospeda la base de datos. La base de datos se puede crear en cualquier servidor y en cualquier región de Azure. La restauración geográfica se basa en las [copias de seguridad automatizadas de bases de datos](sql-database-automated-backups.md) del almacenamiento con redundancia geográfica de Azure y realiza el proceso de restauración a partir de la copia de seguridad con replicación geográfica. Por tanto, es resistente a las interrupciones de almacenamiento que se produzcan en la región primaria.



## Detalles de la restauración geográfica

La restauración geográfica usa la misma tecnología como la restauración a un momento dado, pero hay una diferencia importante. Restaura la base de datos a partir de una copia de la última copia de seguridad diaria en el almacenamiento de blobs con replicación geográfica (RA-GRS). Para cada base de datos activa, el servicio mantiene una cadena de copias de seguridad que incluye una copia de seguridad completa semanal, varias copias de seguridad diferenciales diarias y registros de transacciones que se guardan cada 5 minutos. Estos blobs tienen replicación geográfica, lo que garantiza que las copias de seguridad diarias están disponibles, incluso después de un error masivo en la región principal. A continuación, se muestra la replicación geográfica de las copias de seguridad diarias y semanales copiadas en los contenedores de almacenamiento.

![restauración geográfica](./media/sql-database-geo-restore/geo-restore-1.png)


Si un incidente a gran escala en los resultados de una región, genera la falta de disponibilidad de una aplicación de base de datos, puede utilizar la restauración geográfica para restaurar una base de datos desde la copia de seguridad más reciente en un servidor de otra región. Todas las copias de seguridad tienen replicación geográfica y pueden tener un retraso entre el momento en que se realiza la copia de seguridad y el momento en que se realiza la replicación geográfica en el blob de Azure de otra región. Este retraso puede ser de hasta una hora, con el fin de que, en caso de desastre, puede haber una pérdida de datos de hasta una hora, es decir, un RPO de hasta una hora. A continuación se muestra la restauración de la base de datos desde la última copia de seguridad diaria.


![restauración geográfica](./media/sql-database-geo-restore/geo-restore-2.png)



## Tiempo de recuperación de una restauración geográfica

El tiempo de recuperación se ve afectado por diversos factores: el tamaño y nivel de rendimiento de la base de datos y el número de solicitudes simultáneas de restauración que se procesan en la región de destino. Si hay una interrupción prolongada en una región, es posible que haya un gran número de solicitudes de restauración geográfica procesadas por parte de otras regiones. Si hay un gran número de solicitudes, puede aumentar el tiempo de recuperación de las bases de datos de dicha región.


## Resumen

Aunque la restauración geográfica está disponible en todos los niveles de servicio, es la más básica de las soluciones de recuperación ante desastres disponibles en Base de datos de SQL con el RPO y el tiempo de recuperación estimado (ERT) más largos. En las bases de datos Basic con un tamaño máximo de 2 GB, la restauración geográfica proporciona una solución de recuperación ante desastres razonable con un ERT de 12 horas. En bases de datos Standard o Premium más grandes, si se desean tiempos de recuperación más cortos o reducir la probabilidad de pérdida de datos, sería conveniente considerar la posibilidad de usar la replicación geográfica activa. La replicación geográfica activa ofrece RPO y ERT mucho menores, ya que solo es necesario iniciar una conmutación por error en un elemento secundario replicado continuamente. Para obtener más información, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).

## Pasos siguientes

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Restauración geográfica mediante el Portal de Azure](sql-database-geo-restore-portal.md)
- [Restauración geográfica mediante PowerShell](sql-database-geo-restore-powershell.md)

## Recursos adicionales

- [P+F de BCDR de Base de datos SQL](sql-database-bcdr-faq.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Overview: SQL Database Point-in-Time Restore (Información general: Restauración a un momento dado de Base de datos SQL)](sql-database-point-in-time-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->