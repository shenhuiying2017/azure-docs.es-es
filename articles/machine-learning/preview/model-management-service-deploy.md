---
title: "Implementación de servicio web de Administración de modelos de Azure Machine Learning| Microsoft Docs"
description: "En este documento se describen los pasos necesarios para implementar un modelo de aprendizaje automático con Administración de modelos de Azure Machine Learning."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: 965e33f3c7d050dca8f6c4e92d75cb7c7a8fa60d
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Implementación de un modelo de Machine Learning como un servicio web

Administración de modelos de Azure Machine Learning proporciona interfaces para implementar modelos como servicios web basados en Docker en contenedor. Puede implementar modelos creados con plataformas como Spark, Microsoft Cognitive Toolkit (CNTK), Keras, Tensorflow y Python. 

En este documento se describen los pasos para implementar los modelos como servicios web mediante la interfaz de la línea de comandos (CLI) de Administración de modelos de Azure Machine Learning.

## <a name="deploying-web-services"></a>Implementación de servicios web
Con las CLI, puede implementar servicios web para que se ejecuten en el equipo local o en un clúster.

Se recomienda empezar por una implementación local. Valide primero el funcionamiento del modelo y el código y, a continuación, implemente el servicio web en un clúster para su uso en la escala de producción. Para más información sobre cómo configurar el entorno para la implementación de clústeres, consulte [Model Management configuration](deployment-setup-configuration.md) (Configuración de Administración de modelos). 

Estos son los pasos de implementación:
1. Usar el modelo de Machine Learning guardado y entrenado
2. Crear un esquema para los datos de entrada y salida del servicio web
3. Crear una imagen de contenedor basada en Docker
4. Crear e implementar el servicio web

### <a name="1-save-your-model"></a>1. Guardar el modelo
Comience con su archivo de modelo entrenado guardado, por ejemplo, mymodel.pkl. La extensión de archivo varía según la plataforma que use para entrenar y guardar el modelo. 

Por ejemplo, puede usar la biblioteca de Pickle de Python para guardar un modelo entrenado en un archivo. A continuación se muestra un [ejemplo](http://scikit-learn.org/stable/modules/model_persistence.html) con el conjunto de datos Iris:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Crear un archivo schema.json
Este paso es opcional. 

Cree un esquema para validar automáticamente la entrada y salida de su servicio web. La CLI también usa el esquema para generar un documento de Swagger para el servicio web.

Para crear el esquema, importe las bibliotecas siguientes:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
A continuación, defina las variables de entrada como una trama de datos de Spark, una trama de datos de Pandas o una matriz de NumPy. En el ejemplo siguiente se utiliza una matriz de Numpy:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```
En el ejemplo siguiente se utiliza una trama de datos de Spark:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

En el ejemplo siguiente se utiliza una trama de datos de Pandas:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Crear un archivo score.py
Proporciona un archivo score.py, que carga el modelo y devuelve los resultados de predicción utilizando el modelo.

El archivo debe incluir dos funciones: init y run.

Agregue el siguiente código en la parte superior del archivo score.py para habilitar la funcionalidad de recopilación de datos que le ayuda a recopilar datos de entrada y de predicción del modelo

```python
from azureml.datacollector import ModelDataCollector
```

Consulte la sección sobre la [recopilación de datos de modelo](how-to-use-model-data-collection.md) para obtener más información sobre cómo usar esta característica.

#### <a name="init-function"></a>Función init
Utilice la función init para cargar el modelo guardado.

Ejemplo de una sencilla función init que carga el modelo:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Función run
La función run utiliza el modelo y los datos de entrada para devolver una predicción.

Ejemplo de una sencilla la función run que procesa la entrada y devuelve el resultado de la predicción:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. Registrar un modelo
El comando siguiente se usa para registrar un modelo creado en el paso 1 anterior,

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Cree el manifiesto.
El comando siguiente le permiten crear un manifiesto para el modelo,

```
az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]
```
Puede agregar un modelo previamente registrado al manifiesto mediante el argumento `--model-id` o `-i` en el comando mostrado anteriormente. Se pueden especificar varios modelos con argumentos -i adicionales.

### <a name="6-create-an-image"></a>6. Crear una imagen 
Puede crear una imagen con la opción de haber creado su manifiesto antes. 

```
az ml image create -n [image name] -manifest-id [the manifest ID]
```

O bien, puede crear el manifiesto y la imagen con un único comando. 

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Para obtener más detalles sobre los parámetros del comando, escriba -h al final del comando, por ejemplo, az ml image create -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Crear e implementar el servicio web
Implemente el servicio mediante el comando siguiente:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>También puede utilizar un único comando para realizar los cuatro pasos anteriores. Use -h con el comando service create para obtener más detalles.

### <a name="8-test-the-service"></a>8. Probar el servicio
Utilice el comando siguiente para obtener información sobre cómo llamar al servicio:

```
az ml service usage realtime -i <service id>
```

Llame al servicio utilizando la función run desde el símbolo del sistema:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

En el ejemplo siguiente se llama a un servicio web Iris de ejemplo:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>pasos siguientes
Ahora que ha probado el servicio web para que se ejecute localmente, se puede implementar en un clúster para su uso a gran escala. Para más información sobre cómo configurar un clúster para la implementación del servicio web, consulte [Model Management configuration](deployment-setup-configuration.md) (Configuración de Administración de modelos). 
