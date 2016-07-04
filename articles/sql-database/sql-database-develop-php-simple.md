<properties
	pageTitle="Conexión a Base de datos SQL mediante PHP en Windows | Microsoft Azure"
	description="Presenta un programa PHP de ejemplo que se conecta a la base de datos SQL de Azure desde un cliente de Windows y proporciona vínculos a los componentes de software necesarios que necesita el cliente."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="meetb"/>


# Conexión a la base de datos SQL mediante PHP en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema muestra cómo puede conectarse a la base de datos SQL de Azure desde una aplicación cliente escrita en PHP que se ejecuta en Windows.

## Paso 1: Configuración del entorno de desarrollo

[Configure development environment for PHP development](https://msdn.microsoft.com/library/mt720663.aspx) (Configuración del entorno de desarrollo para el desarrollo de PHP)

## Paso 2: Creación de una base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.


## Paso 3: Obtención de detalles de la conexión

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Paso 4: ejecución de código de muestra

* [Proof of concept connecting to SQL using PHP](https://msdn.microsoft.com/library/mt720665.aspx) (Prueba de concepto que se conecta a SQL con PHP)
* [Connect resiliently to SQL with PHP](https://msdn.microsoft.com/library/mt720667.aspx) (Conexión firme a SQL con PHP)


## Pasos siguientes

* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md).
* Puede encontrar más información en [Microsoft PHP Driver para SQL Server](https://msdn.microsoft.com/library/dn865013.aspx).
* Para obtener más información sobre el uso y la instalación de PHP, vea [Acceso a bases de datos de SQL Server con PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## Recursos adicionales 

* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Descubra todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0622_2016-->