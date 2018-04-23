---
title: Introducción a R Server en Azure HDInsight | Microsoft Docs
description: Aprenda a usar R Server en HDInsight para crear aplicaciones de cara al análisis de macrodatos.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 19c286db9a8a2aa537badc83d98a1b74b73e9873
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introducción a las funcionalidades de R de código abierto de R Server en HDInsight

Microsoft R Server (también conocido como Microsoft Machine Learning Server) está disponible como una opción de implementación cuando se crean clústeres de HDInsight en Azure. El tipo de clúster que proporciona esta opción se denomina **R Server**. Esta funcionalidad proporciona a los científicos de datos, a los estadísticos y a los programadores de R acceso a petición a métodos escalables y distribuidos para realizar análisis en HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

R Server en HDInsight proporciona las funcionalidades más recientes para realizar análisis basados en R de conjuntos de datos de prácticamente cualquier tamaño, cargados en el almacenamiento de Data Lake o Azure Blob Storage. Como el clúster de R Server se ha creado utilizando lenguaje R de código abierto, tiene a su disposición más de 8000 paquetes de R de código abierto que puede utilizar con sus aplicaciones basadas en R. También están disponibles las rutinas de ScaleR, un paquete de análisis de macrodatos de Microsoft que se incluye con R Server.

El nodo perimetral de un clúster proporciona un lugar conveniente para conectarse al clúster y ejecutar los scripts de R. Con un nodo perimetral, tiene la opción de ejecutar las funciones distribuidas paralelizadas de ScaleR en los diferentes núcleos del servidor de nodo perimetral. También puede ejecutarlas en los nodos del clúster utilizando los contextos de proceso de Spark o Hadoop MapReduce de ScaleR.

