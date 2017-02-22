---
title: "Administración de una base de datos SQL con SSMS | Microsoft Docs"
description: "Obtenga información acerca de cómo usar SQL Server Management Studio para administrar bases de datos y servidores de bases de datos SQL."
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: c5f1ab504bcb639260b2500d462a56ea30cab9aa


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Administración de Base de datos SQL de Azure con el uso de SQL Server Management Studio

Puede usar SQL Server Management Studio (SSMS) para administrar las bases de datos y los servidores de Base de datos SQL de Azure. Este tema le guía a través de las tareas comunes con SSMS. Ya debería tener una base de datos y un servidor creados en la Base de datos SQL de Azure antes de comenzar. Consulte [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md) y [Conexión y consulta mediante SSMS](sql-database-connect-query-ssms.md) para más información.

> [!TIP]
> Para ver un tutorial sobre cómo crear servidores y firewalls basados en servidores, consultar las propiedades del servidor, establecer la conexión y consultar la base de datos maestra, crear una base de datos de ejemplo y una base de datos vacía, consultar las propiedades de la base de datos, establecer la conexión y consultar la base de datos de ejemplo, consulte este [tutorial introductorio](sql-database-get-started.md).
>

Se recomienda usar la versión más reciente de SSMS siempre que trabaje con la Base de datos SQL de Azure. 

