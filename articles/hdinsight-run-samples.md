<properties 
	pageTitle="Ejecución de ejemplos de Hadoop en HDInsight | Azure" 
	description="Introducción al uso del servicio HDInsight de Azure con los ejemplos que se proporcionan. Use scripts de PowerShell que ejecutan programas MapReduce en clústeres de datos." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>




# Ejecución de ejemplos de Hadoop en HDInsight

Se proporciona un conjunto de muestras para ayudarle a empezar a ejecutar trabajos de MapReduce en clústeres de Hadoop con HDInsight de Azure. Estas muestras se habilitan en cada uno de los clústeres administrados de HDInsight que se crean. La ejecución de estas muestras le permitirá familiarizarse con el uso de cmdlets de HDInsight de Azure PowerShell para ejecutar trabajos en clústeres de Hadoop.

Los programas de MapReduce también se pueden ejecutar mediante programación desde una aplicación que usa la API .NET de Microsoft para HDInsight. Para obtener más información acerca de cómo usar las API de HDInsight para el envío de trabajos, consulte [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs].

En la Web se encuentra disponible mucha documentación adicional sobre las tecnologías relacionadas con Hadoop, como la transmisión y programación de MapReduce basada en Java, así como la documentación sobre los cmdlets usados en el scripting de PowerShell. Para obtener más información acerca de estos recursos, consulte la sección final **Recursos para HDInsight** del tema [Introducción a Azure HDInsight][hdinsight-introduction].

**Qué son estas muestras**

<p>Estás muestras están diseñadas para ponerle al día rápido acerca de cómo implementar trabajos de Hadoop y para ofrecerle una base de pruebas extensible para trabajar con los conceptos y los procedimientos de scripting que el servicio usa. Le proporcionan ejemplos de tareas comunes como la creación e importación de conjuntos de datos de varios tamaños, la ejecución de trabajos, la composición de trabajos de manera secuencial y el análisis de los resultados de los trabajos. El tamaño de los conjuntos de datos puede variar, lo que le permite observar los efectos que los conjuntos de datos de varios tamaños tienen en el rendimiento del trabajo.</p>


**Requisitos previos**:	

- Debe tener una cuenta de Azure. Si desea conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

- Debe haber aprovisionado un clúster de HDInsight. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md)

- Debe tener instalado Azure PowerShell y haber configurado los clústeres para utilizarlos con su cuenta. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

## Las muestras ##

HDInsight incluye las siguientes muestras.

- [**La muestra del estimador de Pi**][hdinsight-sample-pi-estimator] En este tutorial se explica cómo ejecutar un programa de MapReduce con HDInsight que usa un método estadístico (quasi-Monte Carlo) para estimar el valor de Pi.
- [**La muestra de WordCount**][hdinsight-sample-wordcount] En este tutorial se explica cómo usar un clúster de HDInsight para ejecutar un programa de MapReduce que cuenta las ocurrencias de palabras en un archivo de texto.
- [**La muestra de Graysort de 10 GB**][hdinsight-sample-10gb-graysort] En este tutorial se explica cómo ejecutar una muestra de GraySort de uso general en un archivo de 10 GB mediante HDInsight. Hay tres trabajos para ejecutar: Teragen para genera los datos, Terasort para ordenar los datos y Teravalidate para confirmar que los datos se han ordenado correctamente.
- [**La muestra de transmisión de C#**][hdinsight-sample-csharp-streaming] En este tutorial se explica cómo usar C# para escribir un programa de MapReduce que usa la interfaz de transmisión de Hadoop. 


## Ejecución de las muestras ##

Las muestras se pueden ejecutar con Azure PowerShell. Las instrucciones sobre cómo hacerlo se ofrecen para cada una de las muestras en las páginas a las que remiten los vínculos anteriores.

## Pasos siguientes ##

En este artículo y en los artículos sobre cada muestra ha obtenido información acerca de cómo ejecutar las muestras incluidas en los clústeres de HDInsight con la utilización de Azure PowerShell. Para obtener acceso a tutoriales acerca de cómo usar Pig, Hive y MapReduce con HDInsight, consulte los siguientes temas:

* [Introducción al servicio HDInsight de Azure][hdinsight-get-started]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]
* [Depuración de HDInsight: mensajes de error][hdinsight-errors]


[hdinsight-errors]: ../hdinsight-debug-jobs/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/es-es/library/dn479185.aspx

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-introduction]: ../hdinsight-hadoop-introduction/



[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/


<!--HONumber=42-->
