<properties
   pageTitle="Continuidad del negocio en la nube: copia de seguridad integrada (Base de datos SQL) | Microsoft Azure"
   description="Obtenga más información sobre las copias de seguridad integradas de Base de datos SQL que permiten revertir una Base de datos SQL de Azure a un momento dado previo o copiarla en una nueva base de datos en una región geográfica (hasta 35 días)."
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
   ms.workload="sqldb-features"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Información general: copias de seguridad automatizadas de Base de datos SQL

El servicio Base de datos SQL de Azure protege todas las bases de datos con una copia de seguridad automatizada que se conserva durante 7 días para el nivel Básico, 14 días para el Estándar y 35 días para el Premium. Puede utilizar estas copias de seguridad automatizadas para realizar restauraciones a un momento dado y restaurar una base de datos eliminada tras la eliminación o el daño de los datos por accidente.

Las copias de seguridad de la base de datos se realizan automáticamente, sin necesidad de aceptarlo ni de asumir ningún cargo adicional. Estas copias de seguridad automatizadas y la restauración a un momento dado proporcionan una forma sin costos y sin administración para proteger las bases de datos de daños de un daño o eliminación accidental, con independencia de la causa.

## Costos de la copia de seguridad automatizada

La Base de datos SQL de Microsoft Azure le proporciona hasta un 200 % de almacenamiento de base de datos aprovisionado máximo, para que pueda almacenar copias de seguridad sin coste adicional. Por ejemplo, si tiene una instancia de base de datos de tipo Estándar con un tamaño de base de datos aprovisionado de 250 GB, se le proporcionarán 500 GB para almacenar sus copias de seguridad sin coste adicional. Si su base de datos excede el tamaño del almacenamiento de copias de seguridad suministrado, puede elegir reducir el período de retención poniéndose en contacto con el Servicio técnico de Azure o pagar el almacenamiento de copias de seguridad extra; si se decide por la segunda opción, esta se facturará según la tarifa de almacenamiento de redundancia geográfica con acceso de lectura (RA-GRS) estándar.

## Detalles de la copia de seguridad automatizada

Las bases de datos de niveles Básico, Estándar y Premium están protegidas por copias de seguridad automáticas. Las copias de seguridad completas se realizan cada semana, las copias de seguridad diferenciales, cada día y las copias de seguridad de registro, cada 5 minutos. La primera copia de seguridad completa se programa inmediatamente después de la creación de la base de datos. Normalmente tarda 30 minutos en completarse, pero puede tardar más. Si una base de datos ya es grande, por ejemplo, si se crea como resultado de una copia de base de datos o de restauración de una base de datos grande, la primera copia de seguridad completa puede tardar más tiempo en completarse. Después de la primera copia de seguridad completa, todas las copias de seguridad adicionales se programan automáticamente y se administran silenciosamente en segundo plano. El tiempo exacto de las copias de seguridad completas y diferenciales viene determinado por el sistema para equilibrar la carga general. Los archivos de copia de seguridad se almacenan en una cuenta de almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) para garantizar la disponibilidad con fines de recuperación ante desastres.

## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->