---
title: "Implementación de un modelo para Azure Machine Learning Services (versión preliminar) | Microsoft Docs"
description: "A lo largo de este tutorial se muestra cómo puede aprovechar al máximo Azure Machine Learning Services (versión preliminar). Esta es la parte tres y en ella se describe el modelo de implementación."
services: machine-learning
author: raymondl
ms.author: raymondl, j-martens, aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/7/2018
ms.openlocfilehash: 13ddc0ef8c7eac86e6cd7abb684ce35ae18fba84
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-classify-iris-part-3-deploy-a-model"></a>Tutorial: Clasificación de Iris, parte 3: implementación de un modelo
Azure Machine Learning (versión preliminar) es una solución de análisis de ciencia de datos completa, integrada y avanzada dirigida a los científicos de datos profesionales. Estos pueden usarla para preparar datos, desarrollar experimentos e implementar modelos a escala de nube.

Este tutorial es la **tercera de una serie de tres partes**. Aquí se usará Machine Learning (versión preliminar) para:

> [!div class="checklist"]
> * Buscar el archivo de modelo
> * Generar un archivo de esquema y de script de puntuación
> * Preparar el entorno
> * Crear un servicio web en tiempo real
> * Ejecutar un servicio web en tiempo real
> * Examinar los datos de blob de salida 

