---
title: "Lectura y escritura de archivos de datos de gran tamaño | Microsoft Docs"
description: "Lea y escriba archivos de gran tamaño en experimentos de Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: f5c75b95d9019c15bb402313ce7407fa9abb81d4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="persisting-changes-and-working-with-large-files"></a>Guardar los cambios y trabajar con archivos de gran tamaño
Con el servicio Experimentación de Azure Machine Learning, puede configurar diversos destinos de ejecución. Algunos destinos son locales, como un equipo local o un contenedor de Docker en un equipo local. Otros son remotos, como un contenedor de Docker en un equipo remoto o un clúster de HDInsight. Para obtener más información, consulte [Overview of Azure Machine Learning experiment execution service](experimentation-service-configuration.md) (Introducción al servicio de ejecución de experimentos de Azure Machine Learning). 

Para poder ejecutar un destino, debe copiar la carpeta del proyecto en el destino de proceso. Debe hacerlo incluso si se trata de una ejecución local en la que se use una carpeta temporal local para este propósito. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Aislamiento de ejecución, portabilidad y reproducibilidad
El propósito de este diseño es garantizar el aislamiento, la reproducibilidad y la portabilidad de la ejecución. Si ejecuta dos veces el mismo script, en el mismo destino de proceso o en otro, recibirá los mismos resultados. Los cambios realizados durante la primera ejecución no deberían afectar a los realizados durante la segunda. Con este diseño, puede tratar destinos de proceso como recursos de cálculo sin estado, en que ninguno de ellos tendrá afinidad con los trabajos que se ejecutan una vez finalizados.

## <a name="challenges"></a>Desafíos
Aunque este diseño ofrece las ventajas de portabilidad y repetibilidad, también presenta algunos desafíos únicos.

### <a name="persisting-state-changes"></a>Persistencia de los cambios de estado
Si el script modifica el estado del contexto del proceso, los cambios no se conservan para la siguiente ejecución ni se propagan al equipo cliente automáticamente. 

En concreto, si el script crea una subcarpeta o escribe un archivo, dicha carpeta o archivo no estará presente en el directorio del proyecto después de la ejecución. Los archivos se almacenan en una carpeta temporal en el entorno del destino de proceso. Puede usarlos con finalidades de depuración, pero no confíe en que van a estar allí de forma indefinida.

### <a name="working-with-large-files-in-the-project-folder"></a>Trabajar con archivos de gran tamaño en la carpeta del proyecto

Si la carpeta del proyecto contiene archivos de gran tamaño, la latencia se produce cuando la carpeta se copia en el entorno de proceso de destino al principio de una ejecución. Aunque la ejecución ocurra localmente, debe evitarse el tráfico de disco innecesario. Por esta razón, actualmente limitamos el tamaño máximo del proyecto en 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Opción 1: Usar la carpeta de *resultados*
Esta opción es preferible si se dan las siguientes condiciones:
* Su script genera archivos.
* Espera que los archivos cambien con cada experimento.
* Quiere conservar un historial de estos archivos. 

Los casos de uso comunes son:
* Training a model
* Crear un conjunto de datos
* Trazar un gráfico como archivo de imagen como parte de la ejecución del entrenamiento del modelo 

Además, le interesa comparar los resultados de las ejecuciones, seleccionar un archivo de salida (por ejemplo, un modelo) generado en una ejecución anterior y usarlo para una tarea posterior (por ejemplo, una tarea de puntuación).

Puede escribir archivos en una carpeta denominada *resultados* relativa al directorio raíz. La carpeta recibe un tratamiento especial por parte del Servicio de experimentación. Nada de lo que cree el script en la carpeta durante la ejecución, como un archivo de modelo, un archivo de datos o un archivo de imagen trazada (denominados colectivamente _artefactos_), se copiará en la cuenta de Azure Blob Storage asociada a la cuenta de experimentación una vez finalizada la ejecución. Los archivos pasarán a formar parte del registro del historial de ejecución.

Este es un pequeño ejemplo de código para almacenar un modelo en la carpeta *resultados*:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Puede descargar cualquier artefacto explorando la sección **Archivos de salida** de la página de detalles de la ejecución de una determinada ejecución en Azure Machine Learning Workbench. Solo tiene que seleccionar la ejecución y después seleccionar el botón **Descargar**. Otra opción puede ser escribir el comando `az ml asset download` en la ventana de la interfaz de la línea de comandos (CLI).

Para ver un ejemplo más completo, consulte el script `iris_sklearn.py` de Python en el proyecto de ejemplo _Classifying Iris_ (Clasificación de iris).

## <a name="option-2-use-the-shared-folder"></a>Opción 2: Usar la carpeta compartida
Si no necesita conservar un historial de cada uno de los archivos de ejecución, usar una carpeta compartida a la que se pueda obtener acceso a través de ejecuciones independientes puede ser una buena opción para los escenarios siguientes: 
- El script necesita cargar datos de archivos locales, como archivos .csv o un directorio de archivos de texto o imagen, como datos de prueba o entrenamiento. 
- El script procesa datos sin formato y escribe los resultados intermedios, como los datos de aprendizaje con características de archivos de texto o imagen, para usarlos en una ejecución de aprendizaje posterior. 
- El script genera un modelo que debe retomar el script de puntuación posterior para usarlo en la evaluación. 

Tenga en cuenta que la carpeta compartida reside localmente en el destino del proceso seleccionado. Por lo tanto, esta opción solo funciona si todas las ejecuciones del script que hacen referencia a esta carpeta compartida se ejecutan en el mismo destino de proceso, y el destino de proceso no se recicla entre ejecuciones.

