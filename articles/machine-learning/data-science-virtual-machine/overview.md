---
title: "Introducción a Data Science Virtual Machine de Azure para Linux y Windows | Microsoft Docs"
description: "Componentes y escenarios de análisis fundamentales para instancias de Data Science Virtual Machine en Windows y Linux."
keywords: "herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: a8b9efffd6373ee33026e915b0a14e15d41295b3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Introducción a Data Science Virtual Machine de Azure para Linux y Windows

La máquina virtual de ciencia de datos (DSVM) es una imagen de VM personalizada en la nube de Azure de Microsoft diseñada específicamente para realizar la ciencia de datos. Tiene muchas ciencias de datos conocidas y otras herramientas preinstaladas y preconfiguradas para impulsar la creación de aplicaciones inteligentes para análisis avanzado. Está disponible en Windows Server y en Linux. La edición de Windows de la DSVM se ofrece en Server 2016 y Server 2012. La edición de Linux de la DSVM se ofrece en Ubuntu 16.04 LTS y en las distribuciones de Linux basadas en OpenLogic 7.2 CentOS. 

Este tema describe qué puede hacer con la VM de ciencia de datos, describe algunos de los escenarios clave para el uso de la VM, detalla las funciones clave disponibles en las versiones de Windows y Linux y proporciona instrucciones sobre cómo comenzar a utilizarlas.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>¿Qué puedo hacer con la máquina virtual de ciencia de datos?
El objetivo de la máquina virtual de ciencia de datos es proporcionar a los profesionales de datos de todos los roles y los niveles de conocimiento un entorno de ciencia de datos sin dificultades. Esta VM le ahorra el tiempo que pasaría si implantara un entorno comparable usted mismo. En su lugar, inicie el proyecto de ciencia de datos inmediatamente en una instancia de VM recién creada. 

La máquina virtual de ciencia de datos está diseñada y configurada para trabajar con una amplia variedad de escenarios de uso. Puede escalar verticalmente o reducir verticalmente el entorno a medida que cambian las necesidades del proyecto. Puede usar el idioma que prefiera para programar tareas de ciencia de datos. Puede instalar otras herramientas y personalizar el sistema para sus necesidades exactas.

## <a name="key-scenarios"></a>Escenarios principales
En esta sección se sugieren algunos escenarios clave para los que se pueden implementar la VM de ciencia de datos.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Escritorio de análisis preconfigurados en la nube
La VM de ciencia de datos proporciona una configuración de línea de base para los equipos de ciencia de datos que buscan sustituir sus escritorios locales por un escritorio en la nube administrado. Esta línea de base garantiza que todos los científicos de datos en un equipo tienen una configuración coherente con la que comprobar experimentos y promover la colaboración. También se reducen los costos al reducir la carga del administrador del sistema y guardar en el tiempo necesario para evaluar, instalar y mantener los diversos paquetes de software necesarios para realizar análisis avanzado.  

### <a name="data-science-training-and-education"></a>Educación y formación de ciencia de datos
Los instructores y educadores de empresa que enseñan clases suelen proporcionar una imagen de máquina virtual para garantizar que sus estudiantes tienen una configuración coherente y que los ejemplos funcionan de forma predecible. La VM de ciencia de datos crea un entorno a petición con una configuración coherente que simplifica los desafíos de incompatibilidad y soporte técnico. En los casos en donde estos entornos tienen que crearse con frecuencia, especialmente para las clases de aprendizaje más cortas, estos entornos se benefician sustancialmente.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidad elástica a petición para proyectos de gran escala
La exploración y el modelado de datos a gran escala o las competencias/hackathons de ciencia de datos requieren un escalado horizontal de la capacidad de hardware, normalmente durante poco tiempo. La VM de ciencia de datos puede ayudar a replicar el entorno de ciencia de datos rápidamente a petición, en servidores escalados horizontalmente que permiten experimentos que requieren que se ejecuten recursos informáticos de alta potencia.

