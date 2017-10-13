---
title: "Cómo leer y escribir archivos de datos de gran tamaño | Microsoft Docs"
description: "Cómo leer y escribir archivos de gran tamaño de experimentos de Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: fb3158ef786ad73440a59c07b38476a98ced0768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="persisting-changes-and-dealing-with-large-files"></a>Guardar los cambios y trabajar con archivos de gran tamaño

## <a name="execution-isolation-portability-and-reproducibility"></a>Aislamiento de ejecución, portabilidad y reproducibilidad
El servicio de experimentación de Azure Machine Learning le permite configurar distintos objetivos de ejecución. Estos objetivos pueden ser locales, como un equipo local o un contenedor de Docker en un equipo local, o remotos, como un contenedor de Docker en una máquina remota o un clúster de HDInsight. Consulte el artículo acerca de la [ejecución de la experimentación de configuración](experiment-execution-configuration.md) para obtener más detalles. Antes de que se produzca cualquier ejecución, la carpeta del proyecto se copia en ese destino de proceso. Esto es cierto incluso en el caso de una ejecución local que usa una carpeta temporal local para este propósito. 

El propósito de este diseño es garantizar la portabilidad, la reproducibilidad y el aislamiento de la ejecución. Si ejecuta la misma secuencia de comandos dos veces, en el mismo destino de proceso o en otro, se garantiza que recibirá los mismos resultados. Los cambios realizados en la primera ejecución no deberían afectar a la segunda. Con este diseño, se pueden tratar destinos de proceso como recursos de cálculo sin estado y sin afinidad con los trabajos que se ejecutan una vez que se finalizan.

## <a name="challenges"></a>Desafíos
Aparte de las ventajas de portabilidad y de repetibilidad, este diseño también aporta algunos desafíos únicos:
### <a name="persisting-state-changes"></a>Persistencia de los cambios de estado
Si la secuencia de comandos modifica el estado en el contexto del proceso, los cambios no se conservan para la siguiente ejecución ni se propagan al equipo cliente automáticamente. 

Más concretamente, si la secuencia de comandos crea una subcarpeta o escribe un archivo, no encontrará dicha carpeta ni archivo en el directorio del proyecto después de la ejecución. Sin embargo, sí que los encontrará en una carpeta temporal en el entorno de destino del proceso donde quiera que se encuentre. Se pueden usar para fines de depuración, pero nunca se puede depender de su existencia.

### <a name="dealing-with-large-files-in-project-folder"></a>Trabajar con archivos de gran tamaño en la carpeta del proyecto

Si la carpeta del proyecto contiene archivos de gran tamaño, la latencia se produce cuando la carpeta del proyecto se copia en el entorno de proceso de destino al principio de cada ejecución. Aunque la ejecución ocurre localmente, debe evitarse el tráfico de disco innecesario. Esta es la razón por la que actualmente se limita el tamaño máximo del proyecto a 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Opción 1: Usar la carpeta de resultados
Esta es la opción preferida si la secuencia de comandos genera archivos que quiere que cambien con cada ejecución experimental, y quiere mantener un historial de los mismos. El caso de uso más común es si entrena un modelo, crea un conjunto de datos o traza un gráfico como un archivo de imagen que forma parte de la ejecución de aprendizaje del modelo. También si desea comparar los resultados de las ejecuciones y quiere volver atrás para seleccionar un archivo de salida (por ejemplo, un modelo) generado en una ejecución anterior y usarlo para una tarea posterior (por ejemplo, una tarea de puntuación).

Básicamente, puede escribir archivos en una carpeta denominada `outputs` relativa al directorio raíz. Se trata de una carpeta especial que recibe un tratamiento especial por parte del Servicio de experimentación. Nada de lo que cree la secuencia de comandos ahí durante la ejecución, como un archivo de modelo, un archivo de datos o un archivo de imagen trazada (denominados colectivamente _artefactos_), se copiará en la cuenta de Azure Blob Storage asociada a la cuenta de experimentación una vez finalizada la ejecución. Pero sí que pasará a formar parte del registro del historial de ejecución.

Este es un ejemplo rápido para almacenar un modelo en la carpeta `outputs`:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Puede descargar cualquier _artefacto_ si explora la sección **Archivos de salida** de la página de detalles de la ejecución de una determinada ejecución en Azure Machine Learning Workbench, la selecciona y hace clic en el botón **Descargar**. O bien, puede usar el comando `az ml asset download` desde la ventana de CLI.

Para ver un ejemplo más completo, consulte la secuencia de comandos `iris_sklearn.py` de Python en el proyecto de ejemplo _Classifying Iris_ (Clasificación de Iris).

## <a name="option-2-use-the-shared-folder"></a>Opción 2: Configurar la carpeta compartida
Usar una carpeta compartida a la que se puede obtener acceso a través de ejecuciones independientes puede ser una buena opción para los escenarios siguientes, siempre y cuando no sea necesario mantener un historial de cada una de las ejecuciones de estos archivos: 
- La secuencia de comandos necesita cargar datos de archivos locales, como archivos csv o un directorio de archivos de texto o imagen, como datos de prueba o aprendizaje. 
- La secuencia de comandos procesa datos sin formato y escribe los resultados intermedios, como los datos de formación con características de archivos de texto o imagen, para usarlos en una ejecución de formación posterior. 
- Su secuencia de comandos genera un modelo que debe retomar la secuencia de comandos de puntuación posterior para usarla en la evaluación. 

Una advertencia importante es que la carpeta compartida vive localmente en el destino de proceso que elija. Por lo tanto, esto solo funciona si todas las ejecuciones de la secuencia de comando que hacen referencia a esta carpeta compartida se ejecutan en el mismo destino de proceso, y el destino de proceso no se recicla entre ejecuciones.

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

