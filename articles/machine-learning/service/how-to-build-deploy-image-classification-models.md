---
title: Compilación e implementación de un modelo de clasificación de imágenes con Azure Machine Learning Package for Computer Vision
description: Obtenga información sobre cómo compilar, entrenar, probar e implementar un modelo de clasificación de imágenes de Computer Vision con Azure Machine Learning Package for Computer Vision.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: bd9f01e76c68fa41616818251b5b54553059cbcc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Compilación e implementación de modelos de clasificación de imágenes con Azure Machine Learning

En este artículo, obtenga información sobre cómo usar **Azure Machine Learning Package for Computer Vision** (AMLPCV) para entrenar, probar e implementar un modelo de clasificación de imágenes. 

Es posible solucionar gran cantidad de problemas en el dominio Computer Vision mediante la clasificación de imágenes. Estos problemas incluyen compilar modelos que respondan preguntas como las siguientes:
+ _¿Hay un OBJETO en la imagen? Por ejemplo, "perro", "automóvil", "nave", etc._
+ _¿Qué gravedad de enfermedad ocular revela el escáner de retina de este paciente?_

Al compilar e implementar este modelo con AMLPCV, realiza los pasos siguientes:
1. Creación del conjunto de datos
2. Visualización y anotación de imágenes
3. Aumento de imágenes
4. Definición del modelo de red neuronal profunda (DNN)
5. Entrenamiento de clasificadores
6. Evaluación y visualización
7. Implementación del servicio web
8. Prueba de carga del servicio web