### <a name="short-term-experimentation-and-evaluation"></a>Evaluación y experimentación a corto plazo
La máquina virtual de ciencia de datos puede usarse para evaluar o aprender herramientas como Microsoft ML Server, SQL Server, herramientas de Visual Studio, Jupyter, aprendizaje profundo/kits de herramientas de ML y nuevas herramientas conocidas de la comunidad con el mínimo esfuerzo de instalación. Puesto que la VM de ciencia de datos se puede configurar rápidamente, se puede aplicar en otros escenarios de uso a corto plazo como la replicación de los experimentos publicados, la ejecución de demostraciones, el seguimiento de tutoriales en las sesiones en línea o los tutoriales de conferencia.

### <a name="deep-learning"></a>Aprendizaje profundo
La VM de ciencia de datos puede usarse para el modelo de aprendizaje mediante algoritmos de aprendizaje profundo en hardware basado en GPU (unidades de procesamiento de gráficos). Al utilizar la funcionalidad de escalado de máquinas virtuales que ofrece la nube de Azure, la DSVM lo ayudará a usar hardware basado en GPU en la nube según las necesidades. Se puede cambiar a una máquina virtual basada en GPU cuando hay que entrenar modelos grandes o cuando se necesitan cálculos de alta velocidad conservando al mismo tiempo el mismo disco del SO.  La edición de Windows Server 2016 de la DSVM viene preinstalada con controladores de GPU, marcos y la versión de GPU de los algoritmos de aprendizaje profundo. En Linux, el aprendizaje profundo en GPU solo está habilitado en la [máquina virtual de ciencia de datos para edición de Linux (Ubuntu)](http://aka.ms/dsvm/ubuntu). Puede implementar la edición de Ubuntu o Windows Server 2016 de la máquina virtual de ciencia de datos (DSVM) en una máquina virtual de Azure no basada en GPU, en cuyo caso todas las plataformas de aprendizaje profundo se reservarán al modo CPU. Anteriormente, para Windows Server 2012 publicamos un [kit de herramientas de aprendizaje profundo](http://aka.ms/dsvm/deeplearning), pero ahora recomendamos usar Windows Server 2016 para las cargas de trabajo de aprendizaje profundo basadas en Windows. La edición de Linux basada en CentOS de la máquina visual de aprendizaje profundo contiene solo las compilaciones CPU de algunas de las herramientas de aprendizaje profundo (Microsoft Cognitive Toolkit, TensorFlow, MXNet), pero no tiene preinstalados los controladores de GPU ni las plataformas. 

## <a name="whats-included-in-the-data-science-vm"></a>¿Qué se incluye en la VM de ciencia de datos?
La máquina virtual de ciencia de datos tiene muchas herramientas de ciencia de datos y aprendizaje profundo conocidas ya instaladas y configuradas. También incluye herramientas que facilitan trabajar con diversos productos y análisis de datos de Azure. Puede explorar y compilar modelos predictivos en conjuntos de datos a gran escala mediante Microsoft ML Server (R, Python) o con SQL Server 2017. También se incluye un host de otras herramientas de la comunidad de código abierto y desde Microsoft, así como código de ejemplo y bloc de notas. En la tabla siguiente se detalla y compara los componentes principales incluidos en las ediciones de Windows y Linux de la máquina virtual de ciencia de datos.


| **Herramienta**                                                           | **Edición de Windows** | **Edición de Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) con paquetes populares preinstalados   |Y                      | Y             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition incluye: <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Marco de RevoScaleR/RevoScalePy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) de alto rendimiento paralelo y distribuido (R y Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;*   [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) Nuevos algoritmos de aprendizaje automático de última generación de Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Operacionalización de R y Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro Plus con activación compartida: Excel, Word y PowerPoint   |Y                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 con paquetes populares preinstalados    |Y                      |Y              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) con paquetes populares para lenguaje Julia preinstalados                         |Y                      |Y              |
| Bases de datos relacionales                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/)(solo CentOS) |
| Herramientas de base de datos                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * Controladores ODBC/JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (herramienta de consulta), <br /> * bcp, sqlcmd <br /> * Controladores ODBC/JDBC|
| Análisis de base de datos escalable con ML Services de SQL Server (R, Python) | Y     |N              |
| **[Jupyter Notebook Server](http://jupyter.org/) con los kernel siguientes,**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 y 3.5 | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;*   [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (solo Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub (servidor de cuadernos de varios usuarios)| N | Y |
| **Herramientas de desarrollo, entornos de desarrollo integrados y editores de código**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) &gt; con Git Plugin, Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs) y [Herramientas de R para Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [PyCharm](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Juno (Julia IDE)](http://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim y Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git y GitBash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | Y | N |
| PowerBI Desktop | Y | N |
| SDK para obtener acceso al conjunto de aplicaciones Cortana Intelligence y Azure | Y | Y |
| **Herramientas de movimiento de datos y administración** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Explorador de Azure Storage | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [CLI de Azure](https://docs.microsoft.com/cli/azure/overview) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Adlcopy (Almacén de Azure Data Lake)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Herramienta DocDB Data Migration](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): traslado de datos entre local y la nube | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Utilidades de línea de comandos Unix/Linux | Y | Y |
| [Apache Drill](http://drill.apache.org) para la exploración de datos | Y | Y |
| **Herramientas de aprendizaje automático** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integración con [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Rattle](http://rattle.togaware.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (solo Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [H2O](https://www.h2o.ai/h2o/) | N | Y (solo Ubuntu) |
| **Herramientas de aprendizaje profundo basado en GPU** |Edición de Windows Server 2016  |Edición de Ubuntu |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (anteriormente conocido como CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [MXNet](http://mxnet.io/) | Y | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Caffe y Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [CUDA, CUDNN, Nvidia Driver](https://developer.nvidia.com/cuda-toolkit) | Y | Y |
| **Plataforma de macrodatos (solo Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* [Spark](http://spark.apache.org/) Standalone local | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) local | N | Y |



## <a name="get-started-with-the-windows-data-science-vm"></a>Introducción a la máquina virtual de ciencia de datos en Windows
* Cree una instancia de la edición de la DSVM de Windows que quiera accediendo a
  * [DSVM basada en Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  o 
  * [DSVM basada en Windows Server 2012](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 
* Haga clic en el botón **OBTENERLA AHORA**.
* Inicie sesión en la VM desde el escritorio remoto con las credenciales que especificó cuando creó la VM.
* Para detectar e iniciar las herramientas disponibles, haga clic en el menú **Inicio**.

## <a name="get-started-with-the-linux-data-science-vm"></a>Introducción a la VM de ciencia de datos de Linux
* Cree una instancia de la edición de la DSVM de Linux que quiera accediendo a 
  * [DSVM basada en Ubuntu](http://aka.ms/dsvm/ubuntu)

  o

  * [DSVM basada en OpenLogic CentOS](http://aka.ms/dsvm/centos)

  
* Haga clic en el botón **Obtenerla ahora**.
* Inicie sesión en una VM desde un cliente SSH, como Putty o comando SSH con las credenciales que especificó cuando creó la VM.
* En el símbolo del sistema de shell, especifique dsvm-more-info.
* Para un escritorio gráfico, descargue el cliente X2Go para la plataforma cliente [aquí](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) y siga las instrucciones en el documento de VM de ciencia de datos de Linux [Aprovisionamiento de máquina virtual de ciencia de datos de Linux](linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Pasos siguientes
### <a name="for-the-windows-data-science-vm"></a>Para la VM de ciencia de datos de Windows
* Para obtener más información sobre cómo ejecutar las herramientas específicas disponibles en la versión de Windows, vea [Aprovisionamiento de la máquina virtual de ciencia de datos de Microsoft](provision-vm.md) y
* Para obtener información sobre cómo realizar diversas tareas necesarias para el proyecto de ciencia de datos en la VM de Windows, vea [Diez cosas que puede hacer en la máquina virtual de ciencia de datos](vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Una VM de ciencia de los datos de Linux
* Para obtener más información sobre cómo ejecutar las herramientas específicas disponibles en la versión de Linux, vea [Aprovisionamiento de la máquina virtual de ciencia de datos de Linux](linux-dsvm-intro.md).
* Para ver un tutorial que muestra cómo llevar a cabo varias tareas de ciencia de datos comunes con la VM de Linux, consulte [Ciencia de datos en la máquina virtual de ciencia de datos de Linux](linux-dsvm-walkthrough.md).

