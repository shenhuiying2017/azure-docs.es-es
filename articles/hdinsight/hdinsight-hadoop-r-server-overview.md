---
title: "Introducción a R Server en Azure HDInsight | Microsoft Docs"
description: "Obtenga una introducción a R Server en HDInsight. Aprenda a usar R Server para crear aplicaciones de cara al análisis de macrodatos."
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: cd57f3a43142b3af3546eafd9749123fadd333c2
ms.openlocfilehash: 2f5cf1155f116b22fa28cf86bb8c87667df981b7
ms.lasthandoff: 01/14/2017


---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introducción a las funcionalidades de R de código abierto de R Server en HDInsight

Gracias a Microsoft Azure HDInsight, Microsoft R Server ahora está disponible como opción al crear clústeres de HDInsight en Azure. Esta nueva característica provee a los científicos de datos, estadísticos y programadores de R de acceso a petición a métodos escalables y distribuidos para realizar análisis en HDInsight.

Los clústeres se pueden ajustar a los proyectos y las tareas en cuestión, así como desactivarse cuando ya no se requieran. Como forman parte de HDInsight de Azure, estos clústeres incluyen soporte ininterrumpido de nivel empresarial, un Acuerdo de Nivel de Servicio con un tiempo de actividad del 99,9 % y la flexibilidad suficiente como para integrarse con otros componentes del ecosistema de Azure.

R Server en HDInsight proporciona las características más recientes para realizar análisis basados en R de conjuntos de datos de prácticamente cualquier tamaño que estén cargados en el almacenamiento de Data Lake o Azure Blob Storage. Puesto que R Server se basa en el lenguaje R de código abierto, las aplicaciones basadas en R que compile podrán usar cualquiera de los más de 8000 paquetes de R de código abierto, así como las rutinas de ScaleR: el paquete de análisis de macrodatos de Microsoft incluido en R Server.

El nodo perimetral de un clúster proporciona un lugar conveniente para conectarse al clúster y ejecutar los scripts de R. Con un nodo perimetral, tiene la opción de ejecutar funciones distribuidas paralelizadas de ScaleR en los diferentes núcleos del servidor de nodo perimetral. También tiene la opción de ejecutarlas en estos nodos del clúster utilizando los contextos de proceso de Spark o Hadoop MapReduce de ScaleR.

