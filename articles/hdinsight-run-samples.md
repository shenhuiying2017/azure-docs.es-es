<properties linkid="manage-services-hdinsight-howto-run-samples" urlDisplayName="Run HDInsight Samples" pageTitle="Run the HDInsight samples | Azure" metaKeywords="" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the HDInsight samples" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Ejecución de muestras de HDInsight
==================================

Se proporciona un conjunto de muestras para ayudarle a comenzar a usar HDInsight de Azure. Estas muestras se habilitan en cada uno de los clústeres de HDInsight que se crean. La ejecución de estas muestras le permitirá familiarizarse con los cmdlets de HDInsight de Azure PowerShell.

Los programas de MapReduce también se pueden ejecutar mediante programación desde una aplicación que usa la API .NET de Microsoft para HDInsight. Para obtener más información acerca de cómo usar las API de HDInsight para el envío de trabajos, consulte [Envío de trabajos de Hadoop mediante programación](/es-es/manage/services/hdinsight/submit-hadoop-jobs-programmatically/).

En la Web se encuentra disponible mucha documentación adicional sobre las tecnologías relacionadas con Hadoop, como la transmisión y programación de MapReduce basada en Java, así como la documentación sobre los cmdlets usados en el scripting de PowerShell. Para obtener más información acerca de estos recursos, consulte la sección final **Recursos para HDInsight** del tema [Introducción a Azure HDInsight](/es-es/manage/services/hdinsight/introduction-hdinsight/).

**Qué son estas muestras**

Estás muestras están diseñadas para ponerle al día rápido acerca de cómo implementar trabajos de Hadoop y para ofrecerle una base de pruebas extensible para trabajar con los conceptos y los procedimientos de scripting que el servicio usa. Le proporcionan ejemplos de tareas comunes como la creación e importación de conjuntos de datos de varios tamaños, la ejecución de trabajos, la composición de trabajos de manera secuencial y el análisis de los resultados de los trabajos. El tamaño de los conjuntos de datos puede variar, lo que le permite observar los efectos que los conjuntos de datos de varios tamaños tienen en el rendimiento del trabajo.

**Requisitos previos**:

-   Debe tener una cuenta de Azure. Para conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/).

-   Debe aprovisionar un clúster de HDInsight. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](/es-es/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Debe tener instalado Azure PowerShell y haber configurado los clústeres para utilizarlos con su cuenta. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell](/es-es/documentation/articles/install-configure-powershell/).

Las muestras
------------

HDInsight incluye las siguientes muestras.

-   [**La muestra del estimador de Pi**](/es-es/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/) En este tutorial se explica cómo ejecutar un programa de MapReduce con HDInsight que usa un método estadístico (quasi-Monte Carlo) para estimar el valor de Pi.
-   [**La muestra de WordCount**](/es-es/manage/services/hdinsight/howto-run-samples/sample-wordcount/) En este tutorial se explica cómo usar un clúster de HDInsight para ejecutar un programa de MapReduce que cuenta las ocurrencias de palabras en un archivo de texto.
-   [**La muestra de Graysort de 10 GB**](/es-es/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/) En este tutorial se explica cómo ejecutar una muestra de GraySort de uso general en un archivo de 10 GB con la utilización de HDInsight. Hay tres trabajos para ejecutar: Teragen para genera los datos, Terasort para ordenar los datos y Teravalidate para confirmar que los datos se han ordenado correctamente.
-   [**La muestra de transmisión de C\#**](/es-es/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/) En este tutorial se explica cómo usar C\# para escribir un programa de MapReduce que usa la interfaz de transmisión de Hadoop.

Ejecución de las muestras
-------------------------

Las muestras se pueden ejecutar con Azure PowerShell. Las instrucciones sobre cómo hacerlo se ofrecen para cada una de las muestras en las páginas a las que remiten los vínculos anteriores.

Pasos siguientes
----------------

En este artículo y en los artículos sobre cada muestra ha obtenido información acerca de cómo ejecutar las muestras incluidas en los clústeres de HDInsight con la utilización de Azure PowerShell. Para obtener acceso a tutoriales acerca de cómo usar Pig, Hive y MapReduce con HDInsight, consulte los siguientes temas:

-   [Introducción al servicio HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/)
-   [Uso de Pig con HDInsight](/es-es/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Uso de Hive con HDInsight](/es-es/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Envío de trabajos de Hadoop mediante programación](/es-es/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Azure HDInsight SDK documentation](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)
-   [Depuración de HDInsight: mensajes de error](/es-es/manage/services/hdinsight/debug-error-messages/)

