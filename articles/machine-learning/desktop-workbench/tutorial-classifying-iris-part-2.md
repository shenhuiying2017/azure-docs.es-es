---
title: Tutorial para compilar un modelo para servicios de Azure Machine Learning (versión preliminar) | Microsoft Docs
description: A lo largo de este tutorial se muestra cómo puede aprovechar al máximo Azure Machine Learning Services (versión preliminar). Esta es la segunda parte y explica la experimentación.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: b0d268937300abd105349d340f7bf1dae398fa8f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>Tutorial 2: Clasificación de Iris - Creación de un modelo
Los servicios de Azure Machine Learning (versión preliminar) son una solución de análisis avanzado y ciencia de datos integrada que permite a los científicos de datos profesionales preparar datos, desarrollar experimentos e implementar modelos a escala de la nube.

Este tutorial es la **segunda de una serie de tres partes**. Aquí se usarán los servicios de Azure Machine Learning para:

> [!div class="checklist"]
> * Abrir scripts y revisar el código.
> * Ejecutar scripts en un entorno local.
> * Revisar los historiales de ejecución.
> * Ejecutar scripts en una ventana de CLI de Azure local.
> * Ejecutar scripts en un entorno de Docker local.
> * Ejecutar scripts en un entorno de Docker remoto.
> * Ejecutar scripts en un entorno de Azure HDInsight en la nube.

