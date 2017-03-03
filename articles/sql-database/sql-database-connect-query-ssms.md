---
title: "Conexión a SQL Database - SQL Server Management Studio | Microsoft Docs"
description: "Aprenda a conectarse a Base de datos SQL en Azure con SQL Server Management Studio (SSMS). Después, ejecute una consulta de ejemplo con Transact-SQL (T-SQL)."
metacanonical: 
keywords: "conexión a base de datos sql,sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: a5eaf43aa01e5d30171ea038db7ba985c9684fb7
ms.lasthandoff: 02/16/2017


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-t-sql-query"></a>Conexión a Base de datos SQL con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo

Este artículo explica cómo conectarse a una Base de datos SQL de Azure con SQL Server Management Studio (SSMS). Después de conectarse correctamente, ejecutamos una consulta de Transact-SQL (T-SQL) simple para comprobar la comunicación con la base de datos.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

1. Si aún no lo ha hecho descargue e instale la versión más reciente de SSMS en [Descarga de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para estar siempre actualizado, la versión más reciente de SSMS le avisará cuando haya una nueva versión disponible para su descarga.

2. Después de la instalación, escriba **Microsoft SQL Server Management Studio** en el cuadro de búsqueda de Windows y haga clic en **Entrar** para abrir SSMS:

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. En el cuadro de diálogo Conectar con el servidor, escriba la información necesaria para conectarse a su servidor SQL Server mediante la autenticación de SQL Server.

    ![conectar con el servidor](./media/sql-database-get-started/connect-to-server.png)
4. Haga clic en **Conectar**.

    ![conectado al servidor](./media/sql-database-get-started/connected-to-server.png)
5. En el Explorador de objetos, expanda **Bases de datos** y cualquier base de datos para ver los objetos de esa base de datos.

    ![objetos de nueva base de datos de ejemplo con ssms](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
6. Haga clic con el botón derecho en esta base de datos de ejemplo y, después, haga clic en **Nueva consulta**.

    ![nueva consulta de base de datos de ejemplo con ssms](./media/sql-database-get-started/new-sample-db-query-ssms.png)
7. Escriba la siguiente consulta en la ventana de consulta:

   ```select * from sys.objects```
   
8.  En la barra de herramientas, haga clic en **Ejecutar** para devolver una lista de todos los objetos del sistema en la base de datos de ejemplo.

    ![objetos del sistema de consulta a la nueva base de datos de ejemplo con ssms](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

> [!Tip]
> Para ver un tutorial, consulte [Tutorial: aprovisionamiento y acceso a una instancia de Azure SQL Database mediante Azure Portal y SQL Server Management Studio](sql-database-get-started.md).    
>

## <a name="next-steps"></a>Pasos siguientes

- Puede usar instrucciones T-SQL para crear y administrar bases de datos en Azure casi de la misma manera que con SQL Server. Si está familiarizado con el uso de T-SQL con SQL Server, consulte [Información sobre Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md) para obtener un resumen de las diferencias.
- Si no está familiarizado con T-SQL, consulte [Tutorial: Escribir instrucciones Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) y [Referencia de Transact-SQL (motor de base de datos)](https://msdn.microsoft.com/library/bb510741.aspx).
- Para una introducción al tutorial de autenticación de SQL Server, consulte [Autenticación y autorización de SQL](sql-database-control-access-sql-authentication-get-started.md)
- Para una introducción al tutorial de autenticación de Azure Active Directory, consulte [Autenticación y autorización de Azure AD](sql-database-control-access-aad-authentication-get-started.md)
- Para más información acerca de SSMS, consulte [Usar SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).


