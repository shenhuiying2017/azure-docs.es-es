<properties
	pageTitle="Ejecución de ejemplos de Hadoop en HDInsight | Microsoft Azure"
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
	ms.date="03/30/2015" 
	ms.author="bradsev"/>




#Ejecución de ejemplos de Hadoop en HDInsight

Se proporciona un conjunto de muestras para ayudarle a empezar a ejecutar trabajos de MapReduce en clústeres de Hadoop con HDInsight de Azure. Estas muestras se habilitan en cada uno de los clústeres administrados de HDInsight que se crean. La ejecución de estas muestras le permitirá familiarizarse con el uso de cmdlets de Azure PowerShell para ejecutar trabajos en clústeres de Hadoop.

Los programas de MapReduce también se pueden ejecutar mediante programación desde una aplicación si se usa la API .NET de Microsoft para HDInsight. Para obtener más información acerca de cómo usar las API de HDInsight para el envío de trabajos, consulte [Envío de trabajos de Hadoop en HDInsight][hdinsight-submit-jobs].

En la web se encuentra disponible mucha documentación adicional sobre las tecnologías relacionadas con Hadoop, como la transmisión y programación de MapReduce basada en Java y documentación sobre los cmdlets que se usan en el scripting de Windows PowerShell. Para obtener más información sobre estos recursos, consulte la sección final **Recursos para HDInsight** de [Introducción a Azure HDInsight][hdinsight-introduction].

**Qué son estas muestras**

<p>Estás muestras están diseñadas para ponerle al día rápido acerca de cómo implementar trabajos de Hadoop y para ofrecerle una base de pruebas extensible para trabajar con los conceptos y los procedimientos de scripting que el servicio usa. Le ofrecen ejemplos de tareas comunes, como la creación e importación de conjuntos de datos de varios tamaños, la ejecución y composición de trabajos de manera secuencial y el análisis de los resultados de los trabajos. Los tamaños de los conjuntos de datos que use pueden variar, lo que le permite observar los efectos que los conjuntos de datos de varios tamaños tienen en el rendimiento del trabajo.</p>


**Requisitos previos**:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **un clúster de HDInsight**. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).
- **Una estación de trabajo con Azure PowerShell**. Consulte [Instalación y uso de Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## Las muestras ##

HDInsight incluye las siguientes muestras:

- [**La muestra de Hadoop del estimador de Pi**][hdinsight-sample-pi-estimator]: se muestra cómo ejecutar un programa de MapReduce con HDInsight que usa un método estadístico (quasi-Monte Carlo) para estimar el valor de pi.
- [**Ejecución de un ejemplo de MapReduce que cuenta palabras en un clúster de Hadoop**][hdinsight-sample-wordcount]: se explica cómo usar un clúster de HDInsight para ejecutar un programa de MapReduce que cuenta las ocurrencias de palabras en un archivo de texto.
- [**La muestra de Hadoop de Graysort de 10 GB**][hdinsight-sample-10gb-graysort]: se muestra cómo ejecutar una muestra de GraySort de uso general en un archivo de 10 GB mediante el uso de HDInsight. Hay tres trabajos que se deben ejecutar: Teragen para generar los datos, Terasort para ordenarlos y Teravalidate para confirmar que los datos se han ordenado correctamente.
- [**La muestra de MapReduce que cuenta palabras con transmisión de C# en Hadoop**][hdinsight-sample-csharp-streaming] se explica cómo usar C# para escribir un programa de MapReduce que usa la interfaz de transmisión de Hadoop.


## Ejecución de las muestras ##

Las muestras se pueden ejecutar con Azure PowerShell. Las instrucciones sobre cómo hacerlo se ofrecen para cada una de las muestras anteriores.

##Pasos siguientes ##

En este artículo y en los artículos en cada una de las muestras, ha obtenido información sobre cómo ejecutar las muestras incluidas en los clústeres de HDInsight mediante Azure PowerShell. Para obtener acceso a tutoriales sobre cómo usar Pig, Hive y MapReduce con HDInsight, consulte los siguientes temas:

* [Introducción al uso de Hadoop con Hive en HDInsight para analizar el uso de datos móviles][hdinsight-get-started]
* [Uso de Pig con Hadoop en HDInsight][hdinsight-use-pig]
* [Uso de Hive con Hadoop en HDInsight][hdinsight-use-hive]
* [Envío de trabajos de Hadoop en HDInsight][hdinsight-submit-jobs]
* [Documentación de SDK de HDInsight de Azure][hdinsight-sdk-documentation]
* [Depuración de Hadoop en HDInsight: mensajes de error][hdinsight-errors]


[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
 

<!---HONumber=62-->