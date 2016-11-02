<properties
	pageTitle="Conexión a Base de datos SQL con .NET (C#) | Microsoft Azure"
	description="Use el código de ejemplo de este inicio rápido para crear una aplicación moderna con C# con el respaldado de una base de datos relacional eficaz en la nube con la base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="tobbox"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="tobiast"/>

# Conexión a Base de datos SQL mediante .NET (C#)

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## Paso 1: Configuración del entorno de desarrollo

[Configure development environment for ADO.NET development](https://msdn.microsoft.com/library/mt718321.aspx) (Configuración del entorno de desarrollo para el desarrollo de ADO.NET)

## Paso 2: Creación de una base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.

## Paso 3: Obtención de la cadena de conexión

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## Paso 4: ejecución de código de muestra

* [Proof of concept connecting to SQL using ADO.NET](https://msdn.microsoft.com/library/mt718320.aspx) (Prueba de concepto que se conecta a SQL con ADO.NET)
* [Connect resiliently to SQL with ADO.NET](https://msdn.microsoft.com/library/mt703195.aspx) (Conexión firme a SQL con ADO.NET)

## Pasos siguientes

* [Crear una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementar al Servicio de aplicaciones de Azure](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* [Creación de un servicio usando una base de datos SQL existente con el backend .NET de Servicios móviles](../mobile-services/mobile-services-dotnet-backend-use-existing-sql-database.md)
* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md).
* Puede encontrar más información en [Microsoft ADO.NET para SQL Server](https://msdn.microsoft.com/library/mt657768.aspx).

## Recursos adicionales 

* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Descubra todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0622_2016-->