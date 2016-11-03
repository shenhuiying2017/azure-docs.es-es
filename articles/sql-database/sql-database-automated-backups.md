<properties
   pageTitle="Más información sobre copias de seguridad de SQL Database | Microsoft Azure" 
   description="Aprenda sobre las copias de seguridad de base de datos integradas de SQL Database, que permiten restaurar una instancia de Azure SQL Database a un momento dado previo o copiar una base de datos en una nueva base de datos de una región geográfica (hasta 35 días)."
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




<!-- # H1 Title

 H1 title should answer the question "What is in this topic?" Write the title in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
-->

# <a name="learn-about-sql-database-backups"></a>Más información sobre las copias de seguridad de SQL Database

<!-- Introduction

1. Sentence #1 begins with "Learn about ..." and gives the scope of what the article will cover.
2. Sentence #2 explains the key capability or selling point of the feature.
3. Sentence #3 begins with "Use this feature to ..." and gives a common use case.

-->

SQL Database crea una copia de seguridad local de la base de datos cada cinco minutos y usa almacenamiento con redundancia geográfica con acceso de lectura de Azure (RA-GRS) para copiar algunas de las copias de seguridad de base de datos en otra región geográfica. Estas copias de seguridad tienen lugar **automáticamente y sin cargos adicionales**. Use copias de seguridad locales de la base de datos para [restaurar una base de datos a un punto en el tiempo](sql-database-point-in-time-restore-portal.md) en el mismo servidor. Use copias de seguridad con redundancia geográfica para [restaurar la base de datos en una región geográfica distinta](sql-database-geo-restore-portal.md).  

>[AZURE.NOTE] Las copias de seguridad locales y con redundancia geográfica se producen automáticamente. No es necesario hacer nada para que tengan lugar y no hay cargos adicionales. 

En el diagrama siguiente, se ejecuta SQL Database en la región del este de EE. UU. Se crea una copia de seguridad de la base de datos cada cinco minutos, que se almacena localmente en el almacenamiento con redundancia geográfica con acceso de lectura de Azure (RA-GRS). Azure copia las copias de seguridad de la base de datos en un centro de datos emparejado de la región del oeste de EE. UU.

![Restauración geográfica](./media/sql-database-geo-restore/geo-restore-1.png)

<!--## What is <feature>?" -->

## <a name="what-is-a-sql-database-backup"></a>¿Qué es una copia de seguridad de SQL Database?  

