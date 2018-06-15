---
title: Conexión a Azure SQL Data Warehouse | Microsoft Docs
description: Conéctese a Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b2aff8379667191017830bf441ee101e773d2dfc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
ms.locfileid: "31596177"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Conexión a Azure SQL Data Warehouse
Conéctese a Azure SQL Data Warehouse.

## <a name="find-your-server-name"></a>Búsqueda del nombre de servidor
El nombre del servidor del ejemplo siguiente es samplesvr.database.windows.net. Para buscar el nombre del servidor completo:

1. Vaya a [Azure Portal][Azure portal].
2. Haga clic en **Almacenes de SQL Data Warehouse**.
3. Haga clic en el almacén de datos al que desea conectarse.
4. Busque el nombre del servidor completo:
   
    ![Nombre del servidor completo][1]

## <a name="supported-drivers-and-connection-strings"></a>Cadenas de conexión y controladores admitidos
Azure SQL Data Warehouse es compatible con [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] y [JDBC][JDBC]. Para buscar la versión más reciente y su documentación, haga clic en uno de los controladores anteriores. Para generar automáticamente la cadena de conexión del controlador que está usando en Azure Portal, haga clic en el vínculo **Mostrar las cadenas de conexión de la base de datos** del ejemplo anterior. Los siguientes son también algunos ejemplos del aspecto de una cadena de conexión para cada controlador.

> [!NOTE]
> Considere la posibilidad de establecer el tiempo de espera de conexión en 300 segundos para permitir que la conexión se conserve durante breves períodos de falta de disponibilidad.
> 
> 

### <a name="adonet-connection-string-example"></a>Ejemplo de cadena de conexión de ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Ejemplo de cadena de conexión de ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Ejemplo de cadena de conexión de PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Ejemplo de cadena de conexión de JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Configuración de conexión
SQL Data Warehouse normaliza algunas opciones de configuración durante la conexión y la creación de objetos. Estas opciones de configuración no se pueden invalidar e incluyen:

| Configuración de base de datos | Valor |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ACTIVAR |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ACTIVAR |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Pasos siguientes
Para conectarse y realizar consultas con Visual Studio, consulte [Conexión a SQL Data Warehouse con Visual Studio y SSDT][Query with Visual Studio]. Para más información acerca de las opciones de autenticación, consulte [Autenticación en Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