Para ver un ejemplo más completo, consulte el archivo `iris_sklearn_shared_folder.py` en el proyecto de ejemplo _Classifying Iris_ (Clasificación de iris).

Para poder usar esta función, se deben establecer algunas configuraciones sencillas en el archivo `.compute` que representan el contexto de la ejecución de destino en la carpeta `aml_config`. La ruta de acceso actual a esta carpeta puede variar en función del destino de proceso que elija y el valor que configure.

### <a name="configure-local-compute-context"></a>Configurar un contexto de proceso local

Para habilitar esta función en un contexto de proceso local, solo tiene que agregar la línea siguiente al archivo `.compute` que representa el entorno _local_ (normalmente denominado `local.compute`).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

`~/.azureml/share` es la ruta de acceso predeterminada de la carpeta base. Puede cambiarla a cualquier ruta de acceso absoluta local a la que pueda tener acceso mediante la ejecución de la secuencia de comandos. El nombre de la cuenta de experimentación, el nombre del área de trabajo y el nombre del proyecto se anexan automáticamente al directorio base, que constituye la ruta de acceso completa al directorio compartido. Por ejemplo, los archivos pueden escribirse en la ruta de acceso siguiente, y recuperarse de la misma, si utiliza el valor predeterminado anterior:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-docker-compute-context-local-or-remote"></a>Configurar el contexto de proceso de Docker (local o remoto)
Para habilitar esta función en un contexto de proceso de Docker, debe agregar las dos líneas siguientes al archivo _.compute_ de Docker local o remoto.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>`sharedVolume` debe establecerse en `true` cuando use la variable de entorno `AZUREML_NATIVE_SHARE_DIRECTORY` para obtener acceso a la carpeta compartida, si no la ejecución fallará.

El código que se ejecuta en el contenedor de Docker siempre ve esta carpeta compartida como `/azureml-share/`. La ruta de acceso de esta carpeta tal como la ve el contenedor de Dock no es configurable. Así mismo, tampoco debería depender del nombre de esta carpeta en el código. En su lugar, use siempre el nombre de la variable de entorno `AZUREML_NATIVE_SHARE_DIRECTORY` para dirigirse a esta carpeta. Se asigna a una carpeta local en el contexto de proceso o de equipo host de Docker. El directorio base de esta carpeta local es el valor configurable de la opción `nativeSharedDirectory` del `.compute`. La ruta de acceso local de la carpeta compartida en el equipo host, si usa el valor predeterminado anterior, es la siguiente:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!TIP]
>Observe que la ruta de acceso de la carpeta compartida en el disco local es la misma entre un contexto de proceso local y un contexto de proceso local de Docker. Esto significa que incluso puede compartir archivos entre una ejecución local y una ejecución de Docker local.

Puede colocar datos de entrada directamente en estas carpetas y esperar que las ejecuciones locales o de Docker en esa máquina puedan cogerlos directamente. También puede escribir archivos en esta carpeta desde las ejecuciones locales o de Docker, y esperar que los archivos se guarden en esa carpeta y sobrevivan al ciclo de vida de la ejecución.

Para obtener más información acerca de los archivos del servicio de ejecución de Azure Machine Learning, consulte este artículo: [Execution Configuration Files](experiment-execution-configuration-reference.md) (Archivos de configuración de ejecución).

>[!NOTE]
>La variable `AZUREML_NATIVE_SHARE_DIRECTORY` del entorno no se admite en el contexto de proceso de HDInsight. Sin embargo, es fácil lograr el mismo resultado utilizando explícitamente una ruta de acceso absoluta de WASB para leer y escribir en el servicio Blob Storage adjunto.

## <a name="option-3-use-external-durable-storage"></a>Opción 3: Usar un almacenamiento duradero externo

Por supuesto, es libre de utilizar almacenamiento duradero externo para persistir el estado durante la ejecución. Esto resulta útil en los escenarios siguientes:
- Los datos de entrada ya están almacenados en un almacenamiento duradero accesible desde el entorno de proceso de destino.
- Estos archivos no tienen que formar parte de los registros del historial de ejecución.
- Sin embargo, deben compartirse por las ejecuciones de los diferentes entornos de proceso.
- y deben sobrevivir al propio contexto de proceso.

Un ejemplo es usar Azure Blob Storage desde el código de Python o PySpark.

Este es un ejemplo rápido acerca de cómo usar Azure Blob Storage desde el código de Python:
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

# Export the mydata.csv file to blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

También presentamos un ejemplo rápido acerca de cómo adjuntar cualquier Azure Blob Storage arbitrario a un tiempo de ejecución de HDI Spark:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusión
Puesto que Azure Machine Learning ejecuta secuencias de comandos mediante la copia de la carpeta completa del proyecto en el contexto del proceso de destino, se debe prestar especial atención a los archivos de entrada, salida e intermediarios de gran tamaño. Puede usar la carpeta `outputs` especial, la carpeta compartida accesible a través de la variable `AZUREML_NATIVE_SHARE_DIRECTORY` del entorno o el almacenamiento duradero externo para las transacciones de archivos grandes. 

## <a name="next-steps"></a>Pasos siguientes
- Consulte [Configuring Experimentation Execution](experiment-execution-configuration-reference.md) (Configuración de la ejecución de experimentación).
- Consulte cómo el proyecto del tutorial [Clasificación de iris](tutorial-classifying-iris-part-1.md) usa la carpeta `outputs` para conservar el modelo entrenado.