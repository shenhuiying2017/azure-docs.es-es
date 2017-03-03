---
title: "¿Qué es una máquina virtual de ciencia de datos? | Microsoft Docs"
description: "Obtenga información acerca de los escenarios clave, funciones y cómo empezar a trabajar con máquinas virtuales de ciencia de datos, un entorno y un kit de herramientas preparado para el análisis."
keywords: "herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 6c664a055e577881d4fcccd5b0ba4047d88aa9ef
ms.openlocfilehash: 64b413451c6ce47cc3aa14322b2aa0342e1e3ffe
ms.lasthandoff: 02/02/2017


---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introducción a la máquina virtual de la ciencia de datos basada en la nube para Linux y Windows
La máquina virtual de ciencia de datos es una imagen VM personalizada en la nube de Azure de Microsoft diseñada específicamente para realizar la ciencia de datos. Tiene muchas ciencias de datos conocidas y otras herramientas preinstaladas y preconfiguradas para impulsar la creación de aplicaciones inteligentes para análisis avanzado. Está disponible en Windows Server 2012 o en las versiones basadas de Linux basadas en OpenLogic 7.2 CentOS. 

Este tema describe qué puede hacer con la VM de ciencia de datos, describe algunos de los escenarios clave para el uso de la VM, detalla las funciones clave disponibles en las versiones de Windows y Linux y proporciona instrucciones sobre cómo comenzar a utilizarlas.

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>¿Qué puedo hacer con la máquina virtual de ciencia de datos?
El objetivo de la máquina virtual de ciencia de datos es proporcionar a los profesionales de datos de todos los roles y los niveles de conocimiento un entorno de ciencia de datos sin dificultades. Esta VM le ahorra el tiempo que pasaría si implantara un entorno comparable usted mismo. En su lugar, inicie el proyecto de ciencia de datos inmediatamente en una instancia de VM recién creada. 

La VM de ciencia de datos está diseñada y está configurada para trabajar con amplios escenarios de uso. Puede escalar verticalmente o reducir verticalmente el entorno a medida que cambian las necesidades del proyecto. Puede usar el idioma que prefiera para programar tareas de ciencia de datos. Puede instalar otras herramientas y personalizar el sistema para sus necesidades exactas.

## <a name="key-scenarios"></a>Escenarios principales
En esta sección se sugieren algunos escenarios clave para los que se pueden implementar la VM de ciencia de datos.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Escritorio de análisis preconfigurados en la nube
La VM de ciencia de datos proporciona una configuración de línea de base para los equipos de ciencia de datos que buscan sustituir sus escritorios locales por un escritorio en la nube administrado. Esta línea de base garantiza que todos los científicos de datos en un equipo tienen una configuración coherente con la que comprobar experimentos y promover la colaboración. También se reducen los costos al reducir la carga del administrador del sistema y guardar en el tiempo necesario para evaluar, instalar y mantener los diversos paquetes de software necesarios para realizar análisis avanzado.  

### <a name="data-science-training-and-education"></a>Educación y formación de ciencia de datos
Los instructores y educadores de empresa que enseñan clases suelen proporcionar una imagen de máquina virtual para garantizar que sus estudiantes tienen una configuración coherente y que los ejemplos funcionan de forma predecible. La VM de ciencia de datos crea un entorno a petición con una configuración coherente que simplifica los desafíos de incompatibilidad y soporte técnico. En los casos en donde estos entornos tienen que crearse con frecuencia, especialmente para las clases de aprendizaje más cortas, estos entornos se benefician sustancialmente.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidad elástica a petición para proyectos de gran escala
La exploración y el modelado de datos a gran escala o las competencias/hackathons de ciencia de datos requieren un escalado horizontal de la capacidad de hardware, normalmente durante poco tiempo. La VM de ciencia de datos puede ayudar a replicar el entorno de ciencia de datos rápidamente a petición, en servidores escalados horizontalmente que permiten experimentos que requieren que se ejecuten recursos informáticos de alta potencia.

### <a name="short-term-experimentation-and-evaluation"></a>Evaluación y experimentación a corto plazo
La VM de ciencia de datos puede usarse para evaluar o aprender herramientas como Microsoft R Server, SQL Server, herramientas de Visual Studio, Jupyter, aprendizaje en profundidad/kits de herramientas de ML y nuevas herramientas conocidas de la comunidad con el mínimo esfuerzo de instalación. Puesto que la VM de ciencia de datos se puede configurar rápidamente, se puede aplicar en otros escenarios de uso a corto plazo como la replicación de los experimentos publicados, la ejecución de demostraciones, el seguimiento de tutoriales en las sesiones en línea o los tutoriales de conferencia.

## <a name="whats-included-in-the-data-science-vm"></a>¿Qué se incluye en la VM de ciencia de datos?
La máquina virtual de ciencia de datos tiene muchas herramientas de ciencia de datos conocidas ya instaladas y configuradas. También incluye herramientas que facilitan trabajar con diversos productos y análisis de datos de Azure. Puede explorar y generar modelos predictivos en conjuntos de datos a gran escala mediante Microsoft R Server o con SQL Server 2016. También se incluye un host de otras herramientas de la comunidad de código abierto y desde Microsoft, así como código de ejemplo y bloc de notas. En la tabla siguiente se detalla y compara los componentes principales incluidos en las ediciones de Windows y Linux de la máquina virtual de ciencia de datos.

