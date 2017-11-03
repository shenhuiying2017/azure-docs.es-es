---
title: "Clasificación de imágenes aéreas | Microsoft Docs"
description: "Proporciona instrucciones para un escenario real de clasificación de imágenes aéreas"
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 09/15/2017
ms.openlocfilehash: e6b673527d77550d4213e6d742156ccc525f6b44
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="aerial-image-classification"></a>Clasificación de imágenes aéreas

Este ejemplo muestra cómo utilizar Azure Machine Learning Workbench para la coordinación de entrenamiento distribuido y la operacionalización de modelos de clasificación de imagen. Usamos el paquete [Microsoft Machine Learning para Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) para caracterizar imágenes con modelos CNTK previamente entrenados y para entrenar clasificadores utilizando las características derivadas. A continuación, se aplican los modelos entrenados de forma paralela a grandes conjuntos de imágenes en la nube. Estos pasos se realizan en un clúster de [Spark de Azure HDInsight](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/), lo que nos permite escalar la velocidad de entrenamiento y de operacionalización mediante la agregación o eliminación de nodos de trabajo.

El formato de transferencia de aprendizaje que se muestra tiene ventajas importantes sobre volver a entrenar o ajustar una red neuronal profunda: no requiere proceso GPU, es inherentemente rápido y escalable de forma arbitraria y se ajusta con menos parámetros. Por lo tanto, este método es una excelente opción cuando hay pocos ejemplos de entrenamiento disponibles, como suele ser el caso en casos de uso personalizados. Muchos usuarios informan de que la transferencia de aprendizaje genera modelos de alto rendimiento, lo que les permite evitar redes neuronales profundas entrenadas desde cero con un costo mucho mayor.

El clúster de Spark utilizado en este ejemplo tiene 40 nodos de trabajo y su precio es de aproximadamente 40 $/h. Realizar este tutorial requiere aproximadamente dos horas. Cuando haya terminado, siga las instrucciones de limpieza para eliminar los recursos que ha creado y no incurrir en más cargos.

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería

El repositorio de GitHub público para este escenario real contiene todos los materiales, incluidos ejemplos de código, necesarios para este ejemplo:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Descripción del caso de uso

En este escenario, se entrenan modelos de aprendizaje automático para clasificar el tipo de terreno que se muestra en imágenes aéreas de trazados de 224 metros x 224 metros. Los modelos de clasificación de uso del terreno pueden usarse para realizar el seguimiento de la urbanización, deforestación, pérdida de humedales y otras tendencias medioambientales principales mediante imágenes aéreas recopiladas periódicamente. Hemos preparado conjuntos de imágenes de entrenamiento y validación basados en las imágenes del programa nacional de imágenes agrícolas de EE. UU. y las etiquetas de uso de terrenos publicadas por la base de datos nacional de uso de terrenos de EE. UU. A continuación se muestran imágenes de ejemplo de cada clase de uso del terreno:

![Regiones de ejemplo para cada etiqueta de uso del terreno](media/scenario-aerial-image-classification/example-labels.PNG)

Después de entrenar y validar el modelo de clasificación, se aplicará a imágenes aéreas del condado de Middlesex, MA (Sede del Centro de investigación y desarrollo de Microsoft en Nueva Inglaterra [NERD]) para demostrar cómo se pueden usar estos modelos para estudiar las tendencias en desarrollo urbano.

Para generar un clasificador de imágenes mediante la transferencia de aprendizaje, los científicos de datos a menudo construyen varios modelos con una variedad de métodos y seleccionan el modelo de mayor rendimiento. Azure Machine Learning Workbench puede ayudar a los científicos de datos en la coordinación del entrenamiento en entornos de proceso diferentes, realizar un seguimiento y comparan el rendimiento de los distintos modelos y aplicar el modelo elegido a grandes conjuntos de datos en la nube.

## <a name="scenario-structure"></a>Estructura del escenario

En este ejemplo, los datos de imágenes y los modelos previamente entrenados se almacenan en una cuenta de Azure Storage. Un clúster de Spark de Azure HDInsight lee estos archivos y crea un modelo de clasificación de imágenes mediante MMLSpark. El modelo entrenado y las predicciones, a continuación, se escriben en la cuenta de almacenamiento, donde se pueden analizar y visualizar con Jupyter Notebook en ejecución local. Azure Machine Learning Workbench coordina la ejecución remota de scripts en el clúster de Spark. También realiza un seguimiento de las métricas de precisión para varios modelos entrenados usando distintos métodos, lo que permite al usuario seleccionar el modelo de mayor rendimiento.

