<properties
   pageTitle="Restauración de una base de datos en Almacenamiento de datos SQL de Azure (Portal) | Microsoft Azure"
   description="Tareas del Portal de Azure para restaurar una base de datos activa o eliminada en Almacenamiento de datos SQL de Azure."
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
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restauración de una base de datos en Almacenamiento de datos SQL de Azure (Portal)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-restore-database-overview.md)
- [Portal](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST](sql-data-warehouse-manage-restore-database-rest-api.md)

Tareas de PowerShell para restaurar una base de datos activa o eliminada en Almacenamiento de datos SQL de Azure.

Tareas de este tema:

- Restauración de una base de datos activa
- Restauración de una base de datos eliminada

## Antes de empezar

Compruebe su capacidad de DTU de Base de datos SQL. Como Almacenamiento de datos SQL se restaura a una nueva base de datos en su servidor SQL lógico, es importante asegurarse de que el servidor SQL al que va a restaurar tiene suficiente capacidad de DTU para la nueva base de datos. Consulte este blog para más información sobre [cómo ver y aumentar la cuota de DTU][].


## Restauración de una base de datos activa

Para restaurar una base de datos:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Bases de datos SQL**.
3. Desplácese hasta la base de datos y selecciónela.
4. En la parte superior de la hoja de la base de datos, haga clic en **Restaurar**.
5. Especifique un nuevo **nombre de base de datos**, seleccione un **punto de restauración** y después haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.


## Restauración de una base de datos eliminada

Para restaurar una base de datos eliminada, consulte:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Servidores SQL**.
3. Vaya al servidor y selecciónelo.
4. Desplácese hacia abajo hasta la opción Operaciones de la hoja del servidor y haga clic en el icono **Bases de datos eliminadas**.
5. Seleccione la base de datos eliminada que desee restaurar.
5. Especifique un nuevo **nombre de base de datos** y haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.


## Pasos siguientes
Para más información, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][] e [Administración de base datos en Almacenamiento de datos SQL de Azure][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: sql-database-business-continuity.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md
[Administración de base datos en Almacenamiento de datos SQL de Azure]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->

<!--Blog references-->
[cómo ver y aumentar la cuota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->