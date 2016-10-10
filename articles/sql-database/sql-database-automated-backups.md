<properties
   pageTitle="Copias de seguridad de SQL Database | Microsoft Azure"
   description="Obtenga más información sobre las copias de seguridad de base de datos integradas de SQL Database, que permiten revertir una base de datos de Azure SQL Database a un momento dado previo o copiarla en una nueva base de datos de una región geográfica (hasta 35 días)."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="carlrab"/>

# Copias de seguridad de SQL Database

SQL Database crea copias de seguridad de base de datos automáticamente como parte de la oferta de servicio, sin necesidad de aceptarlo ni de asumir ningún costo adicional. Utilice las copias de seguridad de base de datos para restaurar una base de datos a un momento dado anterior. En este artículo se explican los aspectos específicos de la característica de copia de seguridad de base de datos en SQL Database.

## ¿Qué es una copia de seguridad de base de datos?  

Una copia de seguridad de base de datos es un archivo que almacena información sobre el estado de la base de datos en un momento dado. SQL Database crea copias de seguridad [completas](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciales](https://msdn.microsoft.com/library/ms175526.aspx) y de [registro de transacciones](https://msdn.microsoft.com/library/ms191429.aspx). Cuando se restaura una base de datos a un momento dado, el servicio averigua qué copias de seguridad deben restaurarse.

Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para más información, consulte [Información general acerca de la continuidad empresarial](sql-database-business-continuity.md).

## Almacenamiento con redundancia geográfica

El servicio de SQL Database almacena archivos de copias de seguridad de base de datos en una cuenta de almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS). La característica RA-GRS de Azure Storage replica los archivos de copias de seguridad en un [centro de datos asociado](../best-practices-availability-paired-regions.md). Esta replicación geográfica garantiza que pueda restaurar una base de datos en caso de que no pueda tener acceso a la copia de seguridad de base de datos desde su región de base de datos principal. En el ejemplo siguiente, SQL Database crea copias de seguridad de base de datos en la región Este de EE. UU. y las almacena en una cuenta de RA-GRS. Luego, Azure Storage realiza una replicación geográfica de las copias de seguridad en un centro de datos asociado de la región Oeste de EE. UU.

![restauración geográfica](./media/sql-database-geo-restore/geo-restore-1.png)

>[AZURE.NOTE] En Azure Storage, el término *replicación* hace referencia a la copia de archivos desde una ubicación a otra. La *replicación de base de datos* de SQL hace referencia a mantener varias bases de datos secundarias sincronizadas con una base de datos principal.

