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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 43b124fc3eb72adc5d299b218c9e16ec83d1a240
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

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

Las [instrucciones paso a paso](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/) comienzan por la guía en la creación y preparación de una cuenta de Azure storage y un clúster de Spark, incluida la transferencia de datos y la instalación de dependencias. A continuación, describe cómo iniciar trabajos de entrenamiento y comparar el rendimiento de los modelos resultantes. Por último, describe cómo aplicar el modelo elegido a un gran conjunto de imágenes en el clúster de Spark y analizar los resultados de la predicción localmente.


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

    ````
    az login
    ```

    You are asked to visit a URL and type in a provided temporary code; the website requests your Azure account credentials.
    
1. When login is complete, return to the CLI and run the following command to determine which Azure subscriptions are available to your Azure account:

    ```
    az account list
    ```

    This command lists all subscriptions associated with your Azure account. Find the ID of the subscription you would like to use. Write the subscription ID where indicated in the following command, then set the active subscription by executing the command:

    ```
    az account set --subscription [subscription ID]
    ```

1. The Azure resources created in this example are stored together in an Azure resource group. Choose a unique resource group name and write it where indicated, then execute both commands to create the Azure resource group:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### Create the storage account

We now create the storage account that hosts project files that must be accessed by the HDInsight Spark.

1. Choose a unique storage account name and write it where indicated in the following `set` command, then create an Azure storage account by executing both commands:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Issue the following command to list the storage account keys:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Record the value of `key1` as the storage key in the following command, then run the command to store the value.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. In your favorite text editor, load the `settings.cfg` file from the Azure Machine Learning Workbench project's "Code" subdirectory, and insert the storage account name and key as indicated. Save and close the `settings.cfg` file.
1. If you have not already done so, download and install the [AzCopy](http://aka.ms/downloadazcopy) utility. Ensure that the AzCopy executable is on your system path by typing "AzCopy" and pressing Enter to show its documentation.
1. Issue the following commands to copy all of the sample data, pretrained models, and model training scripts to the appropriate locations in your storage account:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Expect file transfer to take up to 20 minutes. While you wait, you can proceed to the following section. You may need to open another Command Line Interface through Workbench and redefine the temporary variables there.

#### Create the HDInsight Spark cluster

Our recommended method to create an HDInsight cluster uses the HDInsight Spark cluster Resource Manager template included in the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project.

1. The HDInsight Spark cluster template is the "template.json" file under the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project. By default, the template creates a Spark cluster with 40 worker nodes. If you must adjust that number, open the template in your favorite text editor and replace all instances of "40" with the worker node number of your choice.
    - You may encounter out-of-memory errors if the number of worker nodes you choose is small. To combat memory errors, you may run the training and operationalization scripts on a subset of the available data as described later in this document.
2. Choose a unique name and password for the HDInsight cluster and write them where indicated in the following command. Then create the cluster by issuing the following command:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Your cluster's deployment may take up to 30 minutes (including provisioning and script action execution).

### Prepare the Azure Machine Learning Workbench execution environment

#### Register the HDInsight cluster as an Azure Machine Learning Workbench compute target

Once HDInsight cluster creation is complete, register the cluster as a compute target for your project as follows:

1.  Issue the following command from the Azure Machine Learning Command Line Interface:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    This command adds two files, `myhdi.runconfig` and `myhdi.compute`, to your project's `aml_config` folder.

1. Open the `myhdi.compute` file in your favorite text editor. Modify the `yarnDeployMode: cluster` line to read `yarnDeployMode: client`, then save and close the file.
1. Run the following command to prepare your environment for use:
   ```
   az ml experiment prepare -c myhdi
   ```

#### Install local dependencies

Open a CLI from Azure Machine Learning Workbench and install dependencies needed for local execution by issuing the following command:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## Data acquisition and understanding

This scenario uses publicly available aerial imagery data from the [National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) at 1-meter resolution. We have generated sets of 224 pixel x 224 pixel PNG files cropped from the original NAIP data and sorted according to land use labels from the [National Land Cover Database](https://www.mrlc.gov/nlcd2011.php). A sample image with label "Developed" is shown at full size:

![A sample tile of developed land](media/scenario-aerial-image-classification/sample-tile-developed.png)

Class-balanced sets of ~44k and 11k images are used for model training and validation, respectively. We demonstrate model deployment on a ~67k image set tiling Middlesex County, MA -- home of Microsoft's New England Research and Development (NERD) center. For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Location of Middlesex County, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

During setup, the aerial image sets used in this example were transferred to the storage account that you created. The training, validation, and operationalization images are all 224 pixel x 224 pixel PNG files at a resolution of one pixel per square meter. The training and validation images have been organized into subfolders based on their land use label. (The land use labels of the operationalization images are unknown and in many cases ambiguous; some of these images contain multiple land types.) For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

To view example images in your Azure storage account (optional):
1. Log in to the [Azure portal](https://portal.azure.com).
1. Search for the name of your storage account in the search bar along the top of your screen. Click on your storage account in the search results.
2. Click on the "Blobs" link in the storage account's main pane.
3. Click on the container named "train." You should see a list of directories named according to land use.
4. Click on any of these directories to load the list of images it contains.
5. Click on any image and download it to view the image.
6. If desired, click on the containers named "test" and "middlesexma2016" to view their contents as well.

## Modeling

### Training models with MMLSpark
The `run_mmlspark.py` script in the "Code\02_Modeling" subfolder of the Workbench project is used to train an [MMLSpark](https://github.com/Azure/mmlspark) model for image classification. The script first featurizes the training set images using an image classifier DNN pretrained on the ImageNet dataset (either AlexNet or an 18-layer ResNet). The script then uses the featurized images to train an MMLSpark model (either a random forest or a logistic regression model) to classify the images. The test image set is then featurized and scored with the trained model. The accuracy of the model's predictions on the test set is calculated and logged to Azure Machine Learning Workbench's run history feature. Finally, the trained MMLSpark model and its predictions on the test set are saved to blob storage.

Select a unique output model name for your trained model, a pretrained model type, and an MMLSpark model type. Write your selections where indicated in the following command template, then begin retraining by executing the command from an Azure ML Command Line Interface:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

An additional `--sample_frac` parameter can be used to train and test the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of trained model accuracy. For more information on this and other parameters, run `python Code\02_Modeling\run_mmlspark.py -h`.

Users are encouraged to run this script several times with different input parameters. The performance of the resulting models can then be compared in Azure Machine Learning Workbench's Run History feature.

### Comparing model performance using the Workbench Run History feature

After you have executed two or more training runs of either type, navigate to the Run History feature in Workbench by clicking the clock icon along the left-hand menu bar. Select `run_mmlspark.py` from the list of scripts at left. A pane loads comparing the test set accuracy for all runs. To see more detail, scroll down and click on the name of an individual run.

## Deployment

To apply one of your trained models to aerial images tiling Middlesex County, MA using remote execution on HDInsight, insert your desired model's name into the following command and execute it:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

An additional `--sample_frac` parameter can be used to operationalize the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of prediction completeness. For more information on this and other parameters, run `python Code\03_Deployment\batch_score_spark -h`.

This script writes the model's predictions to your storage account. The predictions can be examined as described in the next section.

## Visualization

The "Model prediction analysis" Jupyter notebook in the "Code\04_Result_Analysis" subfolder of the Workbench project visualizes a model's predictions. Load and run the notebook as follows:
1. Open the project in Workbench and click on the folder ("Files") icon along the left-hand menu to load the directory listing.
2. Navigate to the "Code\04_Result_Analysis" subfolder and click on the notebook named "Model prediction analysis." A preview rendering of the notebook should be displayed.
3. Click "Start Notebook Server" to load the notebook.
4. In the first cell, enter the name of the model whose results you would like to analyze where indicated.
5. Click on "Cell -> Run All" to execute all cells in the notebook.
6. Read along with the notebook to learn more about the analyses and visualizations it presents.

## Cleanup
When you have completed the example, we recommend that you delete all of the resources you have created by executing the following command from the Azure Command Line Interface:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## References

- [The Embarrassingly Parallel Image Classification repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Describes dataset construction from freely available imagery and labels
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub repository
   - Contains additional examples of model training and evaluation with MMLSpark

## Conclusions

Azure Machine Learning Workbench helps data scientists easily deploy their code on remote compute targets. In this example, local code was deployed for remote execution on an HDInsight cluster. Azure Machine Learning Workbench's run history feature tracked the performance of multiple models and helped us identify the most accurate model. Workbench's Jupyter notebooks feature helped visualize our models' predictions in an interactive, graphical environment.

## Next steps
To dive deeper into this example:
- In Azure Machine Learning Workbench's Run History feature, click the gear symbols to select which graphs and metrics are displayed.
- Examine the sample scripts for statements calling the `run_logger`. Check that you understand how each metric is being recorded.
- Examine the sample scripts for statements calling the `blob_service`. Check that you understand how trained models and predictions are stored and retrieved from the cloud.
- Explore the contents of the containers created in your blob storage account. Ensure that you understand which script or command is responsible for creating each group of files.
- Modify the training script to train a different MMLSpark model type or to change the model hyperparameters. Use the run history feature to determine whether your changes increased or decreased the model's accuracy.

