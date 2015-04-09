<properties 
	pageTitle="Uso de Hive en Hadoop de HDInsight para el análisis de registros de sitios web | Azure" 
	description="Aprenda a usar Hive con HDInsight para analizar registros de sitios web. Usará un archivo de registro como entrada en una tabla de HDInsight y HiveQL para consultar los datos." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="nitinme"/>

# Uso de Hive con HDInsight para analizar registros de sitios web

Aprenda a usar HiveQL con HDInsight para analizar registros de un sitio web. El análisis de registros de sitios web se puede usar para segmentar su audiencia en función de actividades similares, categorizar a los visitantes de un sitio por datos demográficos, descubrir el contenido que ven, los sitios web de los que proceden, etc.

En este ejemplo, usará un clúster de HDInsight para analizar archivos de registro de sitios web para saber la frecuencia de las visitas al sitio web en un día desde sitios web externos y un resumen de los errores del sitio web que encuentran los usuarios. Aprenderá a:

- Conectarse a un blob de almacenamiento de Azure que contiene archivos de registro de sitios web.
- Crear tablas de HIVE para consultar esos registros.
- Crear consultas de HIVE para analizar los datos.
- Usar Microsoft Excel para conectarse a HDInsight (usando una conexión ODBC) para recuperar los datos analizados.

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##Requisitos previos

- Debe haber aprovisionado un **clúster de HDInsight**. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. 
- Debe tener Microsoft Excel 2010 o Microsoft Excel 2013 instalado.
- Debe tener [Microsoft Hive ODBC Driver](http://www.microsoft.com/download/details.aspx?id=40886) para importar datos de Hive en Excel.


##Para ejecutar el ejemplo

1. En el Portal de administración de Azure, haga clic en el clúster donde desea ejecutar el ejemplo y, a continuación, en **Consola de consultas** en la parte inferior. También puede abrir directamente la Consola de consultas con la siguiente dirección URL:

	 	https://<clustername>.azurehdinsight.net
	
	Cuando se le pida, autentíquese con el nombre de usuario y la contraseña de administrador que usó cuando aprovisionó este clúster.
  
2. En la página web que se abre, haga clic en la pestaña **Galería de introducción** y, en la categoría **Ejemplos**, haga clic en el ejemplo **Análisis de registros de sitios web**.
3. Siga las instrucciones que se proporcionan en la página web para finalizar el ejemplo.

##Pasos siguientes
Pruebe el ejemplo de análisis de datos de sensor usando HDInsight de Azure. Consulte [Análisis de datos de sensor usando Hive con HDInsight][hdinsight-sensor-data-sample].


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png

<!--HONumber=49-->