Los modelos o las predicciones que se generen a partir de los análisis pueden descargarse y utilizarse en el entorno local. También pueden emplearse en otro lugar de Azure, en concreto a través del [servicio web](../../machine-learning/studio/publish-a-machine-learning-web-service.md) [Azure Machine Learning Studio](http://studio.azureml.net).

## <a name="get-started-with-r-server-on-hdinsight"></a>Introducción al uso de R Server en HDInsight

Para crear un clúster de R Server en Azure HDInsight, seleccione el tipo de clúster **R Server** cuando cree un clúster de HDInsight en Azure Portal. El tipo de clúster R Server incluye esta plataforma en los nodos de datos del clúster y en un nodo perimetral, que sirve como zona de aterrizaje para los análisis basados en R Server. Consulte [Introducción al uso de R Server en HDInsight](r-server-get-started.md) para conocer una solución alternativa sobre cómo crear el clúster.

## <a name="why-choose-r-server-in-hdinsight"></a>¿Por qué elegir R Server en HDInsight?

R Server en HDInsight le brinda la posibilidad de usar R Server en Azure. R Server incluye:

+ **Las mejores innovaciones en inteligencia artificial que ofrece Microsoft y el código abierto**

  Microsoft no escatima esfuerzos para conseguir que la inteligencia artificial esté a disposición de cada individuo y organización. R Server dispone de un avanzado conjunto de algoritmos distribuidos con una gran escalabilidad, como [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) y [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package), que pueden utilizarse con datos cuyo tamaño supera al de la memoria física y ejecutarse en una gran variedad de plataformas de manera distribuida. Obtenga más información acerca de la colección de [paquetes de R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) y [paquetes de Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) personalizados de Microsoft que se incluyen con el producto.
  
  R Server aúna las innovaciones de Microsoft con las de la comunidad de código abierto (kits de herramientas de R, Python e inteligencia artificial), y todo en una única plataforma de nivel empresarial. Cualquier paquete de aprendizaje automático de código abierto de R o Python puede utilizarse simultáneamente con las innovaciones de Microsoft. 

+ **Puesta en marcha y administración simples, seguras y a gran escala**

  Las empresas que utilizan entornos y paradigmas tradicionales de puesta en marcha terminan invirtiendo mucho tiempo y esfuerzo en este cometido. Algunos de los puntos problemáticos que suelen generar un aumento de los costos y provocar retrasos son el tiempo necesario para traducir los modelos, las iteraciones que se requieren para mantener la validez y la vigencia, la aprobación normativa y la administración de permisos durante el proceso de puesta en marcha.

  R Server cuenta con avanzados mecanismos de [puesta en marcha](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) gracias a los cuales, desde el momento en que se completa el modelo de aprendizaje automático, solo se necesitan unos pocos clics para generar las API de los servicios web. Estos [servicios web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) se hospedan en una malla de servidores locales o en la nube y pueden integrarse con aplicaciones de línea de negocio. Disponer de una malla flexible en la que realizar las implementaciones le brinda la capacidad de escalar el sistema fácilmente en función de las necesidades del negocio, tanto en las puntuaciones por lotes como en las puntuaciones en tiempo real. Para obtener instrucciones, consulte [Operationalize R Server on HDInsight](r-server-operationalize.md) (Puesta en marcha de R Server en HDInsight)///.

+ **Relaciones sólidas entre los ecosistemas para facilitar el éxito a los clientes con un costo total de propiedad óptimo**

  Las personas que se embarcan en iniciativas para conseguir que sus aplicaciones sean inteligentes o que simplemente quieren conocer el nuevo mundo de la inteligencia artificial y el aprendizaje automático necesitan los recursos apropiados para poder ponerse en marcha. Además de esta documentación, Microsoft dispone de diversos recursos de aprendizaje y ha contratado los servicios de varios socios para impartir cursos que le ayudarán a ponerse en marcha y a alcanzar una productividad óptima rápidamente.


## <a name="key-features-of-r-server-on-hdinsight"></a>Características clave de R Server en HDInsight

Las siguientes características se incluyen con R Server en HDInsight.

| Categoría de la característica | DESCRIPCIÓN |
|------------------|-------------|
| Habilitado para R | [Paquetes de R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) para soluciones escritas en R, con una distribución de código abierto de R y una infraestructura de entorno de ejecución para la ejecución de scripts. |
| Habilitado para Python | [Módulos de Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) para soluciones escritas en Python, con una distribución de código abierto de Python y una infraestructura de entorno de ejecución para la ejecución de scripts.  
| [Modelos previamente entrenados](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Para análisis visuales y análisis textuales de sentimientos, preparados para puntuar los datos proporcionados. |
| [Implementación y consumo](r-server-operationalize.md) | Ponga en marcha su servidor e implemente las soluciones como servicios web. |
| [Ejecución remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-server-or-client) | Inicie sesiones remotas en R Server a través de la red desde la estación de trabajo del cliente. |


## <a name="data-storage-options-for-r-server-on-hdinsight"></a>Opciones de almacenamiento de datos de R Server en HDInsight

El almacenamiento predeterminado del sistema de archivos HDFS de los clústeres de HDInsight puede asociarse con una cuenta de Azure Storage o un almacén de Azure Data Lake Store. Esta asociación garantiza que cualquier dato cargado en el almacenamiento de clúster durante el transcurso del análisis se convierta en persistente y esté disponible aunque se elimine el clúster. Existen diversas herramientas para tratar la transferencia de datos a la opción de almacenamiento que seleccione, entre ellas el componente de carga basado en el portal de la cuenta de almacenamiento y la utilidad [AzCopy](../../storage/common/storage-use-azcopy.md).

Tiene la opción de agregar acceso a los almacenes de Data Lake y Blob Storage adicionales durante el aprovisionamiento del clúster independientemente de la opción de almacenamiento principal en uso. Consulte [Introducción al uso de R Server en HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) para información sobre cómo agregar acceso a cuentas adicionales. Vea el artículo suplementario [Opciones de Azure Storage para R Server en HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) para información adicional sobre el uso de varias cuentas de almacenamiento.

También se puede usar [Archivos de Azure](../../storage/files/storage-how-to-use-files-linux.md) como opción de almacenamiento para emplearse en el nodo perimetral. Gracias a Azure Files, podrá montar en el sistema de archivos Linux un recurso compartido de archivos creado en el servicio Azure Storage. Para más información acerca de estas opciones de almacenamiento de datos de R Server en un clúster de HDInsight, consulte [Opciones de Azure Storage para R Server en HDInsight](r-server-storage.md).

## <a name="access-machine-learning-server-on-the-cluster"></a>Acceso a Machine Learning Server en el clúster

Puede conectarse a Microsoft Machine Learning Server en el nodo perimetral utilizando un explorador. Se instala de forma predeterminada durante la creación del clúster. Para obtener más información, consulte [Introducción al uso de R Server en HDInsight](r-server-get-started.md). También puede conectarse a ML Server desde la línea de comandos utilizando SSH/PuTTY para acceder a la consola de R. 

## <a name="develop-and-run-r-scripts"></a>Desarrollo y ejecución de scripts de R

Los scripts de R que se crean y ejecutan pueden usar cualquiera de los más de 8000 paquetes de R de código abierto, además de las rutinas distribuidas y paralelizadas disponibles en la biblioteca de ScaleR. En general, el script que se ejecuta con R Server en el nodo perimetral lo hace dentro del intérprete de R de ese nodo. Las excepciones son los pasos que necesitan llamar a una función ScaleR con un contexto de proceso establecido en Hadoop MapReduce (RxHadoopMR) o Spark (RxSpark). En este caso, la función se ejecuta de forma distribuida en esos nodos de datos (tarea) del clúster asociados a los datos a los que se hacen referencia. Para más información sobre las distintas opciones de contexto de proceso, consulte [Opciones de contexto de proceso para R Server en HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Uso de modelos

Cuando se complete el modelado de datos, podrá utilizar el modelo para realizar predicciones de nuevos datos en Azure o en un entorno local. Este proceso se conoce como "puntuación". La puntuación puede realizarse en HDInsight, Azure Machine Learning o de forma local.

### <a name="score-in-hdinsight"></a>Puntuación en HDInsight
Para realizar puntuaciones en HDInsight, escriba una función de R que llame al modelo para realizar predicciones de un nuevo archivo de datos que se haya cargado en la cuenta de almacenamiento. Después, vuelva a guardar las predicciones en la cuenta de almacenamiento. Puede ejecutar esta rutina a petición en el nodo perimetral del clúster o mediante un trabajo programado.

### <a name="score-in-azure-machine-learning-aml"></a>Puntuación en Azure Machine Learning (AML)
Si desea realizar la puntuación mediante Azure Machine Learning, utilice el paquete de R de Azure Machine Learning de código abierto, conocido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html), para publicar el modelo como un servicio web de Azure. Para mayor comodidad, este paquete está preinstalado en el nodo perimetral. Después, utilizando los recursos de Azure Machine Learning, cree una interfaz de usuario para el servicio web y, si es necesario, llame a dicho servicio web para efectuar las puntuaciones.

Si elige esta opción, tendrá que convertir los objetos de modelo de ScaleR en objetos de modelo de código abierto equivalentes para poder usarlos con el servicio web. Para esta conversión, utilice las funciones de coerción de ScaleR, como `as.randomForest()`, para los modelos basados en conjuntos.

### <a name="score-on-premises"></a>Puntuación en un entorno local
Para puntuar de forma local después de crear el modelo, este se puede serializar en R, descargarlo, anular la serialización y, después, usarlo para puntuar nuevos datos. Puede puntuar nuevos datos adoptando el enfoque descrito antes en [Puntuación en HDInsight](#scoring-in-hdinsight) o mediante [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Mantenimiento del clúster

### <a name="install-and-maintain-r-packages"></a>Instalación y mantenimiento de paquetes de R

La mayoría de los paquetes de R que utiliza se necesitan en el nodo perimetral, puesto que es donde se ejecutan la mayoría de los scripts de R. Para instalar más paquetes de R en el nodo perimetral, puede usar el método `install.packages()` habitual en R.

Si simplemente usa rutinas de la biblioteca ScaleR en el clúster, normalmente no hará falta instalar más paquetes de R en los nodos de datos. Sin embargo, puede que necesite más paquetes para admitir el uso de la ejecución de **rxExec** o **RxDataStep** en los nodos de datos.

En estos casos, los paquetes adicionales pueden instalarse con una acción de script después de crear el clúster. Para más información, consulte [Manage R Server in HDInsight cluster](r-server-hdinsight-manage.md) (Administrar R Server en un clúster de HDInsight)///.

### <a name="change-hadoop-mapreduce-memory-settings"></a>Cambio de la configuración de memoria de Hadoop MapReduce

Los clústeres pueden modificarse para cambiar la cantidad de memoria disponible en R Server cuando se ejecuta un trabajo de MapReduce. Para ello, use la IU de Apache Ambari, que se encuentra disponible a través de la hoja del Portal de Azure de su clúster. Para ver instrucciones para acceder a la IU de Ambari de su clúster, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](../hdinsight-hadoop-manage-ambari.md).

También se puede cambiar la cantidad de memoria disponible en R Server utilizando los modificadores de Hadoop en la llamada a **RxHadoopMR**, tal y como se muestra a continuación:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Escalado del clúster

Los clústeres de R Server que ya existen en HDInsight pueden escalarse o reducirse verticalmente utilizando el portal. Puede utilizar el escalado vertical para conseguir capacidad adicional en caso de que necesite albergar tareas de procesamiento de mayor envergadura o puede reducir el escalado de un clúster cuando esté inactivo. Para ver instrucciones sobre cómo escalar un clúster, consulte [Administración de clústeres de HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Mantenimiento del sistema

El mantenimiento para aplicar revisiones de SO y otras actualizaciones se realiza en las máquinas virtuales Linux subyacentes de un clúster de HDInsight durante las horas de poca actividad con el objetivo. Normalmente, el mantenimiento se realiza a las 3:30 (en la hora local de la máquina virtual) todos los lunes y jueves. Las actualizaciones se llevan a cabo de forma que no afecten a más de un cuarto del clúster a la vez.  

Como los nodos principales son redundantes y no todos los nodos de datos se ven afectados, es posible que se ralenticen los trabajos que se estén ejecutando durante ese tiempo. No obstante, deben seguir ejecutándose hasta que finalicen. Cualquier software personalizado o dato local que haya instalado se conserva a través de estos eventos de mantenimiento, salvo que se produzca un error irrecuperable que requiera recompilar el clúster.

## <a name="ide-options-for-r-server-on-an-hdinsight-cluster"></a>Opciones del IDE de R Server en un clúster de HDInsight

El nodo perimetral de Linux de un clúster de HDInsight es la zona de aterrizaje de los análisis basados en R. Las versiones recientes de HDInsight proporcionan una instalación predeterminada de RStudio Server en el nodo perimetral como un IDE basado en explorador. Puede ser más productivo usar RStudio Server como un IDE para desarrollar y ejecutar scripts de R que si se utiliza simplemente la consola de R.

Además, puede instalar un IDE de escritorio y acceder con él al clúster utilizando un contexto de proceso remoto de MapReduce o Spark. Las opciones incluyen [Herramientas de R para Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) de Microsoft, RStudio y [StatET](http://www.walware.de/goto/statet) basado en Eclipse de Walware.

Finalmente, puede acceder a la consola de R del nodo perimetral escribiendo **R** en el símbolo del sistema de Linux una vez que se haya conectado mediante SSH o PuTY. Cuando utilice la interfaz de la consola, es conveniente ejecutar un editor de texto para desarrollar scripts de R en otra ventana y cortar y pegar las secciones del script en la consola de R según sea necesario.

## <a name="pricing"></a>Precios

Los precios asociados a un clúster de HDInsight con R Server están estructurados de forma similar a los precios de los clústeres de HDInsight estándar. Se basan en el tamaño de las máquinas virtuales subyacentes en los nodos de nombre, de datos y perimetral, con el extra de un aumento de la hora de núcleo. Para más información sobre los precios de HDInsight, vea [HDInsight Precios](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca del uso de R Server en clústeres de HDInsight, consulte los temas siguientes:

* [Introducción al clúster de R Server en Azure HDInsight](r-server-get-started.md)
* [Opciones de contexto de proceso para un clúster de R Server en HDInsight](r-server-compute-contexts.md)
* [Opciones de Azure Storage para un clúster de R Server en HDInsight](r-server-storage.md)
