---
title: Restauración de instancias de Almacenamiento de datos SQL de Azure (información general) | Microsoft Docs
description: Información general de las opciones de restauración de bases de datos para recuperar una base de datos en Almacenamiento de datos SQL de Azure.
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/28/2016
ms.author: lakshmir;barbkess;sonyama

---
# Restauración de instancias de Almacenamiento de datos SQL de Azure (información general)
> [!div class="op_single_selector"]
> * [Información general][Información general]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Almacenamiento de datos SQL Azure protege los datos con copias de seguridad automatizadas y almacenamiento con redundancia local. Las copias de seguridad automatizadas proporcionan una manera de proteger las bases de datos de daños o eliminaciones accidentales sin esfuerzos de administración. En el caso de que un usuario modificara o eliminara datos de manera fortuita o involuntaria, puede garantizar la continuidad empresarial mediante la restauración de la base de datos a un momento dado anterior. Almacenamiento de datos SQL utiliza instantáneas de almacenamiento de Azure para realizar perfectamente y sin tiempos de inactividad una copia de seguridad de la base de datos.

## Copias de seguridad automatizadas
Se realizará automáticamente una copia de seguridad de las bases de datos **activas**, como mínimo, cada 8 horas y se conservarán durante 7 días. De este modo, podrá restaurar la base de datos activa a uno de los distintos puntos de restauración realizados en los últimos 7 días.

Cuando una base de datos está en pausa, se dejarán de realizar nuevas copias de seguridad y las anteriores se eliminarán cuando tengan una antigüedad de 7 días. Si una base de datos está en pausa durante más de 7 días, se guardará la última copia de seguridad, de esta forma, se garantiza que siempre habrá, como mínimo, una copia de seguridad.

Cuando se elimina una base de datos, se guarda la última copia de seguridad durante 7 días.

Ejecute esta consulta para ver cuándo Almacenamiento de datos SQL realizó la última copia de seguridad:

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Si necesita conservar una copia de seguridad durante más de 7 días, bastará simplemente con restaurar uno de los puntos de restauración a una nueva base de datos y, después, si lo desea, pausarla para solo pagar por el espacio de almacenamiento de dicha copia de seguridad.

## Redundancia de datos
Además de las copias de seguridad, Almacenamiento de datos SQL también protege los datos mediante el servicio Almacenamiento premium de Azure con [redundancia local (LRS)][redundancia local (LRS)]. Se mantienen varias copias sincrónicas de los datos en el centro de datos local para garantizar la protección transparente de los datos en caso de errores localizados. Gracias a la redundancia de datos, se garantiza que puedan sobrevivir a problemas de infraestructura, por ejemplo, errores de disco. Asimismo, asegura la continuidad empresarial mediante una infraestructura de alta disponibilidad con tolerancia a errores.

## Restauración de una base de datos
El proceso de restauración de una instancia de Almacenamiento de datos SQL es una operación sencilla que puede realizarse en el Portal de Azure o automatizarse mediante PowerShell o las API de REST.

## Pasos siguientes
Para obtener más información sobre las características de continuidad empresarial de las ediciones de Base de datos SQL de Azure, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: ./sql-database-business-continuity.md
[redundancia local (LRS)]: ../storage/storage-redundancy.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->