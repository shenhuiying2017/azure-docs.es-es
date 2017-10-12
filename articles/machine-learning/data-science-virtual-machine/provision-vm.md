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
ms.openlocfilehash: a438a0d83ad4749953297b6481cba9cec5900140
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Aprovisionamiento de Data Science Virtual Machine de Windows en Azure
Microsoft Data Science Virtual Machine es una imagen de máquina virtual (VM) de Windows Azure preinstalada y configurada con varias herramientas populares que se usan habitualmente para el análisis de datos y el aprendizaje automático. Las herramientas incluidas son:

* Microsoft R Server Developer Edition
* Anaconda Python Distribution
* Notebook de Jupyter (con kernels R, Python)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2016 Developer Edition
* Herramientas de aprendizaje automático y análisis
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK): kit de herramientas de software de aprendizaje profundo de Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): sistema de aprendizaje automático rápido que admite varias técnicas, como el aprendizaje en línea, el uso de hash, la clase AllReduce, las reducciones, learning2search y los aprendizajes activo e interactivo
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): herramienta que proporciona una implementación de árbol ampliada, rápida y precisa
  * [Rattle](http://rattle.togaware.com/) (sigla del inglés “R Analytical Tool To Learn Easily”, la herramienta de análisis de R para aprender fácilmente): herramienta que simplifica la introducción al análisis de datos y al aprendizaje automático en R con una exploración de datos basada en GUI y un modelado con generación automática de códigos en R
  * [mxnet](https://github.com/dmlc/mxnet): un entorno de aprendizaje en profundidad diseñado para lograr eficiencia y flexibilidad
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

No hay ningún cargo de software para esta imagen de VM de ciencia de datos. Solo paga por las cuotas de uso de Azure, que dependen del tamaño de la máquina virtual que aprovisione. Puede encontrar más detalles sobre las cuotas de proceso en la sección de detalles de precios de la página [Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Otras versiones de Data Science Virtual Machine
También hay una imagen de [CentOS](linux-dsvm-intro.md) disponible, con la mayoría de las mismas herramientas que la imagen de Windows. También hay una imagen de [Ubuntu](dsvm-ubuntu-intro.md) disponible, con muchas herramientas similares más marcos de trabajo de aprendizaje profundo.

## <a name="prerequisites"></a>Requisitos previos
Antes de poder crear una Microsoft Data Science Virtual Machine, debe tener lo siguiente:

* **Una suscripción a Azure**: para conseguir una, vea [Obtención de una evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Una cuenta de Azure Storage**: consulte la sección sobre [creación de una cuenta de Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account) para crear una. Como alternativa, la cuenta de almacenamiento puede crearse como parte del proceso de creación de la máquina virtual si no quiere usar una cuenta existente.

## <a name="create-your-microsoft-data-science-virtual-machine"></a>Creación de su Microsoft Data Science Virtual Machine
Estos son los pasos para crear una instancia de Microsoft Data Science Virtual Machine:

1. Navegue a la lista de máquinas virtuales en [Azure Portal](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2. Seleccione el botón **Crear** ubicado en la parte inferior para acceder a un asistente.![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. El asistente que se usó para crear la instancia de Microsoft Data Science Virtual Machine necesita **datos de entrada** para cada uno de los **cinco pasos** que se enumeran en la parte derecha de esta ilustración. Estas son las entradas necesarias para configurar cada uno de estos pasos:
   
   1. **Aspectos básicos**
      
      1. **Nombre**: nombre del servidor de ciencia de datos que está creando.
      2. **Nombre de usuario**: identificador de inicio de sesión de la cuenta del administrador.
      3. **Contraseña**: contraseña de la cuenta del administrador.
      4. **Suscripción**: si tiene más de una suscripción, seleccione aquella en la que se creará y facturará la máquina.
      5. **Grupo de recursos**: puede crear uno nuevo o usar un grupo que ya exista.
      6. **Ubicación**: seleccione el centro de datos más adecuado. Normalmente es el centro de datos que tenga la mayoría de los datos o que esté más cercano a su ubicación física para un acceso más rápido a la red.
   2. **Tamaño**: seleccione uno de los tipos de servidor que cumpla sus requisitos funcionales y las restricciones de costo. Para obtener más opciones de tamaños de máquina virtual, seleccione "Ver todo".
   3. **Configuración**:
      
      1. **Tipo de disco**: elija Premium si prefiere una unidad de estado sólido (SSD), de lo contrario elija "Estándar".
      2. **Cuenta de almacenamiento**: puede crear una nueva cuenta de Azure Storage en su suscripción o usar una existente en la misma *Ubicación* que ha elegido en el paso **Aspectos básicos** del asistente.
      3. **Otros parámetros**: normalmente usará simplemente los valores predeterminados. Puede mover el puntero sobre el vínculo informativo para obtener ayuda sobre los campos específicos en caso de que desee considerar el uso de valores no predeterminados.
   4. **Resumen**: Compruebe que toda la información que ha especificado es correcta.
   5. **Compra**: haga clic en **Comprar** para iniciar el aprovisionamiento. Se proporciona un vínculo a los términos de la transacción. La máquina virtual no tiene ningún cargo adicional más allá del proceso para el tamaño del servidor que eligió en el paso **Tamaño** . 

> [!NOTE]
> El aprovisionamiento tardará entre 10 y 20 minutos. El estado del aprovisionamiento se muestra en Azure Portal.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Acceso a Microsoft Data Science Virtual Machine
Una vez creada la máquina virtual, puede usar el escritorio remoto con las credenciales de la cuenta del administrador que configuró en la sección **Aspectos básicos** anterior. 

Una vez creada y aprovisionada la máquina virtual, está listo para comenzar a usar las herramientas que se instalan y configuran en ella. Hay iconos del menú de inicio e iconos del escritorio para muchas de las herramientas. 

## <a name="how-to-create-a-strong-password-for-jupyter-and-start-the-notebook-server"></a>Creación de una contraseña segura para Jupyter e inicio del servidor de notebooks
De forma predeterminada, el servidor de notebooks de Jupyter está preconfigurado pero deshabilitado en la máquina virtual hasta que se establezca una contraseña para Jupyter. Para crear una contraseña segura para el servidor de cuadernos de Jupyter Notebook instalado en la máquina, ejecute el siguiente comando desde un símbolo del sistema en la máquina virtual de Data Science, o bien haga doble clic en el acceso directo que hemos proporcionado en el escritorio, llamado **Jupyter Set Password & Start** desde una cuenta de administrador de máquina virtual local.

    C:\dsvm\tools\setup\JupyterSetPasswordAndStart.cmd

Siga los mensajes y elija una contraseña segura cuando se le solicite.

El script anterior crea un hash de contraseña y lo almacena en el archivo de configuración de Jupyter ubicado en: **C:\ProgramData\jupyter\jupyter_notebook_config.py**, bajo el nombre de parámetro ***c.NotebookApp.password***.

El script también habilita y ejecuta el servidor de Jupyter en segundo plano. El servidor de Jupyter se crea como una tarea de Windows llamada **Start_IPython_Notebook**, en el programador de tareas de Windows.  Quizás tenga que esperar unos pocos segundos después de establecer la contraseña antes de abrir el notebook en el explorador. Consulte la sección titulada **Jupyter Notebook** acerca de cómo tener acceso al servidor de notebooks de Jupyter. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Herramientas instaladas en Microsoft Data Science Virtual Machine

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
Si desea usar R para su análisis, la máquina virtual tiene Microsoft R Server Developer Edition instalado. Microsoft R Server es una plataforma de análisis de nivel empresarial que se puede implementar ampliamente gracias a que R es compatible, escalable y seguro. Compatible con una gran variedad de estadísticas de macrodatos, con funcionalidades de modelado de predicción y de aprendizaje automático, R Server admite toda la gama de análisis: exploración, análisis, visualización y modelado. Al usar y ampliar R de código abierto, Microsoft R Server es totalmente compatible con scripts, funciones y paquetes CRAN de R, a fin de analizar datos a escala empresarial. También resuelve las limitaciones en memoria de R de origen abierto agregando el procesamiento paralelo y fragmentado de datos. Esto permite ejecutar análisis de datos mucho mayor de lo que cabe en la memoria principal.  La edición Visual Studio Community Edition incluida en la máquina virtual contiene la extensión R Tools for Visual Studio que proporciona un IDE completo para trabajar con R. También puede descargar y usar otros IDE, así como [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Para el desarrollo con Python, se ha instalado Anaconda Python Distribution 2.7 y 3.5. Esta distribución contiene Python base, junto con aproximadamente 300 de los paquetes de matemáticas, ingeniería y análisis de datos más populares. Puede usar Herramientas de Python para Visual Studio (PTVS) que se instala en la edición de Visual Studio 2015 Community o uno de los IDE incluidos con Anaconda, como IDLE o Spyder. Para iniciar uno de ellos, busque en la barra de búsqueda (tecla **Win** + **S**).

> [!NOTE]
> Para que Python Tools for Visual Studio apunte a Anaconda Python 2.7 y 3.5, tiene que crear entornos personalizados para cada versión. Para establecer estas rutas de entorno en Visual Studio 2015 Community Edition, vaya a **Herramientas** -> **Herramientas de Python** -> **Entornos de Python** y haga clic en **+ Personalizar**. 
> 
> 

Anaconda Python 2.7 se instala en C:\Anaconda y Anaconda Python 3.5 se instala en c:\Anaconda\envs\py35. Consulte la [documentación de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para ver los pasos detallados. 

### <a name="jupyter-notebook"></a>Jupyter Notebook
La distribución de Anaconda también incluye una instancia de Jupyter Notebook, un entorno para compartir código y análisis. Previamente se ha configurado un servidor de notebooks de Jupyter con kernels de Python 2.7, Python 3.4, Python 3.5 y R. Hay un icono del escritorio llamado "Jupyter Notebook" para iniciar el explorador y tener acceso al servidor Notebook. Si está en la máquina virtual a través de un escritorio remoto, también puede visitar [https://localhost:9999/](https://localhost:9999/) para acceder al servidor de Jupyter Notebook cuando inicie sesión en la máquina virtual.

> [!NOTE]
> Continúe aunque aparezca una advertencia de certificado. 
> 
> 

Hemos empaquetado varios notebooks de ejemplo en Python y en R. Los notebooks de Jupyter muestran cómo trabajar con Microsoft R Server, SQL Server 2016 R Services (análisis en bases de datos), Python, Microsoft Cognitive ToolKit para un aprendizaje en profundidad y otras tecnologías de Azure una vez que inicie sesión en Jupyter. Puede ver el vínculo a los ejemplos en la página de inicio del notebook después de que se autentique en Jupyter Notebook con la contraseña creada en el paso anterior. 

### <a name="visual-studio-2015-community-edition"></a>Visual Studio 2015 Community Edition
Visual Studio Community Edition instalado en la máquina virtual. Se trata de una versión gratuita del popular IDE de Microsoft que puede usar para fines de evaluación y para equipos pequeños. Puede revisar los términos de licencia [aquí](https://www.visualstudio.com/support/legal/mt171547).  Haga doble clic en el icono del escritorio o en el menú **Inicio** para abrir Visual Studio. Para buscar programas, también puede usar **Win** + **S** y escribir "Visual Studio". Una vez ahí, puede crear proyectos en lenguajes como C#, Python, R o node.js. También encontrará complementos instalados que resultan prácticos para trabajar con servicios de Azure como Azure Data Catalog, Azure HDInsight (Hadoop, Spark) y Azure Data Lake. 

> [!NOTE]
> Quizás vea un mensaje que indica que el período de evaluación ha expirado. Escriba las credenciales de la cuenta Microsoft o cree una nueva cuenta gratuita para obtener acceso a Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition
La máquina virtual incluye la versión para desarrolladores de SQL Server 2016 con R Services para ejecutar análisis en bases de datos. R Services proporciona una plataforma para desarrollar e implementar aplicaciones inteligentes. Puede usar el completo y eficaz lenguaje R y los numerosos paquetes de la comunidad para crear modelos y generar predicciones con sus datos de SQL Server. Puede mantener análisis cerca de los datos porque los servicios de R (en bases de datos) integran el lenguaje R con SQL Server. Esto elimina los costos y riesgos de seguridad asociados con el movimiento de datos.

> [!NOTE]
> La edición para desarrolladores de SQL Server 2016 solo puede utilizarse para fines de desarrollo y prueba. Para ejecutarlo en producción necesita una licencia. 
> 
> 

Para tener acceso a SQL Server, inicie **SQL Server Management Studio**. El nombre de la máquina virtual se rellenará como Nombre del servidor. Use Autenticación de Windows cuando inicie sesión como administrador en Windows. Una vez que esté en SQL Server Management Studio, puede crear otros usuarios, crear bases de datos, importar datos y ejecutar consultas SQL. 

Para habilitar el análisis en bases de datos con Microsoft R, ejecute el siguiente comando como una acción puntual en SQL Server Management Studio después de iniciar sesión como administrador del servidor. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Las tablas de Azure
En la VM se instalan varias herramientas de Azure:

* Hay un acceso directo del escritorio para tener acceso a la documentación del SDK de Azure. 
* **AzCopy**: se usa para trasladar datos hacia y desde la cuenta de Microsoft Azure Storage. Escriba **Azcopy** en el símbolo del sistema para ver su uso. 
* **Explorador de Microsoft Azure Storage**: se usa para explorar los objetos que almacenó en la cuenta de Azure Storage y para transferir los datos hacia y desde Azure Storage. Puede escribir **Explorador de Storage** en la búsqueda o buscarlo en el menú Inicio de Windows para acceder a esta herramienta. 
* **Adlcopy**: se usa para trasladar datos a Azure Data Lake. Para ver su uso escriba **adlcopy** en el símbolo del sistema. 
* **dtui**: se usa para trasladar datos hacia y desde Azure Cosmos DB, una base de datos NoSQL en la nube. Escriba **dtui** en el símbolo del sistema. 
* **Microsoft Data Management Gateway**: permite el traslado de datos entre orígenes de datos locales y en la nube. Se usa en herramientas como Azure Data Factory. 
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
| Elemento | Directorio |
| --- | --- |
| Configuraciones del servidor de notebooks de Jupyter |C:\ProgramData\jupyter |
| Directorio principal de ejemplos de notebooks de Jupyter |c:\dsvm\notebooks |
| Otros ejemplos |c:\dsvm\samples |
| Anaconda (predeterminado: Python 2.7) |c:\Anaconda |
| Entorno Anaconda Python 3.5 |c:\Anaconda\envs\py35 |
| Directorio de la instancia de R Server Standalone (instancia predeterminada de R basada en R3.2.2) |C:\Archivos de programa\Microsoft SQL Server\130\R_SERVER |
| Directorio de la instancia en base de datos de R Server (R3.2.2) |C:\Archivos de programa\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES |
| Directorio de la instancia de Microsoft R Open (R3.3.1) |C:\Archivos de programa\Microsoft\MRO-3.3.1 |
| Herramientas varias |c:\dsvm\tools |

> [!NOTE]
> Las instancias de Microsoft Data Science Virtual Machine creadas antes de la versión 1.5.0 (antes del 3 de septiembre de 2016) usan una estructura de directorios ligeramente diferente de la especificada en la tabla anterior. 
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Estos son algunos pasos para proseguir con el aprendizaje y la exploración. 

* Explore las diversas herramientas de Data Science Virtual Machine haciendo clic en el menú Inicio y comprobando las herramientas incluidas en el menú.
* Vaya a **C:\Archivos de programa\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** para ver ejemplos de uso de la biblioteca RevoScaleR de R que admite análisis de datos a escala empresarial.  
* Lea el artículo [Diez cosas que puede hacer en Data Science Virtual Machine](http://aka.ms/dsvmtenthings)
* Aprenda a crear soluciones analíticas completas mediante el uso sistemático del [Proceso de ciencia de los datos en equipos (TDSP)](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visite [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com) para ver ejemplos de aprendizaje automático y de análisis de datos usando Cortana Intelligence Suite. También hemos proporcionado un icono en el menú **Inicio** y en el escritorio de la máquina virtual para el acceso a esta galería.

