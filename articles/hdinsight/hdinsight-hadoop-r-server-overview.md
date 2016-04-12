<properties
	pageTitle="¿Qué es R en HDInsight? Introducción al servidor de R en HDInsight | Microsoft Azure"
	description="Qué es R en HDInsight y cómo usar R para crear aplicaciones para el análisis de macrodatos."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/29/2016"
   ms.author="jeffstok"/>


# Información general: Servidor de R en HDInsight

Con HDInsight Premium, el servidor de R ahora está disponible como una opción al crear clústeres de HDInsight de Azure. Esta nueva característica provee a los científicos de datos, estadísticos y programadores de R de acceso a petición a métodos escalables y distribuidos para realizar análisis en HDInsight. Los clústeres se pueden ajustar a los proyectos y a las tareas en cuestión, y desechar cuando ya no son necesarios. Al ser parte de HDInsight de Azure, estos clústeres incluyen soporte ininterrumpido de nivel empresarial, SLA con un tiempo de actividad del 99,9 % y la flexibilidad suficiente como para integrarse con otros componentes del ecosistema de Azure.

>[AZURE.NOTE] El servidor de R solo está disponible con HDInsight Premium. Actualmente, HDInsight Premium solo está disponible para los clústeres de Hadoop y Spark. Por tanto, en este momento el servidor de R solo se puede usar con clústeres de Hadoop y Spark en HDInsight. Para más información, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](hdinsight-component-versioning.md).

