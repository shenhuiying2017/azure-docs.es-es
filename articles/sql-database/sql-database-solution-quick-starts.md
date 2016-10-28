<properties
   pageTitle="Tutoriales de soluciones de Base de datos SQL de Azure | Microsoft Azure"
   description="Conozca las soluciones de Base de datos SQL de Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# Exploración de tutoriales de soluciones de Base de datos SQL de Azure

Este artículo contiene información general sobre los tutoriales de soluciones de Base de datos SQL de Azure. Estos tutoriales de inicio rápido se encuentran en el repositorio de muestras de GitHub de SQL Server y demostran el uso de Base de datos SQL en una solución completa basada en escenarios del mundo real. Para ver tutoriales paso a paso sencillos que demuestran el uso de una determinada característica de Base de datos SQL, consulte [Exploración de tutoriales de Base de datos SQL de Azure](sql-database-explore-tutorials.md).

## Pruebe la demostración y laboratorio práctico de WingTipTickets

La demostración y laboratorio práctico [Azure SQL Database WingTipTickets](https://github.com/microsoft/wingtiptickets) muestran una aplicación de ejemplo basada en Base de datos SQL de Azure y Búsqueda de Azure que se usa para la venta de entradas para conciertos.


## Recopilación y supervisión de datos de uso de recursos entre varios grupos

[Solution Quick Start: Elastic Pool telemetry using PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) (Inicio rápido de solución: telemetría de grupo elástico con PowerShell) proporciona una solución para la recopilación y la supervisión del uso de recursos de SQL Database en varios grupos de una suscripción. Si hay un gran número de bases de datos en una suscripción, es complicado supervisar cada grupo elástico por separado.

Para resolver este problema, se pueden combinar las consultas de T-SQL y los cmdlets de PowerShell de Base de datos SQL para recopilar datos de uso de recursos de varios grupos y sus bases de datos. Esto ayuda a supervisar y analizar el uso de recursos de forma más eficaz.

Este inicio rápido proporciona un conjunto de secuencias de comandos de PowerShell y consultas de T-SQL, junto con documentación sobre lo que hace la solución y cómo implementarlo.

## Introducción a base de datos elástica en un escenario SaaS

 [Inicio rápido de solución: panel personalizado de grupo elástico para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) proporciona una solución para un escenario Software-as-a-Solution (SaaS) que aprovecha la característica de base de datos elástica de Base de datos SQL para proporcionar un back-end de base de datos escalable y rentable para una aplicación SaaS.

En esta solución, se le guiará a través de la implementación de una aplicación web. Esta aplicación web le permite visualizar la carga que se crea en una base de datos elástica mediante un generador de carga que utiliza un panel personalizado que complementa el Portal de Azure.

Este inicio rápido le proporciona una aplicación web de supervisión y un generador de carga junto con la documentación sobre lo que hace la aplicación y cómo utilizarla.

## Creación de una Base de datos SQL de Azure con desarrollo Entity Framework y Code First

El vídeo y la muestra en [Code First a nueva base de datos](https://msdn.microsoft.com/data/jj193542.aspx) proporciona una introducción al desarrollo de Code First que se dirige a una nueva base de datos. Este escenario se destina a una base de datos que no existe, pero que se creará mediante Code First. Como alternativa, en el escenario se crea una base de datos vacía a la que Code First agrega nuevas tablas.

Code First le permite definir su modelo mediante el uso de las clases de C# o Visual Basic. NET. Puede realizar la configuración adicional opcional usando atributos en sus clases y propiedades o usando una API fluida.

## Integración de herramientas para bases de datos elásticas en una aplicación de Entity Framework

El ejemplo de [Biblioteca de cliente de base de datos elástica con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) muestra los cambios que debe realizar en una aplicación de Entity Framework para integrarla con [herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md). Se centra en la composición de la [administración de mapas de particiones](sql-database-elastic-scale-shard-map-management.md) y el [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) con el enfoque Code First de Entity Framework.

El [ejemplo de Code First en un nuevo ejemplo de base de datos para EF](http://msdn.microsoft.com/data/jj193542.aspx) servirá como ejemplo de ejecución en todo momento. El código de ejemplo que acompaña a este documento forma parte del conjunto de herramientas de bases de datos elásticas en los ejemplos de código de Visual Studio.

## Integrar herramientas de base de datos elástica con seguridad de nivel de fila

[Aplicaciones para varios inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md) muestra los cambios que debe realizar en una aplicación de Entity Framework para integrar [herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md) con [seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131). También se ilustra cómo usar estas tecnologías conjuntamente para crear una aplicación con un nivel de datos altamente escalable que admita particiones multiinquilino.

Esto se realiza mediante ADO.NET SqlClient o Entity Framework. Este ejemplo amplía la [biblioteca de cliente de Base de datos elásticas con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) agregando compatibilidad con las bases de datos de particiones multiinquilino. También genera una aplicación de consola sencilla para la creación de blogs y publicaciones, con cuatro inquilinos y dos bases de datos de particiones multiinquilino.

## Crear encuestas en línea con la aplicación de encuestas de Tailspin

Esta [aplicación de ejemplo de encuestas de Tailspin](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) es una aplicación web multiinquilino que permite a los usuarios crear encuestas en línea. Asimismo, trata algunos problemas importantes relacionados con la forma de administrar las identidades de usuario en una aplicación multiinquilino, incluidos el inicio de sesión, la autenticación, la autorización y los roles de aplicación.

## Obtenga información acerca de las características de seguridad más recientes de Base de datos SQL con la aplicación de demostración de Clinic de Contoso

Esta [aplicación de demostración de Clinic de Contoso](https://github.com/Microsoft/azure-sql-security-sample) presenta las últimas características de seguridad de Base de datos SQL.

## Pasos siguientes

[Exploración de tutoriales de Base de datos SQL de Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0907_2016-->