<properties 
	pageTitle="Escenarios habituales para usar la Factoría de datos de Azure" 
	description="Obtenga información sobre escenarios habituales para usar el servicio Factoría de datos de Azure." 
	services="data-factory" 	
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Escenarios habituales para usar la Factoría de datos de Azure
En esta sección se describen algunos escenarios de ejemplo que admite actualmente la Factoría de datos de Azure, y seguirán creciendo como escenarios de centros.

> [AZURE.NOTE]Lea el artículo [Introducción a la Factoría de datos de Azure][datafactory-introduction] artículo antes de leer este.

##Escenario n.º 1: orígenes de datos para el centro de datos
![Orígenes en el centro de datos][image-data-factory-introduction-secenario1-source-datahub]

Las empresas tienen datos de tipos dispares que se encuentran en diversos orígenes. El primer paso en la creación de un sistema de producción de información es conectarse a todos los orígenes necesarios de datos y procesamiento, como servicios SaaS, recursos web compartidos de archivos, FTP, servicios web y mover los datos según sea necesario para su posterior procesamiento.

Sin la factoría de datos, las empresas deben crear componentes de movimiento de datos personalizados o escribir servicios personalizados para integrar estos orígenes de datos y procesamientos. Esto resulta costoso; además, es difícil integrar y mantener dichos sistemas y a menudo se carece de capacidades de supervisión y alerta de clase empresarial y de los controles que un servicio completamente administrado puede ofrecer.
  
Con la Factoría de datos de Azure, los servicios de almacenamiento y procesamiento de datos se recopilan en un contenedor de centros que facilita y optimiza las actividades de cálculo y almacenamiento, permite la administración unificada del consumo de los recursos y proporciona servicios para el movimiento de los datos cuando es necesario.

##Escenario n.º 2: operacionalización de la producción de información
Los escenarios de operacionalización son el siguiente paso lógico después de los escenarios de orígenes de datos. Una vez que los datos están presentes en un centro, puede crear y operacionalizar canalizaciones de datos para producir de forma confiable datos transformados según una programación sostenible y controlada para suministrar entornos de producción con datos de confianza. La transformación de datos en la Factoría de datos de Azure tiene lugar a través de un procesamiento Hive, Pig y C\# personalizado que se ejecuta en Hadoop \(HDInsight de Azure\). Estas transformaciones se pueden usar para limpiar datos, enmascarar campos de datos críticos y llevar a cabo otras operaciones en los datos en una amplia variedad de formas complejas. Por lo general, la operatividad se logra con servicios personalizados e infraestructura compleja y difícil de mantener, y supone una serie de desafíos de implementación, administración, escalado, solución de problemas y control de versiones como solución.
  
Con la factoría de datos como un servicio completamente administrado, los usuarios pueden operacionalizar estas canalizaciones definiéndolas con programaciones periódicas puntuales o complejas, y la orquestación se controla directamente mediante el servicio Factoría de datos. Con concentradores, la administración de clústeres para todos los datos y procesamiento dentro de un concentrador se controla en nombre del usuario, por tanto, los usuarios se pueden centrar en los análisis transformativos en lugar de la administración de la infraestructura. La factoría de datos de Azure quita los desafíos de trabajar con servicios personalizados frágiles y permite a las empresas producir información de confianza de manera confiable y reproducible.


##Escenario n.º 3: integración de la producción de información con la detección de datos
Los enfoques y tecnologías tradicionales de BI, si bien proporcionan un "origen autorizado de la verdad", casi siempre tienen efectos secundarios graves: un trabajo pendiente constante de solicitudes debido a un proceso de solicitud de cambio cuidadosamente controlado. Para adaptarse rápidamente a las cuestiones empresariales cambiantes, las empresas precisan de una gran flexibilidad para conectar sus sistemas de producción de la información con sus sistemas de consumo de la información. La factoría de datos de Azure ayuda a abordar el desafío de conectar estos sistemas con canalizaciones de datos optimizadas para la producción de información y el desafío del consumo de la información al permitir que estén disponibles datos de confianza actualizados en formatos de consumo fácil.
  
La Factoría de datos de Azure es compatible con las siguientes capacidades para permitir el consumo sencillo de los datos producidos:

- Movimiento fácil \(de forma puntual o programada\) de los activos de datos producidos a mercados de datos relacionales para el consumo mediante el uso de herramientas de BI existentes \(Excel, Tableau, etc.\).
- Consumo de los activos de datos producidos por una factoría de datos usando directamente Power Query en Excel.

Vea los temas siguientes sobre el consumo de datos mediante Power Query:

- [Power Query: conexión al almacenamiento de tablas de Microsoft Azure][Power-Query-Azure-Table]
- [Power Query: conexión al almacenamiento de blobs de Microsoft Azure][Power-Query-Azure-Blob]
- [Power Query: conexión a Base de datos SQL de Microsoft Azure][Power-Query-Azure-SQL]
- [Power Query: conexión a SQL Server local][Power-Query-OnPrem-SQL] 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[copy-data-with-adf]: http://azure.microsoft.com/documentation/articles/data-factory-copy-activity/
[use-pig-hive]: http://azure.microsoft.com/documentation/articles/data-factory-pig-hive-activities/
[run-map-reduce]: http://azure.microsoft.com/documentation/articles/data-factory-map-reduce/
[azure-ml-adf]: http://azure.microsoft.com/documentation/articles/data-factory-create-predictive-pipelines/

[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md
[datafactory-introduction]: data-factory-introduction.md

[image-data-factory-introduction-secenario1-source-datahub]: ./media/data-factory-common-scenarios/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]: ./media/data-factory-common-scenarios/Scenario2-OperationalizeInformationProduction.png




<!--HONumber=54-->