[CNTK](https://www.microsoft.com/cognitive-toolkit/) se usa como el marco de aprendizaje profundo, el entrenamiento se realiza de manera local en una máquina con tecnología GPU, como la máquina virtual de ciencia de datos de aprendizaje profundo ([Deep learning Data Science VM ](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)) y la implementación usa la CLI de operacionalización de Azure ML.

Consulte la [documentación de referencia del paquete](https://aka.ms/aml-packages/vision) para ver la referencia detallada de cada módulo y clase.

## <a name="prerequisites"></a>requisitos previos

1. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

1. Deben estar configuradas e instaladas las siguientes cuentas y la siguiente aplicación:
   - Una cuenta de Experimentación de Azure Machine Learning. 
   - Una cuenta de Administración de modelos de Azure Machine Learning.
   - Azure Machine Learning Workbench instalado.

   Si todavía no se crearon o instalaron las tres, siga las instrucciones que aparecen en el artículo sobre la [instalación de Workbench y el inicio rápido de Azure Machine Learning](../service/quickstart-installation.md). 

1. Azure Machine Learning Package for Computer Vision debe estar instalado. Aprenda a [instalar este paquete aquí](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Notebook y datos de ejemplo

### <a name="get-the-jupyter-notebook"></a>Obtención de Jupyter Notebook

Descargue el Notebook para ejecutar el ejemplo que se describe aquí.

> [!div class="nextstepaction"]
> [Obtención de Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Carga de los datos de ejemplo

En el ejemplo siguiente se usa un conjunto de datos que consta de 63 imágenes de vajilla. Cada imagen está etiquetada como perteneciente a una de cuatro clases distintas (cuencos, tazas, cubiertos, platos). La cantidad de imágenes de este ejemplo es pequeña para que podamos ejecutar rápidamente el ejemplo. En la práctica, se deben proporcionar al menos 100 imágenes por clase. Todas las imágenes se encuentran en *"../sample_data/imgs_recycling/"*, en subdirectorios llamados "bowl", "cup", "cutlery" y "plate".

![Conjunto de datos de Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>Contexto de almacenamiento

El contexto de almacenamiento se usa para determinar dónde se almacenarán los distintos archivos de salida, como las imágenes aumentadas o los archivos del modelo DNN. Para más información sobre los contextos de almacenamiento, consulte la [documentación de StorageContext](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test). 

Por lo general, no es necesario establecer explícitamente el contenido de almacenamiento. Sin embargo, para evitar su límite de 25 MB en el tamaño de proyecto que impone Azure Machine Learning Workbench, establezca el directorio de salida de Azure Machine Learning Package for Computer Vision en una ubicación fuera del proyecto Azure Machine Learning ("../../../../cvtk_output"). Asegúrese de quitar el directorio "cvtk_output" una vez que ya no sea necesario.


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>Crear un conjunto de datos

Cuando haya importado las dependencias y establecido el contexto de almacenamiento, puede crear el objeto de conjunto de datos.

Para crear ese objeto con Azure Machine Learning Package for Computer Vision, proporcione el directorio raíz de las imágenes en el disco local. Este directorio debe seguir la misma estructura general que el conjunto de datos de vajilla, es decir, debe contener subdirectorios con las imágenes reales:
- root
    - label 1
    - label 2
    - ...
    - label n
  
Entrenar un modelo de clasificación de imágenes para otro conjunto de datos es tan sencillo como cambiar la ruta de acceso raíz `dataset_location` en el código siguiente para apuntar a imágenes distintas.


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

El objeto de conjunto de datos proporciona la funcionalidad para descargar imágenes con [Bing Image Search API](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Se admiten dos tipos de consultas de búsqueda: 
+ Consultas de texto normal
+ Consultas de dirección URL de imagen

Estas consultas junto con la etiqueta de clase se deben proporcionar dentro de un archivo de texto codificado en JSON. Por ejemplo: 

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

Además, debe crear explícitamente un objeto de contexto para contener la clave de Bing Image Search API. Esto requiere una suscripción a Bing Image Search API.

## <a name="visualize-and-annotate-images"></a>Visualización y anotación de imágenes

Puede visualizar las imágenes y las etiquetas correctas en el objeto de conjunto de datos con el widget siguiente. 

Si aparece un error que indica que no se detectó JavaScript del widget, ejecute este comando para solucionarlo:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Conjunto de datos de Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Aumento de imágenes

El [`augmentation`módulo](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation) proporciona la funcionalidad para aumentar un objeto de conjunto de datos con todas las transformaciones que se describen en la biblioteca [imgaug](https://github.com/aleju/imgaug). Las transformaciones de imágenes se pueden agregar en una sola canalización, en cuyo caso todas las transformaciones de la canalización se aplican simultáneamente a cada imagen. 

Si quisiera aplicar distintos pasos de aumento por separado o de una manera distinta, puede definir varias canalizaciones y pasarlas a la función *augment_dataset*. Para más información y ejemplos del aumento de imágenes, consulte la [documentación de imgaug](https://github.com/aleju/imgaug).

Agregar imágenes aumentadas al conjunto de entrenamiento resulta especialmente útil para los conjuntos de datos pequeños. Como el proceso de entrenamiento de DNN es más lento debido al mayor número de imágenes de entrenamiento, se recomienda empezar la experimentación sin aumento.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>Definición de los modelos de DNN

Estos modelos de Red neuronal profunda previamente entrenados son compatibles con este paquete: 
+ AlexNet
+ Resnet-18
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

Estas DNN se pueden usar como clasificador o como caracterizador. 

Puede encontrar más información sobre las redes [aquí](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md) y hay disponible una introducción básica al aprendizaje de transferencia [aquí](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

Los parámetros predeterminados de clasificación de imágenes para este paquete son una resolución de 224 x 224 píxeles y una DNN Resnet-18. Estos parámetros se seleccionaron para funcionar correctamente en una amplia variedad de tareas. La precisión a menudo se puede mejorar, por ejemplo, mediante el aumento de la resolución de imágenes a 500 x 500 píxeles o mediante la selección de un modelo más profundo (Resnet-50). Sin embargo, la modificación de los parámetros puede generar un aumento considerable en el tiempo de entrenamiento. Consulte el artículo sobre [cómo mejorar la precisión](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Entrenamiento del clasificador

Puede elegir uno de los métodos siguientes para la DNN previamente entrenada.

  - **Perfeccionamiento de la DNN**, que entrena la DNN para que realice directamente la clasificación. Si bien el entrenamiento de la DNN es lento, habitualmente lleva a los mejores resultados porque todas las ponderaciones de la red se pueden mejorar durante el entrenamiento para una mayor precisión.

  - **Características de la DNN**, que ejecuta la DNN tal cual para obtener una representación dimensional inferior de una imagen (512, 2048 o 4096 flotantes). Luego, esa representación se usa como entrada para entrenar un clasificador independiente. Como la DNN se mantiene sin modificaciones, este enfoque es mucho más rápido en comparación con el perfeccionamiento de la DNN; sin embargo, la precisión no es tan buena. Pero entrenar un clasificador externo, como un SVM lineal (como se muestra en el código siguiente), puede proporcionar una sólida línea de base y ayudar a comprender la viabilidad de un problema.
  
Es posible usar TensorBoard para visualizar el progreso del entrenamiento. Para activar TensorBoard:
1. Agregue el parámetro `tensorboard_logdir=PATH` como se muestra en el código siguiente.
1. Inicie el cliente TensorBoard con el comando `tensorboard --logdir=PATH` en una consola nueva.
1. Abra un explorador web como lo indica TensorBoard que, de manera predeterminada, es localhost:6006. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![png](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Evaluación y visualización del rendimiento del modelo

Puede evaluar el rendimiento del modelo entrenado en cualquier conjunto de datos de prueba independiente mediante el módulo de evaluación. Estas son algunas de las métricas de evaluación que calcula:
 
+ Precisión (de manera predeterminada, en promedio de clases)
+ Curva PR
+ Curva ROC
+ Área bajo la curva
+ Matriz de confusión


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![png](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Conjunto de datos de Azure Machine Learning](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Conjunto de datos de Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Operacionalización: implementación y consumo

La operacionalización es el proceso de publicar modelos y código como servicios web y el consumo de estos servicios para generar resultados empresariales. 

Una vez que el modelo está entrenado, puede implementarlo como servicio web para su consumo mediante la [CLI de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Los modelos se pueden implementar en una máquina local o en un clúster de Azure Container Service (ACS). Con ACS, puede escalar manualmente el servicio web o usar la funcionalidad de escalado automático.

**Inicio de sesión con la CLI de Azure**

Con una cuenta [Azure](https://azure.microsoft.com/) con una suscripción válida, inicie sesión con el comando de CLI siguiente:
<br>`az login`

+ Para cambiar a otra suscripción de Azure, use el comando:
<br>`az account set --subscription [your subscription name]`

+ Para ver la cuenta de administración del modelo actual, use el comando:
  <br>`az ml account modelmanagement show`

**Creación y establecimiento del entorno de implementación**

Solo tiene que establecer el entorno de implementación una vez. Si todavía no tiene uno, configure ahora mismo el entorno de implementación con [estas instrucciones](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Siga los pasos para configurar la implementación local o en clúster correctamente en función de sus necesidades.
+ Las implementaciones locales son compatibles con máquinas Linux y Windows 10, pero no para la máquina virtual de ciencia de datos Windows ni la máquina virtual de aprendizaje profundo. 
+ Las implementaciones de entornos en clúster son compatibles tanto con Linux como con Windows. 

Para ver el entorno de implementación activo, use el comando de CLI siguiente:
<br>`az ml env show`
   
Comando de la CLI de Azure de ejemplo para crear y establecer el entorno de implementación

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Administración de servicios web e implementaciones

Es posible usar las API siguientes para implementar modelos como servicios web, administrar esos servicios web y administrar las implementaciones.

|Task|API|
|----|----|
|Crear objeto de implementación|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Implementar el servicio web|`deploy_obj.deploy()`|
|Puntuar la imagen|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Eliminar el servicio web|`deploy_obj.delete()`|
|Compilar una imagen de Docker sin servicio web|`deploy_obj.build_docker_image()`|
|Mostrar la implementación existente|`AMLDeployment.list_deployment()`|
|Eliminar si existe el servicio con el nombre de la implementación|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Documentación de la API:** consulte la [documentación de referencia del paquete](https://aka.ms/aml-packages/vision) para ver la referencia detallada de cada módulo y clase.

**Referencia de la CLI:** para opciones más avanzadas relativas a la implementación, consulte la [referencia de la CLI de administración de modelos](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Administración de implementación en Azure Portal**: puede hacer seguimiento de las implementaciones y administrarlas en [Azure Portal](https://ms.portal.azure.com/). En Azure Portal, busque la página de su cuenta de Administración de modelos de Machine Learning mediante su nombre. Luego vaya a la página de la cuenta de Administración de modelos > Administración de modelos > Servicios.


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Consumo del servicio web 

Una vez que implementa el modelo como un servicio web, puede puntuar las imágenes con el servicio web mediante uno de estos métodos:

- Puntuación del servicio web directamente con el objeto de implementación mediante `deploy_obj.score_image(image_path_or_url)`

- Uso de la dirección URL del punto de conexión de servicio y la clave de servicio (el valor None es para la implementación local) con: `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Formar las solicitudes HTTP directamente para puntuar el punto de conexión del servicio web. Esta opción es para usuarios avanzados.

### <a name="score-with-existing-deployment-object"></a>Puntuación con objeto de implementación existente

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Puntuación con dirección URL del punto de conexión de servicio y clave de servicio

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>Puntuación de punto de conexión con solicitud HTTP directamente

El código de ejemplo siguiente forma la solicitud HTTP directamente en Python. Sin embargo, puede hacerlo en otros lenguajes de programación.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Análisis del resultado serializado del servicio web

El resultado del servicio web es una cadena JSON. Puede analizar esta cadena JSON con distintas clases de modelo DNN.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre Azure Machine Learning Package for Computer Vision en estos artículos:

+ Obtenga información sobre cómo [mejorar la precisión de este modelo](how-to-improve-accuracy-for-computer-vision-models.md).

+ Lea la [introducción al paquete y aprenda a instalarlo](https://aka.ms/aml-packages/vision).

+ Explore la [documentación de referencia](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) de este paquete.

+ Obtenga información sobre [otros paquetes Python para Azure Machine Learning](reference-python-package-overview.md).
