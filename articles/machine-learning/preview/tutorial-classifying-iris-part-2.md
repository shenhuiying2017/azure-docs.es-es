---
title: Compilar un modelo para Azure Machine Learning Services | Microsoft Docs
description: "A lo largo de este tutorial se muestra cómo puede aprovechar al máximo Azure Machine Learning Services (versión preliminar). Esta es la parte 2 que trata sobre la experimentación."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.openlocfilehash: 976407daee45e2f3a8360c1316227cc3399ad43e
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="classifying-iris-part-2-build-a-model"></a>Parte 2 de la clasificación de Iris: compilación de un modelo
Azure Machine Learning Services (versión preliminar) es una solución de análisis avanzado y ciencia de datos completa e integrada que permite a los científicos de datos profesionales preparar datos, desarrollar experimentos e implementar modelos a escala de la nube.

Este tutorial es la segunda parte de una serie de tres partes. En este tutorial, usará Azure Machine Learning Services (versión preliminar) para aprender a:

> [!div class="checklist"]
> * Trabajar en Azure Machine Learning Workbench.
> * Abrir scripts y revisar el código.
> * Ejecutar scripts en un entorno local.
> * Revisar el historial de ejecución.
> * Ejecutar scripts en un entorno de Docker local.
> * Ejecutar scripts en una ventana de CLI de Azure local.
> * Ejecutar scripts en un entorno de Docker remoto.
> * Ejecutar scripts en un entorno de HDInsight en la nube.

