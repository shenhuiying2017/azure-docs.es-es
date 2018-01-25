---
title: "Compilación un modelo para Azure Machine Learning Services (versión preliminar) | Microsoft Docs"
description: "A lo largo de este tutorial se muestra cómo puede aprovechar al máximo Azure Machine Learning Services (versión preliminar). Esta es la segunda parte y explica la experimentación."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 11/06/2017
ms.openlocfilehash: 3337ba9fa6845deb98813259821f77ff713f9eb1
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="classify-iris-part-2-build-a-model"></a>Parte 2 de la clasificación de Iris: compilación de un modelo
Azure Machine Learning Services (versión preliminar) es una solución de análisis avanzado y ciencia de datos completa e integrada que permite a los científicos de datos profesionales preparar datos, desarrollar experimentos e implementar modelos a escala de la nube.

Este tutorial es la segunda parte de una serie de tres partes. Aquí se usará Azure Machine Learning Services (versión preliminar) para:

> [!div class="checklist"]
> * Usar Azure Machine Learning Workbench.
> * Abrir scripts y revisar el código.
> * Ejecutar scripts en un entorno local.
> * Revisar el historial de ejecuciones.
> * Ejecutar scripts en un entorno de Docker local.
> * Ejecutar scripts en una ventana de la CLI de Azure local.
> * Ejecutar scripts en un entorno de Docker remoto.
> * Ejecutar scripts en un entorno de Azure HDInsight en la nube.

