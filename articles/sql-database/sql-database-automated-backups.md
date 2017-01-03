---
title: "Copias de seguridad de SQL Database (automática y con redundancia geográfica) | Microsoft Docs"
description: "SQL Database crea automáticamente una copia de seguridad local de la base de datos cada pocos minutos y usa almacenamiento con redundancia geográfica con acceso de lectura de Azure para proporcionar redundancia geográfica."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2016
ms.author: sashan;carlrab;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a5b69625272b96fc9e42b9b7e57b5401351d8d6e
ms.openlocfilehash: 9e58a144be039db609ad1400246ef2de05c1b84d


---
# <a name="learn-about-sql-database-backups"></a>Más información sobre las copias de seguridad de SQL Database
<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.

Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)

GUIDELINES for the H1 

    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  

    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

GUIDELINES for introduction

    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

SQL Database crea automáticamente copias de seguridad de la base de datos y usa almacenamiento con redundancia geográfica con acceso de lectura de Azure (RA-GRS) para ofrecer redundancia geográfica. Estas copias de seguridad se crean automáticamente y sin cargos adicionales. No es necesario hacer nada para que se produzcan. Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Si desea conservar las copias de seguridad en su propio contenedor de almacenamiento, puede configurar una directiva de retención de copias de seguridad a largo plazo. Para obtener más información, consulte el tema sobre la [retención a largo plazo](sql-database-long-term-retention.md).

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.

    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>¿Qué es una copia de seguridad de SQL Database?
<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->


<!----------------- 
    Explains first component of the backup feature
------------------>

