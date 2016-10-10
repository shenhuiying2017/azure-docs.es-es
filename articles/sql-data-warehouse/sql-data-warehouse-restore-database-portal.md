<properties
   pageTitle="Restauración de una instancia de Almacenamiento de datos SQL de Azure (Portal) | Microsoft Azure"
   description="Tareas del Portal de Azure para restaurar una instancia de Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# Restauración de instancias de Almacenamiento de datos SQL de Azure (Portal)

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

En este artículo, obtendrá información sobre cómo restaurar una instancia de Almacenamiento de datos SQL de Azure mediante el Portal de Azure.

## Antes de empezar

**Compruebe la capacidad DTU.** Cada instancia de Almacenamiento de datos SQL está hospedada en un servidor SQL Server (p. ej., myserver.database.windows.net) que tiene una cuota de DTU predeterminada. Antes de que pueda restaurar una instancia de Almacenamiento de datos SQL, compruebe que su servidor SQL Server tiene suficientes cuotas de DTU restantes para la base de datos en proceso de restauración. Para más información sobre cómo calcular la unidad DTU necesaria o solicitar más DTU, consulte [Request a DTU quota change][] \(Solicitar un cambio en la cuota de DTU).


## Restauración de una base de datos activa o en pausa

Para restaurar una base de datos:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **Examinar** y, después, seleccione **Servidores SQL**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Vaya al servidor y selecciónelo.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Busque la instancia de Almacenamiento de datos SQL desde la que desea realizar la restauración y selecciónela.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. En la parte superior de la hoja Almacenamiento de datos, haga clic en **Restaurar**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Especifique un nuevo **nombre de base de datos**.
7. Seleccione el **punto de restauración** más reciente.
    1. No se olvide de elegir el punto de restauración más reciente. Puesto que los puntos de restauración se muestran en formato UTC, a veces, la opción predeterminada que se muestra no es el punto de restauración más reciente.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Haga clic en **Aceptar**.
9. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Configuración de la base de datos después de realizar la recuperación][].


## Restauración de una base de datos eliminada

Para restaurar una base de datos eliminada, consulte:

1. Inicie sesión en el [Portal de Azure][].
2. En el lado izquierdo de la pantalla, seleccione **Examinar** y, después, seleccione **Servidores SQL**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Vaya al servidor y selecciónelo.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Desplácese hacia abajo hasta la sección Operaciones de la hoja del servidor.
5. Haga clic en el icono de **Bases de datos eliminadas**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Seleccione la base de datos eliminada que desee restaurar.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Especifique un nuevo **nombre de base de datos**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Haga clic en **Aceptar**.
9. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.

>[AZURE.NOTE] Para configurar la base de datos una vez finalizada la restauración, consulte [Configuración de la base de datos después de realizar la recuperación][].

## Pasos siguientes
Para obtener más información sobre las características de continuidad empresarial de las ediciones de Base de datos SQL de Azure, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: ./sql-database-business-continuity.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configuración de la base de datos después de realizar la recuperación]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0928_2016-->