La función de carpeta compartida le permite leer y escribir en una carpeta especial identificada por una variable de entorno, `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Ejemplo
Estos son algunos ejemplos de código de Python para usar esta carpeta de uso compartido para leer y escribir un archivo de texto:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Para ver un ejemplo más completo, consulte el archivo *iris_sklearn_shared_folder.py* en el proyecto de ejemplo _Classifying Iris_ (Clasificación de iris).

Para poder usar esta función, se deben establecer algunas configuraciones sencillas en el archivo *.compute* que representan el contexto de la ejecución de destino en la carpeta *aml_config*. La ruta de acceso actual a la carpeta puede variar en función del destino de proceso que elija y el valor que configure.

### <a name="configure-local-compute-context"></a>Configurar un contexto de proceso local

Para habilitar esta función en un contexto de proceso local, agregue la línea siguiente al archivo *.compute* que representa el entorno _local_ (normalmente denominado *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

La ruta ~/.azureml/share es la ruta de acceso predeterminada de la carpeta base. Puede cambiarla a cualquier ruta de acceso absoluta local a la que pueda tener acceso mediante la ejecución del script. El nombre de la cuenta de experimentación, el nombre del área de trabajo y el nombre del proyecto se anexan automáticamente al nombre del directorio base, que constituye la ruta de acceso completa al directorio compartido. Por ejemplo, los archivos pueden escribirse en la ruta de acceso siguiente, y recuperarse de la misma, si utiliza el valor predeterminado anterior:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Configurar el contexto de proceso de Docker (local o remoto)
Para habilitar esta función en un contexto de proceso de Docker, debe agregar las dos líneas siguientes al archivo *.compute* de Docker local o remoto.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>La propiedad **sharedVolumes** debe establecerse en *true* cuando use la variable de entorno `AZUREML_NATIVE_SHARE_DIRECTORY` para obtener acceso a la carpeta compartida. En caso contrario, se produce un error de ejecución.

El código que se ejecuta en el contenedor de Docker siempre ve esta carpeta compartida como */azureml-share/*. La ruta de acceso de esta carpeta tal como la ve el contenedor de Docker no es configurable. No use el nombre de esta carpeta en el código. En su lugar, use siempre el nombre de la variable de entorno `AZUREML_NATIVE_SHARE_DIRECTORY` para hacer referencia a esta carpeta. Se asigna a una carpeta local en el contexto de proceso o de equipo host de Docker. El directorio base de esta carpeta local es el valor configurable de la opción `nativeSharedDirectory` del archivo *.compute*. La ruta de acceso local de la carpeta compartida en el equipo host, si usa el valor predeterminado, es la siguiente:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>La ruta de acceso de la carpeta compartida en el disco local es la misma tanto si se trata de un contexto de proceso local como de un contexto de proceso local de Docker. Esto significa que incluso puede compartir archivos entre una ejecución local y una ejecución de Docker local.

Puede colocar datos de entrada directamente en estas carpetas y esperar que las ejecuciones locales o de Docker en esa máquina puedan tomarlos directamente. También puede escribir archivos en esta carpeta desde las ejecuciones locales o de Docker, y esperar que los archivos se guarden en esa carpeta y sobrevivan al ciclo de vida de la ejecución.

Para obtener más información, consulte [Azure Machine Learning Workbench execution configuration files](experimentation-service-configuration-reference.md) (Archivos de configuración de ejecución de Azure Machine Learning Workbench).

>[!NOTE]
>La variable `AZUREML_NATIVE_SHARE_DIRECTORY` del entorno no se admite en el contexto de proceso de HDInsight. Pero es fácil lograr el mismo resultado usando explícitamente una ruta de acceso de Azure Blob Storage para leer y escribir en el servicio Blob Storage adjunto.

## <a name="option-3-use-external-durable-storage"></a>Opción 3: Usar un almacenamiento duradero externo

Puede usar almacenamiento duradero externo para conservar el estado durante la ejecución. Esta opción es útil en los escenarios siguientes:
- Los datos de entrada ya están almacenados en un almacenamiento duradero accesible desde el entorno de proceso de destino.
- Estos archivos no tienen que formar parte de los registros del historial de ejecución.
- Los archivos deben compartirse entre ejecuciones de diferentes entornos de proceso.
- Los archivos deben poder sobrevivir al propio contexto de proceso.

Una opción es usar Azure Blob Storage desde el código de Python o PySpark. Este es un breve ejemplo:

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

También le ofrecemos un ejemplo rápido para adjuntar cualquier Azure Blob Storage arbitrario a un tiempo de ejecución de HDI Spark:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusión
Puesto que Azure Machine Learning ejecuta scripts mediante la copia de la carpeta completa del proyecto en el contexto de proceso de destino, se debe prestar especial atención a los archivos de entrada, salida e intermediarios de gran tamaño. En el caso de transacciones de archivos de gran tamaño, puede usar la carpeta de resultados especial, la carpeta compartida accesible a través de la variable del entorno `AZUREML_NATIVE_SHARE_DIRECTORY` o el almacenamiento duradero externo. 

## <a name="next-steps"></a>Pasos siguientes
- Consulte el artículo [Azure Machine Learning Workbench execution configuration files](experimentation-service-configuration-reference.md) (Archivos de configuración de ejecución de Azure Machine Learning Workbench).
- Vea cómo para el proyecto del tutorial [Classifying Iris](tutorial-classifying-iris-part-1.md) (Clasificación de iris), se usa la carpeta de salida para conservar el modelo entrenado.
