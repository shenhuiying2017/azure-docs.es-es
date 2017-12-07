---
title: "Archivos de configuración del servicio Experimentación de Azure Machine Learning"
description: "En este documento se detallan los valores de la configuración del servicio Experimentación de Azure Machine Learning."
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 16c72f8c22307a124fdb670aabca771084c0d1ec
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Archivos de configuración del servicio Experimentación de Azure Machine Learning

Cuando se ejecuta un script en Azure Machine Learning (Azure ML) Workbench, el comportamiento de la ejecución se controla mediante archivos de la carpeta **aml_config**. Esta carpeta está bajo la carpeta raíz del proyecto. Es importante entender el contenido de estos archivos para lograr el resultado deseado para la ejecución de forma óptima.

A continuación se encuentran los archivos pertinentes de esta carpeta:
- conda_dependencies.yml
- spark_dependencies.yml
- archivos de destino de proceso
    - \<nombre de destino de proceso>.compute
- archivos de configuración de ejecución
    - \<nombre de configuración de ejecución>.runconfig

>[!NOTE]
>Normalmente, se tiene un archivo de destino de proceso y un archivo de configuración de ejecución para cada destino de proceso que se crea. Sin embargo, puede crear estos archivos de forma independiente y hacer que varios archivos de configuración de ejecución apunten al mismo destino de proceso.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Este archivo es un [archivo de entorno de conda](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) que especifica la versión de runtime de Python y los paquetes de los que depende el código. Cuando Azure ML Workbench ejecuta un script en un contenedor de Docker o clúster de HDInsight, crea un [entorno de conda](https://conda.io/docs/using/envs.html) para que se ejecute el script. 

En este archivo, especifica los paquetes de Python que necesita el script para su ejecución. El servicio Experimentación de Azure Machine Learning crea el entorno de conda en la imagen de Docker según la lista de dependencias. El motor de ejecución debe poder acceder a la lista de paquetes aquí. Por esta razón, los paquetes tienen que enumerarse en canales como:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* un punto de conexión accesible públicamente (URL)
* o una ruta de acceso de archivo local
* otros accesibles por el motor de ejecución

>[!NOTE]
>Cuando se ejecuta en el clúster de HDInsight, Azure ML Workbench crea un entorno de conda solo para su ejecución. Esto permite a los distintos usuarios ejecuten en entornos de Python diferentes en el mismo clúster.  

Este es un ejemplo de un archivo **conda_dependencies.yml** típico.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench usa el mismo entorno de conda sin recompilación, siempre y cuando **conda_dependencies.yml** permanezca intacto. Sin embargo, si cambia algo en este archivo, se recompila la imagen de Docker.

>[!NOTE]
>Si la ejecución tiene como destino el contexto de proceso _local_, el archivo **conda_dependencies.yml** **no** se usa. Las dependencias de paquetes para su entorno de Python local de Azure ML Workbench deben instalarse manualmente.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Este archivo especifica el nombre de la aplicación de Spark cuando envía un script de PySpark y paquetes de Spark que deben instalarse. También puede especificar cualquier repositorio público de Maven, así como paquetes de Spark que pueden encontrarse en esos repositorios de Maven.

Aquí tiene un ejemplo:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Los parámetros de ajuste de clúster, como el tamaño de trabajo, núcleos, deben incluirse en la sección “configuration” del archivo spark_dependecies.yml. 

>[!NOTE]
>Si ejecuta el script en el entorno de Python, se omite el archivo *spark_dependencies.yml*. Solo tiene efecto si se ejecuta frente a Spark (ya sea en Docker o en el clúster de HDInsight).

## <a name="run-configuration"></a>Configuración de ejecución
Para especificar una configuración de ejecución determinada, se necesita un par de archivos en su lugar. Normalmente se generan utilizando un comando CLI. Pero también puede clonar los existentes, cambiarles el nombre y editarlos.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

Este comando crea un par de archivos según el destino de proceso especificado. Supongamos que dio el nombre _foo_ al destino de proceso. Este comando genera _foo.compute_ y _foo.runconfig_ en la carpeta **aml_config**.

>[!NOTE]
> Los nombres _local_ o _docker_ de los archivos de configuración de ejecución son arbitrarios. Para su comodidad, Azure ML Workbench agrega estas dos configuraciones de ejecución cuando se crea un proyecto en blanco. Puede cambiar el nombre de los archivos “<run configuration name>.runconfig” que vienen con la plantilla del proyecto o crear nuevos archivos con el nombre que desee.

### <a name="compute-target-namecompute"></a>\<nombre de destino de proceso>.compute
El archivo _\<compute target name>.compute_ especifica la información de conexión y configuración para el destino de proceso. Es una lista de pares nombre-valor. A continuación se encuentran las opciones admitidas.

**type**: tipo de entorno de proceso. Los valores admitidos son:
  - local
  - docker
  - remotedocker
  - cluster

**baseDockerImage**: imagen de Docker usada para ejecutar el script de Python/PySpark. El valor predeterminado es _microsoft/mmlspark:plus-0.7.91_. Asimismo, se admite otra imagen: _microsoft/mmlspark:plus-gpu-0.7.91_, que proporciona a la GPU acceso al equipo host (si hay GPU).

**address**: la dirección IP o FQDN (nombre de dominio completo) de la máquina virtual o nodo principal del clúster de HDInsight.

**username**: el nombre de usuario de SSH para acceder a la máquina virtual o al nodo principal de HDInsight.

**password**: la contraseña cifrada de la conexión SSH.

**sharedVolumes**: marca para indicar que el motor de ejecución debe usar la característica de volumen compartido de Docker para enviar archivos de proyecto hacia un lado y otro. Tener esta marca activada puede acelerar la ejecución, ya que Docker puede acceder a los proyectos directamente sin necesidad de copiarlos. Es recomendable establecerlo en _false_ si el motor de Docker se ejecuta en Windows, dado que el uso compartido de volúmenes para Docker en Windows puede ser inestable. Establézcalo en _true_ si se ejecuta en MacOS o Linux.

**nvidiaDocker**: esta marca, cuando se establece en _true_, indica al servicio Experimentación de Machine Learning que use el comando _nvidia-docker_, en lugar del comando _docker_ habitual, para iniciar la imagen de Docker. El motor de _nvidia-docker_ permite al contenedor de Docker acceder al hardware de la GPU. El valor se requiere si se desea realizar la ejecución de la GPU en el contenedor de Docker. Solo el host de Linux admite _nvidia-docker_. Por ejemplo, las DSVM basadas en Linux en Azure vienen con _nvidia-docker_. A partir de ahora, _nvidia-docker_ no se admite en Windows.

**nativeSharedDirectory**: esta propiedad especifica el directorio base (por ejemplo: _~/.azureml/share/_) donde se pueden guardar los archivos para poder compartir a través de ejecuciones en el mismo destino de proceso. Si esta configuración se utiliza cuando se ejecuta en un contenedor de Docker, _sharedVolumes_ debe establecerse en true. En caso contrario, se produce un error de ejecución.

### <a name="run-configuration-namerunconfig"></a>\<nombre de configuración de ejecución>.runconfig
_\<run configuration name>.runconfig_ especifica el comportamiento de ejecución de experimento de Azure Machine Learning. Puede configurar el comportamiento de ejecución, como el seguimiento del historial de ejecución o qué destino de proceso usar, además de muchos otros. Los nombres de los archivos de configuración de ejecución se usan para rellenar la lista desplegable del contexto de ejecución en la aplicación de escritorio de Azure ML Workbench.

**ArgumentVector**: en esta sección se especifica el script que se ejecuta como parte de la ejecución, así como los parámetros del script. Por ejemplo, si tiene el siguiente fragmento de código en el archivo “<run configuration name>.runconfig” 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_“az ml experiment submit foo.runconfig”_ ejecuta automáticamente el comando con el archivo _myscript.py_ pasando 234 como parámetro y establece la marca --verbose.

**Target**: este parámetro es el nombre del archivo _.compute_ al que hacer referencia el archivo _/runconfig_. En general, apunta al archivo _foo.compute_, pero puede modificarlo para que apunte a otro destino de proceso.

**Environment Variables**: esta sección permite a los usuarios establecer variables de entorno como parte de las ejecuciones. El usuario puede especificar variables de entorno mediante pares de nombre-valor en el formato siguiente:
```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Puede accederse a estas variables de entorno en el código del usuario. Por ejemplo, este código de phyton imprime la variable de entorno denominada “EXAMPLE_ENV_VAR”.
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: esta propiedad especifica si Azure ML Workbench debe iniciar una sesión de Spark para ejecutar el script. El valor predeterminado es _PySpark_. Establézcalo en _Python_ si no ejecuta el código de PySpark, lo que puede ayudar a iniciar el trabajo más rápido con menos sobrecarga.

**CondaDependenciesFile**: esta propiedad apunta al archivo que especifica las dependencias del entorno de conda en la carpeta *aml_config*. Si lo establece en _null_, apunta al archivo predeterminado **conda_dependencies.yml**.

**SparkDependenciesFile**: esta propiedad apunta al archivo que especifica las dependencias de Spark en la carpeta **aml_config**. Se establece en _null_ de manera predeterminada y apunta al archivo predeterminado **spark_dependencies.yml**.

**PrepareEnvironment**: esta propiedad, cuando se establece en _true_, indica al servicio Experimentación que prepare el entorno de conda en función de las dependencias de conda especificadas como parte de la ejecución inicial. Esta propiedad es efectiva únicamente cuando se ejecuta en un entorno de Docker. Esta configuración no tiene ningún efecto si se ejecuta en un entorno _local_. 

**TrackedRun**: esta marca indica al servicio Experimentación si se realiza un seguimiento de la ejecución en la infraestructura del historial de ejecución de Azure ML Workbench. El valor predeterminado es _true_. 

**UseSampling**: _UseSampling_ especifica si los conjuntos de datos de ejemplo activos para orígenes de datos se usan para la ejecución o no. Si se establece en _false_, los orígenes de datos ingieren y usan los datos completos leídos del almacén de datos. Si establece en _true_, se usan las muestras activas. Los usuarios pueden usar “DataSourceSettings” para especificar qué conjuntos de datos de ejemplo concretos usar si desea invalidar el ejemplo activo. 

**DataSourceSettings**: esta sección de configuración especifica la configuración de orígenes de datos. En esta sección, el usuario especifica qué muestra de datos existente para un origen de datos determinado se utiliza como parte de la ejecución. 

La opción de configuración siguiente especifica que el ejemplo denominado “MySample” se usa para el origen de datos denominado “MyDataSource”.
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: las sustituciones de orígenes de datos pueden usarse cuando el usuario desea cambiar de un origen de datos a otro sin cambiar el código. Por ejemplo, los usuarios pueden cambiar la referencia del origen de datos para pasar desde un archivo local muestreado hacia el conjunto de datos original más grande, almacenado en Azure Blob. Cuando se utiliza una sustitución, Azure ML Workbench ejecuta los orígenes de datos y los paquetes de preparación de datos haciendo referencia al origen de datos sustituto.

En el ejemplo siguiente se reemplazan las referencias de “mylocal.datasource” en los orígenes de datos y los paquetes de preparación de datos de Azure ML por “myremote.dsource”. 
 
```
DataSourceSubstitutions:
    myocal.dsource: myremote.dsource
```

En función de la sustitución anterior, el siguiente código de ejemplo ahora lee de “myremote.dsource”, en lugar de hacerlo de “mylocal.dsource” sin que los usuarios modificaran el código.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Pasos siguientes
Más información acerca de la [configuración del servicio Experimentación](experimentation-service-configuration.md).