Este tutorial usa el [conjunto de datos Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. Las capturas de pantalla son específicas de Windows, pero la experiencia con MacOS es casi idéntica.

## <a name="prerequisites"></a>requisitos previos
Finalice la primera parte de esta serie de tutoriales. Siga el [tutorial de preparación de datos](tutorial-classifying-iris-part-1.md) para crear recursos de Azure Machine Learning e instalar la aplicación Azure Machine Learning Workbench antes de comenzar los pasos de este tutorial.

Si lo desea, puede experimentar con la ejecución de scripts en un contenedor de Docker local. Para ello, necesita que haya un motor Docker (es suficiente Community Edition) instalado y que se haya iniciado localmente en una máquina Mac OS o Windows. Para más información acerca de cómo instalar Docker, consulte [las instrucciones de instalación de Docker](https://docs.docker.com/engine/installation/).

Si desea experimentar con la ejecución de scripts de distribución en un contenedor de Docker de una máquina virtual de Azure remota o un clúster de Azure HDInsight Spark, puede seguir las [instrucciones para crear un instancia de Azure Data Science Virtual Machine o un clúster de HDInsight](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Revisión de iris_sklearn.py y los archivos de configuración
1. Abra la aplicación Azure Machine Learning Workbench y abra el proyecto **myIris** que creó en la parte anterior de la serie de tutoriales.

2. Después de que el proyecto este abierto, haga clic en el botón **Archivos** (el icono de la carpeta) del panel izquierdo para abrir la lista de archivos de la carpeta del proyecto.

3. Seleccione el archivo **iris_sklearn.py**. El código Python se abre en una nueva pestaña del editor de texto de Workbench.

   ![Abrir un archivo](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Es posible que el código que ve no sea exactamente el mismo que el anterior, ya que este proyecto de ejemplo se actualiza con frecuencia.

4. Revise el código de script de Python para familiarizarse con el estilo de codificación. Este script realiza las siguientes tareas:

   - Carga el paquete de preparación de datos **iris.dprep** para crear un objeto [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Use el paquete de preparación de datos `iris.dprep` que se incluye en el proyecto de ejemplo, que debería ser el mismo que el archivo `iris-1.dprep` que creó en la parte 1 de este tutorial.

   - Agrega características aleatorias para que el problema sea más difícil de resolver. Dicha aleatoriedad es necesaria porque Iris es un conjunto de datos pequeño que se clasifica fácilmente con casi un 100 % de precisión.

   - Usa la biblioteca de Machine Learning [scikit-learn](http://scikit-learn.org/stable/index.html) para compilar un modelo de regresión logística. 

   - Serializa el modelo mediante la inserción de la biblioteca [pickle](https://docs.python.org/2/library/pickle.html) en un archivo de la carpeta `outputs`. Luego, el script lo carga y lo vuelve a deserializar en la memoria.

   - Usa el modelo deserializado para realizar una predicción en un nuevo registro. 

   - Traza dos gráficos, una matriz de confusión y una curva ROC (característica operativa del receptor) de varias clases, para lo que usa la biblioteca [matplotlib](https://matplotlib.org/), y, después, los guarda en la carpeta `outputs`.

   - El objeto `run_logger` se utiliza en todo el proceso para registrar la tasa de regularización y modelar la precisión en los registros. Los registros se trazan automáticamente en el historial de ejecución.


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>Ejecución del script iris_sklearn.py en un entorno local

Vamos a preparar la ejecución del script **iris_sklearn.py** por primera vez. Este script requiere los paquetes **scikit-learn** y **matplotlib**. El paquete **scikit-aprender** lo está instala Azure Machine Learning Workbench. Sin embargo, es preciso instalar **matplotlib**. 

1. En Azure Machine Learning Workbench, seleccione el menú **Archivo** y, después, **Abrir símbolo del sistema** para abrir el símbolo del sistema. A la ventana de la interfaz de la línea de comandos se la denomina *ventana de la CLI de Azure Machine Learning Workbench*, o *ventana de la CLI* para abreviar.

2. En la ventana de la CLI, escriba el siguiente comando para instalar el paquete **matplotlib** de Python. Debería finalizar en menos de un minuto.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Si omite el comando `pip install` anterior, el código de `iris_sklearn.py` se ejecuta correctamente. Si solo ejecuta `iris_sklearn.py`, el código no genera los la salida de la matriz de confusión ni los trazados de curva ROC de varias clases, tal como se muestra en las visualizaciones del historial.

3. Vuelva a la ventana de la aplicación Workbench. 

4. En la barra de herramientas de la parte superior de la pestaña **iris_sklearn.py**, seleccione esta opción para abrir el menú desplegable que hay junto al icono **Guardar** y, después, seleccione **Configuración de ejecución**. Seleccione **local** como entorno de ejecución, y escriba `iris_sklearn.py` como script que se ejecuta.

5. A continuación, muévase a la derecha de la barra de herramientas y escriba `0.01` en el campo **Argumentos**. 

   ![Control de ejecución](media/tutorial-classifying-iris/run_control.png)

6. Haga clic en el botón **Ejecutar**. Se programará un trabajo de forma inmediata. El trabajo se muestra en el panel **Trabajos** de la derecha de la ventana de Workbench. 

7. Poco después, el estado del trabajo pasa de **Enviando** a **Ejecutando** y luego a **Completado**.

   ![Ejecutar sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Seleccione **Completado** en el texto de estado del trabajo en el panel **Trabajos**. Se abrirá una ventana emergente que mostrará el texto de salida estándar (StdOut) del script que se está ejecutando. Para cerrar el texto stdout, haga clic en el botón **Cerrar** (**x**) de la esquina superior derecha de la ventana emergente.

9. En el mismo estado de trabajo del panel **Trabajos**, seleccione el texto azul **iris_sklearn.py [n]** (_n_ es el número de ejecución) inmediatamente encima del estado **Completado** y de la hora de inicio. Se abre la ventana **Run Properties** (Propiedades de ejecución), que muestra la siguiente información de esa ejecución concreta:
   - Información de **Run Properties** (Propiedades de ejecución)
   - Archivos de **salidas**
   - **Visualizaciones**, en caso de haber
   - **Registros** 

   Cuando la ejecución finaliza, la ventana emergente muestra los resultados siguientes:

   >[!NOTE]
   >Al haber introducido cierta aleatoriedad en el conjunto de aprendizaje anterior, los resultados exactos pueden variar con respecto a los que se muestran aquí.

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

    Escriba una serie de valores numéricos diferentes en el campo **Argumentos** comprendida entre `0.001` y `10`. Seleccione **Ejecutar** para ejecutar el código varias veces. El valor del argumento que cambia cada vez se introduce en el algoritmo de regresión logística del código, lo que produce conclusiones diferentes cada vez.

## <a name="review-the-run-history-in-detail"></a>Detalles de la revisión del historial
En Azure Machine Learning Workbench, cada ejecución de script se captura como un registro del historial de ejecución. Si abre la vista **Ejecuciones**, puede ver el historial de ejecuciones de un script concreto.

1. Para ello, haga clic en el botón **Ejecuciones** (icono del reloj) de la barra de herramientas izquierda. Luego, haga clic en **iris_sklearn.py** para mostrar el **panel de ejecuciones** de `iris_sklearn.py`.

   ![Vista de ejecución](media/tutorial-classifying-iris/run_view.png)

2. Se abrirá la pestaña **Run Dashboard** (Panel de ejecución). Consulte las estadísticas que se han capturado durante varias ejecuciones. Los gráficos se representan en la parte superior de la pestaña. Cada ejecución tiene un número consecutivo y sus detalles se muestran en la tabla de la parte inferior de la pantalla.

   ![Panel de ejecución](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtre la tabla y seleccione cualquiera de los gráficos para ver el estado, la duración, la precisión y la tasa de regularización de cada ejecución. 

4. Seleccione dos o tres ejecuciones en la tabla **Ejecuciones** y haga clic en el botón **Comparar** para abrir un panel de comparación detallado. Revise la comparación en paralelo. Haga clic en el botón **Lista de ejecuciones** de la parte superior izquierda del panel **Comparación** para volver al **panel de ejecución**.

5. Seleccione una ejecución individual para ver la vista de detalles de la misma. Observe que las estadísticas de la ejecución seleccionada se enumeran en la sección **Run Properties** (Propiedades de ejecución). Los archivos escritos en la carpeta de salida se enumeran en sección **Salidas**, de donde se pueden descargar.

   ![Detalles de ejecución](media/tutorial-classifying-iris/run_details.png)

   Los dos trazados, la matriz de confusión y la curva ROC de varias clases, se representan en la sección **Visualizaciones**. Todos los archivos de registro también pueden encontrarse en la sección **Registros**.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Ejecución de scripts en un entorno de Docker local

Con Machine Learning puede configurar fácilmente entornos de ejecución adicionales, como Docker, y ejecutar su script en ellos. 

>[!IMPORTANT]
>Para llevar a cabo este paso, debe tener un motor Docker instalado e iniciado localmente. Para más información, consulte las instrucciones de instalación de Docker.

1. En el panel izquierdo, seleccione el icono de la **carpeta** para abrir la lista **Archivos** de su proyecto. Expanda la carpeta `aml_config`. 

2. Hay varios entornos preconfigurados, como **docker-python**, **docker spark** y **local**. 

   Cada uno de ellos tiene dos archivos, como `docker-python.compute` y `docker-python.runconfig`. Abra cada archivo y observe que algunas opciones se pueden configurar en el editor de texto.  

   Para hacer limpieza, seleccione **Cerrar** (**x**) en las pestañas de los editores de texto abiertos.

3. Ejecute el script **iris_sklearn.py** mediante el entorno **docker-python**: 

   - En la barra de herramientas izquierda, haga clic en el icono del **reloj** para abrir el panel **Ejecuciones**. Seleccione **Todas las ejecuciones**. 
   - En la parte superior de la pestaña **Todas las ejecuciones**, seleccione **docker-python** como entorno de destino, en lugar del valor predeterminado, **local**. 
   - Después, muévase hacia la derecha y seleccione el script **iris_sklearn.py** para ejecutarlo. 
   - Deje el campo **Argumentos** en blanco, ya que el script especifica un valor predeterminado. 
   - Haga clic en el botón **Ejecutar**.

4. Observe que se inicia un nuevo trabajo, que aparece en el panel **Trabajos** de la derecha de la ventana de Workbench.

   La primera vez que realiza una ejecución e Docker, tarda unos minutos más en finalizar. 

   En segundo plano, Azure Machine Learning Workbench compila un nuevo archivo de docker. 
   El nuevo archivo hace referencia a la imagen de Docker base especificada en el archivo `docker.compute` y a los paquetes de Python de dependencia especificados en el archivo `conda_dependencies.yml`. 
   
   El motor Docker realiza las siguientes tareas:

    - Descarga la imagen base de Azure.
    - Instala los paquetes de Python especificados en el archivo `conda_dependencies.yml`.
    - Inicia un contenedor de Docker
    - Copia o hace referencia, en función de la configuración de la ejecución, a la copia local de la carpeta del proyecto.      
    - Ejecuta el script `iris_sklearn.py`.

   Al final, debería ver el mismo resultado que cuando el destino es el entorno **local**.

5. Ahora, vamos probar Spark. La imagen base de Docker contiene una instancia de Spark preinstalada y configurada. Por este motivo, puede ejecutar un script de PySpark en ella. Se trata de una manera fácil de desarrollar y probar un programa Spark sin que tenga que dedicar nada de tiempo a instalarlo y configurarlo. 

   Abra el archivo `iris_spark.py` . Este script carga el archivo de datos `iris.csv` y usa el algoritmo de regresión logística de la biblioteca de aprendizaje automático de Spark para clasificar el conjunto de datos Iris. Ahora, cambie el entorno de ejecución a **docker-spark**, el script a **iris_spark.py** y vuelva a ejecutarlo. Este proceso tarda un poco más porque se tiene que crear e iniciar una sesión de Spark en el contenedor de Docker. También puede ver que la propiedad StdOut es diferente que la propiedad StdOut de `iris_spark.py`.

6. Realice algunas ejecuciones más y experimente con distintos argumentos. 

7. Abra el archivo `iris_spark.py` para ver el modelo de regresión logística compilado con la biblioteca de aprendizaje automático de Spark. 

8. Interactúe con el panel **Trabajos**, ejecute una vista del historial y ejecute una vista de detalles de las ejecuciones en los distintos entornos de ejecución.

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>Ejecución de scripts en la ventana de la CLI de Azure Machine Learning

1. En Azure Machine Learning Workbench, abra la ventana de la línea de comandos, seleccione el menú **Archivo** y, después, **Abrir símbolo del sistema**. Se inicia el símbolo del sistema en la carpeta del proyecto con el símbolo `C:\Temp\myIris\>`.

   >[!IMPORTANT]
   >Debe usar la ventana de línea de comandos (que se abre desde Workbench) para realizar los pasos siguientes.

2. Utilice el símbolo del sistema para iniciar sesión en Azure. 

   La aplicación Workbench y CLI usan memorias caché de credenciales independientes al autenticarse con recursos de Azure. Esto solo tendrá que hacerlo una vez hasta que expire el token de la caché. El comando **az account list** devuelve la lista de suscripciones disponibles para su inicio de sesión. Si hay más de una, utilice el valor del identificador de la suscripción deseada. Establezca dicha suscripción como la cuenta predeterminada que se usa con el comando **az account set -s** y, luego, especifique el valor del identificador de la suscripción. Luego, confirme el valor con el comando **show** de la cuenta.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Una vez que finaliza la autenticación y se establece el contexto de la suscripción de Azure actual, escriba los siguientes comandos en la ventana de la CLI para instalar **matplotlib** y envíe el script de Python para ejecutarlo a modo de experimento.

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Revise el resultado. Tiene la misma salida y los mismos resultados que tenía cuando usó Workbench para ejecutar el script. 

5. Vuelva a ejecutar el mismo script mediante el entorno de ejecución de Docker, en caso de que Docker esté instalado en el equipo.

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. En Workbench, haga clic en el icono **Carpeta** del panel izquierdo para enumerar los archivos del proyecto y abra el script de Python denominado **run.py**. 

   Este script es útil para ejecutar varias tasas de regularización en bucle. Ejecute el experimento varias veces con estas tasas. Este script inicia un `iris_sklearn.py` trabajo con una tasa de regularización de `10.0` (un número demasiado grande). Luego, el script recorta la tasa por la mitad en la siguiente ejecución, y así sucesivamente hasta que la tasa no es menor que `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Para abrir el script **run.py** desde la línea de comandos, ejecute los siguientes comandos:

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Cuando `run.py` finaliza, se ve un grafo en la vista de listas del historial de ejecución de Workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Ejecución en un contenedor de Docker en una máquina remota
Para ejecutar el script en un contenedor de Docker de una máquina remota de Linux, debe tener acceso a SSH (nombre de usuario y contraseña) en esa máquina remota. Además, dicha máquina remota debe tener instalado un motor de Docker y este debe de estar en ejecución. La manera más fácil de obtener una máquina Linux es crear en Azure una imagen de Data Science Virtual Machine (DSVM) basada en Ubuntu. Aprenda a [crear una DSVM de Ubuntu para usarla en Azure Machine Learning Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>La DSVM basada en CentOS *no* se admite.

1. Una vez creada la máquina virtual, puede asociarla como un entorno de ejecución mediante la generación de un par de `.runconfig` y el archivo `.compute` con el comando siguiente. Ejecute el comando siguiente para generar los archivos: Asignaremos el nombre `myvm` al nuevo entorno.
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach remotedocker --name myvm --address <IP address> --username <username> --password <password>
   ```
   
   >[!NOTE]
   >La dirección IP también puede ser un nombre de dominio completo (FQDN) direccionable de forma pública, como `vm-name.southcentralus.cloudapp.azure.com`. Es aconsejable agregar el FQDN a DSVM y usarlo aquí, en lugar de una dirección IP. Esta práctica es recomendable porque se puede desactivar la máquina virtual en algún momento para ahorrar. Además, la próxima vez que inicie la máquina virtual, es posible que la dirección IP haya cambiado.

   A continuación, ejecute el siguiente comando en la construcción en que se encuentra la imagen de Docker en la máquina virtual para prepararlo para ejecutar los scripts:
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >También puede cambiar el valor de `PrepareEnvironment` en `myvm.runconfig` del valor predeterminado de `false` a `true`. Este cambio prepara automáticamente el contenedor de Docker en la primera ejecución.

2. Edite el archivo `myvm.runconfig` generado en `aml_config` y cambie el valor predeterminado del marco de `PySpark` a `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Si deja el valor del marco como PySpark, que también deberían funcionar. Sin embargo, la eficacia disminuye si realmente no necesita una sesión de Spark para ejecutar el script de Python.

3. Emita el mismo comando que antes en la ventana de la CLI, pero esta vez use _myvm_ como destino:
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   El comando se ejecuta como si estuviera en un entorno `docker-python`, salvo que la ejecución se realiza en la máquina virtual Linux remota. La ventana de CLI muestra la misma información de salida.

4. Vamos a probar a usar Spark en el contenedor. Abra el Explorador de archivos. También puede hacerlo desde la ventana de la CLI si está familiarizado con los comandos básicos de manipulación de archivos. Haga una copia del archivo `myvm.runconfig` y denomínelo `myvm-spark.runconfig`. Edite el nuevo archivo para cambiar la configuración `Framework` de `Python` a `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   No realice ningún cambio en el archivo `myvm.compute`. Para la ejecución de Spark se usa la misma imagen de Docker de la misma máquina virtual. En el nuevo archivo `myvm-spark.runconfig`, el campo `target` apunta al mismo archivo `myvm.compute` a través de su nombre `myvm`.

5. Escriba el siguiente comando para ejecutarlo en la instancia de Spark en el contenedor de Docker remoto:
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Ejecución del script en un clúster de HDInsight
También puede ejecutar este script en un clúster de HDInsight Spark. Aprenda a [crear el clúster de HDInsight Spark para usarlo en Azure Machine Learning Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>El clúster de HDInsight debe usar Azure Blob como almacenamiento principal. Aún no se admite el almacenamiento de Azure Data Lake.

1. Si tiene acceso a un proyecto de Spark para el clúster de Azure HDInsight, genere un comando de configuración de ejecución de HDInsight como se muestra aquí. Especifique el nombre del clúster de HDInsight y su nombre de usuario y contraseña de HDInsight como parámetros. Use el comando siguiente:

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <username> --password <password>

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   El FQDN del nodo principal del clúster suele ser `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` es el nombre de usuario de SSH de clúster. El valor predeterminado es `sshuser` si no se cambia al configurar HDInsight. El valor no es `admin`, que es el otro usuario creado durante la configuración para habilitar el acceso al sitio web del administrador del clúster. 

2. Ejecute el siguiente comando y el script se ejecutará en el clúster de HDInsight:

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Al realizar una ejecución en un clúster de HDInsight remoto, también puede ver los detalles de ejecución del trabajo YARN (Yet Another Resource Negotiator) en `https://<cluster_name>.azurehdinsight.net/yarnui` mediante la cuenta de usuario `admin`.


## <a name="next-steps"></a>pasos siguientes
En esta segunda parte de la serie de tutoriales de tres partes, ha aprendido a usar Azure Machine Learning Services para:
> [!div class="checklist"]
> * Usar Azure Machine Learning Workbench.
> * Abrir scripts y revisar el código.
> * Ejecutar scripts en un entorno local.
> * Revisar el historial de ejecuciones.
> * Ejecutar scripts en un entorno de Docker local.
> * Ejecutar scripts en una ventana de la CLI de Azure local.
> * Ejecutar scripts en un entorno de Docker remoto.
> * Ejecutar scripts en un entorno de HDInsight en la nube.

Está listo para continuar y pasar a la tercera parte de la serie. Ahora que ha creado el modelo de regresión logístico, vamos a implementarlo como un servicio web en tiempo real.

> [!div class="nextstepaction"]
> [Implementar un modelo](tutorial-classifying-iris-part-3.md)