![Esquema del escenario real de clasificación de imágenes aéreas](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Estas instrucciones paso a paso le guían en la creación y preparación de una cuenta de Azure Storage y un clúster de Spark, lo que incluye la transferencia de datos y la instalación de dependencias. A continuación, describe cómo iniciar trabajos de entrenamiento y comparar el rendimiento de los modelos resultantes. Por último, describe cómo aplicar el modelo elegido a un gran conjunto de imágenes en el clúster de Spark y analizar los resultados de la predicción localmente.


## <a name="set-up-the-execution-environment"></a>Configuración del entorno de ejecución

Las instrucciones siguientes le guían a través del proceso de configuración del entorno de ejecución de este ejemplo.

### <a name="prerequisites"></a>Requisitos previos
- Una [cuenta de Azure](https://azure.microsoft.com/en-us/free/) (hay disponibles versiones gratuitas de prueba)
    - En este ejemplo se crea un clúster de Spark de HDInsight con 40 nodos de trabajo (168 núcleos en total). Asegúrese de que su cuenta tiene suficientes núcleos disponibles revisando la pestaña "Uso y cuotas" de la suscripción en Azure Portal.
    - Si tiene menos núcleos disponibles, puede modificar la plantilla del clúster de HDInsight para reducir el número de nodos de trabajo aprovisionados. Se proporcionan instrucciones para esto en la sección "Creación del clúster de Spark de HDInsight ".
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - Siga la [Guía rápida de instalación y creación](quickstart-installation.md) para instalar Azure Machine Learning Workbench y crear cuentas de Experimentación y de administración del modelo.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), una utilidad gratuita para coordinar la transferencia de archivos entre cuentas de Azure Storage
    - Compruebe que la carpeta que contiene el ejecutable AzCopy está en la variable de entorno PATH del sistema. (Las instrucciones acerca de cómo modificar las variables de entorno están disponibles [aquí](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp)) Este ejemplo se ha probado en un equipo con Windows 10; debería ser capaz de ejecutarlo desde cualquier equipo Windows, incluidas las instancias de Azure Data Science Virtual Machine. Pueden ser necesarias modificaciones menores (por ejemplo, cambios en rutas de acceso) cuando se ejecuta este ejemplo en macOS.

### <a name="set-up-azure-resources"></a>Configuración de los recursos de Azure

Este ejemplo necesita un clúster de Spark de HDInsight y una cuenta de Azure Storage para alojar los archivos pertinentes. Siga estas instrucciones para crear estos recursos en un nuevo grupo de recursos de Azure:

#### <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Aerial Image Classification" (Clasificación de imágenes aéreas) y seleccione la plantilla
5.  Haga clic en **Crear**
 
#### <a name="create-the-resource-group"></a>Creación del grupo de recursos

1. Desde el proyecto de Azure Machine Learning Workbench, para abrir una interfaz de línea de comandos (CLI), haga clic en Archivo -> Abrir símbolo del sistema.
1. Desde la interfaz de línea de comandos, inicie sesión en la cuenta de Azure ejecutando el comando siguiente:

    ```
    az login
    ```

    Se le pide que visite una dirección URL y que escriba un código temporal proporcionado; el sitio web solicita las credenciales de su cuenta de Azure.
    
1. Una vez completado el inicio de sesión, vuelva a la CLI y ejecute el comando siguiente para determinar qué suscripciones de Azure están disponibles para su cuenta de Azure:

    ```
    az account list
    ```

    Este comando muestra todas las suscripciones asociadas a su cuenta de Azure. Busque el identificador de la suscripción que quiere usar. Escriba el identificador de la suscripción donde se indica en el siguiente comando y luego establezca la suscripción activa mediante la ejecución del comando:

    ```
    az account set --subscription [subscription ID]
    ```

1. Los recursos de Azure creados en este ejemplo se almacenan juntos en un grupo de recursos de Azure. Elija un nombre de grupo de recursos único y escríbalo donde se indica. A continuación, ejecute ambos comandos para crear el grupo de recursos de Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Creación de la cuenta de almacenamiento

Ahora se creará la cuenta de almacenamiento que hospeda los archivos de proyecto a los que se debe acceder mediante HDInsight Spark.

1. Elija un nombre de cuenta de almacenamiento único y escríbalo donde se indica en el siguiente comando `set`. A continuación, cree una cuenta de almacenamiento de Azure mediante la ejecución de ambos comandos:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Emita el siguiente comando para mostrar las claves de la cuenta de almacenamiento:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Registre el valor de `key1` como clave de almacenamiento en el siguiente comando y luego ejecute el comando para almacenar el valor.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. En su editor de texto favorito, cargue el archivo `settings.cfg` del subdirectorio "Code" del proyecto de Azure Machine Learning Workbench e inserte el nombre y la clave de la cuenta de almacenamiento como se indica. Las líneas modificadas del archivo deben tener un aspecto similar al siguiente:
    ```
    [Settings]
        # Credentials for the Azure Storage account
        storage_account_name = storacctname
        storage_account_key = kpI...88 chars total...Q==
    ```
    Guarde y cierre el archivo `settings.cfg`.
1. Si no lo ha hecho ya, descargue e instale la utilidad [AzCopy](http://aka.ms/downloadazcopy). Compruebe que el ejecutable de AzCopy se encuentra en la ruta de acceso del sistema; para ello, escriba "AzCopy" y presione Entrar para mostrar su documentación.
1. Emita los siguientes comandos para copiar todos los datos de ejemplo, modelos ya entrenados y scripts de entrenamiento de modelos en las ubicaciones adecuadas de la cuenta de almacenamiento:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    La transferencia de archivos puede tardar hasta 20 minutos. Mientras espera, puede continuar con la siguiente sección. Puede que deba abrir otra interfaz de la línea de comandos mediante Workbench y volver a definir ahí las variables temporales.

#### <a name="create-the-hdinsight-spark-cluster"></a>Creación del clúster de HDInsight Spark

El método recomendado para crear un clúster de HDInsight es usar la plantilla de Resource Manager del clúster de HDInsight Spark incluida en la subcarpeta "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" de este proyecto.

1. La plantilla del clúster de HDInsight Spark es el archivo "template.json" de la subcarpeta "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" de este proyecto. De forma predeterminada, la plantilla crea un clúster de Spark con 40 nodos de trabajo. Si debe ajustar ese número, abra la plantilla en el editor de texto que prefiera y reemplace todas las instancias de "40" por el número de nodos de trabajo de su elección.
    - Puede encontrar errores de memoria insuficiente si el número de nodos de trabajo que elija es pequeño. Para hacer frente a los errores de memoria, puede ejecutar los scripts de entrenamiento y operacionalización en un subconjunto de los datos disponibles como se describe más adelante en este documento.
2. Elija un nombre único y una contraseña para el clúster de HDInsight y escríbalos donde se ha indicado en el siguiente comando. A continuación, emita el siguiente comando para crear el clúster:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

La implementación del clúster puede tardar hasta 30 minutos (incluidos el aprovisionamiento y la ejecución de acciones de script).

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Preparación del entorno de ejecución de Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Registro del clúster de HDInsight como un destino de proceso de Azure Machine Learning Workbench

Una vez completada la creación del clúster de HDInsight, registre el clúster como destino de proceso para el proyecto como se indica a continuación:

1.  Emita el siguiente comando desde la interfaz de la línea del comandos de Azure Machine Learning:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    Este comando agrega dos archivos, `myhdi.runconfig` y `myhdi.compute`, a la carpeta `aml_config` de su proyecto.

1. Abra el archivo `myhdi.compute` en el editor de texto que prefiera. Modifique la línea `yarnDeployMode: cluster` para que ponga `yarnDeployMode: client` y luego guarde y cierre el archivo.
1. Ejecute el siguiente comando para preparar el entorno para su uso:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Instalación de las dependencias locales

Abra una CLI desde Azure Machine Learning Workbench e instale las dependencias necesarias para la ejecución local con el siguiente comando:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## <a name="data-acquisition-and-understanding"></a>Adquisición y comprensión de los datos

En este escenario se usan datos de imágenes aéreas disponibles públicamente en el [programa nacional de imágenes agrícolas](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) con una resolución de 1 metro. Se han generado conjuntos de archivos PNG de 224 x 224 píxeles recortados de los datos NAIP originales y ordenados de acuerdo con las etiquetas de uso del terreno de la [base de datos nacional de uso de terrenos](https://www.mrlc.gov/nlcd2011.php). Una imagen de ejemplo con la etiqueta "Developed" se muestra en tamaño completo:

![Un icono de ejemplo del terreno desarrollado](media/scenario-aerial-image-classification/sample-tile-developed.png)

Para el entrenamiento y la validación de los modelos, se han empleado conjuntos de imágenes de ~44 k y 11 k, respectivamente, con equilibrio de clase. La implementación del modelo se demuestra en un conjunto de imágenes de ~67 k en mosaico del condado de Middlesex, MA, sede del Centro de investigación y desarrollo de Microsoft en Nueva Inglaterra (NERD). Para más información sobre cómo se construyeron estos conjuntos de imágenes, consulte el [repositorio de Git de clasificación de imágenes embarazosamente paralelas](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Ubicación del condado de Middlesex, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Durante la instalación, los conjuntos de imágenes aéreas usados en este ejemplo se han transferido a la cuenta de almacenamiento que ha creado. Las imágenes de entrenamiento, validación y operacionalización son todas archivos PNG de 224 x 224 píxeles con una resolución de un píxel por metro cuadrado. Las imágenes de entrenamiento y validación se han organizado en subcarpetas en función de su etiqueta de uso del terreno. (Las etiquetas de uso del terreno de las imágenes de operacionalización son desconocidas y, en muchos casos, ambiguas; algunas de estas imágenes contienen varios tipos de terrenos). Para más información sobre cómo se construyeron estos conjuntos de imágenes, consulte el [repositorio de Git de clasificación de imágenes embarazosamente paralelas](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Para ver imágenes de ejemplo en la cuenta de almacenamiento de Azure (opcional), siga estos pasos:
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque el nombre de la cuenta de almacenamiento en la barra de búsqueda de la parte superior de la pantalla. Haga clic en la cuenta de almacenamiento en los resultados de búsqueda.
2. Haga clic en el vínculo "Blob" en el panel principal de la cuenta de almacenamiento.
3. Haga clic en el contenedor llamado "train". Verá una lista de directorios designados según el uso del terreno.
4. Haga clic en cualquiera de estos directorios para cargar la lista de imágenes que contiene.
5. Haga clic en cualquier imagen y descárguela para verla.
6. Si lo desea, haga clic también en los contenedores llamados "test" y "middlesexma2016" para ver su contenido.

## <a name="modeling"></a>Modelado

### <a name="training-models-with-mmlspark"></a>Entrenamiento de modelos con MMLSpark
El script `run_mmlspark.py` de la subcarpeta "Code\02_Modeling" del proyecto de Workbench se usa para entrenar un modelo [MMLSpark](https://github.com/Azure/mmlspark) para la clasificación de imágenes. El primer script caracteriza las imágenes del conjunto de entrenamiento mediante un DNN de clasificador de imágenes entrenado previamente en el conjunto de imágenes de ImageNet (AlexNet o ResNet de 18 capas). El script usa luego las imágenes caracterizadas para entrenar un modelo MMLSpark (un bosque aleatorio o un modelo de regresión logística) para clasificar las imágenes. A continuación, el conjunto de imágenes de prueba se caracteriza y se puntúa con el modelo entrenado. La exactitud de las predicciones del modelo en el conjunto de pruebas se calcula y se registra en la característica de historial de ejecución de Azure Machine Learning Workbench. Por último, el modelo MMLSpark entrenado y sus predicciones sobre el conjunto de pruebas se guardan en Blob Storage.

Seleccione un nombre de modelo de salida único para el modelo entrenado, un tipo de modelo previamente entrenado y un tipo de modelo MMLSpark. Escriba las selecciones donde se indica en la siguiente plantilla de comandos y comience de nuevo el entrenamiento ejecutando el comando desde una interfaz de la línea de comandos de Azure ML:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Se puede usar un parámetro `--sample_frac` adicional para entrenar y probar el modelo con un subconjunto de datos disponibles. Con una pequeña fracción de muestra se reducen el tiempo de ejecución y los requisitos de memoria, pero disminuye la exactitud del modelo entrenado. Para más información sobre este y otros parámetros, ejecute `python Code\02_Modeling\run_mmlspark.py -h`.

Se aconseja a los usuarios que ejecuten este script varias veces con diferentes parámetros de entrada. El rendimiento de los modelos resultantes se puede comparar luego en la característica de historial de ejecución de Azure Machine Learning Workbench.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Comparación del rendimiento de los modelos mediante la característica de historial de ejecución de Workbench

Después de que ha ejecutado dos o más series de entrenamiento de cualquier tipo, vaya a la característica Historial de ejecución de Workbench; para ello, haga clic en el icono de reloj en la barra de menú de la izquierda. Seleccione `run_mmlspark.py` en la lista de scripts de la izquierda. Se carga un panel donde se compara la exactitud del conjunto de pruebas en todas las ejecuciones. Para ver más detalles, desplácese hacia abajo y haga clic en el nombre de una ejecución individual.

## <a name="deployment"></a>Implementación

Para aplicar uno de los modelos entrenados a imágenes aéreas en mosaico del condado de Middlesex, MA mediante la ejecución remota en HDInsight, inserte el nombre del modelo deseado en el siguiente comando y ejecútelo:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Se puede usar un parámetro `--sample_frac` adicional para operacionalizar el modelo con un subconjunto de datos disponibles. Con una pequeña fracción de muestra se reducen el tiempo de ejecución y los requisitos de memoria, pero disminuye la exactitud de la predicción. Para más información sobre este y otros parámetros, ejecute `python Code\03_Deployment\batch_score_spark -h`.

Este script escribe las predicciones del modelo en su cuenta de almacenamiento. Las predicciones se pueden examinar como se describe en la sección siguiente.

## <a name="visualization"></a>Visualización

En el cuaderno de Jupyter "Model prediction analysis" de la subcarpeta "Code\04_Result_Analysis" del proyecto de Workbench se visualizan las predicciones de un modelo. Cargue y ejecute el cuaderno de la manera siguiente:
1. Abra el proyecto en Workbench y haga clic en el icono ("Files") de la carpeta junto al menú izquierdo para cargar el listado de directorios.
2. Vaya a la subcarpeta "Code\04_Result_Analysis" y haga clic en el cuaderno con el nombre "Model prediction analysis". Se muestra una representación en vista previa del cuaderno.
3. Haga clic en "Start Notebook Server" (Iniciar servidor de Notebook) para cargar el cuaderno.
4. En la primera celda, escriba donde se indica el nombre del modelo cuyos resultados desea analizar.
5. Haga clic en "Celda -> Ejecutar todo" para ejecutar todas las celdas del cuaderno.
6. Interprete los resultados conjuntamente con el cuaderno para aprender más sobre los análisis y las visualizaciones que presenta.

## <a name="cleanup"></a>Limpieza
Cuando se haya completado el ejemplo, se recomienda que elimine todos los recursos que ha creado mediante la ejecución del siguiente comando desde la interfaz de la línea de comandos de Azure:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Referencias

- [The Embarrassingly Parallel Image Classification repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification) (El repositorio de clasificación de imágenes embarazosamente paralelas)
   - Describe la construcción de conjuntos de datos de imágenes y etiquetas disponibles de forma gratuita.
- Repositorio de GitHub de[MMLSpark](https://github.com/Azure/mmlspark)
   - Contiene ejemplos adicionales de entrenamiento y evaluación de modelos con MMLSpark

## <a name="conclusions"></a>Conclusiones

Azure Machine Learning Workbench ayuda a los científicos de datos a implementar fácilmente su código en destinos de proceso remotos. En este ejemplo, se implementó código local para la ejecución remota en un clúster de HDInsight. La característica de historial de ejecución de Azure Machine Learning Workbench ha permitido realizar el seguimiento del rendimiento de varios modelos y ha ayudado a identificar el modelo más exacto. La característica de cuadernos de Jupyter de Workbench ha ayudado a visualizar las predicciones de nuestros modelos en un entorno gráfico interactivo.

## <a name="next-steps"></a>Pasos siguientes
Para profundizar más en este ejemplo:
- En la característica de historial de ejecución de Machine Learning Workbench, haga clic en los símbolos de engranaje para seleccionar qué gráficos y métricas se van a mostrar.
- Examine los scripts de ejemplo de las instrucciones mediante la llamada a `run_logger`. Compruebe que comprende cómo se registra cada métrica.
- Examine los scripts de ejemplo de las instrucciones mediante la llamada a `blob_service`. Compruebe que comprende cómo se almacenan los modelos entrenados y las predicciones y cómo se recuperan de la nube.
- Explore el contenido de los contenedores creados en la cuenta de Blob Storage. Asegúrese de que entiende que un script o comando es responsable de crear cada grupo de archivos.
- Modifique el script de entrenamiento para entrenar un tipo de modelo MMLSpark diferente o para cambiar los hiperparámetros del modelo. Utilice la característica de historial de ejecución para determinar si los cambios aumentan o disminuyen la exactitud del modelo.
