<properties
   pageTitle="Conexión a Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Cómo encontrar el nombre de servidor y la cadena de conexión de su instancia de Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# Conexión a Almacenamiento de datos SQL de Azure

Este artículo le ayuda a conectarse a SQL Data Warehouse por primera vez.

## Búsqueda del nombre de servidor

El primer paso para conectarse a SQL Data Warehouse es saber cómo encontrar el nombre del servidor. Por ejemplo, el nombre del servidor del ejemplo siguiente es sample.database.windows.net. Para buscar el nombre del servidor completo:

1. Vaya al [Portal de Azure][].
2. Haga clic en **Bases de datos SQL**.
3. Haga clic en la base de datos a la que desea conectarse.
4. Busque el nombre del servidor completo:

    ![Nombre del servidor completo][1]

## Cadenas de conexión y controladores admitidos

Azure SQL Data Warehouse es compatible con [ADO.NET][], [ODBC][], [PHP][] y [JDBC][]. Haga clic en uno de los controladores anteriores para buscar la versión más reciente y su documentación. Para generar automáticamente la cadena de conexión del controlador que está usando en Azure Portal, puede hacer clic en el vínculo **Mostrar las cadenas de conexión de la base de datos** del ejemplo anterior. Los siguientes son también algunos ejemplos del aspecto de una cadena de conexión para cada controlador.

> [AZURE.NOTE] Considere la posibilidad de establecer el tiempo de espera de conexión en 300 segundos para permitir que la conexión se conserve durante breves períodos de falta de disponibilidad.

### Ejemplo de cadena de conexión de ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Ejemplo de cadena de conexión de ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Ejemplo de cadena de conexión de PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Ejemplo de cadena de conexión de JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Configuración de conexión

SQL Data Warehouse normaliza algunas opciones de configuración durante la conexión y la creación de objetos. Estas opciones de configuración no se pueden invalidar e incluyen:

| Configuración de base de datos | Valor |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ACTIVAR |
| [QUOTED\_IDENTIFIERS][] | ACTIVAR |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## Pasos siguientes

Para conectarse y realizar consultas con Visual Studio, consulte [Realización de consultas con Visual Studio][]. Para más información acerca de las opciones de autenticación, consulte [Autenticación en Azure SQL Data Warehouse][].

<!--Articles-->
[Realización de consultas con Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Autenticación en Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal de Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0928_2016-->