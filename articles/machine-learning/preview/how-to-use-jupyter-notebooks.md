---
title: Cómo usar Jupyter Notebooks de Azure Machine Learning Workbench | Microsoft Docs
description: Guía para usar la característica Jupyter Notebook de Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: c21b7096f689efedacd6e7d55d83912d35dff803
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Uso de Jupyter Notebooks de Azure Machine Learning Workbench

Azure Machine Learning Workbench admite la experimentación de ciencia de datos interactiva a través de su integración con Jupyter Notebooks. En este artículo se describe cómo hacer un uso eficaz de esta característica para aumentar la velocidad y la calidad de la experimentación de ciencia de datos interactiva.

## <a name="prerequisites"></a>requisitos previos
- [Crear cuentas de Azure Machine Learning e instalar Azure Machine Learning Workbench](quickstart-installation.md).
- Estar familiarizado con [Jupyter Notebook](http://jupyter.org/). Este artículo no trata sobre aprender a usar Jupyter.

## <a name="jupyter-notebook-architecture"></a>Arquitectura de Jupyter Notebook
En un nivel alto, la arquitectura de Jupyter Notebook incluye tres componentes. Cada uno de ellos se puede ejecutar en entornos de proceso distintos:

- **Cliente**: recibe la entrada del usuario y muestra la salida representada.
- **Servidor**: servidor web que hospeda los archivos del bloc de notas (archivos .ipynb).
- **Kernel**: el entorno en tiempo de ejecución donde se produce la ejecución de las celdas del bloc de notas.

Para más información, consulte la [documentación de Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). En el diagrama siguiente ilustra cómo se asigna este cliente, el servidor y la arquitectura de kernel a los componentes de Azure Machine Learning:

![Arquitectura de Jupyter Notebook](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Kernels en blocs de notas de Azure Machine Learning Workbench
Puede acceder a distintos kernels en Azure Machine Learning Workbench mediante la definición de configuraciones de ejecución y los destinos de proceso en la carpeta `aml_config` del proyecto. Agregar un nuevo destino de proceso mediante la emisión del comando `az ml computetarget attach` es equivalente a agregar un kernel nuevo.

>[!NOTE]
>Revise [Configuración del servicio Experimentación de Azure Machine Learning](experimentation-service-configuration.md) para más detalles sobre las configuraciones de ejecución y los destinos de proceso.

### <a name="kernel-naming-convention"></a>Convención de nomenclatura de Kernel
Azure Machine Learning Workbench genera kernels personalizados de Jupyter. Estos kernels se denominan *\<nombre del proyecto> \<nombre de configuración de ejecución>*. Por ejemplo, si tiene una configuración de ejecución denominada _docker-python_ en un proyecto denominado _myIris_, Azure Machine Learning pone a disposición un kernel denominado *myIris docker-python.* El kernel de ejecución se establece en el menú **Kernel** de Jupyter Notebook, en el submenú **Change kernel** (Cambiar kernel). El nombre del kernel de ejecución aparece en el extremo derecho de la barra de menús.
 
Actualmente, Azure Machine Learning Workbench admite los siguientes tipos de kernels.

### <a name="local-python-kernel"></a>Kernel de Python local
Este kernel de Python admite la ejecución en las máquinas locales. Se integra con el soporte técnico del historial de ejecución de Azure Machine Learning. El nombre del kernel suele ser *my_project_name local*.

>[!NOTE]
>No use el kernel Python 3. Es un kernel independiente que Jupyter proporciona de manera predeterminada y que no está integrado con las funcionalidades de Azure Machine Learning. Por ejemplo, las funciones mágicas `%azureml` de Jupyter devuelven errores del tipo "no se encuentra". 

### <a name="python-kernel-in-docker-local-or-remote"></a>Kernel de Python en Docker (local o remoto)
Este kernel de Python se ejecuta en un contenedor de Docker en la máquina local o en una máquina virtual Linux remota. El nombre del kernel suele ser *my_project docker*. El archivo `docker.runconfig` asociado tiene el campo `Framework` establecido en `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Kernel de PySpark en Docker (local o remoto)
Este kernel de PySpark ejecuta scripts en un contexto de Spark en ejecución dentro del contenedor de Docker, ya sea en la máquina local o en una VM Linux remota. El nombre del kernel suele ser *my_project docker*. El archivo `docker.runconfig` asociado tiene el campo `Framework` establecido en `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Kernel de PySpark en el clúster de Azure HDInsight
Este kernel se ejecuta en el clúster remoto de Azure HDInsight que se adjunta como un destino de proceso para el proyecto. El nombre del kernel suele ser *my_project my_hdi.* 

>[!IMPORTANT]
>En el archivo `.compute` del destino de proceso de HDI, debe cambiar el campo `yarnDeployMode` a `client` (el valor predeterminado es `cluster`) para usar este kernel. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Inicio de un servidor de Jupyter desde Azure Machine Learning
Desde Azure Machine Learning Workbench, puede acceder a los blocs de notas a través de la pestaña **Blocs de notas**. El proyecto de ejemplo _Classifying Iris_ (Clasificación Iris) incluye un bloc de notas `iris.ipynb` de ejemplo.

![pestaña Bloc de notas](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Cuando abre un bloc de notas en Azure Machine Learning Workbench, se muestra en su propia pestaña de documentos en **Modo de vista previa**. Se trata de una vista de solo lectura que no requiere un servidor ni un kernel de Jupyter en ejecución.

![vista previa de bloc de notas](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Al seleccionar el botón **Start Notebook Server** (Iniciar servidor del bloc de notas) se inicia el servidor de Jupyter y el bloc de notas se cambia al **modo de edición**. La conocida interfaz de usuario de Jupyter Notebook aparece insertada en Workbench. Ahora puede establecer un kernel desde el menú **Kernel** e iniciar la sesión interactiva del bloc de notas. 

>[!NOTE]
>En el caso de los kernels no locales, pueden tardar un minuto o dos en iniciarse si es la primera vez que los usa. Puede ejecutar el comando `az ml experiment prepare` desde la ventana de la CLI para preparar el destino de proceso y que el kernel se inicie mucho más rápido una vez preparado.

![Modo de edición](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Se trata de una experiencia de Jupyter Notebook totalmente interactiva. Desde esta ventana se admiten todas las operaciones y los métodos abreviados de teclado normales del bloc de notas, con la excepción de algunas operaciones de archivo que se pueden hacer a través de la pestaña **Blocs de notas** y **Archivo** de Workbench.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Inicio de un servidor de Jupyter desde la línea de comandos
También puede iniciar una sesión de bloc de notas mediante la emisión de `az ml notebook start` desde la ventana de línea de comandos:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
El explorador predeterminado se abre automáticamente con el servidor de Jupyter señalando al directorio de inicio del proyecto. También puede usar la dirección URL y el token que se muestran en la ventana de la CLI para abrir otras ventanas del explorador de manera local. 

![Panel del proyecto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Ahora puede seleccionar un archivo del bloc de notas `.ipynb`, abrirlo y establecer el kernel (si no se ha establecido) e iniciar una sesión interactiva.

![Panel del proyecto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Usar comandos mágicos para administrar experimentos

Puede usar los [comandos mágicos](http://ipython.readthedocs.io/en/stable/interactive/magics.html) de las celdas del bloc de notas para realizar un seguimiento del historial de ejecución y guardar los resultados, como modelos o conjuntos de datos.

Para realizar un seguimiento de las ejecuciones individuales de las celdas del bloc de notas, use el comando mágico `%azureml history on`. Después de activar el historial, cada ejecución de celda aparece como una entrada en el historial de ejecución:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Para desactivar el seguimiento de las ejecuciones de celdas, use el comando mágico `%azureml history off`.

Puede usar el comando mágico `%azureml upload` para guardar los archivos de datos y el modelo de la ejecución. Los objetos guardados aparecen como resultados en la vista del historial de ejecuciones:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Los resultados se deben guardar en una carpeta denominada *outputs*.

## <a name="next-steps"></a>Pasos siguientes
- Para información sobre cómo usar Jupyter Notebook, consulte la [documentación oficial de Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Para una descripción más detallada del entorno de ejecución de Experimentación de Azure Machine Learning, consulte [Configuración del servicio Experimentación de Azure Machine Learning](experimentation-service-configuration.md).

