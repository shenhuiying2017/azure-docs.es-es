<properties
   pageTitle="Consulta de Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Consulta de Almacenamiento de datos SQL Azure con las utilidades de la línea de comandos sqlcmd."
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
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# Consulta de Almacenamiento de datos SQL de Azure (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizaje automático de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)

En este tutorial se usa la utilidad de línea de comandos [sqlcmd][] para consultar una instancia de Azure SQL Data Warehouse.

## 1\. Conectar

Para empezar a trabajar con [sqlcmd][], abra el símbolo del sistema y escriba **sqlcmd** seguido de la cadena de conexión de la base de datos de Almacenamiento de datos SQL. La cadena de conexión requiere los siguientes parámetros:

+ **Servidor (-S):** servidor con el formato `<`Nombre del servidor`>`.database.windows.net
+ **Base de datos (-d):** nombre de la base de datos.
+ **Habilitar identificadores entre comillas (-I):** los identificadores entre comillas deben estar habilitados para poder conectarse a una instancia de SQL Data Warehouse.

Para utilizar la autenticación de SQL Server, debe agregar los parámetros de nombre de usuario y contraseña:

+ **Usuario (-U):** usuario de servidor con el formato `<`usuario`>`
+ **Contraseña (-P):** la contraseña asociada con el usuario.

Por ejemplo, la cadena de conexión podría ser similar a la siguiente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para usar la autenticación integrada de Azure Active Directory, debe agregar los parámetros de Azure Active Directory:

+ **Autenticación de Azure Active Directory (-G):** use Azure Active Directory para la autenticación.

Por ejemplo, la cadena de conexión podría ser similar a la siguiente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] Necesita [habilitar la autenticación de Azure Active Directory](sql-data-warehouse-authentication.md) para autenticarse con Active Directory.

## 2\. Consultar

Después de la conexión, puede emitir cualquier instrucción Transact-SQL en la instancia. En este ejemplo, las consultas se envían en modo interactivo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Los siguientes ejemplos muestran cómo se pueden ejecutar las consultas en el modo por lotes con la opción -Q o mediante la canalización de su SQL a sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## Pasos siguientes

Consulte [documentación de sqlcmd][sqlcmd] para más información sobre las opciones disponibles en sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->

<!---HONumber=AcomDC_0907_2016-->