Este tutorial usa el [conjunto de datos Iris](https://en.wikipedia.org/wiki/iris_flower_data_set) atemporal. Las capturas de pantalla son específicas de Windows, pero la experiencia con MacOS es casi idéntica.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
Complete las dos primeras partes de esta serie de tutoriales:

   * Siga el [tutorial de preparación de datos](tutorial-classifying-iris-part-1.md) para crear recursos de Machine Learning e instalar la aplicación Azure Machine Learning Workbench.
   * Siga el [tutorial para compilar un modelo](tutorial-classifying-iris-part-2.md) para crear un modelo de regresión logística en Machine Learning.

Necesita tener instalado un motor de Docker que se ejecute localmente. Como alternativa, puede implementar en un clúster de Azure Container Service en Azure.

## <a name="download-the-model-pickle-file"></a>Descarga del archivo pickle de modelo
En la parte anterior del tutorial, el script **iris_sklearn.py** se ejecutó localmente en Machine Learning Workbench. Esta acción serializó el modelo de regresión logística con el popular paquete de serialización de objetos de Python [pickle](https://docs.python.org/3/library/pickle.html). 

1. Abra la aplicación Machine Learning Workbench y abra el proyecto **myIris** que ha creado en las partes anteriores de la serie de tutoriales.

2. Después de que el proyecto esté abierto, haga clic en el icono de carpeta **Files** (Archivos), en el panel izquierdo, para abrir la lista de archivos de la carpeta del proyecto.

3. Seleccione el archivo **iris_sklearn.py**. El código Python se abre en una nueva pestaña del editor de texto de Workbench.

4. Revise el archivo **iris_sklearn.py** para ver dónde se generó el archivo pickle. Seleccione Ctrl+F para abrir el cuadro de diálogo **Buscar** y, a continuación, busque la palabra **pickle** en el código Python.

   Este fragmento de código muestra cómo se generó el archivo de salida de pickle. El archivo pickle de salida se denomina **model.pkl** en el disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Busque el archivo pickle del modelo en los archivos de salida de una ejecución anterior.
   
   Cuando ejecutó el script **iris_sklearn.py**, el archivo de modelo se escribió en la carpeta **outputs** denominada **model.pkl**. Esta carpeta se hospeda en el entorno de ejecución que elije para ejecutar el script, y no en la carpeta del proyecto local. 
   
   a. Para buscar el archivo, seleccione el icono del reloj **Runs** (Ejecuciones), en el panel izquierdo, para abrir la lista **All Runs** (Todas las ejecuciones). 

   b. Se abre la pestaña **All Runs** (Todas las ejecuciones). En la tabla de ejecuciones, seleccione una de las ejecuciones recientes cuyo destino era **local** y cuyo nombre de script era **iris_sklearn.py**. 

   c. Se abre la página **Run Properties** (Propiedades de ejecución). En la sección superior derecha del panel, observe la sección **Outputs** (Salidas).

   d. Para descargar el archivo pickle, active la casilla junto al archivo **model.pkl** y, luego, seleccione **Download** (Descargar). Guárdelo en la raíz de la carpeta del proyecto. El archivo es necesario en los próximos pasos.

   ![Descarga del archivo pickle](media/tutorial-classifying-iris/download_model.png)

   Obtenga más información sobre la carpeta `outputs` en el artículo [Lectura y escritura de archivos de datos de gran tamaño](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Obtención de los archivos de esquema y de script de puntuación
Para implementar el servicio web junto con el archivo de modelo, también necesita un script de puntuación. Opcionalmente, necesitará un esquema para los datos de entrada del servicio web. El script de puntuación carga el archivo **model.pkl** de la carpeta actual y lo usa para generar nuevas predicciones.

1. Abra la aplicación Machine Learning Workbench y abra el proyecto **myIris** que ha creado en la parte anterior de la serie de tutoriales.

2. Después de que el proyecto esté abierto, haga clic en el icono de carpeta **Files** (Archivos), en el panel izquierdo, para abrir la lista de archivos de la carpeta del proyecto.

3. Seleccione el archivo **score_iris.py**. Se abre el script de Python. Este archivo se usa como el archivo de puntuación.

   ![Archivo de puntuación](media/tutorial-classifying-iris/model_data_collection.png)

4. Para obtener el archivo de esquema, ejecute el script. Seleccione el entorno **local** y el script **score_iris.py** en la barra de comandos y, luego, seleccione **Run** (Ejecutar). 

5. Este script crea un archivo JSON en la carpeta **Outputs** (Salidas), que captura el esquema de datos de entrada requerido por el modelo.

6. Observe el panel **Jobs** (Trabajos) en el lado derecho de **Project Dashboard** (Panel del proyecto). Espere a que el último trabajo denominado **score_iris.py** aparezca con el estado **Completed** (Completado) en verde. A continuación, seleccione el hipervínculo **score_iris.py** correspondiente a la última ejecución del trabajo para ver los detalles de dicha ejecución. 

7. En la página **Run Properties** (Propiedades de ejecución), en la sección **Salidas**, seleccione el archivo recién creado **service_schema.json**. Active la casilla situada junto al nombre de archivo y, a continuación, seleccione **Download** (Descargar). Guárdelo en la carpeta raíz del proyecto.

8. Vuelva a la pestaña anterior donde abrió el script **score_iris.py**. Mediante la recopilación de datos, puede capturar entradas y predicciones de modelos desde el servicio web. Los pasos siguientes son de especial interés para la recopilación de datos.

9. Examine el código de la parte superior del archivo, que importa la clase **ModelDataCollector**, ya que contiene la funcionalidad de recopilación de datos del modelo:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Revise las siguientes líneas de código de la función **init()**, que crea instancias de **ModelDataCollector**:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
    ```

11. Revise las siguientes líneas de código de la función **run(input_df)**, que recopila los datos de entrada y predicción:

    ```python
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

Ya está listo para preparar el entorno y poner el modelo en funcionamiento.

## <a name="prepare-to-operationalize-locally"></a>Preparación para la operacionalización local
Use la implementación _modo local_ para que se ejecute en contenedores de Docker en su equipo local.

Puede usar el _modo local_ para desarrollo y pruebas. El motor de Docker debe ejecutarse localmente para completar los pasos siguientes y poner el modelo en funcionamiento. Puede usar la marca `-h` al final de cada comando para mostrar el mensaje de ayuda correspondiente.

>[!NOTE]
>Aunque no tenga ningún motor de Docker en el entorno local, puede continuar, solo debe crear un clúster en Azure para la implementación. Simplemente, asegúrese de eliminar el clúster cuando finalice el tutorial para que no se le apliquen los cargos actuales.

1. Abra la interfaz de la línea de comandos (CLI).
   En la aplicación Machine Learning Workbench, en el menú **File** (Archivo), seleccione **Open Command Prompt** (Abrir símbolo del sistema).

   Se abrirá el símbolo de la línea de comandos en la ubicación actual de la carpeta del proyecto **c:\temp\myIris>**.

2. Cree el entorno. Debe ejecutar este paso una vez por cada entorno. Por ejemplo, ejecútelo una vez para el entorno de desarrollo y otra vez para producción. Use el _modo local_ para este primer entorno. Puede probar los modificadores `-c` o `--cluster` en el siguiente comando para configurar un entorno en _modo clúster_posteriormente.

   El siguiente comando de configuración requiere que tenga acceso de colaborador a la suscripción. Si no lo tiene, al menos necesita tener acceso de colaborador al grupo de recursos en el que va a realizar la implementación. En ese caso, es preciso que especifique el nombre del grupo de recursos como parte del comando de configuración mediante la marca `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Siga las instrucciones en pantalla para aprovisionar una cuenta de almacenamiento para guardar las imágenes de Docker, una instancia de Azure Container Registry para enumerar imágenes de Docker y una cuenta de Azure Application Insights para recopilar la telemetría. Si utiliza el modificador `-c`, el comando además creará un clúster de Container Service.
   
   El nombre del clúster es una forma de identificar el entorno. La ubicación debe ser la misma que la ubicación de la cuenta de Administración de modelos que creó en Azure Portal.

   Para asegurarse de que dicho entorno se configura correctamente, use el siguiente comando para comprobar el estado:

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   Asegúrese de que "Estado de aprovisionamiento" tiene el valor "Correcto" (tal y como se muestra) antes de configurar el entorno en el paso 5:

   ![Estado de aprovisionamiento](media/tutorial-classifying-iris/provisioning_state.png)
 
3. Si no ha creado una cuenta de Administración de modelos en alguna de las partes anteriores de este tutorial, hágalo ahora. Esta es una instalación que solo se realiza una vez.
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. Establezca la cuenta de Administración de modelos.
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. Configure el entorno.

   Una vez finalizada la instalación, use el siguiente comando para establecer las variables de entorno necesarias para hacer funcionar el entorno. Use el mismo nombre de entorno que usó anteriormente en el paso 2. Use el mismo nombre de grupo de recursos que obtuvo en la ventana de comandos cuando se completó el proceso de instalación.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. Para comprobar que ha configurado correctamente su entorno de operacionalización para la implementación del servicio web local, escriba el siguiente comando:

   ```azurecli
   az ml env show
   ```

Ya está listo para crear el servicio web en tiempo real.

>[!NOTE]
>Puede usar de nuevo la cuenta y el entorno de Administración de modelos en las implementaciones sucesivas de servicios web. No es necesario crearlos para cada servicio web. Una cuenta o un entorno pueden tener asociados varios servicios web.

## <a name="create-a-real-time-web-service-in-one-command"></a>Creación de un servicio web en tiempo real en un comando
1. Use el comando siguiente para crear un servicio web en tiempo real:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   Este comando genera un identificador del servicio web que puede usar más adelante.

   Los siguientes conmutadores se utilizan con el comando **az ml service create realtime**:

   * `-f`: el nombre de archivo del script de puntuación.

   * `--model-file`: el archivo de modelo. En este caso, es el archivo model.pkl de pickle.

   * `-s`: el esquema del servicio. Se generó en el paso anterior mediante la ejecución del script **score_iris.py** en un entorno local.

   * `-n`: el nombre de la aplicación, que debe estar en minúsculas.

   * `-r`: entorno de tiempo de ejecución del modelo. En este caso, es un modelo de Python. Los entornos de tiempo de ejecución válidos son `python` y `spark-py`.

   * `--collect-model-data true`: este modificador habilita la recopilación de datos.

   * `-c`: ruta de acceso al archivo de dependencias de conda en el que se especifican los paquetes adicionales.

   >[!IMPORTANT]
   >El nombre del servicio, que también es el nuevo nombre de la imagen de Docker, debe estar en minúsculas. De lo contrario, obtiene un error. 

2. Al ejecutar el comando, el modelo y los archivos de puntuación se cargan en la cuenta de almacenamiento que creó como parte de la configuración del entorno. El proceso de implementación compila una imagen de Docker con el modelo, el esquema y el archivo de puntuación que contiene, y lo inserta en la instancia de Azure Container Registry: **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   El comando extrae la imagen localmente en el equipo y luego inicia un contenedor de Docker basado en esa imagen. Si su entorno está configurado en modo de clúster, el contenedor de Docker se implementa en su lugar en el clúster de Kubernetes de Azure Cloud Services.

   Como parte de la implementación, se crea un punto de conexión de REST de HTTP para el servicio web en la máquina local. Después de unos minutos, el comando debe finalizar con un mensaje de confirmación. El servicio web está listo para la acción.

3. Para ver el contenedor de Docker en ejecución, use el comando **docker ps**:

   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Creación de un servicio web en tiempo real mediante comandos independientes
Como alternativa al comando **az ml service create realtime** mostrado anteriormente, también puede realizar los pasos por separado. 

En primer lugar, registre el modelo. A continuación, genere el manifiesto, compile la imagen de Docker y cree el servicio web. Este enfoque le proporciona mayor flexibilidad en cada paso. Además, puede volver a usar las entidades generadas en los pasos anteriores y recompilar las entidades solo cuando sea necesario.

1. Proporcione el nombre del archivo pickle para registrar el modelo.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Este comando genera un identificador de modelo.

2. Cree un manifiesto.

   Para crear un manifiesto, use el siguiente comando y proporcione el identificador de modelo del paso anterior:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   Este comando genera un identificador de manifiesto.

3. Cree una imagen de Docker.

   Para crear una imagen de Docker, use el siguiente comando y especifique la salida del valor del identificador de manifiesto del paso anterior. Opcionalmente, también puede incluir las dependencias de conda mediante el modificador `-c`.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
   ```
   Este comando genera un identificador de imagen de Docker.
   
4. Cree el servicio.

   Para crear un servicio, use el siguiente comando y proporcione el valor del identificador de imagen del paso anterior:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Este comando genera un identificador de servicio web.

Ya está preparado para ejecutar el servicio web.

## <a name="run-the-real-time-web-service"></a>Ejecutar un servicio web en tiempo real.

Para probar el servicio web **irisapp** en ejecución, use un registro codificado en JSON que contenga una matriz de cuatro números aleatorios.

1. El servicio web incluye datos de ejemplo. Cuando se realiza la ejecución en modo local, puede llamar al comando **az ml service usage realtime**. Dicha llamada recupera un comando de ejecución de ejemplo que se puede usar para probar el servicio. La llamada también recupera la dirección URL de puntuación que puede usar para incorporar el servicio a su propia aplicación personalizada.

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. Para probar el servicio, ejecute el comando de ejecución del servicio devuelto:
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```

   La salida es **"Iris-setosa"**, que es la clase prevista. (Es posible que su resultado sea diferente). 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Visualización de los datos recopilados en Azure Blob Storage

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Busque todas las cuentas de almacenamiento. Para ello, seleccione **Todos los servicios**.

3. En el cuadro de búsqueda, escriba **Cuentas de almacenamiento** y, a continuación, seleccione Entrar.

4. En el cuadro de búsqueda **Cuentas de almacenamiento**, seleccione el recurso **Cuenta de almacenamiento** que coincida con su entorno. 

   > [!TIP]
   > Para determinar qué cuenta de almacenamiento está en uso:
   > 1. Abra Machine Learning Workbench.
   > 2. Seleccione el proyecto en el que está trabajando.
   > 3. Abra un símbolo de la línea de comandos desde el menú **File** (Archivo).
   > 4. En el símbolo de la línea de comandos, escriba `az ml env show -v` y compruebe el valor de *storage_account*. Este es el nombre de la cuenta de almacenamiento.

5. Cuando se abra el panel **Storage account** (Cuenta de almacenamiento), seleccione **Blobs** en la sección **Services** (Servicios). Busque el contenedor denominado **modeldata**. 
 
   Si no ve los datos, es probable que deba esperar hasta 10 minutos tras la primera solicitud de servicio web para ver que los datos se propagan a la cuenta de almacenamiento.

   Los datos fluyen en blobs con la siguiente ruta de acceso del contenedor:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Puede consumir estos datos desde Azure Blob Storage. Hay una gran variedad de herramientas que usan el software de Microsoft y herramientas de código abierto como:

   * Machine Learning: abra el archivo CSV y agréguelo como un origen de datos.

   * Excel: abra los archivos CSV diarios como una hoja de cálculo.

   * [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): cree gráficos con datos extraídos de los datos CSV en blobs.

   * [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): cargue los datos CSV en una tabla de Hive y ejecute las consultas SQL directamente en los blobs.

   * [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): cree un DataFrame con una gran parte de los datos CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes
En esta tercera parte de la serie de tutoriales de tres partes, ha aprendido a usar Machine Learning para:
> [!div class="checklist"]
> * Buscar el archivo de modelo
> * Generar un archivo de esquema y de script de puntuación
> * Preparar el entorno
> * Crear un servicio web en tiempo real
> * Ejecutar un servicio web en tiempo real
> * Examinar los datos de blob de salida 

Ha ejecutado correctamente un script de aprendizaje en varios entornos de proceso. También ha creado, serializado y operacionalizado un modelo a través de un servicio web basado en Docker. 

Ahora está listo para realizar la preparación avanzada de datos:
> [!div class="nextstepaction"]
> [Tutorial 4: preparación avanzada de datos](tutorial-bikeshare-dataprep.md)
