<properties
	pageTitle="Conexión a Base de datos SQL mediante Node.js"
	description="Este tema muestra un ejemplo de código Node.js que puede usar para conectarse a la base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="meetb"/>

# Conexión a Base de datos SQL mediante Node.js

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

En este tema se muestra cómo conectarse a una base de datos SQL de Azure y consultarla mediante Node.js. Puede ejecutar esta muestra desde las plataformas Windows, Ubuntu Linux o Mac.

## Paso 1: Configuración del entorno de desarrollo

[Consulte el artículo Prerequisites for using the tedious Node.js Driver for SQL Server (Requisitos previos para usar el controlador tedioso de Node.js para SQL Server).](https://msdn.microsoft.com/library/mt652094.aspx)

## Paso 2: Creación de una base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.

## Paso 3: Obtención de detalles de la conexión

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Paso 4: ejecución de código de muestra

[Proof of Concept connecting to SQL using Node.js](https://msdn.microsoft.com/library/mt715784.aspx) (Prueba de concepto que se conecta a SQL con Node.js)

<!---HONumber=AcomDC_0413_2016-->