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
   ms.date="06/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Consulta de Almacenamiento de datos SQL de Azure (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizaje automático de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Este tutorial usa la utilidad de línea de comandos sqlcmd para consultar un Almacenamiento de datos SQL de Azure.

## Requisitos previos

Para seguir paso a paso este tutorial, necesita:

-  [sqlcmd.exe][]. Para descargar este archivo, consulte la página de [Utilidades de la línea de comandos 11 de Microsoft para SQL Server][].

## 1\. Conectar

Para empezar a trabajar con sqlcmd, abra el símbolo del sistema y escriba **sqlcmd** seguido de la cadena de conexión de la base de datos de Almacenamiento de datos SQL. La cadena de conexión deberá tener los siguientes parámetros obligatorios:

+ **Servidor (-S):** servidor con el formato `<`Nombre del servidor`>`.database.windows.net
+ **Base de datos (-d):** nombre de la base de datos.
+ **Usuario (-U):** usuario de servidor con el formato `<`usuario`>`
+ **Contraseña (-P):** la contraseña asociada con el usuario.
+ **Habilitar identificadores entre comillas (-I):** los identificadores entre comillas deben estar habilitados para poder conectarse a una instancia de Almacenamiento de datos SQL.

Por ejemplo, la cadena de conexión podría ser similar a la siguiente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] El parámetro opcional -I, que habilita identificadores entre comillas, es actualmente obligatorio para conectarse al Almacenamiento de datos SQL.

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
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
C:\>"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## Pasos siguientes

Para más información acerca de todas las opciones de sqlcmd, consulte la [documentación de sqlcmd][sqlcmd.exe].

<!--Articles-->
[connecting with PowerBI]: sql-data-warehouse-integrate-power-bi.md


<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/library/ms162773.aspx
[Utilidades de la línea de comandos 11 de Microsoft para SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portal]: https://portal.azure.com

<!--Image references-->

<!---HONumber=AcomDC_0622_2016-->