Este tutorial usa el [conjunto de datos de Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal con finalidades de simplicidad. Las capturas de pantalla son específicas de Windows, pero la experiencia con macOS es casi idéntica.

## <a name="prerequisites"></a>Requisitos previos
Debe completar la primera parte de esta serie de tutoriales. Siga el [tutorial de preparación de datos](tutorial-classifying-iris-part-1.md) para crear recursos de Azure Machine Learning e instalar la aplicación Azure Machine Learning Workbench antes de comenzar los pasos de este tutorial.

Si lo desea, puede experimentar con la ejecución de scripts en un contenedor de Docker local. Para ello, necesitará un motor Docker (Community Edition es suficiente) instalado e iniciado localmente en su máquina macOS o Windows. Más información sobre las [instrucciones de instalación de Docker](https://docs.docker.com/engine/installation/).

Si desea experimentar con la distribución del script para la ejecución en un contenedor de Docker de máquina virtual de Azure remota o de clúster de HDInsight Spark, puede seguir las [instrucciones para crear una máquina virtual de ciencia de datos de Azure basada en Ubuntu o un clúster de HDI](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-configuration-files"></a>Revisión de los archivos de configuración e iris_sklearn.py
1. Inicie la aplicación **Azure Machine Learning Workbench** y abra el proyecto **myIris** que creó en la parte anterior de la serie de tutoriales.

2. Una vez que el proyecto esté abierto, haga clic en el botón **Archivos** (icono de carpeta) en la barra de herramientas izquierda de Azure Machine Learning Workbench para abrir la lista de archivos de la carpeta del proyecto.

3. Seleccione el archivo **iris_sklearn.py** y se abrirá el código Python en una nueva pestaña del editor de texto de Workbench.

   ![archivo abierto](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Es posible que el código que ve no sea exactamente el mismo que el código anterior, dado que este proyecto de ejemplo se actualiza con frecuencia.

4. Revise el código de script de Python para familiarizarse con el estilo de codificación. Tenga en cuenta que el script realiza las siguientes tareas:

   - Carga el paquete de preparación de datos **iris.dprep** para crear una clase [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Usamos el paquete de preparación de datos `iris.dprep` que se incluye con el proyecto de ejemplo, que debe ser el mismo que el archivo `iris-1.dprep` creado en la parte 1 de este tutorial.

   - Agrega características aleatorias para que el problema sea más difícil de resolver. (La aleatoriedad es necesaria porque Iris es un conjunto de datos pequeño que se puede clasificar fácilmente con una precisión de cerca del 100 %).

   - Usa la biblioteca de aprendizaje automático [scikit-learn](http://scikit-learn.org/stable/index.html) para compilar un modelo de regresión logística simple. 

   - Serializa el modelo con la biblioteca [pickle](https://docs.python.org/2/library/pickle.html) en un archivo de la carpeta `outputs` y, a continuación, lo carga y lo deserializa en la memoria

   - Usa el modelo deserializado para realizar una predicción en un nuevo registro. 

   - Traza dos gráficos (matriz de confusión y curva de ROC de varias clases) mediante la biblioteca [matplotlib](https://matplotlib.org/) y los guarda en la carpeta `outputs`.

   - El objeto `run_logger` se usa en su totalidad para registrar la tasa de regularización y modelar la precisión en registros, y los registros se trazan automáticamente en el historial de ejecución.


## <a name="execute-irissklearnpy-script-in-local-environment"></a>Ejecución del script iris_sklearn.py en el entorno local

Vamos a preparar la ejecución del script **iris_sklearn.py** por primera vez. Este script requiere los paquetes scikit-learn y matplotlib. Azure ML Workbench ya instala **scikit-learn**. Sin embargo, es necesario instalar **matplotlib**. 

1. En Azure Machine Learning Workbench, haga clic en el menú **Archivo** y elija **Abrir símbolo del sistema** para iniciar el símbolo del sistema. Nos referimos a esta ventana de la interfaz de la línea de comandos como ventana de CLI de Azure Machine Learning Workbench o, de forma más abreviada, como ventana CLI.

2. En la ventana CLI, escriba el siguiente comando para instalar el paquete **matplotlib** de Python. Debería completarse en menos de un minuto.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Si omite el comando `pip install` anterior, el código de `iris_sklearn.py` se ejecuta correctamente, pero no genera los trazados de curva ROC de varias clases ni los resultados de la matriz de confusión tal y como se muestra en las visualizaciones del historial.

3. Vuelva a la ventana de la aplicación Workbench. 

4. En la parte superior izquierda de la pestaña **iris_sklearn.py**, situada junto al icono Guardar, haga clic en la lista desplegable para elegir la selección **Configuración de ejecución**.  Elija **local** como entorno de ejecución, y `iris_sklearn.py` como el script que se debe ejecutar.

5. A continuación, desplácese hacia la derecha de la misma pestaña y rellene el campo **Argumentos** con un valor de `0.01`. 

   ![img](media/tutorial-classifying-iris/run_control.png)

6. Haga clic en el botón **Ejecutar**. Se programará un trabajo de forma inmediata. El trabajo se muestra en el panel **Trabajos** del lado derecho de la ventana Workbench. 

7. Transcurridos unos instantes, el estado del trabajo cambia de **Enviando** a **Ejecutando** y, poco después, a **Completado**.

   ![ejecutar sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Haga clic en la palabra **Completado** en el texto de estado del trabajo del panel Trabajos. Se abrirá una ventana emergente que mostrará el texto de salida estándar (StdOut) del script que se está ejecutando. Para cerrar el texto StdOut, haga clic en el botón **X** en la esquina superior derecha de la ventana emergente.

9. En el mismo estado de trabajo del panel Trabajos, haga clic en el texto azul **iris_sklearn.py [n]** (_n_ es el número de ejecución) justo encima del estado **Completado** y la hora de inicio. Se abre la página **Run properties** (Propiedades de ejecución) y se muestra la información de las propiedades de ejecución, los archivos de **Salida**, las **Visualizaciones** y los **Registros** de esta ejecución concreta. 

   Cuando se completa la ejecución, la ventana emergente muestra los resultados siguientes:

   >[!NOTE]
   >Puesto que introducimos aleatoriedad en el conjunto de aprendizaje anterior, los resultados exactos varían ligeramente.

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
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. Cierre la pestaña **Run Properties** (Propiedades de ejecución) y vuelva a la pestaña **iris_sklearn.py**. 

11. Repita las ejecuciones adicionales. 

    Escriba una serie de valores numéricos diferentes en el campo **Argumentos** comprendida entre `0.001` y `10`. Haga clic en **Ejecutar** para ejecutar el código varias veces. El valor del argumento que cambia cada vez se introduce en el algoritmo de regresión logística del código, lo que produce resultados diferentes cada vez.

## <a name="review-run-history-in-detail"></a>Detalles de la revisión del historial
En Azure Machine Learning Workbench, cada ejecución de script se captura como un registro del historial de ejecución. Para ver el historial de ejecución de un script determinado, abra la vista **Ejecuciones**.

1. Haga clic en el botón **Ejecuciones** (icono de reloj) de la barra de herramientas de la izquierda para abrir la lista de **Ejecuciones**. A continuación, haga clic en **iris_sklearn.py** para mostrar **Run Dashboard** (Panel de ejecución) de `iris_sklearn.py`.

   ![img](media/tutorial-classifying-iris/run_view.png)

2. Se abrirá la pestaña **Run Dashboard** (Panel de ejecución). Revise las estadísticas que se han capturado durante varias ejecuciones. Los grafos se representan en la parte superior de la pestaña y cada ejecución numerada con detalles se muestra en la tabla en la parte inferior de la página.

   ![img](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtre la tabla y haga clic en los grafos de forma interactiva para ver el estado, la duración, la precisión y la tasa de regularización de cada ejecución. 

4. Seleccione dos o tres ejecuciones en la tabla **Ejecuciones** y haga clic en el botón **Comparar** para abrir una página de comparación detallada. Revise la comparación en paralelo. Haga clic en el botón **Lista de ejecuciones** en la esquina superior izquierda de la página de comparación para volver a **Run Dashboard** (Panel de ejecuciones).

5. Haga clic en una ejecución individual para ver la vista de detalles de ejecución. Observe que las estadísticas de la ejecución seleccionada se muestran en la sección _Run Properties_ (Propiedades de ejecución). Los archivos escritos en la carpeta de salida se enumeran en sección **Salida** y se puede descargar.

   ![img](media/tutorial-classifying-iris/run_details.png)

   Los dos trazados, la matriz de confusión y la curva ROC de varias clases, se representan en la sección **Visualizaciones**. Todos los archivos de registro también pueden encontrarse en la sección **Registros**.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Ejecución de scripts en un entorno de Docker local

Azure ML le permite configurar fácilmente entornos de ejecución adicionales como Docker y ejecutar su script en esos entornos. 

>[!IMPORTANT]
>Para llevar a cabo este paso, debe tener el motor Docker instalado e iniciado localmente. Vea la guía de instalación para obtener más detalles.

1. En la barra de herramientas de la izquierda, seleccione el icono de carpeta para abrir la lista **Archivos** de su proyecto. Expanda la carpeta `aml_config`. 

2. Observe que hay varios entornos preconfigurados, como **docker-python**, **docker spark** y **local**. 

   Cada entorno tiene dos archivos, como `docker-python.compute` y `docker-python.runconfig`. Abra cada tipo de archivo y observe que hay algunas opciones configurables en el editor de texto.  

   Cierre (X) las pestañas de todos los editores de texto abiertos a fin de realizar una limpieza.

3. Ejecute el script **iris_sklearn.py** mediante el entorno **docker-python**. 

   - En la barra de herramientas izquierdo, haga clic en el icono de reloj para abrir el panel **Ejecuciones**. Haga clic en **Todas las ejecuciones**. 
   - En la parte superior de la pestaña **Todas las ejecuciones**, elija **docker-python** como entorno de destino en lugar del entorno **local** predeterminado. 
   - A continuación, desplácese hacia la derecha y elija el script **iris_sklearn.py** para la ejecución. 
   - Deje el campo **Argumentos** en blanco, ya que el script especifica un valor predeterminado. 
   - Haga clic en el botón **Ejecutar**.

4. Observe que se inicia un nuevo trabajo tal y como se muestra en el panel **Trabajos** en la derecha de la ventana Workbench.

   Cuando realiza una ejecución con Docker por primera vez, el proceso tarda unos minutos más en completarse. 

   Entre bambalinas, Azure Machine Learning Workbench crea un nuevo archivo de Docker que hace referencia a la imagen base de Docker especificada en el archivo `docker.compute` y a los paquetes de Python de dependencia especificados en el archivo `conda_dependencies.yml`. A continuación, el motor de Docker descarga la imagen base de Azure, instala los paquetes de Python especificados en el archivo `conda_dependencies.yml` e inicia un contenedor de Docker. Luego, copia (o, en función de la configuración de ejecución, hace referencia a) la copia local de la carpeta del proyecto y ejecuta el script `iris_sklearn.py`. Al final, debería ver el mismo resultado exacto que cuando usa el entorno **local** como destino.

5. Ahora vamos probar Spark. La imagen base de Docker contiene una instancia de Spark previamente instalada y configurada. Por este motivo, puede ejecutar un script de PySpark en ella. Se trata de una manera sencilla de desarrollar y probar el programa Spark sin tener que dedicar tiempo en instalarlo y configurarlo. 

   Abra el archivo `iris_spark.py` . Este script carga el archivo de datos `iris.csv` y usa el algoritmo de regresión logística de la biblioteca de ML de Spark para clasificar el conjunto de datos de Iris. Ahora cambie el entorno de ejecución a **docker-spark** y el script, a **iris_spark.py** y vuélvalo a ejecutar. Este proceso tarda un poco más debido a que se debe crear e iniciar una sesión de Spark dentro del contenedor de Docker. También puede ver que la propiedad StdOut es diferente que la propiedad StdOut de `iris_spark.py`.

6. Realice algunas ejecuciones más y experimente con distintos argumentos. 

7. Abra el archivo `iris_spark.py` para ver el modelo de regresión logística simple compilado con la biblioteca de ML de Spark. 

8. Interactúe con el panel **Trabajos**, la vista de listas del historial de ejecución y la vista de detalles de las ejecuciones en los distintos entornos de ejecución.

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>Ejecución de scripts en la ventana CLI de Azure ML

1. Con Azure Machine Learning Workbench, inicie la ventana de la línea de comandos. Para ello, haga clic en el menú **Archivo** y, a continuación, en **Abrir símbolo del sistema**. Se iniciará el símbolo del sistema en la carpeta del proyecto con el símbolo `C:\Temp\myIris\>`.

   >[!Important]
   >Debe usar la ventana de línea de comandos (que se inicia desde Workbench) para realizar los pasos siguientes:

2. Use el símbolo del sistema (CLI) para iniciar sesión en Azure. 

   La aplicación Workbench y CLI usan memorias caché de credenciales independientes al autenticarse con recursos de Azure. Solo necesitará hacerlo una vez, hasta que expire el token de caché. El comando **az account list** devuelve la lista de suscripciones disponibles para su inicio de sesión. Si hay más de una, use el valor de identificador de la suscripción que quiera, proporciónelo y establézcalo como la cuenta predeterminada para usar con el comando **az set account -s**. A continuación, confirme la configuración con el comando account show.

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. Una vez se haya autenticado y se haya establecido el contexto de la suscripción de Azure actual, escriba los siguientes comandos en la ventana de CLI para instalar matplotlib y envíe el script de Python para ejecutarlo a modo de experimento.

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Revise el resultado. Tenga en cuenta que obtiene el mismo resultado que cuando realizó la ejecución previamente en este tutorial con Workbench para ejecutar el script. 

5. Ejecute el mismo script mediante el entorno de ejecución de Docker si tiene instalado Docker en su máquina.

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_spark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. En Azure Machine Learning Workbench, haga clic en el icono Carpeta de la barra de herramientas izquierda para enumerar los archivos de proyecto y abrir el script de Python denominado **run.py**. 

   Este script es útil para realizar un bucle de varias tasas de regularización y ejecutar el experimento varias veces con estas tasas. Este script inicia un trabajo `iris_sklearn.py` con una tasa de regularización de `10.0` (un número enorme) y reduce la tasa a la mitad en la siguiente ejecución y así sucesivamente, hasta que la tasa no sea menor que `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Para iniciar el script **run.py** desde la línea de comandos, ejecute los siguientes comandos:

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Cuando finalice `run.py`, verá un grafo en la vista de listas del historial de ejecución de Azure Machine Learning Workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Ejecución en un contenedor de Docker en una máquina remota
Para ejecutar el script en un contenedor de Docker de una máquina remota de Linux, debe tener acceso a SSH (nombre de usuario y contraseña) en esa máquina remota. Asimismo, esa máquina remota debe tener el motor de Docker instalado y en ejecución. La manera más fácil de obtener una máquina Linux consiste en crear una [máquina virtual de ciencia de datos basada en Ubuntu (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) en Azure. (Tenga en cuenta que la DSVM basada en CentOS no se admite). 

1. Una vez creada la máquina virtual, puede asociarla como un entorno de ejecución mediante la generación de un par de `.runconfig` y el archivo `.compute` con el comando siguiente. Asignaremos el nombre `myvm` al nuevo entorno.
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >El área de la dirección IP también puede ser un nombre de dominio completo (FQDN) direccionable de forma pública, como `vm-name.southcentralus.cloudapp.azure.com`. Le recomendamos que agregue el FQDN en DSVM y lo use aquí en lugar de la dirección IP, ya que podría desactivar la máquina virtual en algún momento para ahorrar costos. Además, la próxima vez que inicie la máquina virtual, es posible que la dirección IP haya cambiado.

   A continuación, ejecute el siguiente comando de construcción de la imagen de Docker en la máquina virtual a fin de prepararlo para ejecutar los scripts.
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >También puede cambiar el valor de `PrepareEnvironment` en `myvm.runconfig` del valor predeterminado `false` a `true`. Esto preparará automáticamente el contenedor de Docker en la primera ejecución.

2. Edite el archivo `myvm.runconfig` generado en `aml_config` y cambie el valor predeterminado `PySpark` de Framework a `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Si deja la configuración de Framework en PySpark, también debería funcionar correctamente. Sin embargo, no resulta muy eficaz si realmente no necesita una sesión de Spark para ejecutar el script de Python.

3. Emita el mismo comando que antes en la ventana de CLI, pero use _myvm_ como destino esta vez:
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   El comando se ejecuta como si usara un entorno `docker-python`, excepto en que la ejecución se realiza en la VM de Linux remota. La ventana de CLI muestra la misma información de salida.

4. Vamos a probar Spark en el contenedor. Abra el Explorador de archivos (también puede hacerlo desde la ventana de CLI si está familiarizado con los comandos básicos de manipulación de archivos). Haga una copia del archivo `myvm.runconfig` y denomínelo `myvm-spark.runconfig`. Edite el nuevo archivo para cambiar la configuración `Framework` de `Python` a `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   No realice ningún cambio en el archivo `myvm.compute`. La misma imagen de Docker de la misma máquina virtual pasa a usarse para la ejecución de Spark. En el nuevo archivo `myvy-spark.runconfig`, el campo `target` apunta al mismo archivo `myvm.compute` a través de su nombre `myvm`.

5. Escriba el siguiente comando para ejecutarlo en la instancia de Spark en el contenedor de Docker remoto:
   ```azureli
   REM execute iris_spark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Ejecución del script en un clúster de HDInsight
También puede ejecutar este script en un clúster real de Spark. 

1. Si tiene acceso a un proyecto de Spark para el clúster Azure HDInsight, genere un comando de ejecución HDI tal y como se muestra. Proporcione el nombre del clúster de HDInsight, su nombre de usuario de HDInsight y la contraseña como parámetros. Use el comando siguiente:

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   El nombre de dominio completo (FQDN) del nodo principal del clúster suele ser `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` es el nombre de usuario de SSH de clúster. El valor predeterminado es `sshuser` si no lo cambia durante el aprovisionamiento de HDI. No es `admin`, que es el otro usuario creado durante el aprovisionamiento para habilitar el acceso al sitio web de administrador del clúster. 

2. Ejecute el comando siguiente y las ejecuciones del script en el clúster de HDInsight:

   ```azurecli
   REM execute iris_spark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Al realizar una ejecución con un clúster HDI remoto, también puede ver los detalles de ejecución del trabajo YARN en `https://<cluster_name>.azurehdinsight.net/yarnui` mediante la cuenta de usuario `admin`.


## <a name="next-steps"></a>Pasos siguientes
En esta segunda parte de la serie de tutoriales de tres partes, ha aprendido a usar Azure Machine Learning Services para:
> [!div class="checklist"]
> * Trabajar en Azure Machine Learning Workbench.
> * Abrir scripts y revisar el código.
> * Ejecutar scripts en un entorno local.
> * Revisar el historial de ejecución.
> * Ejecutar scripts en un entorno de Docker local.
> * Ejecutar scripts en una ventana de CLI de Azure local.
> * Ejecutar scripts en un entorno de Docker remoto.
> * Ejecutar scripts en un entorno de HDInsight en la nube.

Está listo para continuar y pasar a la tercera parte de la serie. Ahora que hemos creado el modelo de regresión logística, vamos a implementarlo como un servicio web en tiempo real.

> [!div class="nextstepaction"]
> [Implementar un modelo](tutorial-classifying-iris-part-3.md)