> [!IMPORTANT]
> Utilice siempre la versión más reciente de SSMS porque se mejora continuamente para trabajar con las últimas actualizaciones de Azure y Base de datos SQL. Para obtener la versión más reciente, consulte [Descarga de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Crear y administrar bases de datos SQL de Azure
Mientras está conectado a la base de datos **maestra** , puede crear bases de datos en el servidor y modificar o anular las existentes. En los pasos siguientes se describe cómo realizar varias tareas comunes de administración de bases de datos con Management Studio. Para realizar estas tareas, asegúrese de que está conectado a la base de datos **maestra** con el inicio de sesión principal a nivel de servidor que creó cuando creó el servidor.

Para abrir una ventana de consulta en Management Studio, abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, en **Nueva consulta**.

* Use la instrucción **CREATE DATABASE** para crear una base de datos. Para obtener más información, consulte [CREATE DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/dn268335.aspx). La siguiente instrucción crea una base de datos con el nombre **myTestDB** y especifica que se trata de una base de datos Standard S0 Edition con un tamaño máximo de 250 GB.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Haga clic en **Ejecutar** para ejecutar la consulta.

* Use la instrucción **ALTER DATABASE** para modificar una base de datos existente, por ejemplo, si desea cambiar el nombre y la edición de la base de datos. Para obtener más información, consulte [ALTER DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/ms174269.aspx). La instrucción siguiente modifica la base de datos creada en el paso anterior para cambiar a la edición Standard S1.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* Use la instrucción **DROP DATABASE** para eliminar una base de datos existente. Para obtener más información, consulte [DROP DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/ms178613.aspx). La instrucción siguiente elimina la base de datos **myTestDB** , pero no la anula ahora porque la usará para crear inicios de sesión en el paso siguiente.
  
      DROP DATABASE myTestBase;
* La base de datos maestra tiene la vista **sys.databases** , que puede usar para ver los detalles acerca de todas las bases de datos. Para ver todas las bases de datos existentes, ejecute la siguiente instrucción:
  
      SELECT * FROM sys.databases;
* En Base de datos SQL, no se admite la instrucción **USE** para cambiar entre bases de datos. En su lugar, necesita establecer una conexión directa con la base de datos de destino.

> [!NOTE]
> Muchas de las instrucciones de Transact-SQL que crean o modifican una base de datos deben ejecutarse en su propio lote y no se pueden agrupar con otras instrucciones de Transact-SQL. Para obtener más información, vea la información específica de las instrucciones.
> 
> 

## <a name="create-and-manage-logins"></a>Crear y administrar los inicios de sesión
La base de datos **maestra** contiene los inicios de sesión y de cuáles tienen permiso para crear bases de datos u otros inicios de sesión. Para administrar inicios de sesión, conéctese a la base de datos **maestra** con el inicio de sesión principal del nivel del servidor creado cuando configuró el servidor. Puede usar las instrucciones **CREATE LOGIN**, **ALTER LOGIN** o **DROP LOGIN** para ejecutar consultas en la base de datos maestra que administra los inicios de sesión en todo el servidor. Para obtener más información, consulte [Administración de bases de datos e inicios de sesión en Base de datos SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 

* Use la instrucción **CREATE LOGIN** para crear un inicio de sesión a nivel de servidor. Para obtener más información, consulte [CREATE LOGIN (Base de datos SQL)](https://msdn.microsoft.com/library/ms189751.aspx). La siguiente instrucción crea un inicio de sesión denominado **login1**. Reemplace **password1** por la contraseña que desee.
  
      CREATE LOGIN login1 WITH password='password1';
* Use la instrucción **CREATE USER** para conceder permisos a nivel de base de datos. Todos los inicios de sesión deben crearse en la base de datos **maestra** . Para que un inicio de sesión se conecte a otra base de datos, debe concederle permisos a nivel de base de datos con el uso de la instrucción **CREATE USER** en dicha base de datos. Para obtener más información, consulte [CREATE USER (Base de datos SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 
* Para conceder permisos a login1 para una base de datos llamada **myTestDB**, siga estos pasos:
  
  1. Para actualizar el Explorador de objetos y ver la base de datos **myTestDB** que ha creado, haga clic con el botón derecho en el nombre del servidor en el Explorador de objetos y, a continuación, haga clic en **Actualizar**.  
     
     Si cerró la conexión, puede seleccionar **Conectar Explorador de objetos** para volver a establecerla en el menú Archivo.
  2. Haga clic con el botón derecho en la base de datos **myTestDB** y seleccione **Nueva consulta**.
  3. Ejecute la instrucción siguiente en la base de datos myTestDB para crear un usuario de base de datos con el nombre **login1User** que se corresponda con el inicio de sesión de nivel de servidor **login1**.
     
         CREATE USER login1User FROM LOGIN login1;
* Use el procedimiento almacenado **sp\_addrolemember** para conceder a la cuenta de usuario el nivel apropiado de permisos para la base de datos. Para más información, consulte [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). La instrucción siguiente concede a **login1User** permisos de solo lectura a la base de datos mediante la adición de **login1User** al rol **db\_datareader**.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Use la instrucción **ALTER LOGIN** para modificar un inicio de sesión existente, por ejemplo, si desea cambiar la contraseña de inicio de sesión. Para obtener más información, consulte [ALTER LOGIN (Base de datos SQL)](https://msdn.microsoft.com/library/ms189828.aspx). La instrucción **ALTER LOGIN** debe ejecutarse en la base de datos **maestra**. Vuelva a la ventana de consulta conectada a la base de datos. La instrucción siguiente modifica el inicio de sesión **login1** para restablecer la contraseña. Reemplace **newPassword** por la contraseña que desee y **oldPassword** por la contraseña actual de inicio de sesión.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* Use la instrucción **DROP LOGIN** para eliminar un inicio de sesión existente. Al eliminar un inicio de sesión a nivel de servidor también se eliminan todas las cuentas de usuario de la base de datos asociadas. Para obtener más información, consulte [DROP DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/ms178613.aspx). La instrucción **DROP LOGIN** debe ejecutarse en la base de datos **maestra**. La instrucción elimina el inicio de sesión **login1** .
  
      DROP LOGIN login1;
* La base de datos maestra tiene la vista **sys.sql\_logins**, que puede usar para ver los inicios de sesión. Para ver todos los inicios de sesión existentes, ejecute la siguiente instrucción:
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Supervisar Base de datos SQL mediante las vistas de administración dinámica
Base de datos SQL admite varias vistas de administración dinámica que puede usar para supervisar una base de datos individual. A continuación, se facilitan algunos ejemplos del tipo de datos de supervisión que puede recuperar en estas vistas. Para obtener todos los detalles y más ejemplos de uso, consulte [Supervisión de Base de datos SQL de Azure mediante vistas de administración dinámica](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* Para consultar una vista de administración dinámica, es necesario disponer de los permisos **VER ESTADO DE BASE DE DATOS** . Para conceder el permiso **VER ESTADO DE BASE DE DATOS** a un usuario específico de la base de datos, conéctese a la base de datos y ejecute la siguiente instrucción en la base de datos:
  
      GRANT VIEW DATABASE STATE TO login1User;
* Calcule el tamaño de la base de datos; para ello, use la vista **sys.dm\_db\_partition\_stats**. La vista **sys.dm\_db\_partition\_stats** devuelve información sobre la página y el recuento de filas para cada partición de la base de datos, que puede usar para calcular el tamaño de la base de datos. La siguiente consulta devuelve el tamaño de la base de datos en megabytes:
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* Use las vistas **sys.dm\_exec\_connections** y **sys.dm\_exec\_sessions** para recuperar información sobre las conexiones de usuario actuales y las tareas internas asociadas con la base de datos. La consulta siguiente devuelve información sobre la conexión actual:
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* Use la vista **sys.dm\_exec\_query\_stats** para recuperar estadísticas de agregado acerca del rendimiento para los planes de consulta en caché. La siguiente consulta devuelve información acerca de las cinco consultas principales clasificadas en función del tiempo de CPU promedio.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;




<!--HONumber=Feb17_HO3-->