SQL Database emplea tecnología de SQL Server para crear copias de seguridad [completas](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciales](https://msdn.microsoft.com/library/ms175526.aspx) y del [registro de transacciones](https://msdn.microsoft.com/library/ms191429.aspx). Por lo general, las copias de seguridad del registro de transacciones se producen cada 5-10 minutos, y la frecuencia depende del nivel de rendimiento y de la cantidad de actividad de la base de datos. Las copias del registro de transacciones, junto con las completas y diferenciales, permiten restaurar una base de datos a un momento específico en el mismo servidor que hospede la base de datos. Cuando se restaura una base de datos, el servicio calcula qué copia de seguridad completa, diferencial o del registro de transacciones es necesario restaurar.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Puede utilizar estas copias de seguridad para realizar lo siguiente:

* Restaurar una base de datos a un momento dado dentro del período de retención. Esta operación creará una base de datos nueva en el mismo servidor de la base de datos original.
* Restaurar una base de datos eliminada al momento en que se eliminó o a cualquier otro punto comprendido en el periodo de retención. La base de datos eliminada solo se puede restaurar en el mismo servidor donde se creara la original.
* Restaurar una base de datos en otra región geográfica. Esta opción permite recuperarse de un desastre en una región geográfica cuando no puede acceder a su servidor y base de datos. Crea una nueva base de datos en cualquier servidor existente del mundo. 
* Restaurar una base de datos desde una copia de seguridad específica guardada en el almacén de Azure Recovery Services. Esta opción permite restaurar una versión antigua de la base de datos para respetar una solicitud de cumplimiento o ejecutar una versión antigua de la aplicación. Consulte el tema sobre la [retención a largo plazo](sql-database-long-term-retention.md).
* Para llevar a cabo una restauración, consulte el artículo sobre la [restauración de bases de datos a partir de copias de seguridad](sql-database-recovery-using-backups.md).

<!----------------- 
    Explains first component of the backup feature
------------------>

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

> [!NOTE]
> En Azure Storage, el término *replicación* hace referencia a la copia de archivos desde una ubicación a otra. La *replicación de base de datos* de SQL hace referencia a mantener varias bases de datos secundarias sincronizadas con una base de datos principal. 
> 
> 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>¿Cuánto almacenamiento de copia de seguridad se incluye sin costo?
SQL Database proporciona hasta un 200 % de almacenamiento de base de datos aprovisionado máximo, para que pueda almacenar copias de seguridad, sin costos adicionales. Por ejemplo, si tiene una instancia de base de datos de tipo Estándar con un tamaño de base de datos aprovisionado de 250 GB, tendrá 500 GB para almacenar copias de seguridad sin costos adicionales. Si la base de datos supera el almacenamiento de copias de seguridad proporcionado, puede reducir el período de retención; para ello, póngase en contacto con el soporte técnico de Azure. Otra opción consiste en pagar por un almacenamiento de copias de seguridad adicional que se facturará según la tarifa estándar de almacenamiento redundante geográficamente con acceso de lectura (RA-GRS). 

## <a name="how-often-do-backups-happen"></a>¿Con qué frecuencia se producen las copias de seguridad?
Las copias de seguridad de bases de datos completas se producen con una frecuencia semanal, las diferenciales tienden a llevarse a cabo en intervalos de pocas horas y las de registro de transacciones suelen realizarse cada 5-10 minutos. La primera copia de seguridad completa se programa inmediatamente después de la creación de la base de datos. Normalmente, se completa en 30 minutos pero puede tardar más si la base de datos tiene un tamaño considerable. Por ejemplo, la copia de seguridad inicial puede tardar más en una base de datos restaurada o una copia de la base de datos. Después de la primera copia de seguridad completa, todas las copias de seguridad adicionales se programan automáticamente y se administran silenciosamente en segundo plano. El servicio SQL Database determina el momento exacto en el que se producen todas las copias de seguridad de la base de datos a medida que equilibra la carga de trabajo global del sistema. 

La replicación geográfica del almacenamiento de copias de seguridad se produce según la programación de replicación de Azure Storage.

## <a name="how-long-do-you-keep-my-backups"></a>¿Cuánto tiempo se mantienen las copias de seguridad?
Cada copia de seguridad de SQL Database tiene un período de retención que se basa en el [nivel de servicio](sql-database-service-tiers.md) de la base de datos. El período de retención de una base de datos es el siguiente:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

* Nivel de servicio Básico: 7 días.
* Nivel de servicio Estándar: 35 días
* Nivel de servicio Premium: 35 días.

Si cambia la base de datos de los niveles de servicio Estándar o Premium a Básico, las copias de seguridad se guardan durante siete días. Todas las copias de seguridad existentes con una antigüedad de más de siete días ya no estarán disponibles. 

Si actualiza la base de datos del nivel de servicio Básico a Estándar o Premium, SQL Database mantiene las copias de seguridad existentes hasta que tienen 35 días de antigüedad. Además, mantiene nuevas copias de seguridad que se producen durante 35 días.

Si elimina una base de datos, SQL Database mantiene las copias de seguridad de la misma manera que para una base de datos en línea. Por ejemplo, suponga que elimina una base de datos básica que tiene un período de retención de siete días. Una copia de seguridad con cuatro días de antigüedad se guarda durante tres días más.

> [!IMPORTANT]
> Si elimina el servidor de Azure SQL que hospeda las bases de datos SQL, todas las bases de datos que pertenecen al servidor también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.
> 
> 

## <a name="how-to-extend-the-backup-retention-period"></a>¿Cómo se puede ampliar el periodo de retención de las copias de seguridad?
Si la aplicación requiere que las copias de seguridad estén disponibles durante un periodo mayor, puede ampliar el periodo de retención integrado configurando la directiva de retención de copias de seguridad a largo plazo para bases de datos individuales (directiva de LTR). De este modo, podrá ampliar el periodo de retención integrado de 35 días a un máximo de 10 años. Para obtener más información, consulte el tema sobre la [retención a largo plazo](sql-database-long-term-retention.md).

Una vez que agregue la directiva de LTR a una base de datos mediante Azure Portal o la API, las copias de seguridad completas de la base de datos semanales se copiarán de forma automática en su almacén del servicio Azure Backup. Si la base de datos se cifra con TDE, las copias de seguridad se cifran automáticamente en reposo.  El almacén de Recovery Services eliminará de forma automática sus copias de seguridad caducadas según su fecha y hora y la directiva de LTR.  Por ello, no tiene que administrar la programación de copias de seguridad ni preocuparse por la limpieza de archivos antiguos. La API de restauración admite las copias de seguridad almacenadas en el almacén siempre que este se encuentre en la misma suscripción que la base de datos SQL. Puede usar el portal o PowerShell para acceder a estas copias de seguridad.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Pasos siguientes
Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para descubrir otras soluciones de continuidad empresarial de Azure SQL Database, consulte el artículo de [información general sobre la continuidad empresarial](sql-database-business-continuity.md).




<!--HONumber=Nov16_HO5-->


