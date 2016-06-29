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
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/20/2016"
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

## Recursos adicionales

Para obtener información sobre los patrones comunes de la arquitectura de datos de aplicaciones de base de datos de software como servicio (SaaS) multiinquilino, consulte [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->