Los modelos o las predicciones que se generan a partir de los análisis pueden descargarse para utilizarse en un entorno local. También pueden emplearse en otro lugar de Azure; por ejemplo, a través de un [servicio web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) [Azure Machine Learning Studio](http://studio.azureml.net).

## <a name="get-started-with-r-on-hdinsight"></a>Introducción a R en HDInsight
Para incluir R Server en un clúster de HDInsight, debe seleccionar el tipo de clúster R Server al crear un clúster de HDInsight mediante Azure Portal. El tipo de clúster R Server incluye esta plataforma en los nodos de datos del clúster, así como un nodo perimetral como zona de aterrizaje para los análisis basados en R Server. Consulte [Introducción al uso de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-get-started.md) para ver un tutorial detallado sobre cómo crear un clúster.

## <a name="learn-about-data-storage-options"></a>Información sobre las opciones de almacenamiento de datos
El almacenamiento predeterminado del sistema de archivos HDFS de los clústeres de HDInsight puede asociarse con una cuenta de Azure Storage o un almacén de Azure Data Lake Store. De este modo, se garantiza que cualquier dato cargado en el almacenamiento de clúster durante el transcurso del análisis se convierta en persistente. Existen diversas herramientas para transferir datos a la opción de almacenamiento que elija, entre ellos el componente de carga basado en el portal de la cuenta de almacenamiento y la utilidad [AzCopy](../storage/storage-use-azcopy.md).

Independientemente de si elige Data Lake o Azure Blob Storage como almacenamiento principal del clúster, tiene la opción de agregar acceso a los almacenes de Data Lake y Blob Storage adicionales durante el proceso de aprovisionamiento del clúster. Consulte [Introducción al uso de R Server en HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-get-started) para obtener información sobre cómo agregar acceso a cuentas adicionales, además del artículo complementario [Opciones de Azure Storage para R Server en HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-storage) para obtener información sobre cómo usar varias cuentas de almacenamiento.

También se puede usar [Archivos de Azure](../storage/storage-how-to-use-files-linux.md) como opción de almacenamiento para emplearse en el nodo perimetral. Gracias a Archivos de Azure, podrá montar en el sistema de archivos Linux un recurso compartido de archivos creado en el servicio Almacenamiento de Azure. Para obtener más información sobre las opciones de almacenamiento de datos de R Server en un clúster de HDInsight, consulte el artículo que explica las [opciones de Azure Storage para R Server en clústeres de HDInsight](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Acceso a R Server en el clúster
Después de crear un clúster con R Server, puede conectarse a la instancia de R Server del nodo perimetral con un explorador si ha elegido incluir RStudio Server durante el proceso de aprovisionamiento (o si lo ha agregado más tarde), o bien mediante SSH o PuTTY para acceder a la consola de R. Para obtener más información sobre cómo instalar RStudio Server una vez que se haya creado un clúster, consulte [Instalación de RStudio con R Server en HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Desarrollo y ejecución de scripts de R
Los scripts de R que se crean y ejecutan pueden usar cualquiera de los más de 8000 paquetes de R de código abierto, además de las rutinas distribuidas y paralelizadas de la biblioteca ScaleR. En general, el script que se ejecuta con R Server en el nodo perimetral lo hace dentro del intérprete de R de ese nodo. Las excepciones son los pasos que llaman a una función ScaleR con un contexto de proceso establecido en Hadoop MapReduce (RxHadoopMR) o Spark (RxSpark).

En estos casos, la función se ejecuta de forma distribuida en esos nodos de datos (tarea) del clúster asociado a los datos a los que se hacen referencia. Para más información sobre las distintas opciones de contexto de proceso, consulte [Opciones de contexto de proceso para R Server en HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Uso de modelos
Cuando se complete el modelado de datos, podrá utilizar el modelo para realizar predicciones de nuevos datos tanto en Azure como en un entorno local. Este proceso se conoce como "puntuación". Estos son algunos ejemplos.

### <a name="score-in-hdinsight"></a>Puntuación en HDInsight
Para realizar puntuaciones en HDInsight, escriba una función de R que llame al modelo para realizar predicciones de un nuevo archivo de datos que se haya cargado en la cuenta de almacenamiento. Después, guarde las predicciones en la cuenta de almacenamiento. Puede ejecutar la rutina a petición en el nodo perimetral del clúster o mediante un trabajo programado.  

### <a name="score-in-azure-machine-learning"></a>Puntuación en Aprendizaje automático de Microsoft Azure
Para puntuar mediante un servicio web Machine Learning de Azure, use el paquete de R de Aprendizaje automático de Azure de código abierto conocido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) para publicar el modelo como servicio web de Azure. Para mayor comodidad, este paquete está preinstalado en el nodo perimetral. Después, utilice los recursos de Aprendizaje automático con el fin de crear una interfaz de usuario para el servicio web y, luego, según proceda, realice una llamada a dicho servicio web para efectuar puntuaciones.

Si elige esta opción, tendrá que convertir los objetos de modelo de ScaleR en objetos de modelo de código abierto equivalentes para poder usarlos en el servicio web. Para ello, se pueden usar las funciones de coerción de ScaleR, como `as.randomForest()` , para los modelos basados en conjuntos.

### <a name="score-on-premises"></a>Puntuación en un entorno local
Para puntuar de forma local después de crear el modelo, este se puede serializar en R, descargarlo, anular la serialización y, después, usarlo para puntuar nuevos datos. Puede puntuar nuevos datos adoptando el enfoque descrito antes en [Puntuación en HDInsight](#scoring-in-hdinsight) o mediante [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Mantenimiento del clúster
### <a name="install-and-maintain-r-packages"></a>Instalación y mantenimiento de paquetes de R
La mayoría de los paquetes de R que utiliza se necesitan en el nodo perimetral, puesto que es donde se ejecutan la mayoría de los scripts de R. Para instalar más paquetes de R en el nodo perimetral, puede usar el método `install.packages()` habitual en R.

En la mayoría de los casos, no hará falta instalar más paquetes de R en los nodos de datos si simplemente usa rutinas de la biblioteca ScaleR en el clúster. Sin embargo, puede que necesite más paquetes para admitir el uso de la ejecución de **rxExec** o **RxDataStep** en los nodos de datos.

En tales casos, estos paquetes adicionales pueden instalarse utilizando una acción de script después de crear el clúster. Para más información, consulte [Creación de un clúster de HDInsight con R Server](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Cambio de la configuración de memoria de Hadoop MapReduce
Un clúster puede modificarse para cambiar la cantidad de memoria disponible en R Server cuando se ejecuta un trabajo de MapReduce. Para ello, use la IU de Apache Ambari, que se encuentra disponible a través de la hoja del Portal de Azure de su clúster. Para ver instrucciones para acceder a la IU de Ambari de su clúster, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

También se puede cambiar la cantidad de memoria disponible para R Server utilizando los modificadores de Hadoop en la llamada a **RxHadoopMR** , tal y como se muestra a continuación:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Escalado del clúster
Un clúster existente puede escalarse o reducirse verticalmente a través del Portal. Gracias al escalado, puede conseguir la capacidad adicional que podría necesitar para albergar tareas de procesamiento de mayor envergadura, o bien puede volver a escalar un clúster cuando esté inactivo. Para ver instrucciones sobre cómo escalar un clúster, consulte [Administración de clústeres de HDInsight](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Mantenimiento del sistema
El mantenimiento se realiza en las máquinas virtuales Linux subyacentes de un clúster de HDInsight durante las horas de poca actividad con el objetivo de aplicar revisiones de sistema operativo y otras actualizaciones. Normalmente, el mantenimiento se realiza a las 3:30 (en la hora local de la máquina virtual) todos los lunes y jueves. Las actualizaciones se llevan a cabo de forma que no afecten a más de un cuarto del clúster a la vez.  

Como los nodos principales son redundantes y no todos los nodos de datos se ven afectados, es posible que se ralenticen los trabajos que se estén ejecutando durante ese tiempo. No obstante, deben seguir ejecutándose hasta que finalicen. Cualquier software personalizado o dato local que haya instalado se conserva a través de estos eventos de mantenimiento, salvo que se produzca un error irrecuperable que requiera recompilar el clúster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Más información sobre las opciones del IDE de R Server en el clúster de HDInsight
El nodo perimetral de Linux de un clúster de HDInsight es la zona de aterrizaje de los análisis basados en R.  Las versiones recientes de HDInsight proporcionan una opción predeterminada para instalar la versión de comunidad de [RStudio Server](https://www.rstudio.com/products/rstudio-server/) en el nodo perimetral como un IDE basado en explorador. Puede ser más productivo usar RStudio Server como un IDE para desarrollar y ejecutar scripts de R que si se utiliza simplemente la consola de R. Si decide no agregar RStudio Server al crear el clúster, pero quiere hacerlo más adelante, consulte [Instalación de RStudio con R Server en HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-install-r-studio).

Otra opción de IDE completa consiste en instalar un IDE de escritorio como el que anunció recientemente Microsoft, [R Tools para Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS), RStudio o [StatET](http://www.walware.de/goto/statet) basado en Eclipse de Walware, y acceder al clúster usando un contexto de proceso remoto de MapReduce o Spark.  

Finalmente, puede acceder simplemente a la consola de R Server en el nodo perimetral escribiendo **R** en la línea de comandos de Linux una vez que se haya conectado a través de SSH o PuTY. El uso de la interfaz de la consola será mejor si ejecuta un editor de texto para desarrollar scripts de R en otra ventana y corta y pega las secciones del script en la consola de R según lo vaya necesitando.

## <a name="learn-about-pricing"></a>Más información sobre los precios
Las tarifas asociadas a un clúster de HDInsight con R Server tienen una estructura similar a la de los clústeres de HDInsight estándar. Se basan en el tamaño de las máquinas virtuales subyacentes en los nodos de nombre, de datos y perimetral, con el extra de un aumento de la hora de núcleo. Para más información sobre los precios de HDInsight y la disponibilidad de una evaluación gratuita de 30 días, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Pasos siguientes
Haga clic en los siguientes vínculos para obtener más información sobre cómo usar R Server con clústeres de HDInsight.

* [Introducción al uso de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-get-started.md)
* [Agregar R Server Studio a HDInsight (si no se ha instalado durante la creación del clúster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opciones de Azure Storage para R Server en HDInsight](hdinsight-hadoop-r-server-storage.md)

