<properties
   pageTitle="Conexión a Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Información general sobre la conexión a Almacenamiento de datos SQL de Azure"
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
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# Conexión a Almacenamiento de datos SQL de Azure

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-connect-overview.md)
- [Autenticación](sql-data-warehouse-authentication.md)
- [Controladores](sql-data-warehouse-connection-strings.md)

Información general sobre la conexión a Almacenamiento de datos SQL de Azure.

## Detección de la cadena de conexión desde el portal

El Almacenamiento de datos SQL está asociado a un servidor SQL de Azure. Para conectarse, necesita el nombre completo del servidor (***nombreDeServidor**.database.windows.net*).

Para buscar el nombre del servidor completo:

1. Vaya al [Portal de Azure][].
2. Haga clic en **Bases de datos SQL** y haga clic en la base de datos a la que desea conectarse. Este ejemplo usa la base de datos de ejemplo AdventureWorksDW.
3. Busque el nombre del servidor completo:

    ![Nombre del servidor completo][1]

## Configuración de conexión
Almacenamiento de datos SQL estandariza algunas opciones de configuración durante la creación de conexiones y objetos. Estas opciones no se pueden invalidar.

| Configuración de base de datos | Valor |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | ACTIVAR |
| QUOTED\_IDENTIFIERS | ACTIVAR |
| NO\_COUNT | DESACTIVAR |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| Intercalación de base de datos | SQL\_Latin1\_General\_CP1\_CI\_AS |

## Sesiones y solicitudes
Una vez que se ha realizado una conexión y se ha establecido una sesión, está listo para escribir y enviar consultas a Almacenamiento de datos SQL.

Cada consulta se representa mediante uno o varios identificadores de solicitud. Todas las consultas enviadas en esa conexión forman parte de una sola sesión y, por lo tanto, estarán representadas por un identificador de sesión único.

Sin embargo, como Almacenamiento de datos SQL es un sistema MPP (procesamiento paralelo masivo) distribuido, tanto los identificadores de sesión como los de solicitud se exponen de manera algo diferente en comparación con SQL Server.

Las sesiones y las solicitudes se representan con sus identificadores respectivos.

| Identificador | Valor de ejemplo |
| :--------- | :------------ |
| Id. de sesión | SID123456 |
| Id. de solicitud | QID123456 |

Observe que el Id. de sesión está precedido por SID, la forma corta de Session ID, y que las solicitudes están precedidas por QID, la forma corta de Query ID.

Necesitará esta información para ayudarle a identificar la consulta al supervisar el rendimiento de las consultas. Puede supervisar el rendimiento de las consultas mediante el [Portal de Azure] y las vistas de administración dinámica.

Esta consulta permite identificar la sesión actual.

```sql
SELECT SESSION_ID()
;
```

Para ver todas las consultas que están en ejecución o que se han ejecutado recientemente en el almacenamiento de datos, puede usar el siguiente ejemplo. Esto crea una vista y, a continuación, la ejecuta.

```sql
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;

SELECT * FROM dbo.vSessionRequests;
```

## Pasos siguientes

Para empezar a consultar el almacenamiento de datos con Visual Studio y otras aplicaciones, consulte [Query with Visual Studio][] (Realización de consultas con Visual Studio).


<!--Arcticles-->

[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Portal de Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0622_2016-->