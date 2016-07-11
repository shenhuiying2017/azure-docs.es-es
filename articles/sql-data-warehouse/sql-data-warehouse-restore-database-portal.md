<properties
   pageTitle="Restauración de una instancia de Almacenamiento de datos SQL de Azure (Portal) | Microsoft Azure"
   description="Tareas del Portal de Azure para restaurar una instancia de Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess;nicw"/>

# Restauración de instancias de Almacenamiento de datos SQL de Azure (Portal)

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

En este artículo, obtendrá información sobre cómo restaurar una instancia de Almacenamiento de datos SQL de Azure mediante el Portal de Azure.

## Antes de empezar

**Compruebe la capacidad DTU**. Cada instancia de Almacenamiento de datos SQL está hospedada en un servidor lógico de SQL Server. Este servidor lógico tiene un límite de capacidad expresado en DTU. Antes de poder restaurar una instancia de Almacenamiento de datos SQL, es importante asegurarse de que el servidor lógico de SQL Server que hospeda la base de datos tiene suficiente capacidad DTU para la base de datos que se está restaurando. Consulte este blog para obtener más información sobre [cómo ver y aumentar la cuota de DTU][].


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

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalización de una base de datos SQL de Azure recuperada][].


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

>[AZURE.NOTE] Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalización de una base de datos SQL de Azure recuperada][].


## Pasos siguientes
Para obtener más información sobre las características de continuidad empresarial de las ediciones de Base de datos SQL de Azure, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: ./sql-database-business-continuity.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Finalización de una base de datos SQL de Azure recuperada]: ./sql-database-recovered-finalize.md

<!--MSDN references-->

<!--Blog references-->
[cómo ver y aumentar la cuota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0629_2016-->