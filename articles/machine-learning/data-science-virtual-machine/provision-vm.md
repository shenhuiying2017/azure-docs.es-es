---
title: Aprovisionamiento de Data Science Virtual Machine de Windows en Azure | Microsoft Docs
description: "Configure y cree una instancia de Data Science Virtual Machine en Azure para realizar análisis y aprendizaje automático."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: d71d8e44d0327515ed302c5c902ce87587e36c7d
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Aprovisionamiento de Data Science Virtual Machine de Windows en Azure
Microsoft Data Science Virtual Machine es una imagen de máquina virtual (VM) de Windows Azure preinstalada y configurada con varias herramientas populares que se usan habitualmente para el análisis de datos y el aprendizaje automático. Las herramientas incluidas son:

* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning-services/) Workbench
* [Microsoft ML Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anaconda Python Distribution
* Notebook de Jupyter (con kernels R, Python y PySpark)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* Instancia independiente de Spark para desarrollo y pruebas locales
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Herramientas de aprendizaje automático y análisis
  * Marcos de aprendizaje profundo: amplio conjunto de marcos de IA, como [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras, incluido en la máquina virtual.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): sistema de aprendizaje automático rápido que admite varias técnicas, como el aprendizaje en línea, el uso de hash, la clase AllReduce, las reducciones, learning2search y los aprendizajes activo e interactivo
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): herramienta que proporciona una implementación de árbol ampliada, rápida y precisa
  * [Rattle](http://rattle.togaware.com/) (sigla del inglés “R Analytical Tool To Learn Easily”, la herramienta de análisis de R para aprender fácilmente): herramienta que simplifica la introducción al análisis de datos y al aprendizaje automático en R con una exploración de datos basada en GUI y un modelado con generación automática de códigos en R
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/): software de minería de datos visual y aprendizaje automático de Java.
  * [Apache Drill](https://drill.apache.org/): motor de consultas SQL sin esquemas para Hadoop, NoSQL y almacenamiento en la nube.  Es compatible con las interfaces ODBC y JDBC para habilitar consultas NoSQL y archivos de herramientas de BI estándar como Power BI, Excel o Tableau.
* Bibliotecas en R y Python para usarlas en Azure Machine Learning y en otros servicios de Azure
* Git, incluido Git Bash, para trabajar con repositorios de código fuente, entre otros, GitHub, Visual Studio Team Services
* Puertos de Windows de varias utilidades de línea de comandos de Linux populares (en otras, awk, sed, perl, grep, buscar, wget, curl, etc.) accesibles mediante el símbolo del sistema. 

La ciencia de datos implica la iteración de una secuencia de tareas:

1. Buscar, cargar y preprocesar datos
2. Compilar y probar modelos
3. Implementar los modelos para consumirse en aplicaciones inteligentes

Los científicos de datos usan varias herramientas para realizar estas tareas. Puede ser bastante lento encontrar las versiones del software adecuadas, descargarlas e instalarlas. Para reducir esta carga, Microsoft Data Science Virtual Machine proporciona una imagen lista para usar que se puede aprovisionar en Azure con las herramientas más populares preinstaladas y configuradas. 

Microsoft Data Science Virtual Machine da un empujón al inicio de los proyecto de análisis. Le permite trabajar en tareas en varios lenguajes, incluidos R, Python, SQL y C#. Visual Studio proporciona un IDE para desarrollar y probar el código que es fácil de usar. El SDK de Azure incluido en la máquina virtual permite crear aplicaciones con varios servicios en la plataforma en la nube de Microsoft. 

No hay ningún cargo de software para esta imagen de VM de ciencia de datos. Solo paga por las cuotas de uso de Azure, que dependen del tamaño de la máquina virtual que aprovisione. Puede encontrar más detalles sobre las cuotas de proceso en la sección de detalles de precios de la página [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) . 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Otras versiones de Data Science Virtual Machine
También hay una imagen de [Ubuntu](dsvm-ubuntu-intro.md) disponible, con muchas herramientas similares más otros marcos de trabajo de aprendizaje profundo. También hay una imagen de [CentOS](linux-dsvm-intro.md) disponible. También ofrecemos la [versión Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) de la máquina virtual de ciencia de datos, aunque algunas herramientas solo están disponibles para la versión Windows Server 2016.  Respecto al resto, este artículo también se aplica a la versión Windows Server 2012.

## <a name="prerequisites"></a>Requisitos previos
Antes de poder crear una Microsoft Data Science Virtual Machine, debe tener lo siguiente:

* **Una suscripción a Azure**: para conseguir una, vea [Obtención de una evaluación gratuita de Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Creación de su Microsoft Data Science Virtual Machine
Estos son los pasos para crear una instancia de Microsoft Data Science Virtual Machine:

1. Navegue a la lista de máquinas virtuales en [Azure Portal](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
2. Seleccione el botón **Crear** ubicado en la parte inferior para acceder a un asistente.![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. El asistente que se usó para crear la instancia de Microsoft Data Science Virtual Machine necesita **datos de entrada** para cada uno de los **cuatro pasos** que se enumeran en la parte derecha de esta ilustración. Estas son las entradas necesarias para configurar cada uno de estos pasos:
   
   1. **Aspectos básicos**
      
      1. **Nombre**: nombre del servidor de ciencia de datos que está creando.
      2. **Tipo de disco de máquina virtual**: elija entre SSD o HDD. Para GPU (serie NC), elija **HDD** como tipo de disco. 
      3. **Nombre de usuario**: identificador de inicio de sesión de la cuenta del administrador.
      4. **Contraseña**: contraseña de la cuenta del administrador.
      5. **Suscripción**: si tiene más de una suscripción, seleccione aquella en la que se creará y facturará la máquina.
      6. **Grupo de recursos**: puede crear uno nuevo o usar un grupo que ya exista.
      7. **Ubicación**: seleccione el centro de datos más adecuado. Normalmente es el centro de datos que tenga la mayoría de los datos o que esté más cercano a su ubicación física para un acceso más rápido a la red.
   2. **Tamaño**: seleccione uno de los tipos de servidor que cumpla sus requisitos funcionales y las restricciones de costo. Para obtener más opciones de tamaños de máquina virtual, seleccione "Ver todo".
   3. **Configuración**:
      
      1. **Usar Managed Disks**: elija Administrado si desea que Azure administre los discos de la máquina virtual.  En caso contrario, debe especificar una cuenta de almacenamiento nueva o existente. 
      2. **Otros parámetros**: normalmente usará simplemente los valores predeterminados. Puede mover el puntero sobre el vínculo informativo para obtener ayuda sobre los campos específicos en caso de que desee considerar el uso de valores no predeterminados.
   4. **Resumen**: compruebe que toda la información que ha especificado es correcta y haga clic en **Crear**. **NOTA**: La máquina virtual no tiene ningún cargo aparte del proceso para el tamaño del servidor que eligió en el paso **Tamaño**. 

> [!NOTE]
> El aprovisionamiento tardará entre 10 y 20 minutos. El estado del aprovisionamiento se muestra en Azure Portal.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Acceso a Microsoft Data Science Virtual Machine
Una vez creada la máquina virtual, puede usar el escritorio remoto con las credenciales de la cuenta del administrador que configuró en la sección **Aspectos básicos** anterior. 

Una vez creada y aprovisionada la máquina virtual, está listo para comenzar a usar las herramientas que se instalan y configuran en ella. Hay iconos del menú de inicio e iconos del escritorio para muchas de las herramientas. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Herramientas instaladas en Microsoft Data Science Virtual Machine

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Machine Learning Workbench es una aplicación de escritorio y una interfaz de la línea de comandos. Workbench tiene un sistema de preparación de datos integrado que aprende sus pasos de preparación de datos conforme los lleva a cabo. También proporciona la administración de proyectos, el historial de ejecuciones y la integración de cuadernos para aumentar la productividad. Aproveche los mejores marcos de código abierto, como TensorFlow, Cognitive Toolkit, Spark ML y scikit-learn para desarrollar sus modelos. En DSVM, proporcionamos un icono del escritorio (InstallAMLFromLocal) para extraer localmente Azure Machine Learning Workbench en el directorio %LOCALAPPDATA% de cada usuario. Los usuarios que necesiten usar una instancia de Workbench deben seguir una acción única de doble clic en el icono del escritorio InstallAMLFromLocal para instalarla. Azure Machine Learning también crea y usa un entorno de Python por usuario que se extrae en la carpeta %LOCALAPPDATA%\amlworkbench\python.

### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Si desea utilizar las bibliotecas empresariales de Microsoft para R o Python escalables para los análisis, la máquina virtual tendrá Microsoft ML Server Developer Edition (conocido anteriormente como Microsoft R Server) instalada. Microsoft ML Server es una plataforma de análisis empresarial que se puede implementar ampliamente, está disponible para R y Python, y es escalable; además es compatible y segura comercialmente. Compatible con una gran variedad de estadísticas de macrodatos, con funcionalidades de modelado de predicción y de aprendizaje automático, ML Server admite toda la gama de análisis: exploración, análisis, visualización y modelado. Al usar y ampliar R y Python de código abierto, Microsoft ML Server es totalmente compatible con los scripts, las funciones y los paquetes CRAN/pip/Conda de R y Python, a fin de analizar datos a escala empresarial. También resuelve las limitaciones en memoria de R de origen abierto agregando el procesamiento paralelo y fragmentado de datos. Esto permite ejecutar análisis de datos mucho mayor de lo que cabe en la memoria principal.  La versión Visual Studio Community Edition incluida en la máquina virtual contiene las extensiones Herramientas de R para Visual Studio y Herramientas de Python para Visual Studio que proporciona un IDE completo para trabajar con R o Python. También se proporcionan otros IDE, como [RStudio](http://www.rstudio.com) y [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) en la máquina virtual. 

### <a name="python"></a>Python
Para el desarrollo con Python, se ha instalado Anaconda Python Distribution 2.7 y 3.5. Esta distribución contiene Python base, junto con aproximadamente 300 de los paquetes de matemáticas, ingeniería y análisis de datos más populares. Puede usar Herramientas de Python para Visual Studio (PTVS) que se instala en la edición de Visual Studio 2015 Community o uno de los IDE incluidos con Anaconda, como IDLE o Spyder. Para iniciar uno de ellos, busque en la barra de búsqueda (tecla **Win** + **S**).

> [!NOTE]
> Para que Python Tools for Visual Studio apunte a Anaconda Python 2.7 y 3.5, tiene que crear entornos personalizados para cada versión. Para establecer estas rutas de entorno en Visual Studio 2015 Community Edition, vaya a **Herramientas** -> **Herramientas de Python** -> **Entornos de Python** y haga clic en **+ Personalizar**. 
> 
> 

Anaconda Python 2.7 se instala en C:\Anaconda y Anaconda Python 3.5 se instala en c:\Anaconda\envs\py35. Consulte la [documentación de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para ver los pasos detallados. 

### <a name="jupyter-notebook"></a>Jupyter Notebook
La distribución de Anaconda también incluye una instancia de Jupyter Notebook, un entorno para compartir código y análisis. Previamente se ha configurado un servidor de notebooks de Jupyter con kernels de Python 2.7, Python 3.5, PySpark, Julia y R. Hay un icono del escritorio llamado "Jupyter Notebook" para iniciar el servidor de Jupyter y el explorador para acceder al servidor de Notebook. 

> [!NOTE]
> Continúe aunque aparezca una advertencia de certificado. 
> 
> 

Hemos empaquetado varios notebooks de ejemplo en Python y en R. Los notebooks de Jupyter muestran cómo trabajar con Microsoft ML Server, SQL Server ML Services (análisis en base de datos), Python, Microsoft Cognitive ToolKit, Tensorflow y otras tecnologías de Azure una vez haya accedido a Jupyter. Puede ver el vínculo a los ejemplos en la página de inicio del notebook después de que se autentique en Jupyter Notebook con la contraseña creada en el paso anterior. 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community Edition
Visual Studio Community Edition instalado en la máquina virtual. Se trata de una versión gratuita del popular IDE de Microsoft que puede usar para fines de evaluación y para equipos pequeños. Puede revisar los términos de licencia [aquí](https://www.visualstudio.com/support/legal/mt171547).  Haga doble clic en el icono del escritorio o en el menú **Inicio** para abrir Visual Studio. Para buscar programas, también puede usar **Win** + **S** y escribir "Visual Studio". Una vez ahí, puede crear proyectos en lenguajes como C#, Python, R o node.js. También encontrará complementos instalados que resultan prácticos para trabajar con servicios de Azure como Azure Data Catalog, Azure HDInsight (Hadoop, Spark) y Azure Data Lake. 

> [!NOTE]
> Quizás vea un mensaje que indica que el período de evaluación ha expirado. Escriba las credenciales de la cuenta Microsoft o cree una nueva cuenta gratuita para obtener acceso a Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition
La máquina virtual (R o Python) incluye la versión para desarrolladores de SQL Server 2017 con ML Services para ejecutar análisis en bases de datos. ML Services proporciona una plataforma para desarrollar e implementar aplicaciones inteligentes. Puede usar estos lenguajes, completos y eficaces, y los numerosos paquetes de la comunidad para crear modelos y generar predicciones con sus datos de SQL Server. Puede mantener análisis cerca de los datos, porque ML Services (en la base de datos) integra los lenguajes R y Python con SQL Server. Esto elimina los costos y riesgos de seguridad asociados con el movimiento de datos.

> [!NOTE]
> La edición para desarrolladores de SQL Server solo puede utilizarse para fines de desarrollo y prueba. Para ejecutarlo en producción necesita una licencia. 
> 
> 

Para tener acceso a SQL Server, inicie **SQL Server Management Studio**. El nombre de la máquina virtual se rellenará como Nombre del servidor. Use Autenticación de Windows cuando inicie sesión como administrador en Windows. Una vez que esté en SQL Server Management Studio, puede crear otros usuarios, crear bases de datos, importar datos y ejecutar consultas SQL. 

Para habilitar el análisis en la base de datos con ML Services de SQL, ejecute el siguiente comando como una acción puntual en SQL Server Management Studio después de iniciar sesión como administrador del servidor. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
En la VM se instalan varias herramientas de Azure:

* Hay un acceso directo del escritorio para tener acceso a la documentación del SDK de Azure. 
* **AzCopy**: se usa para trasladar datos hacia y desde la cuenta de Microsoft Azure Storage. Escriba **Azcopy** en el símbolo del sistema para ver su uso. 
* **Explorador de Microsoft Azure Storage**: se usa para explorar los objetos que almacenó en la cuenta de Azure Storage y para transferir los datos hacia y desde Azure Storage. Puede escribir **Explorador de Storage** en la búsqueda o buscarlo en el menú Inicio de Windows para acceder a esta herramienta. 
* **Adlcopy**: se usa para trasladar datos a Azure Data Lake. Para ver su uso escriba **adlcopy** en el símbolo del sistema. 
* **dtui**: se usa para trasladar datos hacia y desde Azure Cosmos DB, una base de datos NoSQL en la nube. Escriba **dtui** en el símbolo del sistema. 
* **Integration Runtime de Azure Data Factory**: permite el traslado de datos entre orígenes de datos locales y la nube. Se usa en herramientas como Azure Data Factory. 
* **Microsoft Azure PowerShell**: en la máquina virtual también se instala una herramienta para administrar los recursos de Azure en el lenguaje de scripting de PowerShell. 

### <a name="power-bi"></a>Power BI
Para ayudarle a crear paneles y visualizaciones excelentes, se instaló **Power BI Desktop** . Use esta herramienta para extraer datos de orígenes diferentes, para crear los paneles e informes y publicarlos en la nube. Para obtener información, consulte el sitio de [Power BI](http://powerbi.microsoft.com) . Puede encontrar Power BI Desktop en el menú Inicio. 

> [!NOTE]
> Necesitará una cuenta de Office 365 para tener acceso a Power BI. 
> 
> 

## <a name="additional-microsoft-development-tools"></a>Herramientas de desarrollo de Microsoft adicionales
Puede usar el [**Instalador de plataforma web de Microsoft**](https://www.microsoft.com/web/downloads/platform.aspx) para detectar y descargar otras herramientas de desarrollo de Microsoft. También hay un acceso directo a la herramienta que se proporciona en el escritorio de  Microsoft Data Science Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Directorios importantes en la máquina virtual
| item | Directorio |
| --- | --- |
| Configuraciones del servidor de notebooks de Jupyter |C:\ProgramData\jupyter |
| Directorio principal de ejemplos de notebooks de Jupyter |c:\dsvm\notebooks |
| Otros ejemplos |c:\dsvm\samples |
| Anaconda (predeterminado: Python 2.7) |c:\Anaconda |
| Entorno Anaconda Python 3.5 |c:\Anaconda\envs\py35 |
| Python independiente de Microsoft ML Server  | C:\Archivos de programa\Microsoft\ML Server\PYTHON_SERVER |
| Instancia de R predeterminada (independiente de ML Server) |C:\Archivos de programa\Microsoft\ML Server\R_SERVER |
| Directorio de la instancia en la base de datos de ML Services de SQL |C:\Archivos de programa\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench (por usuario) | %localappdata%\amlworkbench | 
| Herramientas varias |c:\dsvm\tools |

> [!NOTE]
> Las instancias de Microsoft Data Science Virtual Machine creadas antes de la versión 1.5.0 (antes del 3 de septiembre de 2016) usan una estructura de directorios ligeramente diferente de la especificada en la tabla anterior. 
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Estos son algunos pasos para proseguir con el aprendizaje y la exploración. 

* Explore las diversas herramientas de Data Science Virtual Machine haciendo clic en el menú Inicio y comprobando las herramientas incluidas en el menú.
* Más información acerca de Azure Machine Learning Services y Workbench en la [página de las guías de inicio rápido y los tutoriales](https://docs.microsoft.com/azure/machine-learning/preview/) del producto. 
* Vaya a **C:\Archivos de programa\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** para ver ejemplos de uso de la biblioteca RevoScaleR de R que admiten análisis de datos a escala empresarial.  
* Lea el artículo [Diez cosas que puede hacer en Data Science Virtual Machine](http://aka.ms/dsvmtenthings)
* Aprenda a crear soluciones analíticas completas mediante el uso sistemático del [Proceso de ciencia de los datos en equipos (TDSP)](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visite la [Galería de Azure AI](http://gallery.cortanaintelligence.com) para ejemplos de aprendizaje automático y análisis de datos donde se usa Azure Machine Learning y servicios de datos relacionados en Azure. También hemos proporcionado un icono en el menú **Inicio** y en el escritorio de la máquina virtual para el acceso a esta galería.