El servidor de R en HDInsight proporciona las funciones más recientes para realizar análisis basados en R en grandes conjuntos de datos cargados en el Blob de Azure (WASB). Puesto que el servidor de R se basa en R de código abierto, las aplicaciones basadas en R que cree podrán usar cualquiera de los más de 8000 paquetes de R de código abierto, así como las rutinas de ScaleR, el paquete de análisis de macrodatos de Microsoft incluido en el servidor de R. El nodo perimetral de los clústeres Premium proporciona una zona de aterrizaje muy útil para conectarse al clúster y ejecutar los scripts de R. Con un nodo perimetral, podrá ejecutar funciones distribuidas en paralelo de ScaleR en los núcleos del servidor del nodo perimetral o en los nodos del clúster mediante el uso de los contextos de proceso de Spark o MapReduce de Hadoop de ScaleR. Los modelos o las predicciones derivadas del análisis se pueden descargar para uso local o emplearlos en cualquier otro lugar en Azure, como a través de un [servicio web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) de [Estudio de aprendizaje automático de Azure](http://studio.azureml.net) (AML).

## Introducción a R en HDInsight

Para incluir el servidor de R en un clúster de HDInsight, debe crear un clúster de Hadoop o de Spark con la opción Premium al crear un clúster en el Portal de Azure. Ambos tipos de clúster usan la misma configuración que incluye el servidor de R en los nodos de datos de un clúster, así como un nodo perimetral como zona de aterrizaje para los análisis basados en el servidor de R. Consulte [Getting Started with R Server on HDInsight](hdinsight-hadoop-r-server-get-started.md) (Introducción al servidor de R en HDInsight) para ver un tutorial detallado sobre cómo crear un clúster.

## Opciones de almacenamiento de datos

Los clústeres de HDInsight se almacenan de forma predeterminada en el Blob de Azure (WASB) con el sistema de archivos HDFS asignado a un contenedor de blobs. Esto garantiza que cualquier dato cargado en el almacenamiento de clúster o escrito en él durante el transcurso del análisis se convierte en persistente. Un método práctico para copiar datos en blobs (y desde estos) es usar la utilidad [AzCopy](../storage/storage-use-azcopy/).

También se puede usar [Archivos de Azure](../storage/storage-how-to-use-files-linux.md) como opción de almacenamiento para su uso en el nodo perimetral. Archivos de Azure permite montar un recurso compartido de archivos creado en una cuenta de almacenamiento de Azure en el sistema de archivos Linux. Para más información sobre las opciones de almacenamiento de datos del servidor de R en un clúster de HDInsight, consulte [Storage options for R Server on HDInsight clusters](hdinsight-hadoop-r-server-storage.md) (Opciones de almacenamiento del servidor de R en clústeres de HDInsight).
  
## Acceso al servidor de R en el clúster

Una vez que se haya creado un clúster con el servidor de R, puede conectarse a la consola R en el nodo perimetral del clúster mediante SSH/Putty o a través de un explorador si decide instalar el IDE de RStudio Server en el nodo perimetral. Para más información sobre cómo instalar RStudio Server, consulte [Installing RStudio Server on HDInsight clusters](hdinsight-hadoop-r-server-install-r-studio.md) (Instalación de RStudio Server en clústeres de HDInsight).

## Desarrollar y ejecutar scripts de R

Los scripts de R que se crean y ejecutan pueden usar cualquiera de los más de 8000 paquetes de código abierto, además de las rutinas distribuidas y en paralelo de la biblioteca ScaleR. Un script ejecutado en el servidor de R en el nodo perimetral lo hará mediante el intérprete de R, excepto los pasos en los que se llame a una de las funciones de ScaleR con un contexto de proceso establecido en MapReduce de Hadoop (RxHadoopMR) o en Spark (RxSpark). En esos casos, la función se ejecutará de forma distribuida entre los nodos de datos (tarea) del clúster asociado a los datos de referencia. Para más información sobre las distintas opciones de contexto deproceso, consulte [Compute context options for R Server on HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md) (Opciones de contexto de proceso del servidor de R en HDInsight Premium).

## Funcionamiento de un modelo

Una vez completado el modelado de datos, puede poner en funcionamiento el modelo para realizar predicciones sobre nuevos datos (algo que se denomina puntuación) tanto en Azure como de forma local. Estos son algunos ejemplos.

### Puntuación en HDInsight

Para puntuar en HDInsight, se podría escribir una función de R que llame a su modelo para realizar predicciones en un nuevo archivo de datos cargado en la cuenta de almacenamiento de Azure y guardar las predicciones en la cuenta de almacenamiento. Puede ejecutar la rutina a petición en el nodo perimetral del clúster o mediante un trabajo programado.

### Puntuación en Aprendizaje automático de Azure 

Para puntuar mediante un servicio web de Aprendizaje automático de Azure, puede usar el [paquete de R de Aprendizaje automático de Azure de código abierto](http://www.inside-r.org/blogs/2015/11/18/enhancements-azureml-package-connect-r-azureml-studio) para [publicar el modelo como un servicio web de Azure](http://www.r-bloggers.com/deploying-a-car-price-model-using-r-and-azureml/), usar los recursos del Aprendizaje automático de Azure para crear una interfaz de usuario para el servicio web y, finalmente, llamar a ese servicio web según sea necesario para la puntuación. Si elige esta opción, debe convertir los objetos de modelo de ScaleR en objetos de modelo de código abierto equivalentes para poder usarlos en el servicio web. Para ello, se pueden usar las funciones de conversión de ScaleR, como `as.randomForest()`, para los modelos basados en conjuntos.
  
### Puntuación local

Para puntuar de forma local después de crear el modelo, dicho modelo se puede serializar en R, descargarlo en una ubicación local, anular su serialización y después usarlo para puntuar nuevos datos. Para ello, puede usar el método descrito anteriormente de [puntuación en HDInsight](#scoring-in-hdinsight) o bien mediante [DeployR](https://deployr.revolutionanalytics.com/).

## Mantenimiento del clúster 

### Instalación y mantenimiento de paquetes de R

La mayoría de los paquetes de R que usa serán necesarios en el nodo perimetral, puesto que es ahí donde se ejecuta la parte principal de los scripts de R. Para instalar más paquetes de R en el nodo perimetral, puede usar el método `install.packages()` habitual en R.
  
En la mayoría de los casos no será necesario instalar más paquetes de R en los nodos de datos, si simplemente usa rutinas de la biblioteca ScaleR para ejecutarlas en el clúster. Sin embargo, puede que necesite más paquetes para admitir el uso de **rxExec** o **RxDataStep** en los nodos de datos. En tales casos, estos paquetes extra deben especificarse mediante una acción de script después de crear el clúster. Para más información, consulte [Creating an HDInsight cluster with R Server](hdinsight-hadoop-r-server-get-started.md) (Creación de un clúster de HDInsight con el servidor de R).
  
### Cambio de la configuración de memoria de MapReduce de Hadoop 

Un clúster puede modificarse para cambiar la cantidad de memoria disponible en el servidor de R cuando se ejecuta un trabajo de MapReduce. Para ello, use la IU de Ambari disponible a través de la hoja del Portal de Azure de su clúster. Para obtener instrucciones sobre cómo tener acceso a la IU de Ambari de su clúster, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

También es posible cambiar la cantidad de memoria disponible en el servidor de R mediante modificadores de Hadoop en la llamada a **RxHadoopMR**, tal como se muestra a continuación.
 
	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### Escalado del clúster

Un clúster existente se puede escalar horizontal o verticalmente a través del Portal de Azure. Esto puede servir para obtener la capacidad adicional que se necesita en procesamientos más voluminosos o para escalar un clúster cuando está inactivo. Para obtener instrucciones sobre cómo escalar un clúster, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-portal-linux.md).

### Mantenimiento del sistema 

El mantenimiento se realiza en las máquinas virtuales de Linux subyacentes en un clúster de HDInsight durante horas de poca actividad para aplicar revisiones de sistema operativo, etc. Normalmente, esto tiene lugar a las 3:30 a. m. (según la hora local de la máquina virtual) los lunes y jueves de cada semana. Las actualizaciones se realizan de manera que no afectan a más de ¼ del clúster a la vez. Dado que los nodos principales son redundantes y no todos los nodos de datos se ven afectados, puede que los trabajos en ejecución durante ese tiempo se ralenticen, pero deben ejecutarse hasta completarse. Cualquier software personalizado que haya instalado o dato local se conserva a través de estos eventos de mantenimiento a menos que se produzca un error irrecuperable que requiera recompilar el clúster.

## Opciones del IDE del servidor de R en el clúster de HDInsight

El nodo perimetral de Linux de un clúster de HDInsight Premium es la zona de aterrizaje de los análisis basados en R. Después de conectarse al clúster, puede iniciar la interfaz de la consola en el servidor de R escribiendo "R" en la línea de comandos de Linux. El uso de la interfaz de la consola será mejor si ejecuta un editor de texto para desarrollar scripts de R en otra ventana y corta y pega las secciones del script en la consola de R según lo vaya necesitando.
  
Una mejora frente al uso de un editor de texto simple para desarrollar scripts de R es emplear un IDE basado en R en el escritorio, como las [herramientas de R para Visual Studio](https://www.visualstudio.com/es-ES/features/rtvs-vs.aspx) (RTV) recientemente anunciadas por Microsoft (que son una familia de herramientas de escritorio y servidor de [RStudio](https://www.rstudio.com/products/rstudio-server/)) o también [StatET](http://www.walware.de/goto/statet), basada en Eclipse de Walware.
  
Otra opción consiste en instalar un IDE en el propio nodo perimetral de Linux. Una opción popular en estos casos es [RStudio Server](https://www.rstudio.com/products/rstudio-server/), que proporciona un IDE basado en explorador que pueden usar los clientes remotos. Si RStudio Server se instala en el nodo perimetral de un clúster de HDInsight Premium, ofrece una experiencia de IDE completa para el desarrollo y ejecución de scripts de R con el servidor de R en el clúster. Además, puede ser notablemente más productivo que el uso predeterminado de la consola de R. Si quiere instalar RStudio Server, consulte [Installing RStudio Server on HDInsight clusters](hdinsight-hadoop-r-server-install-r-studio.md) (Instalación de RStudio Server en clústeres de HDInsight).

## Precios
 
Las cuotas por un clúster de HDInsight Premium con el servidor de R están estructuradas de manera similar a las de los clústeres de HDInsight estándar, y se basarán en el tamaño de las máquinas virtuales subyacentes en los nodos de nombre, de datos y perimetral, con el extra de un aumento de la hora de núcleo para Premium. Para más información sobre los precios de HDInsight Premium, incluido el precio de la versión de vista previa pública y la disponibilidad de una evaluación gratuita de 30 días, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Pasos siguientes

Use los siguientes vínculos para obtener más información sobre cómo usar el servidor de R con clústeres de HDInsight.

- [Getting Started with R Server on HDInsight](hdinsight-hadoop-r-server-get-started.md) (Introducción al servidor de R en HDInsight)

- [Add RStudio Server to HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md) (Agregar un servidor de RStudio a HDInsight Premium)

- [Compute context options for R Server on HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md) (Opciones de contexto de proceso del servidor de R en HDInsight Premium)

- [Azure Storage options for R Server on HDInsight Premium](hdinsight-hadoop-r-server-storage.md) (Opciones de almacenamiento de Azure del servidor de R en HDInsight Premium)

 

<!---HONumber=AcomDC_0330_2016-->