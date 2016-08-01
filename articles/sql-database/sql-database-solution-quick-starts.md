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
   ms.date="06/22/2016"
   ms.author="carlrab"/>

# Exploración de tutoriales de soluciones de Base de datos SQL de Azure

Este artículo contiene información general sobre los tutoriales de soluciones de Base de datos SQL de Azure. Estos tutoriales demuestran el uso de Base de datos SQL en la solución completa basada en escenarios del mundo real. Para ver tutoriales paso a paso sencillos que demuestran el uso de una determinada característica de Base de datos SQL de Azure, consulte [Exploración de tutoriales de Base de datos SQL de Azure](sql-database-explore-tutorials.md).

## Demostración y laboratorio práctico de WingTipTickets

Demostración y laboratorio práctico [Azure SQL Database WingTipTickets](https://github.com/microsoft/wingtiptickets). Estos archivos constituyen unas prácticas guiadas que muestran una aplicación de ejemplo basada en Base de datos SQL de Azure y Búsqueda de Azure que se usa para la venta de entradas para conciertos

## Recopilación y supervisión de datos de uso de recursos entre varios grupos

Este tutorial de soluciones proporciona una solución para la recopilación y la supervisión del uso de recursos de Base de datos SQL de Azure en varios grupos de una suscripción. Si hay un gran número de bases de datos en una suscripción, es complicado supervisar cada grupo elástico por separado. Para resolver este problema, se pueden combinar las consultas de T-SQL y los cmdlets de PowerShell de Base de datos SQL para recopilar datos de uso de recursos de varios grupos y sus bases de datos para la supervisión y el análisis del uso de recursos.

[Manage Mulitiple Elastic Pools in SQL Database Using PowerShell and Power BI](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) (Administración de varios grupos elásticos en Base de datos SQL usando PowerShell y Power BI) en el repositorio de ejemplos de SQL Server de GitHub proporciona un conjunto de scripts de PowerShell y consultas T-SQL, junto con documentación sobre para qué sirven y cómo se usan.

## Introducción al uso de grupos elásticos en un escenario de SaaS

Este tutorial de soluciones proporciona una solución para un escenario de Software como solución (SaaS) que aprovecha los grupos elásticos para proporcionar un back-end de base de datos escalable y rentable de una aplicación SaaS. En esta solución, recorrerá los pasos de implementación de una aplicación web que le permite visualizar la carga que se crea en un grupo elástico. Dicha carga se genera mediante un generador de carga usando un panel personalizado que complementa al Portal de Azure.

[Elastic Pool Custom Dashboard for Saas](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) (Panel personalizado de grupos elásticos para SaaS) en el repositorio de ejemplos de SQL Server de GitHub proporciona un generador de carga, así como una aplicación web de supervisión junto con la documentación sobre para qué sirven y cómo se usan.

## Creación de una base de datos SQL de Azure mediante desarrollo de Entity Framework y Code First

En este vídeo y ejemplo se proporciona una introducción al desarrollo de Code First. Presentaremos un escenario en el que haremos referencia a una nueva base de datos que no existe y que Code First creará, o bien a una vacía a la que agregará nuevas tablas. Code First permite definir un modelo mediante clases de C# o VB.Net. Si lo desea, pueden agregarse más valores de configuración utilizando atributos en las clases y propiedades, o bien mediante una API fluida. Consulte [Codificar primero una nueva base de datos](https://msdn.microsoft.com/data/jj193542.aspx).

## Integración de herramientas para bases de datos elásticas en una aplicación de Entity Framework

Este ejemplo muestra los cambios que hay que realizar en una aplicación de Entity Framework para realizar una integración con las [herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md). Se centra en la composición de [Shard Map Management](sql-database-elastic-scale-shard-map-management.md) y el [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) con Entity Framework Code First. El [ejemplo Codificar primero una nueva base de datos](http://msdn.microsoft.com/data/jj193542.aspx) para EF nos sirve como muestra para todo el ejemplo. El código de ejemplo que acompaña a este documento forma parte del conjunto de ejemplos de las herramientas de bases de datos elásticas en los ejemplos de código de Visual Studio. Consulte [Biblioteca de cliente de base de datos elástica con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md).

## Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila

Este ejemplo muestra los cambios que hay que realizar en una aplicación de Entity Framework para integrar las [herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md) con la [seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131). También se ilustra cómo usar estas tecnologías conjuntamente para crear una aplicación con un nivel de datos altamente escalable que admita particiones multiinquilino mediante el uso de ADO.NET SqlClient o Entity Framework. Este ejemplo amplía la [Biblioteca de cliente de base de datos elásticas con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) agregando compatibilidad con las bases de datos de particiones multiinquilino. También genera una aplicación de consola sencilla para la creación de blogs y publicaciones, con cuatro inquilinos y dos bases de datos de particiones multiinquilino. Consulte [Aplicaciones multiinquilino con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md).

## Aplicación Tailspin Surveys

Este ejemplo es una aplicación web multiinquilino, llamada "Surveys", que permite a los usuarios crear encuestas en línea. Asimismo, muestra algunos problemas importantes relacionados con la administración de las identidades de usuario en una aplicación multiinquilino, incluidos el inicio de sesión, la autenticación, la autorización y los roles de aplicación. Para ejecutar este ejemplo, consulte [How to run the Tailspin Surveys sample application](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) (Ejecución de la aplicación de ejemplo Tailspin Surveys).

## Aplicación de demostración de Clinic de Contoso

Este ejemplo muestra las características de seguridad de Azure SQL DB (V12). Para ejecutar este ejemplo, consulte [Contoso Clinic Demo Application](https://github.com/Microsoft/azure-sql-security-sample) (Aplicación de demostración de Clinic de Contoso).

## Pasos siguientes

[Exploración de tutoriales de Base de datos SQL de Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0720_2016-->