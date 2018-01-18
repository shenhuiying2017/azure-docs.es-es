---
title: "Introducción a R Server en Azure HDInsight | Microsoft Docs"
description: "Aprenda a usar R Server en HDInsight para crear aplicaciones de cara al análisis de macrodatos."
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 860099b2634765f3088199c89341f0b48b20b801
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introducción a las funcionalidades de R de código abierto de R Server en HDInsight

Microsoft R Server está disponible como opción de implementación al crear clústeres de HDInsight en Azure. Esta nueva característica provee a los científicos de datos, estadísticos y programadores de R de acceso a petición a métodos escalables y distribuidos para realizar análisis en HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

El tamaño de los clústeres se puede ajustar apropiadamente a los proyectos y las tareas en cuestión y luego desactivarse cuando ya no se requieran. Como forman parte de Azure HDInsight, estos clústeres incluyen soporte ininterrumpido de nivel empresarial, un Acuerdo de Nivel de Servicio con un tiempo de actividad del 99,9 % y la posibilidad de integrarse con otros componentes del ecosistema de Azure.

R Server en HDInsight proporciona las funcionalidades más recientes para realizar análisis basados en R de conjuntos de datos de prácticamente cualquier tamaño, cargados en el almacenamiento de Data Lake o Azure Blob Storage. Como R Server se basa en el lenguaje R de código abierto integrado, las aplicaciones basadas en R que se crean pueden aprovechar los más de 8000 paquetes de R de código abierto. También están disponibles las rutinas de ScaleR, un paquete de análisis de macrodatos de Microsoft que se incluye con R Server.

El nodo perimetral de un clúster proporciona un lugar conveniente para conectarse al clúster y ejecutar los scripts de R. Con un nodo perimetral, tiene la opción de ejecutar las funciones distribuidas paralelizadas de ScaleR en los diferentes núcleos del servidor de nodo perimetral. También puede ejecutarlas en los nodos del clúster utilizando los contextos de proceso de Spark o Hadoop MapReduce de ScaleR.