Este tutorial usa el [conjunto de datos Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. 

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesita:
- Una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 
- Una cuenta de experimentación y Azure Machine Learning Workbench instalado tal como se describe en este [inicio rápido](../service/quickstart-installation.md)
- El proyecto y los datos de Iris preparados del [Tutorial parte 1](tutorial-classifying-iris-part-1.md).
- Un motor de Docker instalado y que se ejecute localmente. Docker Community Edition es suficiente. Más información sobre cómo instalar Docker: https://docs.docker.com/engine/installation/.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Revisión de iris_sklearn.py y los archivos de configuración

1. Inicie la aplicación Azure Machine Learning Workbench.

1. Abra el proyecto **myIris** que ha creado en la [Parte 1 de la serie de tutoriales](tutorial-classifying-iris-part-1.md).

2. En el proyecto abierto, haga clic en el botón **Archivos** (el icono de la carpeta) del panel izquierdo para abrir la lista de archivos de la carpeta del proyecto.

   ![Abrir el proyecto de Azure Machine Learning Workbench](media/tutorial-classifying-iris/2-project-open.png)

3. Seleccione el archivo de script de Python **iris_sklearn.py**. 

   ![Elección de un script](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   El código se abre en una nueva pestaña del editor de texto de Workbench. Se trata del script que se utiliza a lo largo de esta parte del tutorial. 

   >[!NOTE]
   >Es posible que el código que ve no sea exactamente el mismo que el anterior, ya que este proyecto de ejemplo se actualiza con frecuencia.
   
   ![Abrir un archivo](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Inspeccione el código de script de Python para familiarizarse con el estilo de codificación. 

   El script **iris_sklearn.py** realiza las siguientes tareas:

   * Carga el paquete de preparación de datos predeterminado **iris.dprep** para crear un objeto [pandas.DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

   * Agrega características aleatorias para que el problema sea más difícil de resolver. Dicha aleatoriedad es necesaria porque Iris es un conjunto de datos pequeño que se clasifica fácilmente con casi un 100 % de precisión.

   * Usa la biblioteca de Machine Learning [scikit-learn](http://scikit-learn.org/stable/index.html) para compilar un modelo de regresión logística.  Esta biblioteca incluye Azure Machine Learning Workbench de forma predeterminada.

   * Serializa el modelo mediante el uso de la biblioteca [pickle](https://docs.python.org/3/library/pickle.html) en un archivo de la carpeta `outputs`. 
   
   * Carga el modelo serializado y, después, lo deserializa en la memoria.

   * Usa el modelo deserializado para realizar una predicción en un nuevo registro. 

   * Traza dos gráficos, una matriz de confusión y una curva ROC (característica operativa del receptor) de varias clases, que usa la biblioteca [matplotlib](https://matplotlib.org/), y, después, los guarda en la carpeta `outputs`. Puede instalar esta biblioteca en su entorno si aún no existe.

   * Traza automáticamente la tasa de regularización y la precisión del modelo en el historial de ejecución. El objeto `run_logger` se utiliza en todo el proceso para registrar la tasa de regularización y la precisión del modelo en los registros. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>Ejecución de iris_sklearn.py en el entorno local

1. Inicie la interfaz de la línea de comandos (CLI) de Azure Machine Learning:
   1. Inicie Azure Machine Learning Workbench.

   1. En el menú de Workbench, seleccione **Archivo** > **Open Command Prompt** (Abrir símbolo del sistema). 
   
   La ventana de la interfaz de la línea de comandos (CLI) de Azure Machine Learning se inicia en la carpeta del proyecto `C:\Temp\myIris\>` en Windows. Este proyecto es el mismo que el que creó en la parte 1 del tutorial.

   >[!IMPORTANT]
   >Debe usar esta ventana de la CLI para realizar los pasos siguientes.

1. En la ventana de la CLI, instale la biblioteca de trazado de Python, **matplotlib**, si aún no dispone de la biblioteca.

   El script **iris_sklearn.py** tiene dependencias en dos paquetes de Python: **scikit-learn** y **matplotlib**.  Azure Machine Learning Workbench instala el paquete **scikit-learn** para su comodidad. No obstante, debe instalar **matplotlib** si aún no lo tiene instalado.

   Si prosigue sin haber instalado **matplotlib**, el código de este tutorial puede seguir ejecutándose correctamente. Sin embargo, el código no puede generar la salida de la matriz de confusión ni los trazados de curva ROC de varias clases, tal como se muestra en las visualizaciones del historial.

   ```azurecli
   pip install matplotlib
   ```

   Esta instalación tarda aproximadamente un minuto.

1. Vuelva a la aplicación Workbench. 

1. Busque la pestaña denominada **iris_sklearn.py**. 

   ![Búsqueda de la pestaña con el script](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. En la barra de herramientas de esta pestaña, seleccione **local**, como entorno de ejecución, y `iris_sklearn.py` como el script que se va a ejecutar. Es posible que estos ya estén seleccionados.

   ![Elección de local y del script](media/tutorial-classifying-iris/2-local-script.png)

1. A continuación, vaya a la derecha de la barra de herramientas y escriba `0.01` en el campo **Argumentos**. 

   Este valor corresponde a la tasa de regularización del modelo de regresión logística.

   ![Elección de local y del script](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. Haga clic en el botón **Ejecutar**. Se programará un trabajo de forma inmediata. El trabajo se muestra en el panel **Trabajos** de la derecha de la ventana de Workbench. 

   ![Elección de local y del script](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   Poco después, el estado del trabajo pasa de **Submitting** (Enviando) a **Running** (En ejecución) y luego a **Completed** (Completado).

1. Seleccione **Completado** en el texto de estado del trabajo en el panel **Trabajos**. 

   ![Ejecutar sklearn](media/tutorial-classifying-iris/2-completed.png)

   Se abre una ventana emergente que muestra el texto de salida estándar (stdout) del script de la ejecución. Para cerrar el texto stdout, haga clic en el botón **Cerrar** (**x**) de la esquina superior derecha de la ventana emergente.

   ![Salida estándar](media/tutorial-classifying-iris/2-standard-output.png)

9. En el mismo estado del trabajo en el panel **Jobs** (Trabajos), seleccione el texto azul **iris_sklearn.py [n]** (_n_ es el número de ejecución) que está inmediatamente encima del estado **Completed** (Completado) y la hora de inicio. Se abre la ventana **Run Properties** (Propiedades de ejecución), que muestra la siguiente información de esa ejecución concreta:
   - Información de **Run Properties** (Propiedades de ejecución)
   - **Outputs**
   - **Métricas**
   - **Visualizaciones**, en caso de haber
   - **Registros** 

   Cuando la ejecución finaliza, la ventana emergente muestra los resultados siguientes:

   >[!NOTE]
   >Al haber introducido el tutorial cierta aleatoriedad en el conjunto de aprendizaje anterior, los resultados exactos pueden variar con respecto a los que se muestran aquí.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```
    
10. Cierre la pestaña **Run Properties** (Propiedades de ejecución) y vuelve a la pestaña **iris_sklearn.py**. 

11. Repita las ejecuciones adicionales. 

    Escriba una serie de valores en el campo **Arguments** (Argumentos) que oscila entre `0.001` y `10`. Seleccione **Ejecutar** para ejecutar el código varias veces. El valor del argumento que cambia cada vez se introduce en el modelo de regresión logística del código, lo que produce resultados diferentes cada vez.

## <a name="review-the-run-history-in-detail"></a>Detalles de la revisión del historial
En Azure Machine Learning Workbench, cada ejecución de script se captura como un registro del historial de ejecución. Si abre la vista **Ejecuciones**, puede ver el historial de ejecuciones de un script concreto.

1. Para ello, haga clic en el botón **Ejecuciones** (icono del reloj) de la barra de herramientas izquierda. Luego, haga clic en **iris_sklearn.py** para mostrar el **panel de ejecuciones** de `iris_sklearn.py`.

   ![Vista de ejecución](media/tutorial-classifying-iris/run_view.png)

1. Se abrirá la pestaña **Run Dashboard** (Panel de ejecución). 

   Consulte las estadísticas que se han capturado durante varias ejecuciones. Los gráficos se representan en la parte superior de la pestaña. Cada ejecución tiene un número consecutivo y sus detalles se muestran en la tabla de la parte inferior de la pantalla.

   ![Panel de ejecución](media/tutorial-classifying-iris/run_dashboard.png)

1. Filtre la tabla y seleccione cualquiera de los gráficos para ver el estado, la duración, la precisión y la tasa de regularización de cada ejecución. 

1. Seleccione las casillas junto a dos o más ejecuciones en la tabla **Ejecuciones**. Seleccione el botón **Comparar** para abrir un panel de comparación detallado. Revise la comparación en paralelo. 

1. Para volver a **Run Dashboard** (Panel de ejecución), seleccione el botón para volver **Lista de ejecuciones** en la parte superior izquierda del panel **Comparison** (Comparación).

   ![Volver a Lista de ejecuciones](media/tutorial-classifying-iris/2-compare-back.png)

1. Seleccione una ejecución individual para ver la vista de detalles de la misma. Observe que las estadísticas de la ejecución seleccionada se enumeran en la sección **Run Properties** (Propiedades de ejecución). Los archivos escritos en la carpeta de salida se enumeran en sección **Salidas**, de donde se pueden descargar.

   ![Detalles de ejecución](media/tutorial-classifying-iris/run_details.png)

   Los dos trazados, la matriz de confusión y la curva ROC de varias clases, se representan en la sección **Visualizaciones**. Todos los archivos de registro también pueden encontrarse en la sección **Registros**.


## <a name="run-scripts-in-local-docker-environments"></a>Ejecución de scripts en entornos de Docker locales

Si lo desea, puede experimentar con la ejecución de scripts en un contenedor de Docker local. Puede configurar entornos de ejecución adicionales, como Docker, y ejecutar su script en ellos. 

>[!NOTE]
>Si desea experimentar con la ejecución de scripts de distribución en un contenedor de Docker de una máquina virtual de Azure remota o un clúster de Azure HDInsight Spark, puede seguir las [instrucciones para crear un instancia de Azure Data Science Virtual Machine o un clúster de HDInsight](how-to-create-dsvm-hdi.md).

1. Si aún no lo ha hecho, instale e inicie Docker localmente en su máquina de Windows o MacOS. Para más información, consulte las instrucciones de instalación de Docker en https://docs.docker.com/install/. Community Edition es suficiente.

1. En el panel izquierdo, seleccione el icono de la **carpeta** para abrir la lista **Archivos** de su proyecto. Expanda la carpeta `aml_config`. 

2. Hay varios entornos preconfigurados: **docker-python**, **docker spark** y **local**. 

   Cada entorno tiene dos archivos, como `docker.compute` (tanto para **docker-python** como para **docker spark**) y `docker-python.runconfig`. Abra cada archivo y observe que algunas opciones se pueden configurar en el editor de texto.  

   Para hacer limpieza, seleccione **Cerrar** (**x**) en las pestañas de los editores de texto abiertos.

3. Ejecute el script **iris_sklearn.py** mediante el entorno **docker-python**: 

   - En la barra de herramientas izquierda, haga clic en el icono del **reloj** para abrir el panel **Ejecuciones**. Seleccione **Todas las ejecuciones**. 

   - En la parte superior de la pestaña **Todas las ejecuciones**, seleccione **docker-python** como entorno de destino, en lugar del valor predeterminado, **local**. 

   - Después, muévase hacia la derecha y seleccione el script **iris_sklearn.py** para ejecutarlo. 

   - Deje el campo **Argumentos** en blanco, ya que el script especifica un valor predeterminado. 

   - Haga clic en el botón **Ejecutar**.

4. Observe que se inicia un nuevo trabajo, que aparece en el panel **Trabajos** de la derecha de la ventana de Workbench.

   La primera vez que se ejecute en Docker, el trabajo tarda unos minutos más en finalizar. 

   En segundo plano, Azure Machine Learning Workbench compila un nuevo archivo de Docker. 
   El nuevo archivo hace referencia a la imagen de Docker base especificada en el archivo `docker.compute` y a los paquetes de Python de dependencia especificados en el archivo `conda_dependencies.yml`. 
   
   El motor Docker realiza las siguientes tareas:

    - Descarga la imagen base de Azure.
    - Instala los paquetes de Python especificados en el archivo `conda_dependencies.yml`.
    - Inicia un contenedor de Docker
    - Copia o hace referencia, en función de la configuración de la ejecución, a la copia local de la carpeta del proyecto.      
    - Ejecuta el script `iris_sklearn.py`.

   Al final, debería ver exactamente los mismos resultados que cuando el destino es el entorno **local**.

5. Ahora, vamos probar Spark. La imagen base de Docker contiene una instancia de Spark preinstalada y configurada que se puede usar para ejecutar un script de PySpark. Con esta imagen base es fácil desarrollar y probar un programa de Spark sin tener que dedicar tiempo a instalar y configurar Spark. 

   Abra el archivo `iris_spark.py` . Este script carga el archivo de datos `iris.csv` y usa el algoritmo de regresión logística de la biblioteca de aprendizaje automático de Spark para clasificar el conjunto de datos Iris. Ahora, cambie el entorno de ejecución a **docker-spark**, el script a **iris_spark.py** y vuelva a ejecutarlo. Este proceso tarda un poco más porque se tiene que crear e iniciar una sesión de Spark en el contenedor de Docker. También puede ver que la propiedad StdOut es diferente que la propiedad StdOut de `iris_spark.py`.

6. Inicie algunas ejecuciones más y experimente con distintos argumentos. 

7. Abra el archivo `iris_spark.py` para ver el modelo de regresión logística compilado mediante la biblioteca de Machine Learning de Spark. 

8. Interactúe con el panel **Trabajos**, ejecute una vista del historial y ejecute una vista de detalles de las ejecuciones en los distintos entornos de ejecución.

## <a name="run-scripts-in-the-cli-window"></a>Ejecución de scripts en la ventana de la CLI

1. Inicie la interfaz de la línea de comandos (CLI) de Azure Machine Learning:
   1. Inicie Azure Machine Learning Workbench.

   1. En el menú de Workbench, seleccione **Archivo** > **Open Command Prompt** (Abrir símbolo del sistema). 
   
   Se inicia el símbolo del sistema de la CLI en la carpeta del proyecto `C:\Temp\myIris\>` en Windows. Este es el proyecto que creó en la parte 1 del tutorial.

   >[!IMPORTANT]
   >Debe usar esta ventana de la CLI para realizar los pasos siguientes.

1. En la ventana de la CLI, inicie sesión en Azure. [Más información acerca de az login](https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   Es posible que ya haya iniciado sesión. En ese caso, puede omitir este paso.

   1. En el símbolo del sistema, escriba:
      ```azurecli
      az login
      ```

      Este comando devuelve un código que se utiliza en el explorador en https://aka.ms/devicelogin.

   1. Vaya a https://aka.ms/devicelogin en el explorador.

   1. Cuando se le solicite, escriba el código, que ha recibido en la CLI, en el explorador.

   La aplicación Workbench y CLI usan memorias caché de credenciales independientes al autenticarse con recursos de Azure. Después de iniciar sesión, no necesitará autenticarse de nuevo hasta que expire el token almacenado en caché. 

1. Si su organización tiene varias suscripciones de Azure (entorno empresarial), debe establecer la suscripción que va a utilizar. Busque su suscripción, establézcala mediante el identificador de suscripción y pruébela.

   1. Muestre las suscripciones de Azure a las que tenga acceso mediante este comando:
   
      ```azurecli
      az account list -o table
      ```

      El comando **az account list** devuelve la lista de suscripciones disponibles para su inicio de sesión. 
      Si hay más de una, identifique el valor del identificador de suscripción para la suscripción que desea usar.

   1. Establezca la suscripción de Azure que desea utilizar como cuenta predeterminada:
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      donde \<su_identificador_de_suscripción\> es el valor del identificador para la suscripción que quiere usar. No incluya los corchetes.

   1. Confirme la nueva configuración de suscripción solicitando los detalles de la suscripción actual. 

      ```azurecli
      az account show
      ```    

1. En la ventana de la CLI, instale la biblioteca de trazado de Python, **matplotlib**, si aún no dispone de la biblioteca.

   ```azurecli
   pip install matplotlib
   ```

1. En la ventana de la CLI, envíe el script **iris_sklearn.py** como experimento.

   La ejecución de iris_sklearn.py se realiza en el contexto de proceso local.

   + En Windows:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + En MacOS:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   La salida debe ser similar a:
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679
        
    ==========================================
    Serialize and deserialize using the outputs folder.
    
    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
     [ 1 37 12]
     [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. Revise el resultado. Tiene la misma salida y los mismos resultados que tenía cuando usó Workbench para ejecutar el script. 

1. En la ventana de la CLI, ejecute el script de Python, **iris_sklearn.py** de nuevo mediante un entorno de ejecución de Docker (si tiene Docker instalado en su máquina).

   + Si el contenedor está en Windows: 
     |Ejecución<br/>Environment|Comando en Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + Si el contenedor está en MacOS: 
     |Ejecución<br/>Environment|Comando en Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Vuelva a Workbench y:
   1. Seleccione el icono de carpeta en el panel izquierdo para enumerar los archivos del proyecto.
   
   1. Abra el script de Python llamado **run.py**. Este script es útil para ejecutar varias tasas de regularización en bucle. 

   ![Volver a Lista de ejecuciones](media/tutorial-classifying-iris/2-runpy.png)

1. Ejecute el experimento varias veces con estas tasas. 

   Este script inicia un `iris_sklearn.py` trabajo con una tasa de regularización de `10.0` (un número demasiado grande). Luego, el script recorta la tasa por la mitad en la siguiente ejecución, y así sucesivamente hasta que la tasa no es menor que `0.005`. 

   El script contiene el código siguiente:

   ![Volver a Lista de ejecuciones](media/tutorial-classifying-iris/2-runpy-code.png)

1. Ejecute el script **run.py** desde la línea de comandos del modo siguiente:

   ```cmd
   python run.py
   ```

   Este comando envía iris_sklearn.py varias veces con distintas tasas de regularización.

   Cuando `run.py` finaliza, se pueden ver gráficos de distintas métricas en la vista de listas del historial de ejecución del área de trabajo.

## <a name="run-scripts-in-a-remote-docker-container"></a>Ejecución de scripts en un contenedor de Docker remoto
Para ejecutar el script en un contenedor de Docker de una máquina remota de Linux, debe tener acceso a SSH (nombre de usuario y contraseña) en esa máquina remota. Además, dicha máquina debe tener instalado un motor de Docker y este debe de estar en ejecución. La manera más fácil de obtener una máquina Linux es crear en Azure una imagen de Data Science Virtual Machine (DSVM) basada en Ubuntu. Aprenda a [crear una DSVM de Ubuntu para usarla en Azure Machine Learning Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>La DSVM basada en CentOS *no* se admite.

1. Una vez creada la máquina virtual, puede asociarla como un entorno de ejecución mediante la generación de un par de archivos `.runconfig` y `.compute`. Ejecute el comando siguiente para generar los archivos: 

 Vamos a llamar al nuevo destino de proceso `myvm`.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >La dirección IP también puede ser un nombre de dominio completo (FQDN) direccionable de forma pública, como `vm-name.southcentralus.cloudapp.azure.com`. Es aconsejable agregar un FQDN a DSVM y usarlo en lugar de una dirección IP. Esta práctica es recomendable porque se puede desactivar la máquina virtual en algún momento para ahorrar. Además, la próxima vez que inicie la máquina virtual, es posible que la dirección IP haya cambiado.

   >[!NOTE]
   >Además de la autenticación mediante nombre de usuario y contraseña, puede especificar una clave privada y la frase de contraseña correspondiente (si existe) mediante las opciones `--private-key-file` y (opcionalmente) `--private-key-passphrase`.

   A continuación, prepare el destino de proceso **myvm** ejecutando este comando.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   El comando anterior construye la imagen de Docker en la máquina virtual para prepararlo para ejecutar los scripts.
   
   >[!NOTE]
   >También puede cambiar el valor de `PrepareEnvironment` en `myvm.runconfig` del valor predeterminado, `false`, a `true`. Este cambio prepara automáticamente el contenedor de Docker como parte de la primera ejecución.

2. Edite el archivo `myvm.runconfig` generado en `aml_config` y cambie el valor predeterminado del marco de `PySpark` a `Python`:

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >Aunque PySpark también deberían funcionar, resulta más eficaz usar Python si no se necesita una sesión de Spark para ejecutar el script de Python.

3. Emita el mismo comando que antes en la ventana de la CLI, pero esta vez use _myvm_ como destino para ejecutar iris_sklearn.py en un contenedor de Docker remoto:
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   El comando se ejecuta como si estuviera en un entorno `docker-python`, salvo que la ejecución se realiza en la máquina virtual Linux remota. La ventana de CLI muestra la misma información de salida.

4. Vamos a probar a usar Spark en el contenedor. Abra el Explorador de archivos. Haga una copia del archivo `myvm.runconfig` y denomínelo `myvm-spark.runconfig`. Edite el nuevo archivo para cambiar la configuración `Framework` de `Python` a `PySpark`:
   ```yaml
   Framework: PySpark
   ```
   No realice ningún cambio en el archivo `myvm.compute`. Para la ejecución de Spark se usa la misma imagen de Docker de la misma máquina virtual. En el nuevo archivo `myvm-spark.runconfig`, el campo `Target` apunta al mismo archivo `myvm.compute` a través de su nombre `myvm`.

5. Escriba el siguiente comando para ejecutar el script **iris_spark.py** en la instancia de Spark que se ejecuta en el contenedor de Docker remoto:
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>Ejecución de scripts en clústeres de HDInsight
También puede ejecutar este script en un clúster de HDInsight Spark. Aprenda a [crear un clúster de HDInsight Spark para usarlo en Azure Machine Learning Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>El clúster de HDInsight debe usar Azure Blob como almacenamiento principal. Aún no se admite el almacenamiento de Azure Data Lake.

1. Si tiene acceso a un clúster de Spark para Azure HDInsight, genere un comando de configuración de la ejecución de HDInsight como se muestra aquí. Especifique el nombre del clúster de HDInsight y su nombre de usuario y contraseña de HDInsight como parámetros. 

   Use el comando siguiente para crear un destino de proceso que señale a un clúster de HDInsight:

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   Para preparar el clúster de HDInsight, ejecute este comando:

   ```
   az ml experiment prepare -c myhdi
   ```

   El FQDN del nodo principal del clúster suele ser `<your_cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` es el nombre de usuario de SSH del clúster definido durante la configuración de HDInsight. De forma predeterminada, el valor es `sshuser`. El valor no es `admin`, que es el otro usuario creado durante la configuración para habilitar el acceso al sitio web del administrador del clúster. 

2. Ejecute el script **iris_spark.py** en el clúster de HDInsight con este comando:

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Al realizar una ejecución en un clúster de HDInsight remoto, también puede ver los detalles de ejecución del trabajo YARN (Yet Another Resource Negotiator) en `https://<your_cluster_name>.azurehdinsight.net/yarnui` mediante la cuenta de usuario `admin`.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes
En esta segunda parte de la serie de tutoriales de tres partes, ha aprendido cómo:
> [!div class="checklist"]
> * Abrir scripts y revisar el código en Workbench.
> * Ejecutar scripts en un entorno local.
> * Revisar el historial de ejecución.
> * Ejecutar scripts en un entorno de Docker local.

Ahora, puede probar la tercera parte de esta serie de tutoriales en la que puede implementar el modelo de regresión logística que ha creado como servicio web en tiempo real.

> [!div class="nextstepaction"]
> [Tutorial 3: Implementación de modelos](tutorial-classifying-iris-part-3.md)
