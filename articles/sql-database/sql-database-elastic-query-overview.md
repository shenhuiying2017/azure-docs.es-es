<properties 
	title="Elastic database query – previewing May 2015" 
	pageTitle="Consulta de bases de datos elásticas: obtención de vistas previas; mayo de 2015" 
	description="Presentación de la característica de consultas elásticas" 
	metaKeywords="azure sql database elastic global queries" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh" />

# Consulta de bases de datos elásticas: obtención de vistas previas; junio de 2015 

Las consultas de bases de datos elásticas es una nueva característica de la vista previa de Base de datos SQL de Azure programada para finales de junio de 2015. Esta característica permite ejecutar consultas de T-SQL que se distribuyen entre varias bases de datos mediante una única conexión a una base de datos SQL de Azure. Esto permite que un conjunto de particiones de datos, definido mediante un mapa de particiones de bases de datos elásticas, aparezca como un único almacén de datos integrado. Utilizado para fines de integración de datos e informes, todo el procesamiento de consultas distribuidas se controla en segundo plano. Las conexiones se admiten usando cualquier controlador capaz de comunicarse con las bases de datos SQL como, por ejemplo, ADO.Net, ODBC, JDBC, Node.js, PHP, etc.

## Escenarios de consulta de base de datos elásticas

El objetivo de las consultas de bases de datos elásticas es el de proporcionar escenarios de informes en niveles de datos particionados en los que varias bases de datos o particiones aportan filas a un único resultado global de una consulta de T-SQL. La consulta de T-SQL puede estar compuesta por el usuario o por la aplicación directa o indirectamente a través de herramientas que están conectadas a la base de datos de consultas elásticas de consulta global. Esto resulta especialmente útil para la inteligencia empresarial comercial, los informes, las herramientas de integración de datos o el software empaquetado que no se pueden extender directamente mediante las bibliotecas de escala elástica. También facilita el acceso a toda una colección de datos particionados a través de las consultas emitidas por SQL Server Management Studio o Visual Studio. Asimismo, admite el acceso a datos de múltiples particiones transparentes de Entity Framework u otros entornos de ORM. La figura 1 muestra este escenario donde la consulta de base de datos elástica de consulta global ofrece un segundo método para enviar consultas al nivel de datos particionados, además de a la aplicación de nube existente (que puede usar las bibliotecas de escala elástica).

![Consultas de bases de datos elásticas][1]

Los escenarios se caracterizan por las siguientes topologías:

-	Nivel de datos particionados (topología 1): el nivel de datos se ha diseñado para la creación de particiones. Las particiones se crean y administran mediante (1) la biblioteca de cliente de bases de datos elásticas o mediante (2) la creación de particiones automáticas. El caso de informes consiste en calcular los informes de varias particiones con requisitos de funcionamiento o conectividad que van más allá de lo que puede llevar a cabo la consulta de múltiples particiones de la biblioteca cliente de bases de datos elásticas. 

-	Diseño de bases de datos múltiples (topología de 2): en este caso, el nivel de datos se ha particionado verticalmente para que distintos tipos de datos puedan residir en diferentes bases de datos. El caso de informes consiste en calcular los informes de los datos que se distribuyen en varias bases de datos, aunque deben integrarse en un resultado global para el informe.

La vista previa de las consultas de bases de datos elásticas es válida para ambos casos. Los clientes con topología 1 pueden usar su mapa de particiones existentes si utilizan la biblioteca de cliente de base de datos elásticas para administrar las particiones. Las particiones automáticas deberán crear un mapa de particiones con las herramientas de bases de datos elásticas para poder usar esta capacidad de vista previa. Los clientes que sigan la topología 2 deberán crear un mapa de particiones distinto para cada una de sus bases de datos. Estos mapas de particiones apuntarán únicamente a una partición que podrá exponer sus tablas para las consultas entre particiones.

## Pasos siguientes
La consulta de bases de datos elásticas está programada para vista previa a finales de junio de 2015. Vuelva a esta página para obtener más detalles e instrucciones paso a paso sobre el uso de la característica en ese momento.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
<!--anchors-->

<!---HONumber=58_postMigration-->