Para más información sobre:
- Almacenamiento con redundancia geográfica, consulte [Replicación de Azure Storage](../storage/storage-redundancy.md).
- Almacenamiento de RA-GRS, consulte [Almacenamiento con redundancia geográfica con acceso de lectura](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## Costos de copias de seguridad de base de datos

Microsoft Azure SQL Database proporciona hasta un 200 % de almacenamiento de base de datos aprovisionado máximo, para que pueda almacenar copias de seguridad, sin costos adicionales. Por ejemplo, si tiene una instancia de base de datos de tipo Estándar con un tamaño de base de datos aprovisionado de 250 GB, tendrá 500 GB para almacenar copias de seguridad sin costos adicionales. Si la base de datos supera el almacenamiento de copias de seguridad proporcionado, puede reducir el período de retención; para ello, póngase en contacto con el soporte técnico de Azure. Otra opción consiste en pagar por un almacenamiento de copias de seguridad adicional que se facturará según la tarifa estándar de almacenamiento redundante geográficamente con acceso de lectura (RA-GRS).

## Programación de copia de seguridad de base de datos

Las bases de datos de niveles Básico, Estándar y Premium están protegidas por copias de seguridad automáticas. Las copias de seguridad completas de base de datos se realizan cada semana, las copias de seguridad diferenciales de base de datos, cada hora, y las copias de seguridad del registro de transacciones, cada cinco minutos. La primera copia de seguridad completa se programa inmediatamente después de la creación de la base de datos. Normalmente, se completa en 30 minutos pero puede tardar más si la base de datos tiene un tamaño considerable. Por ejemplo, la copia de seguridad inicial puede tardar más en una base de datos restaurada o una copia de la base de datos. Después de la primera copia de seguridad completa, todas las copias de seguridad adicionales se programan automáticamente y se administran silenciosamente en segundo plano. El tiempo exacto de las copias de seguridad de base de datos completas y [diferenciales](https://msdn.microsoft.com/library/ms175526.aspx) se determina según se equilibra la carga de trabajo general.

## Período de retención de copias de seguridad de base de datos

Cada copia de seguridad de SQL Database se conserva durante 7 días para el nivel Básico, 35 días para el Estándar y 35 días para el Premium. Para más información sobre las características disponibles con cada nivel de servicio, consulte los [niveles de servicio](sql-database-service-tiers.md).

### ¿Qué le ocurre a mi período de retención del punto de restauración al cambiar a una versión anterior/actualizar por nivel de servicio?

Después de cambiar a un nivel de rendimiento inferior, el período de retención del punto de restauración cambiará inmediatamente al período de retención del nivel de rendimiento de la base de datos actual. Si el nivel de servicio se actualiza, el período de retención se ampliará solo cuando se haya actualizado la base de datos. Por ejemplo, si una base de datos se degrada al nivel Básico, el período de retención cambia de 35 a 7 días. De forma inmediata, ya no estará disponible ningún punto de restauración de más de siete días. Al actualizar una base de datos a Standard o Premium, el período de retención comienza con 7 días y aumenta hasta llegar a 35 días.

### ¿Cuánto dura el período de retención para una base de datos que se ha quitado? 

El período de retención se determina según el nivel de servicio de la base de datos mientras esta existe o según el número de días que exista la base de datos, el menor de estos dos valores.

> [AZURE.IMPORTANT] Si elimina una instancia de servidor de Azure SQL Database, todas las bases de datos que pertenecen a la instancia también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.


## Usos comunes de copias de seguridad de base de datos

El uso principal de las copias de seguridad de base de datos consiste en ser capaces de restaurar una base de datos a un momento dado durante el período de retención. Con una copia de seguridad de base de datos, puede restaurar una base de datos a un momento dado, restaurar una base de datos eliminada al momento en que se eliminó o restaurar una base de datos a otra región geográfica.

- Para obtener información acerca de la restauración de base de datos, consulte cómo [restaurar una base de datos a partir de copias de seguridad automatizadas](sql-database-recovery-using-backups.md).

Puede utilizar una copia de seguridad de base de datos para copiar una base de datos en un servidor lógico de SQL de cualquier región geográfica. La copia es coherente desde un punto de vista transaccional con el SQL Database actual.

- Para obtener información acerca de cómo copiar una base de datos, consulte cómo [copiar una base de datos](sql-database-copy.md).

También puede almacenar copias de seguridad automatizadas más allá del período de retención mediante la creación de una copia de base de datos que [exporta a un archivo BACPAC](sql-database-export.md). Una vez que tenga el archivo BACPAC, puede archivarlo en un almacenamiento a largo plazo y almacenarlo más allá de su período de retención. O bien, utilice el archivo BACPAC para transferir una copia de su base de datos a SQL Server, en una ubicación local o en una máquina virtual (VM) de Azure.

- Para obtener información acerca de cómo archivar una copia de seguridad de base de datos, consulte cómo [copiar una base de datos](sql-database-copy.md)


## Temas relacionados

### Escenarios

- Para obtener una descripción general de la continuidad empresarial, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL de Azure](sql-database-business-continuity.md).

### Características

Para obtener información acerca de cómo:

- Restaurar una copia de seguridad de base de datos, consulte cómo [restaurar una base de datos a partir de las copias de seguridad iniciadas por el servicio](sql-database-recovery-using-backups.md).
- Archivar una copia de seguridad de base de datos, consulte cómo [copiar una base de datos](sql-database-copy.md).
- Conocer opciones de recuperación más rápidas, consulte cómo realizar una [replicación geográfica activa](sql-database-geo-replication-overview.md).

<!-- ### Tasks -->

<!-- ### Tutorials -->

<!---HONumber=AcomDC_0928_2016-->