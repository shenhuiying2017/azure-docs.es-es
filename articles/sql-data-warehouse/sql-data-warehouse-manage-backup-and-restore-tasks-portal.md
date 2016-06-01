<properties
   pageTitle="Copia de seguridad y restauración en Almacenamiento de datos SQL de Azure (Portal) | Microsoft Azure"
   description="Tareas del Portal de Azure para restaurar una base de datos activa, eliminada o inaccesible en Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Copia de seguridad y restauración de una base de datos en Almacenamiento de datos SQL de Azure (Portal)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Portal](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

Copia de seguridad y restauración de una base de datos en Almacenamiento de datos SQL mediante el Portal de Azure.

Tareas de este tema:

- Restauración de una base de datos activa
- Restauración de una base de datos eliminada
- Restauración de una base de datos inaccesible desde otra región geográfica de Azure

[AZURE.INCLUDE [Directiva de retención de copia de seguridad de Almacenamiento de datos SQL](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Antes de empezar

Compruebe su capacidad de DTU de Base de datos SQL. Como Almacenamiento de datos SQL se restaura a una nueva base de datos en su servidor SQL lógico, es importante asegurarse de que el servidor SQL al que va a restaurar tiene suficiente capacidad de DTU para la nueva base de datos. Consulte este blog para más información sobre [cómo ver y aumentar la cuota de DTU][].

## Restauración de una base de datos activa

Para restaurar una base de datos a partir de una instantánea:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Bases de datos SQL**.
3. Desplácese hasta la base de datos y selecciónela.
4. En la parte superior de la hoja de la base de datos, haga clic en **Restaurar**.
5. Especifique un nuevo **Nombre de base de datos**, seleccione un **Punto de restauración** y después haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.


## Restauración de una base de datos eliminada

Para restaurar una base de datos eliminada, consulte:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Servidores SQL**.
3. Vaya al servidor y selecciónelo.
4. Desplácese hacia abajo hasta la opción Operaciones de la hoja del servidor y haga clic en el icono **Bases de datos eliminadas**.
5. Seleccione la base de datos eliminada que desee restaurar.
5. Especifique un nuevo **Nombre de base de datos** y haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.


## Restauración geográfica de un almacenamiento de datos a partir de una copia de seguridad con redundancia geográfica

Para realizar una restauración geográfica:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **+NUEVO**, **Data and Storage** (Datos y almacenamiento) y, por último, **Almacenamiento de datos SQL**.
3. Seleccione **COPIA DE SEGURIDAD** como origen y, después, la copia de seguridad con redundancia geográfica desde la que desea realizar la recuperación.
4. Especifique el resto de propiedades de la base de datos y haga clic en **Crear**.
5. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.

## Pasos siguientes
Para más información sobre las características de continuidad del negocio de las ediciones de Base de datos SQL de Azure, lea [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->

<!--Blog references-->
[cómo ver y aumentar la cuota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->