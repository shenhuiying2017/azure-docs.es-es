<properties
	pageTitle="Conexión a Base de datos SQL mediante Java con JDBC en Windows | Microsoft Azure"
	description="Este tema muestra un ejemplo de código Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo usa JDBC y se ejecuta en un equipo cliente de Windows."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="lbosq"/>


# Conexión a la base de datos SQL mediante Java con JDBC en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema presenta un ejemplo de código de Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo de Java se basa en la versión 1.8 del kit de desarrollo de Java (JDK). El ejemplo se conecta a una base de datos SQL de Azure mediante el controlador JDBC.

## Paso 1: Configuración del entorno de desarrollo

[Configure development environment for Java development](https://msdn.microsoft.com/library/mt720658.aspx) (Configuración del entorno de desarrollo para el desarrollo de Java)

## Paso 2: Creación de una base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos.

## Paso 3: Obtención de la cadena de conexión

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Si usa el controlador JDBC de JTDS, tiene que agregar "ssl=require" a la dirección URL de la cadena de conexión y establecer la siguiente opción para JVM "-Djsse.enableCBCProtection=false". Esta opción de JVM deshabilita una revisión para una vulnerabilidad de seguridad; por tanto, asegúrese de entender qué riesgo implica antes de establecer esta opción.

## Paso 4: ejecución de código de muestra

* [Proof of Concept connecting to SQL using Java](https://msdn.microsoft.com/library/mt720656.aspx) (Prueba de concepto que se conecta a SQL con Java)

## Pasos siguientes

* Visite el [Centro para desarrolladores de Java](/develop/java/).
* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md).
* Puede encontrar más información en [Microsoft JDBC Driver para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx).

## Recursos adicionales 

* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Descubra todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0622_2016-->