Los modelos o las predicciones que se generan a partir de los análisis pueden descargarse para utilizarse en un entorno local. También pueden emplearse en otro lugar de Azure, en concreto a través del [servicio web](../../machine-learning/studio/publish-a-machine-learning-web-service.md) [Azure Machine Learning Studio](http://studio.azureml.net).

## <a name="get-started-with-r-on-hdinsight"></a>Introducción a R en HDInsight
Para incluir R Server en un clúster de HDInsight, debe seleccionar el tipo de clúster R Server al crear un clúster de HDInsight mediante Azure Portal. El tipo de clúster R Server incluye esta plataforma en los nodos de datos del clúster y en un nodo perimetral, que sirve como zona de aterrizaje para los análisis basados en R Server. Consulte [Introducción al uso de R Server en HDInsight](r-server-get-started.md) para conocer una solución alternativa sobre cómo crear el clúster.

## <a name="learn-about-data-storage-options"></a>Información sobre las opciones de almacenamiento de datos
El almacenamiento predeterminado del sistema de archivos HDFS de los clústeres de HDInsight puede asociarse con una cuenta de Azure Storage o un almacén de Azure Data Lake Store. Esta asociación garantiza que cualquier dato cargado en el almacenamiento de clúster durante el transcurso del análisis se convierta en persistente. Existen diversas herramientas para tratar la transferencia de datos a la opción de almacenamiento que seleccione, entre ellas el componente de carga basado en el portal de la cuenta de almacenamiento y la utilidad [AzCopy](../../storage/common/storage-use-azcopy.md).

Tiene la opción de agregar acceso a los almacenes de Data Lake y Blob Storage adicionales durante el aprovisionamiento del clúster independientemente de la opción de almacenamiento principal en uso. Consulte [Introducción al uso de R Server en HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) para información sobre cómo agregar acceso a cuentas adicionales. Vea el artículo suplementario [Opciones de Azure Storage para R Server en HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) para información adicional sobre el uso de varias cuentas de almacenamiento.

También se puede usar [Archivos de Azure](../../storage/files/storage-how-to-use-files-linux.md) como opción de almacenamiento para emplearse en el nodo perimetral. Gracias a Azure Files, podrá montar en el sistema de archivos Linux un recurso compartido de archivos creado en el servicio Azure Storage. Para más información sobre estas opciones de almacenamiento de datos de R Server en un clúster de HDInsight, consulte [Opciones de Azure Storage para R Server en HDInsight](r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Acceso a R Server en el clúster
Puede conectarse a R Server en el nodo perimetral mediante un explorador. Se instala de forma predeterminada durante la creación del clúster. Para más información, consulte [Introducción al uso de R Server en HDInsight](r-server-get-started.md).

También puede conectarse a R Server desde la línea de comandos mediante SSH/PuTTY para acceder a la consola de R. 

## <a name="develop-and-run-r-scripts"></a>Desarrollo y ejecución de scripts de R
Los scripts de R que se crean y ejecutan pueden usar cualquiera de los más de 8000 paquetes de R de código abierto, además de las rutinas distribuidas y paralelizadas disponibles en la biblioteca ScaleR. En general, el script que se ejecuta con R Server en el nodo perimetral lo hace dentro del intérprete de R de ese nodo. Las excepciones son los pasos que necesitan llamar a una función ScaleR con un contexto de proceso establecido en Hadoop MapReduce (RxHadoopMR) o Spark (RxSpark). En este caso, la función se ejecuta de forma distribuida en esos nodos de datos (tarea) del clúster asociados a los datos a los que se hacen referencia. Para más información sobre las distintas opciones de contexto de proceso, consulte [Opciones de contexto de proceso para R Server en HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Uso de modelos
Cuando se complete el modelado de datos, podrá utilizar el modelo para realizar predicciones de nuevos datos en Azure o en un entorno local. Este proceso se conoce como "puntuación". La puntuación puede realizarse en HDInsight, Azure Machine Learning o de forma local.

### <a name="score-in-hdinsight"></a>Puntuación en HDInsight
Para realizar puntuaciones en HDInsight, escriba una función de R que llame al modelo para realizar predicciones de un nuevo archivo de datos que se haya cargado en la cuenta de almacenamiento. Después, guarde las predicciones en la cuenta de almacenamiento. Puede ejecutar la rutina a petición en el nodo perimetral del clúster o mediante un trabajo programado.  

### <a name="score-in-azure-machine-learning-aml"></a>Puntuación en Azure Machine Learning (AML)
Para puntuar mediante un servicio AML, use el paquete de R de Azure Machine Learning de código abierto conocido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) para publicar el modelo como servicio web de Azure. Para mayor comodidad, este paquete está preinstalado en el nodo perimetral. Después, utilice los recursos de Machine Learning con el fin de crear una interfaz de usuario para el servicio web y, luego, según proceda, realice una llamada a dicho servicio web para efectuar puntuaciones.

Si elige esta opción, tendrá que convertir los objetos de modelo de ScaleR en objetos de modelo de código abierto equivalentes para poder usarlos con el servicio web. Para esta conversión, utilice las funciones de coerción de ScaleR, como `as.randomForest()`, para los modelos basados en conjuntos.

### <a name="score-on-premises"></a>Puntuación en un entorno local
Para puntuar de forma local después de crear el modelo, este se puede serializar en R, descargarlo, anular la serialización y, después, usarlo para puntuar nuevos datos. Puede puntuar nuevos datos adoptando el enfoque descrito antes en [Puntuación en HDInsight](#scoring-in-hdinsight) o mediante [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Mantenimiento del clúster
### <a name="install-and-maintain-r-packages"></a>Instalación y mantenimiento de paquetes de R
La mayoría de los paquetes de R que utiliza se necesitan en el nodo perimetral, puesto que es donde se ejecutan la mayoría de los scripts de R. Para instalar más paquetes de R en el nodo perimetral, puede usar el método `install.packages()` habitual en R.

Si simplemente usa rutinas de la biblioteca ScaleR en el clúster, normalmente no hará falta instalar más paquetes de R en los nodos de datos. Sin embargo, puede que necesite más paquetes para admitir el uso de la ejecución de **rxExec** o **RxDataStep** en los nodos de datos.

En tales casos, estos paquetes adicionales pueden instalarse con una acción de script después de crear el clúster. Para más información, consulte [Creación de un clúster de HDInsight con R Server](r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Cambio de la configuración de memoria de Hadoop MapReduce
Un clúster puede modificarse para cambiar la cantidad de memoria disponible en R Server cuando se ejecuta un trabajo de MapReduce. Para ello, use la IU de Apache Ambari, que se encuentra disponible a través de la hoja del Portal de Azure de su clúster. Para ver instrucciones para acceder a la IU de Ambari de su clúster, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](../hdinsight-hadoop-manage-ambari.md).

También se puede cambiar la cantidad de memoria disponible para R Server utilizando los modificadores de Hadoop en la llamada a **RxHadoopMR** , tal y como se muestra a continuación:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Escalado del clúster
Un clúster existente puede escalarse o reducirse verticalmente a través del Portal. Gracias al escalado, puede conseguir la capacidad adicional que podría necesitar para albergar tareas de procesamiento de mayor envergadura, o bien puede volver a escalar un clúster cuando esté inactivo. Para ver instrucciones sobre cómo escalar un clúster, consulte [Administración de clústeres de HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Mantenimiento del sistema
El mantenimiento para aplicar revisiones de SO y otras actualizaciones se realiza en las máquinas virtuales Linux subyacentes de un clúster de HDInsight durante las horas de poca actividad con el objetivo. Normalmente, el mantenimiento se realiza a las 3:30 (en la hora local de la máquina virtual) todos los lunes y jueves. Las actualizaciones se llevan a cabo de forma que no afecten a más de un cuarto del clúster a la vez.  

Como los nodos principales son redundantes y no todos los nodos de datos se ven afectados, es posible que se ralenticen los trabajos que se estén ejecutando durante ese tiempo. No obstante, deben seguir ejecutándose hasta que finalicen. Cualquier software personalizado o dato local que haya instalado se conserva a través de estos eventos de mantenimiento, salvo que se produzca un error irrecuperable que requiera recompilar el clúster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Más información sobre las opciones del IDE de R Server en el clúster de HDInsight
El nodo perimetral de Linux de un clúster de HDInsight es la zona de aterrizaje de los análisis basados en R. Las versiones recientes de HDInsight proporcionan una instalación predeterminada de RStudio Server en el nodo perimetral como un IDE basado en explorador. Puede ser más productivo usar RStudio Server como un IDE para desarrollar y ejecutar scripts de R que si se utiliza simplemente la consola de R.

Otra opción de IDE completa consiste en instalar un IDE de escritorio y usarlo para acceder al clúster usando un contexto de proceso remoto de MapReduce o Spark. Las opciones incluyen [Herramientas de R para Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) de Microsoft, RStudio y [StatET](http://www.walware.de/goto/statet) basado en Eclipse de Walware.

Finalmente, puede acceder a la consola de R Server en el nodo perimetral escribiendo **R** en el símbolo del sistema de Linux una vez que se haya conectado a través de SSH o PuTY. Cuando utilice la interfaz de la consola, es conveniente ejecutar un editor de texto para desarrollar scripts de R en otra ventana y cortar y pegar las secciones del script en la consola de R según sea necesario.

## <a name="learn-about-pricing"></a>Más información sobre los precios
Las tarifas asociadas a un clúster de HDInsight con R Server tienen una estructura similar a la de los clústeres de HDInsight estándar. Se basan en el tamaño de las máquinas virtuales subyacentes en los nodos de nombre, de datos y perimetral, con el extra de un aumento de la hora de núcleo. Para más información sobre los precios de HDInsight, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>pasos siguientes
Para más información sobre cómo usar R Server con clústeres de HDInsight, consulte los temas siguientes:

* [Introducción al uso de R Server en HDInsight (versión preliminar)](r-server-get-started.md)
* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](r-server-compute-contexts.md)
* [Opciones de Azure Storage para R Server en HDInsight](r-server-storage.md)
