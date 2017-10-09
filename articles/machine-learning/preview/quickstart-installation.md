---
title: "Guía de inicio rápido de instalación de servicios de Azure Machine Learning | Microsoft Docs"
description: "En esta guía de inicio rápido se muestra cómo crear recursos de Azure Machine Learning y cómo instalar Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 91d2f47a528050f644973044f96c0354b91dba25
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Crear cuentas de versión preliminar de Azure Machine Learning e instalar Azure Machine Learning Workbench
Azure Machine Learning es una solución integrada y completa de análisis avanzado y ciencia de datos diseñada para que los científicos de datos profesionales preparen datos, desarrollen experimentos e implementen modelos a escala de nube.

Esta guía de inicio rápido muestra cómo crear cuentas de administración de modelos y experimentación en los servicios de Azure Machine Learning (versión preliminar). También muestra cómo instalar las herramientas de CLI y la aplicación de escritorio de Azure Machine Learning Workbench. A continuación, explorará rápidamente las características de Azure Machine Learning (versión preliminar) mediante el [conjunto de datos de Iris ](https://en.wikipedia.org/wiki/iris_flower_data_set) atemporal para generar un modelo para predecir el tipo de iris en función de sus características físicas.  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
Actualmente, Azure Machine Learning Workbench solo se puede instalar en los siguientes sistemas operativos: Windows 10, Windows Server 2016 y macOS Sierra.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-azure-machine-learning-accounts"></a>Creación de cuentas de Azure Machine Learning
Use Azure Portal para aprovisionar cuentas de Azure Machine Learning. 
1. Seleccione el botón **Nuevo** (+) de la esquina superior izquierda del portal.

2. Escriba "Machine Learning" en la barra de búsqueda. Seleccione el resultado de búsqueda denominado **Experimentación de Machine Learning (versión preliminar)** .  Haga clic en el icono de estrella para marcar la selección como favorita en Azure Portal.

   ![Búsqueda de Azure Machine Learning](media/quickstart-installation/portal-more-services.png)

3. Haga clic en **+ Agregar** para configurar una nueva cuenta de Experimentación de Machine Learning. Se abrirá un formulario detallado.

   ![Cuenta de Experimentación de Machine Learning](media/quickstart-installation/portal-create-experimentation.png)

4. Complete el formulario de Experimentación de Machine Learning con la siguiente información:

   Configuración|Valor sugerido|Descripción
   ---|---|---
   Nombre de cuenta de Experimentación | _Nombre único_ |Elija un nombre único que identifique la cuenta. Puede usar su propio nombre o un nombre de departamento o proyecto que identifique el experimento. El nombre debe tener entre 2 y 32 caracteres y solo puede incluir caracteres alfanuméricos y el carácter "-". 
   La suscripción | _Su suscripción_ |La suscripción de Azure que quiera usar para el experimento. Si tiene varias suscripciones, elija la suscripción en la que se factura el recurso.
   Grupo de recursos | _El grupo de recursos_ | Puede crear un nuevo nombre de grupo de recursos o usar uno existente de la suscripción.
   Ubicación | _Región más cercana a los usuarios_ | Elija la ubicación más cercana a los usuarios y los recursos de datos.
   Número de puestos | 2 | Escriba el número de puestos. Esta selección afecta a los [precios](https://azure.microsoft.com/pricing/details/machine-learning/). Los dos primeros puestos son gratuitos. Para esta guía de inicio rápido, use dos puestos. Puede actualizar el número de puestos posteriormente según sea necesario en Azure Portal.
   Cuenta de almacenamiento | _Nombre único_ | Elija  **	Crear nueva** y proporcione un nombre para crear una nueva cuenta de almacenamiento de Azure, o elija **Usar existente** y seleccione la cuenta de almacenamiento existente en la lista desplegable. La cuenta de almacenamiento es necesaria y se utiliza para almacenar los artefactos de proyecto y ejecutar datos de historial. 
   Cuenta de Workspace para Experimentación | _Nombre único_ | Proporcione un nombre para la nueva área de trabajo. El nombre debe tener entre 2 y 32 caracteres y solo puede incluir caracteres alfanuméricos y el carácter "-".
   Asignar propietario para el área de trabajo | _Su cuenta_ | Seleccione su propia cuenta como propietario del área de trabajo.
   Crear una cuenta de Administración de modelos | *check* | Como parte de la experiencia de creación de la cuenta de Experimentación, también puede crear la cuenta de Administración de modelos de Machine Learning. Este recurso se usa cuando está listo para implementar y administrar modelos como servicios web en tiempo real. Se recomienda crear la cuenta de Administración de modelos al mismo tiempo que la cuenta de Experimentación.
   Nombre de cuenta | _Nombre único_ | Elija un nombre único que identifique la cuenta de Administración de modelos. Puede usar su propio nombre o un nombre de departamento o proyecto que identifique el experimento. El nombre debe tener entre 2 y 32 caracteres y solo puede incluir caracteres alfanuméricos y el carácter "-". 
   Plan de tarifa de Administración de modelos | **DEVTEST** | Haga clic en **No se ha seleccionado ningún plan de tarifa** para especificar el plan de tarifa para la nueva cuenta de Administración de modelos. Para ahorrar costos, seleccione el plan de tarifa **DEVTEST** (disponibilidad limitada), si está disponible para su suscripción. En caso contrario, seleccione el plan de tarifa S1 para ahorrar costos. Haga clic en **Seleccionar** para guardar este plan de tarifa. 
   Anclar al panel | _check_ | Active la opción **Anclar al panel** para permitir realizar un seguimiento fácil de la cuenta de Experimentación de Machine Learning en la página del panel frontal de Azure Portal.

5. Haga clic en **Crear** para iniciar el proceso de creación.

6. En la esquina superior derecha de la barra de herramientas de Azure Portal, haga clic en **Notificaciones** (icono de campana) para supervisar el proceso de implementación. 

   La notificación muestra el mensaje "Implementación en curso...". Una vez finalizada, el estado cambia a "Implementación correcta". Una vez finalizada correctamente la implementación, se abre la página de la cuenta de Experimentación de Machine Learning.
   
   ![Notificaciones de Azure Portal](media/quickstart-installation/portal-notification.png)

Ahora, en función del sistema operativo que use en el equipo local, siga una de las dos secciones siguientes para instalar Azure Machine Learning Workbench en el equipo. 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Instalar Azure Machine Learning Workbench en Windows
Instale Azure Machine Learning Workbench en el equipo que ejecuta Windows 10, Windows Server 2016 o una versión posterior.

1. Descargue el instalador más reciente de Azure Machine Learning Workbench **[AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)**.

2. Haga doble clic en el instalador descargado _AmlWorkbenchSetup.msi_ desde el Explorador de archivos.

   >[!IMPORTANT]
   >Descargue el instalador por completo en el disco y, a continuación, ábralo desde allí. No lo inicie directamente desde el widget de descarga del explorador.

3. Siga las instrucciones en pantalla para finalizar la instalación.

   El instalador descarga todos los componentes dependientes necesarios, como Python, Miniconda y otras bibliotecas relacionadas. La instalación puede tardar, aproximadamente, media hora en completar todos los componentes. 

4. Ahora, Azure Machine Learning Workbench ya está instalado en el directorio siguiente:
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>Instalación de Azure Machine Learning Workbench en macOS
Instale Azure Machine Learning Workbench en el equipo que ejecuta macOS Sierra.

1. Instale la biblioteca openssl mediante [Homebrew](http://brew.sh). Vea [Requisitos previos para .NET Core en Mac](https://docs.microsoft.com/dotnet/core/macos-prerequisites) para obtener más detalles.
   ```
   # install Homebrew first if you don't have it already
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   # install latest openssl needed for .NET Core 1.x
   brew update
   brew install openssl
   mkdir -p /usr/local/lib
   ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
   ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
   ```

2. Descargue el instalador más reciente de Azure Machine Learning Workbench **[AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg)**.

   >[!IMPORTANT]
   >Descargue el instalador por completo en el disco y, a continuación, ábralo desde allí. No lo inicie directamente desde el widget de descarga del explorador.

3. Haga doble clic en el instalador descargado _AmlWorkbench.dmg_ desde Finder.

4. Siga las instrucciones en pantalla para finalizar la instalación.

   El instalador descarga todos los componentes dependientes necesarios, como Python, Miniconda y otras bibliotecas relacionadas. La instalación puede tardar, aproximadamente, media hora en completar todos los componentes. 

5. Ahora, Azure Machine Learning Workbench ya está instalado en el directorio siguiente: 

   _/Applications/AmlWorkbench.app_

## <a name="run-azure-machine-learning-workbench-to-log-in-the-first-time"></a>Ejecutar Azure Machine Learning Workbench para iniciar sesión por primera vez
1. Haga clic en el botón **Launch Workbench** (Iniciar Workbench) de la última pantalla del instalador una vez finalizado el proceso de instalación. Si ha cerrado el instalador, busque el acceso directo a Machine Learning Workbench en el escritorio y abra el menú **Azure Machine Learning Workbench** para iniciar la aplicación.

2. Inicie sesión en Workbench con la misma cuenta que usó anteriormente para aprovisionar los recursos de Azure. 

3. Una vez completado correctamente el proceso de inicio de sesión, Workbench intenta encontrar las cuentas de Experimentación de Machine Learning que creó previamente. Busca todas las suscripciones de Azure a las que tiene acceso con sus credenciales. Cuando se encuentra al menos una cuenta de Experimentación, Workbench se abre con esa cuenta. A continuación, se enumeran las áreas de trabajo y los proyectos que se encuentran en esa cuenta. 

   >[!TIP]
   > Si tiene acceso a más de una cuenta de Experimentación, para cambiar de cuenta, haga clic en el icono de avatar de la esquina inferior izquierda de la aplicación Workbench.

Consulte el [programa de instalación del entorno de implementación](deployment-setup-configuration.md) para crear un entorno para la implementación de los servicios web.

## <a name="create-a-new-project"></a>Crear un nuevo proyecto
1. Inicie la aplicación Azure ML Workbench e inicie sesión. 

2. Haga clic en **Archivo** --> **Nuevo proyecto** (o haga clic en el signo  **+** del panel **PROYECTOS**). 

3. Complete los campos **Nombre de proyecto** y **Directorio del proyecto**. La **Descripción del proyecto** es opcional, pero resulta útil. Deje el campo **Visualstudio.com GIT Repository URL** (Dirección URL del repositorio de GIT de Visualstudio.com) en blanco por ahora. Elija un área de trabajo y seleccione **Classifying Iris** (Clasificación de Iris) como plantilla de proyecto.

   >[!TIP]
   >Si lo desea, puede rellenar el campo de texto de repositorio de GIT con la dirección URL de un repositorio de GIT hospedado en un proyecto de [VSTS Visual Studio Team Services](https://www.visualstudio.com). Este repositorio de GIT ya debe existir y debe estar vacío sin bifurcación principal. Además, debe tener acceso de escritura a él. Agregar un repositorio de GIT le permite habilitar la movilidad y compartir escenarios más adelante. [Más información](using-git-ml-project.md).

4. Haga clic en el botón **Crear** para crear el proyecto. El proyecto se creará y se abrirá automáticamente. En este momento, ya puede explorar la página principal de proyecto, los orígenes de datos, los cuadernos y los archivos de código fuente. 

    >[!TIP]
    >También puede abrir el proyecto en VS Code u otros editores. Para hacerlo, configure un vínculo de IDE (entorno de desarrollo integrado) y, a continuación, abra el directorio del proyecto desde este. [Más información](how-to-configure-your-IDE.md). 

## <a name="run-a-python-script"></a>Ejecutar un script de Python
A continuación, ejecutaremos un script en el equipo local. 

1. Cada proyecto se abre en su propia página **Panel del proyecto**. Seleccione `local` como destino de ejecución en la barra de comandos de la parte superior de la aplicación, a la izquierda del botón de ejecución, y `iris_sklearn.py` como script que se ejecutará.  Hay una serie de otros archivos que se incluyen en el ejemplo que puede extraer del repositorio más adelante. 

   ![img](media/quickstart-installation/run_control.png)

2. En el campo de texto **Argumentos**, escriba `0.01`. Este número se usa en el código para establecer la velocidad de regularización, un valor que se usa para configurar cómo se entrena el modelo de regresión lineal. 

3. Haga clic en el botón **Ejecutar** para empezar a ejecutar `iris_sklearn.py` en el equipo. 

   Este código usa el algoritmo de [regresión logística](https://en.wikipedia.org/wiki/logistic_regression) de la popular biblioteca de Python [scikit-learn](http://scikit-learn.org/stable/index.html) para generar el modelo.

4. Si aún no está visible, el panel **Trabajos** aparece a la derecha y contiene un trabajo `iris_sklearn`. Su estado cambia de **Enviando** a **En ejecución** cuando el trabajo empieza a ejecutarse y, transcurridos unos segundos, a **Completado**. 

5. ¡Enhorabuena! Ha ejecutado correctamente un script de Python en Azure ML Workbench.

6. Repita los pasos 2 a 4 varias veces. Cada vez, utilice valores de argumentos diferentes entre `10` y `0.001`.

## <a name="view-run-history"></a>Ver historial de ejecución
1. Navegue hasta la vista **Ejecuciones** y haga clic en **iris_sklearn.py** en la lista de ejecuciones. El panel del historial de ejecución para `iris_sklearn.py` se abre. Muestra cada ejecución que se realizó en `iris_sklearn.py`. 

   ![img](media/quickstart-installation/run_view.png)

2. El panel del historial de ejecución también muestra las métricas principales, un conjunto de grafos predeterminados y una lista de métricas para cada ejecución. Para personalizar esta vista, puede ordenar, filtrar y ajustar las configuraciones. Para ello, haga clic en el icono de configuración o de filtro.

   ![img](media/quickstart-installation/run_dashboard.png)

3. Haga clic en una ejecución completada para obtener una vista detallada de la ejecución específica, incluidas otras métricas, los archivos que se generaron y otros registros potencialmente útiles.

## <a name="next-steps"></a>Pasos siguientes
Ya ha creado correctamente una cuenta de Experimentación de Azure Machine Learning y una cuenta de Administración de modelos de Azure Machine Learning. Ha instalado la aplicación de escritorio Azure Machine Learning Workbench y la interfaz de línea de comandos. Ha creado un nuevo proyecto, ejecutado un script para crear un modelo y explorado el historial de ejecución del script.

Para obtener una experiencia más detallada de este flujo de trabajo, incluida la forma de implementar el modelo Iris como servicio web, siga el completo tutorial de clasificación de Iris, que contiene los pasos detallados para la [preparación de datos](tutorial-classifying-iris-part-1.md), [ experimentación](tutorial-classifying-iris-part-2.md) y [administración de modelos](tutorial-classifying-iris-part-3.md). 

> [!div class="nextstepaction"]
> [Tutorial de clasificación de Iris](tutorial-classifying-iris-part-1.md)

