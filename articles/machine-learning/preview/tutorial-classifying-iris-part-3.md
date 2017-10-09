---
title: "Implementación de un modelo para Azure Machine Learning Services | Microsoft Docs"
description: "A lo largo de este tutorial se muestra cómo puede aprovechar al máximo Azure Machine Learning Services (versión preliminar). Esta es la parte 3 que trata sobre la implementación del modelo."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 0dfcc965d96949527b3e80285061bff320872621
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-3-deploy-a-model"></a>Parte 3 de la clasificación de Iris: implementación de un modelo
Azure Machine Learning Services (versión preliminar) es una solución de análisis avanzado y ciencia de datos completa e integrada que permite a los científicos de datos profesionales preparar datos, desarrollar experimentos e implementar modelos a escala de la nube.

Este tutorial es la tercera parte de una serie de tres partes. En este tutorial, usará Azure Machine Learning Services (versión preliminar) para aprender a:

> [!div class="checklist"]
> * Buscar el archivo de modelo.
> * Generar un archivo de esquema y de script de puntuación.
> * Preparar el entorno.
> * Crear un servicio web en tiempo real.
> * Ejecutar un servicio web en tiempo real.
> * Examinar los datos de blob de salida. 

 Este tutorial usa el [conjunto de datos de Iris](https://en.wikipedia.org/wiki/iris_flower_data_set) atemporal con finalidades de simplicidad. Las capturas de pantalla son específicas de Windows, pero la experiencia con macOS es casi idéntica.

## <a name="prerequisites"></a>Requisitos previos
Complete las dos primeras partes de esta serie de tutoriales:
- En primer lugar, siga el [tutorial de preparación de datos](tutorial-classifying-iris-part-1.md) para crear recursos de Azure Machine Learning e instalar la aplicación Azure Machine Learning Workbench.
- En segundo lugar, siga [un tutorial para compilar un modelo](tutorial-classifying-iris-part-2.md) a fin de crear un modelo de regresión logística en Azure Machine Learning.

## <a name="download-the-model-pickle-file"></a>Descarga del archivo pickle de modelo
En la parte anterior del tutorial, el script `iris_sklearn.py` se ejecutó en Azure Machine Learning Workbench localmente. Esa acción serializa el modelo de regresión logística con el paquete popular de serialización de objetos de Python **[pickle](https://docs.python.org/2/library/pickle.html)**. 

1. Inicie la aplicación **Azure Machine Learning Workbench** y abra el proyecto **myIris** que creó en la parte anterior de la serie de tutoriales.

2. Una vez que el proyecto esté abierto, haga clic en el botón **Archivos** (icono de carpeta) en la barra de herramientas izquierda de Azure Machine Learning Workbench para abrir la lista de archivos de la carpeta del proyecto.

3. Seleccione el archivo **iris_sklearn.py** y se abrirá el código Python en una nueva pestaña del editor de texto de Workbench.

4. Revise el archivo **iris_sklearn.py** para ver dónde se generó el archivo pickle. Use Control + F para abrir el cuadro de diálogo de búsqueda y, a continuación, buscar la palabra **pickle** en el código de Python.

   Este fragmento de código muestra cómo se generó el archivo de salida de pickle. Observe que el archivo pickle de salida se denomina `model.pkl` en el disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Busque el archivo pickle del modelo en los archivos de salida de una ejecución anterior.
   
   Cuando ejecutó el script **iris_sklearn.py**, el archivo de modelo se escribió en la carpeta `outputs` denominada `model.pkl`. Esta carpeta se hospeda en el entorno de ejecución que elije para ejecutar el script, y no en la carpeta del proyecto local. 
   
   - Para buscar el archivo, use la aplicación Azure Machine Learning Workbench y haga clic en el botón **Ejecuciones** (icono del reloj) de la barra de herramientas izquierda para abrir la lista de **Todas las ejecuciones**.  
   - Se abre la pestaña **Todas las ejecuciones**. En la tabla de Ejecuciones, seleccione una de las ejecuciones recientes cuyo destino era **local** y cuyo nombre de script era **iris_sklearn.py**. 
   - Se abrirá la página **Run Properties** (Propiedades de ejecución). En la esquina superior derecha de la página, observe la sección **Salidas**. 
   - Para descargar el archivo pickle, seleccione la casilla junto al archivo **model.pkl** y haga clic en el botón **Descargar**. Guárdelo en la raíz de la carpeta del proyecto. Será necesario en los próximos pasos.

   ![Descarga del archivo pickle](media/tutorial-classifying-iris/download_model.png)

   Obtenga más información sobre la carpeta `outputs` en el artículo [How to read and write large data files](how-to-read-write-files.md) (Cómo leer y escribir archivos de datos de gran tamaño).

## <a name="get-scoring-and-schema-files"></a>Obtener archivos de esquema y puntuación
Para implementar el servicio web, junto con el archivo de modelo, también necesitará un script de puntuación y, opcionalmente, un esquema para los datos de entrada del servicio web. El script de puntuación carga el archivo `model.pkl` desde la carpeta actual y lo usa para generar una clase de Iris recién prevista.  

1. Inicie la aplicación **Azure Machine Learning Workbench** y abra el proyecto **myIris** que creó en la parte anterior de la serie de tutoriales.

2. Una vez que el proyecto esté abierto, haga clic en el botón **Archivos** (icono de carpeta) en la barra de herramientas izquierda de Azure Machine Learning Workbench para abrir la lista de archivos de la carpeta del proyecto.

3. Seleccione el archivo **iris_score.py**. Se abrirá el script de Python. Este archivo se usa como el archivo de puntuación.

4. Para obtener el archivo de esquema, ejecute el script. Elija el entorno **local** y el script **iris_score.py** en la barra de comandos y, a continuación, haga clic en el botón **Ejecutar**. 

5. Este script crea un archivo JSON en la carpeta **Salidas**, que captura el esquema de datos de entrada requerido por el modelo.

   ![Archivo de puntuación](media/tutorial-classifying-iris/model_data_collection.png)

6. En el panel Trabajos de la derecha de la ventana Machine Learning Workbench, espere hasta que el último trabajo **iris_score.py** muestre el estado verde **Completado**. A continuación, haga clic en el hipervínculo **iris_score.py [1]** de la última ejecución de trabajos para ver los detalles de la ejecución **iris_score.py**. 

7. En la página Run Properties (Propiedades de ejecución), en la sección **Salidas**, seleccione el archivo recién creado **service_schema.json**. Guárdelo en la carpeta raíz del proyecto.

8. Vuelva a la pestaña donde ha abierto el script **iris_score.py**. 

   Tenga en cuenta el uso de la recopilación de datos que le permite capturar las entradas del modelo y las predicciones del servicio web. Los puntos siguientes son de especial interés para la recopilación de datos.

9. Revise el código en la parte superior de la clase de importaciones de archivos ModelDataCollector que contiene la funcionalidad de recopilación de datos del modelo:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Revise las siguientes líneas de código de la función `init()`, que crea instancias de ModelDataCollector:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Revise las siguientes líneas de código de la función `run(input_df)`, que recopila datos de entrada y predicción:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Ya está listo para preparar el entorno y poner el modelo en funcionamiento.

## <a name="prepare-to-operationalize-locally"></a>Preparación para la operacionalización local
Use la implementación _modo local_ para que se ejecute en contenedores de Docker en su equipo local.

Puede usar el _modo local_ para desarrollo y pruebas. El motor de Docker debe ejecutarse localmente para completar los pasos siguientes y poner el modelo en funcionamiento. Puede usar la marca `-h` al final de los comandos para obtener ayuda del comando.

>[!NOTE]
>Si no tiene el motor de Docker en el entorno local, igualmente puede seguir creando un clúster en Azure para la implementación. Simplemente, asegúrese de eliminar el clúster cuando finalice el tutorial para que no se le apliquen los cargos actuales.

1. Abra la interfaz de la línea de comandos en Azure Machine Learning Workbench y, en el menú Archivo, haga clic en **Abrir símbolo del sistema**.

   Se abrirá el símbolo de la línea de comandos en la ubicación actual de la carpeta del proyecto `c:\temp\myIris>`.

2. Asegúrese de que el proveedor de recursos de Azure `Microsoft.ContainerRegistry` esté registrado en su suscripción. Debe registrar este proveedor de recursos para poder crear un entorno en el paso 3. Puede comprobar si ya está registrado mediante el comando siguiente:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Debería mostrarse una salida similar a esta: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Si `Microsoft.ContainerRegistry` no es registrado, puede registrarlo con el comando siguiente:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   Es posible que el registro tarde unos minutos y puede comprobar su estado mediante el comando anterior `az provider list` o el siguiente:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

3. Cree el entorno. Este paso debe ejecutarse una vez para cada entorno, por ejemplo, de desarrollo o producción. Use el _modo local_ para este primer entorno. (Puede probar el conmutador `-c` o `--cluster` del siguiente comando para configurar un entorno en el _modo del clúster_ más adelante).

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Siga las instrucciones en pantalla para aprovisionar una cuenta de almacenamiento para almacenar las imágenes de Docker, un Azure Container Registry (ACR) para enumerar imágenes de Docker y una cuenta de AppInsight para recopilar la telemetría. Si usó el modificador `-c`, este también crea un clúster de ACS (Azure Container Service).
   
   El nombre del clúster es una forma de identificar el entorno, y la ubicación debe ser la misma que la ubicación de la cuenta de Administración de modelos que creó en Azure Portal.

4. Cree una cuenta de Administración de modelos (solo deberá configurarla una vez).  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Establezca la cuenta de Administración de modelos.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Configure el entorno.
Una vez completada la configuración, establezca las variables de entorno necesarias para la operacionalización mediante el comando siguiente. El nombre del entorno es el nombre que se usó en el paso 1 anterior. El nombre del grupo de recursos fue el resultado del mismo proceso y debería estar en la ventana de comandos cuando se complete el proceso de instalación.
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

   Para comprobar que ha configurado correctamente su entorno de operacionalización para la implementación del servicio web local, escriba el siguiente comando:

   ```azurecli
   az ml env show
   ```

Ya está listo para crear el servicio web en tiempo real.

## <a name="create-a-real-time-web-service"></a>Creación de un servicio web en tiempo real
Use el comando siguiente para crear un servicio web en tiempo real.

   ```azurecli
   az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   Esto genera un identificador del servicio web que puede usar más adelante.

   A continuación, se muestran los modificadores para el comando `az ml service create realtime`:
   * -n: nombre de la aplicación, todos los caracteres deben ser en minúsculas.
   * -f: nombre de archivo de script de puntuación.
   * --model-file: archivo de modelo, en este caso, es el archivo model.pkl de pickle.
   * -r: tipo de modelo, en este caso, es un modelo de Python.
   * --collect-model-data true: habilita la recopilación de datos.

   >[!IMPORTANT]
   >El nombre del servicio (que también es el nuevo nombre de la imagen de Docker) debe tener todos los caracteres en minúsculas, de lo contrario se produce un error. 

   Al ejecutar el comando, el modelo y el archivo de puntuación se cargan en la cuenta de almacenamiento que creó como parte de la configuración del entorno anterior. El proceso de implementación compila una imagen de Docker con el modelo, el esquema y el archivo de puntuación que contiene, y lo inserta en el registro de ACR: `<ACR_name>.azureacr.io/<imagename>:<version>`. A continuación, extrae esa imagen localmente en el equipo e inicia un contenedor de Docker basado en esa imagen. (Si su entorno está configurado en el modo de clúster, el contenedor de Docker se implementa en el clúster de Kubernetes de ACS en su lugar).

   Como parte de la implementación, se crea un punto de conexión de REST de HTTP para el servicio web en la máquina local. Después de unos minutos, el comando debe finalizar con un mensaje de confirmación y el servicio web estará listo para la acción.

   Puede ver el contenedor de Docker en ejecución mediante el comando `docker ps`:
   ```azurecli
   docker ps
   ```
### <a name="alternative-route"></a>Ruta alternativa
Como alternativa al uso del comando `az ml service create realtime` que se muestra anteriormente, también puede realizar el registro del modelo, la generación del manifiesto, la compilación de la imagen de Docker y los pasos de creación del servicio web individualmente. Esto le proporciona mayor flexibilidad en cada paso, y puede volver a usar las entidades generadas en el paso anterior y recompilar entidades solo cuando sea necesario. Siga las instrucciones siguientes para ver cómo se puede lograr esto:

1. Proporcione el nombre del archivo pickle para registrar el modelo.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Esto genera un identificador de modelo.

2. Cree el manifiesto.

   Para crear un manifiesto, use este comando y proporcione el resultado del identificador del modelo del paso anterior:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris_score.py -r python -i <model ID> -s service_schema.json
   ```
   Esto genera un identificador del manifiesto.

3. Cree una imagen de Docker.

   Para crear una imagen de Docker, use este comando y proporcione el resultado del valor del identificador del manifiesto del paso anterior:

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   Esto genera un identificador de la imagen de Docker.
   
4. Creación del servicio

   Para crear un servicio, use el comando que se muestra y proporcione el resultado del identificador de la imagen del paso anterior:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Esto genera un identificador del servicio web.

Ya está preparado para ejecutar el servicio web.

## <a name="run-the-real-time-web-service"></a>Ejecutar un servicio web en tiempo real.

Para probar el servicio web `irisapp` en ejecución, proporciónele un registro codificado JSON que contenga una matriz de cuatro números aleatorios.

1. La creación del servicio web incluía datos de ejemplo. Al realizar la ejecución en modo local, puede llamar al comando `az ml service show realtime` para recuperar un comando de ejecución de ejemplo que puede usar para probar el servicio. Esto también le proporcionará la dirección URL de puntuación que puede usar para incorporar el servicio en su propia aplicación personalizada:

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. Para probar el servicio, ejecute el comando de ejecución del servicio devuelto.

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}
   ```
   El resultado es `"2"`, que es la clase prevista. (Es posible que su resultado sea diferente). 

3. Si quiere ejecutar el servicio desde fuera de la CLI, necesitará obtener las claves para la autenticación:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Vea los datos recopilados en Azure Blob Storage:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Busque todas las cuentas de almacenamiento. Para ello, haga clic en **Más servicios**.

3. En el cuadro de búsqueda, escriba **Cuentas de almacenamiento** y presione **Entrar**.

4. En la página de búsqueda **Cuentas de almacenamiento**, seleccione el recurso **Cuenta de almacenamiento** que coincida con su entorno. 

   > [!TIP]
   > Para determinar la cuenta de almacenamiento que se usa: abra Azure Machine Learning Workbench, seleccione el proyecto con el que está trabajando y abra el símbolo de la línea de comandos del menú **Archivo** en el símbolo de la línea de comandos, escriba `az ml env show -v` y compruebe el valor *storage_account*. Este es el nombre de la cuenta de almacenamiento.

5. Una vez se abra la página **Cuenta de almacenamiento**, haga clic en el elemento **Contenedores** de la lista de la izquierda. Busque el contenedor denominado **modeldata**. 
 
   Si no ve ningún dato, debe esperar hasta 10 minutos tras la primera solicitud de servicio web para poder ver cómo los datos comienzan a propagarse a la cuenta de almacenamiento.

   Los datos fluyen en blobs con la siguiente ruta de acceso del contenedor:

   `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

6. Puede consumir estos datos desde los blobs de Azure de diferentes maneras y tanto con software de Microsoft como con herramientas de código abierto. 

   En el ejemplo se aproximan los blobs de salida al consumidor:
   - Azure ML Workbench: abra el archivo .csv en Azure ML Workbench mediante la adición del archivo .csv como origen de datos. 
   - Excel: abra los archivos .csv diariamente como una hoja de cálculo.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): cree gráficos con datos extraídos de los datos .csv en blobs.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): cree una trama de datos con una gran parte de datos .csv.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) : cargue los datos .csv en una tabla de Hive y realice las consultas SQL directamente en el blob.

## <a name="next-steps"></a>Pasos siguientes
En esta tercera parte de la serie de tutoriales de tres partes, ha aprendido a usar Azure Machine Learning Services para:
> [!div class="checklist"]
> * Buscar el archivo de modelo.
> * Generar un archivo de esquema y de script de puntuación.
> * Preparación del entorno
> * Creación de un servicio web en tiempo real
> * Ejecutar un servicio web en tiempo real.
> * Examinar los datos de blob de salida. 

Ha ejecutado correctamente un script de aprendizaje en varios entornos de proceso, ha creado y serializado un modelo y lo ha puesto en funcionamiento mediante un servicio web basado en Docker. 

Está listo para realizar la preparación avanzada de datos:
> [!div class="nextstepaction"]
> [Preparación avanzada de datos](tutorial-bikeshare-dataprep.md)


