<properties
   pageTitle="Tutoriales de soluciones de Base de datos SQL de Azure | Microsoft Azure"
   description="Conozca las soluciones de Base de datos SQL de Azure."
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="06/01/2016"
   ms.author="carlrab"/>

# Exploración de tutoriales de soluciones de Base de datos SQL de Azure

Este artículo contiene información general sobre los tutoriales de soluciones de Base de datos SQL de Azure. Estos tutoriales demuestran el uso de Base de datos SQL en la solución completa basada en escenarios del mundo real. Para ver tutoriales paso a paso sencillos que demuestran el uso de una determinada característica de Base de datos SQL de Azure, consulte [Exploración de tutoriales de Base de datos SQL de Azure](sql-database-explore-tutorials.md).

## Recopilación y supervisión de datos de uso de recursos entre varios grupos

Este tutorial de soluciones proporciona una solución para la recopilación y la supervisión del uso de recursos de Base de datos SQL de Azure en varios grupos de una suscripción. Si hay un gran número de bases de datos en una suscripción, es complicado supervisar cada grupo elástico por separado. Para resolver este problema, se pueden combinar las consultas de T-SQL y los cmdlets de PowerShell de Base de datos SQL para recopilar datos de uso de recursos de varios grupos y sus bases de datos para la supervisión y el análisis del uso de recursos.

[Administre varios grupos elásticos en Base de datos SQL mediante PowerShell y Power BI](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools). En el repositorio de ejemplos de SQL Server de GitHub se proporciona un conjunto de scripts de powershell y consultas de T-SQL, junto con documentación sobre cómo funcionan y la forma de usarlos.

## Introducción al uso de grupos elásticos en un escenario de SaaS

Este tutorial de soluciones proporciona una solución para un escenario de Software como solución (SaaS) que aprovecha los grupos elásticos para proporcionar un back-end de base de datos escalable y rentable de una aplicación SaaS. En esta solución, recorrerá los pasos de implementación de una aplicación web que le permite visualizar la carga que se crea en un grupo elástico. Dicha carga se genera mediante un generador de carga usando un panel personalizado que complementa al Portal de Azure.

[Escenario Saas con grupos elásticos](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools). En el repositorio de ejemplos de SQL Server de GitHub se proporciona un generador de carga, así como ejemplos de supervisión de una aplicación web junto con la documentación sobre cómo funcionan y cómo usarlos.

## Pasos siguientes

[Exploración de tutoriales de Base de datos SQL de Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0608_2016-->