| **Edición de Windows** | **Edición de Linux** |
| -- | --|
| Microsoft R Open con paquetes populares preinstalados | Microsoft R Open con paquetes populares preinstalados |
| Microsoft R Server Developer Edition con algoritmos de MicrosoftML y Microsoft R Operationalization  |Microsoft R Server Developer Edition |
| Anaconda Python 2.7, 3.5 |Anaconda Python 2.7, 3.5 con paquetes populares preinstalados|
| JuliaPro con paquetes populares preinstalados | JuliaPro con paquetes populares preinstalados |
| Jupyter Notebook Server (R, Python, Julia) |JupyterHub: Jupyter Notebooks multiusuario (R, Python, Julia, PySpark) |
| SQL Server 2016 Developer Edition SP1: Análisis de base de datos escalable con servicios de R |PostgresSQL, SQuirreL SQL (herramienta de la base de datos), controladores de SQL Server y línea de comandos (bcp, sqlcmd) |
|- Visual Studio Community Edition 2015 (IDE) </br> - Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools </br> - Herramientas R, Node.js y Python para Visual Studio (RTVS 0.5) </br>- R Studio Desktop|IDE y editores </br> - Eclipse con complemento de kit de herramientas de Azure </br> - Emacs (con ESS, auctex) gedit </br> - IntelliJ IDEA</br> - PyCharm</br> - Atom</br> - Código de Visual Studio|
| Power BI Desktop |-- |
| Herramientas de aprendizaje automático </br> - Integración con Azure Machine Learning </br> - Aprendizaje en profundidad/AI con Microsoft Cognitive Toolkit (CNTK 2.0) </br> - Xgboost (herramienta ML conocida en competencias de ciencia de datos) </br> - Vowpal Wabbit (objetivo del aprendizaje en línea rápido) </br> - Rattle (herramienta de análisis y datos de inicio rápido visual) </br> - Mxnet (aprendizaje en profundidad/AI) </br> -Tensorflow  |Herramientas de aprendizaje automático </br> - Integraciones con Azure Machine Learning </br> - CNTK (aprendizaje en profundidad/AI) </br> - Xgboost (herramienta ML conocida en competencias de ciencia de datos) </br> - Vowpal Wabbit (objetivo del aprendizaje en línea rápido) </br> - Rattle (herramienta de análisis y datos de inicio rápido visual) </br> - Mxnet (aprendizaje en profundidad/AI)|
| SDK para obtener acceso al conjunto de aplicaciones Cortana Intelligence y Azure |SDK para obtener acceso al conjunto de aplicaciones Cortana Intelligence y Azure |
| Herramientas para el movimiento de datos y administración de recursos de Azure y macrodatos: Azure Storage Explorer, CLI, PowerShell, AdlCopy (Azure Data Lake), AzCopy, dtui (para DocumentDB), Microsoft Data Management Gateway |Herramientas para la administración de datos y administración de recursos de Azure y macrodatos: Azure Storage Explorer, CLI |
| Git, complemento de Visual Studio Team Services |Git |
| Puerto de Windows de las utilidades de línea de comandos de Linux/Unix más conocidas accesibles a través de la línea de comandos/GitBash |-- |
| Weka | Weka |
| Detalles | Detalles |
| --| Spark local |

## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Cómo empezar a trabajar con la VM de ciencia de datos de Windows
* Cree una instancia de la VM en Windows desplazándose a [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) y seleccionando el botón verde **Crear máquina virtual**.
* Inicie sesión en la VM desde el escritorio remoto con las credenciales que especificó cuando creó la VM.
* Para detectar e iniciar las herramientas disponibles, haga clic en el menú **Inicio**.

## <a name="get-started-with-the-linux-data-science-vm"></a>Introducción a la VM de ciencia de datos de Linux
* Cree una instancia de la VM en Linux (basado en OpenLogic CentOS) desplazándose hasta [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) y seleccionando el botón **Crear máquina virtual**.
* Inicie sesión en una VM desde un cliente SSH, como Putty o comando SSH con las credenciales que especificó cuando creó la VM.
* En el símbolo del sistema de shell, especifique dsvm-more-info.
* Para un escritorio gráfico, descargue el cliente X2Go para la plataforma cliente [aquí](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) y siga las instrucciones en el documento de VM de ciencia de datos de Linux [Aprovisionamiento de máquina virtual de ciencia de datos de Linux](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Pasos siguientes
### <a name="for-the-windows-data-science-vm"></a>Para la VM de ciencia de datos de Windows
* Para obtener más información sobre cómo ejecutar las herramientas específicas disponibles en la versión de Windows, vea [Aprovisionamiento de la máquina virtual de ciencia de datos de Microsoft](machine-learning-data-science-provision-vm.md) y
* Para obtener información sobre cómo realizar diversas tareas necesarias para el proyecto de ciencia de datos en la VM de Windows, vea [Diez cosas que puede hacer en la máquina virtual de ciencia de datos](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Una VM de ciencia de los datos de Linux
* Para obtener más información sobre cómo ejecutar las herramientas específicas disponibles en la versión de Linux, vea [Aprovisionamiento de la máquina virtual de ciencia de datos de Linux](machine-learning-data-science-linux-dsvm-intro.md).
* Para ver un tutorial que muestra cómo llevar a cabo varias tareas de ciencia de datos comunes con la VM de Linux, consulte [Ciencia de datos en la máquina virtual de ciencia de datos de Linux](machine-learning-data-science-linux-dsvm-walkthrough.md).


