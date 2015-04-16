<properties 
	pageTitle="Análisis de datos de sensor usando Hive y Microsoft Azure HDInsight (Hadoop)" 
	description="Vea cómo usar Hive y Excel para analizar y visualizar datos de sensor con HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

#Análisis de datos de sensor usando Hive con HDInsight

Vea cómo analizar datos de sensor usando Hive con HDInsight (Hadoop) y visualizar los datos en Microsoft Excel con Power View.

En este ejemplo, usará Hive para procesar datos de historial producidos por sistemas de calefacción, ventilación y aire acondicionado (HVAC) para identificar sistemas que no pueden mantener de manera fiable una temperatura establecida . Aprenderá a:

- Crear tablas de HIVE para consultar datos almacenados en archivos de valores separados por comas (CSV).
- Crear consultas de HIVE para analizar los datos.
- Usar Microsoft Excel para conectarse a HDInsight (usando una conexión ODBC) para recuperar los datos analizados.
- Usar Power View para visualizar los datos.

![A diagram of the solution architecture](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

##Requisitos previos:

* Un clúster de HDInsight (Hadoop): consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md) para obtener información sobre la creación de un clúster.

* Microsoft Excel 2013

	> [AZURE.NOTE] Microsoft Excel se usa para la visualización de datos con [Power View](https://support.office.com/es-es/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=es-es&rs=es-es&ad=US), que actualmente solo está disponible en Windows.

* [Microsoft Hive ODBC Driver](http://www.microsoft.com/es-es/download/details.aspx?id=40886)

##Para ejecutar el ejemplo

1. En el Portal de administración de Azure, haga clic en el clúster donde desea ejecutar el ejemplo y haga clic en **Consola de consultas** en la parte inferior. También puede abrir directamente la Consola de consultas con la siguiente dirección URL:

	 	https://<clustername>.azurehdinsight.net

	Cuando se le pida, autentíquese con el nombre de usuario y la contraseña de administrador que usó cuando aprovisionó este clúster.

2. En la página web que se abre, haga clic en la pestaña **Galería de introducción** y, en la categoría **Ejemplos**, haga clic en el ejemplo **Análisis de registros de sitios web**.

3. Siga las instrucciones que se proporcionan en la página web para finalizar el ejemplo.
<!--HONumber=42-->
