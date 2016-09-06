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
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Conexión a Almacenamiento de datos SQL de Azure

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-connect-overview.md)
- [Autenticación](sql-data-warehouse-authentication.md)
- [Controladores](sql-data-warehouse-connection-strings.md)

Información general sobre la conexión a Almacenamiento de datos SQL de Azure.

## Detección de la cadena de conexión desde el portal

El Almacenamiento de datos SQL está asociado a un servidor SQL de Azure. Para conectarse, necesita el nombre completo del servidor. Por ejemplo, **miServidor**.database.windows.net.

Para buscar el nombre del servidor completo:

1. Vaya al [Portal de Azure][].
2. Haga clic en **Bases de datos SQL** y haga clic en la base de datos a la que desea conectarse. Este ejemplo usa la base de datos de ejemplo AdventureWorksDW.
3. Busque el nombre del servidor completo:

    ![Nombre del servidor completo][1]

## Configuración de conexión

Almacenamiento de datos SQL estandariza algunas opciones de configuración durante la creación de conexiones y objetos. Estas opciones no se pueden invalidar.

| Configuración de base de datos | Valor |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ACTIVAR |
| [QUOTED\_IDENTIFIERS][] | ACTIVAR |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## Supervisión de conexiones y consultas

Una vez que se ha realizado una conexión y se ha establecido una sesión, está listo para escribir y enviar consultas a Almacenamiento de datos SQL. Para obtener información sobre cómo supervisar las sesiones y las consultas, consulte [Supervisión de la carga de trabajo mediante DMV][].

## Pasos siguientes

Para empezar a realizar consultas en el almacenamiento de datos con Visual Studio y otras aplicaciones, consulte [Query with Visual Studio][] (Realización de consultas con Visual Studio).

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Supervisión de la carga de trabajo mediante DMV]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal de Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0831_2016-->