<!-- 
First sentence begins with "The <feature> is ..." followed by a definition of the feature. Provide a 1-2 paragraph intro to explain what the feature is, how it works, and the importance of the feature for solving business problems.
-->
Una copia de seguridad de base de datos SQL es un archivo que almacena información sobre el estado de la base de datos en un momento dado. SQL Database emplea tecnología SQL para crear copias de seguridad [completas](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciales](https://msdn.microsoft.com/library/ms175526.aspx ) y del [registro de transacciones](https://msdn.microsoft.com/library/ms191429.aspx). Las copias de seguridad del registro de transacciones se producen cada cinco minutos, lo que permite realizar una restauración a un momento dado en el mismo servidor que hospeda la base de datos. Cuando se restaura una base de datos, el servicio calcula qué copia de seguridad completa, diferencial o del registro de transacciones es necesario restaurar.

>[AZURE.NOTE] SQL Database crea tanto copias de seguridad locales de la base de datos como con redundancia geográfica automáticamente. No es necesario hacer nada para que se produzcan. Y sin cargos adicionales.

Use una copia de seguridad de una base de datos para:

- Restaurar una base de datos a un momento dado dentro del período de retención. Con una copia de seguridad de base de datos, puede restaurar una base de datos a un momento dado, restaurar una base de datos eliminada al momento en que se eliminó o restaurar una base de datos a otra región geográfica. Para realizar una restauración, consulte [cómo restaurar una base de datos desde una copia de seguridad de base de datos](sql-database-recovery-using-backups.md).

- Copiar una base de datos en un servidor SQL de la misma región u otra diferente. La copia es coherente desde un punto de vista transaccional con el SQL Database actual. Para realizar una copia, consulte [cómo copiar una base de datos](sql-database-copy.md).

- Archivar una copia de seguridad de la base de datos más allá del período de retención de la copia de seguridad. Para realizar un archivo, [exporte una base de datos SQL a un archivo BACPAC](sql-database-export.md). Luego puede archivar el archivo BACPAC en un almacenamiento a largo plazo y almacenarlo más allá de su período de retención. O bien, utilice el archivo BACPAC para transferir una copia de su base de datos a SQL Server, en una ubicación local o en una máquina virtual (VM) de Azure.

## <a name="backups-have-geographical-redundancy"></a>Las copias de seguridad tienen redundancia geográfica

SQL Database usa la [replicación de Azure Storage](../storage/storage-redundancy.md) para hacer copia de seguridad de la base de datos en una ubicación geográfica diferente. Para proporcionar almacenamiento con redundancia geográfica, SQL Database almacena los archivos de copia de seguridad de la base de datos local en una cuenta de [almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage). Azure replica los archivos de copia de seguridad en un [entro de datos emparejado](../best-practices-availability-paired-regions.md). Esta replicación geográfica garantiza que pueda restaurar una base de datos en caso de que no pueda tener acceso a la copia de seguridad de base de datos desde su región de base de datos principal. 

>[AZURE.NOTE] En Azure Storage, el término *replicación* hace referencia a la copia de archivos desde una ubicación a otra. La *replicación de base de datos* de SQL hace referencia a mantener varias bases de datos secundarias sincronizadas con una base de datos principal. 


## <a name="how-much-backup-storage-is-included-at-no-cost"></a>¿Cuánto almacenamiento de copia de seguridad se incluye sin costo?

SQL Database proporciona hasta un 200 % de almacenamiento de base de datos aprovisionado máximo, para que pueda almacenar copias de seguridad, sin costos adicionales. Por ejemplo, si tiene una instancia de base de datos de tipo Estándar con un tamaño de base de datos aprovisionado de 250 GB, tendrá 500 GB para almacenar copias de seguridad sin costos adicionales. Si la base de datos supera el almacenamiento de copias de seguridad proporcionado, puede reducir el período de retención; para ello, póngase en contacto con el soporte técnico de Azure. Otra opción consiste en pagar por un almacenamiento de copias de seguridad adicional que se facturará según la tarifa estándar de almacenamiento redundante geográficamente con acceso de lectura (RA-GRS). 

## <a name="how-often-do-backups-happen"></a>¿Con qué frecuencia se producen las copias de seguridad?

Las copias de seguridad completas de base de datos se realizan cada semana, las copias de seguridad diferenciales de base de datos, cada hora, y las copias de seguridad del registro de transacciones, cada cinco minutos. La primera copia de seguridad completa se programa inmediatamente después de la creación de la base de datos. Normalmente, se completa en 30 minutos pero puede tardar más si la base de datos tiene un tamaño considerable. Por ejemplo, la copia de seguridad inicial puede tardar más en una base de datos restaurada o una copia de la base de datos. Después de la primera copia de seguridad completa, todas las copias de seguridad adicionales se programan automáticamente y se administran silenciosamente en segundo plano. El tiempo exacto de las copias de seguridad de base de datos completas y [diferenciales](https://msdn.microsoft.com/library/ms175526.aspx) se determina según se equilibra la carga de trabajo general. 

## <a name="how-long-do-you-keep-my-backups"></a>¿Cuánto tiempo se mantienen las copias de seguridad?

Cada copia de seguridad de SQL Database tiene un período de retención que se basa en el [nivel de servicio](sql-database-service-tiers.md) de la base de datos. El período de retención de una base de datos es el siguiente:

- Nivel de servicio Básico: 7 días.
- Nivel de servicio Estándar: 35 días
- Nivel de servicio Premium: 35 días.


Si cambia la base de datos de los niveles de servicio Estándar o Premium a Básico, las copias de seguridad se guardan durante siete días. Todas las copias de seguridad existentes con una antigüedad de más de siete días ya no estarán disponibles. 

Si actualiza la base de datos del nivel de servicio Básico a Estándar o Premium, SQL Database mantiene las copias de seguridad existentes hasta que tienen 35 días de antigüedad. Además, mantiene nuevas copias de seguridad que se producen durante 35 días.
 
Si elimina una base de datos, SQL Database mantiene las copias de seguridad de la misma manera que para una base de datos en línea. Por ejemplo, suponga que elimina una base de datos básica que tiene un período de retención de siete días. Una copia de seguridad con cuatro días de antigüedad se guarda durante tres días más.

> [AZURE.IMPORTANT] Si elimina el servidor de Azure SQL que hospeda las bases de datos SQL, todas las bases de datos que pertenecen al servidor también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.

## <a name="next-steps"></a>Pasos siguientes

Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para ver cómo las copias de seguridad de base de datos pueden adaptarse a su estrategia de negocio, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).





<!--HONumber=Oct16_HO2-->


