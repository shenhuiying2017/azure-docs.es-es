---
title: "Cómo usar Jupyter Notebooks de Azure Machine Learning Workbench | Microsoft Docs"
description: "Guía para utilizar la función Jupyter Notebooks de Azure Machine Learning Workbench"
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 9d8a9f1c32578abff1d98e093469e1a780f6cd80
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>Cómo usar Jupyter Notebooks de Azure Machine Learning Workbench

Azure Machine Learning Workbench admite experimentación de ciencia de datos interactiva a través de su integración de Jupyter Notebook. En este artículo se describe cómo hacer un uso eficaz de esta función para aumentar la velocidad y la calidad de la experimentación de ciencia de datos interactiva.

## <a name="prerequisites"></a>Requisitos previos
- [Instale y cree Azure Machine Learning](quickstart-installation.md).
- Familiarícese con [Jupyter Notebook](http://jupyter.org/), ya que este artículo no trata sobre cómo aprender a usar Jupyter.

## <a name="jupyter-notebook-architecture"></a>Arquitectura de Jupyter Notebook
En un nivel alto, la arquitectura de Jupyter Notebook incluye tres componentes y cada uno puede ejecutarse en entornos de cálculo diferentes:

- **Cliente**: recibe la entrada del usuario y muestra salida representada.
- **Servidor**: servidor web que hospeda los archivos del bloc de notas (archivos .ipynb).
- **Kernel**: el entorno en tiempo de ejecución donde se produce la ejecución real de las celdas del bloc de notas.

Para obtener más información, consulte la [documentación oficial de Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). A continuación se incluye un diagrama que ilustra cómo se asigna este cliente, el servidor y la arquitectura de kernel a los componentes de Azure Machine Learning.

![arquitectura del bloc de notas](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Kernels del bloc de notas de Azure Machine Learning Workbench
Puede tener acceso a diferentes kernels en Azure Machine Learning Workbench mediante la configuración de las ejecuciones y los destinos de proceso en la carpeta `aml_config` del proyecto. La adición de un nuevo destino de proceso mediante la emisión del comando `az ml computetarget attach` es equivalente a la adición de un kernel nuevo.

>[!NOTE]
>Revise [Configurar ejecución](experimentation-service-configuration.md) para obtener más información acerca de las configuraciones de ejecución y los destinos de proceso.

### <a name="kernel-naming-convention"></a>Convención de nomenclatura de Kernel
Los kernels se denominan normalmente con el formato "\<nombre del proyecto> \<nombre de la configuración de ejecución>". Por ejemplo, si tiene una configuración de ejecución denominada _docker-python_ en un proyecto denominado _myIris_, puede encontrar un kernel denominado "myIris docker-python" en la lista de kernels cuando abre un bloc de notas de Jupyter.

Actualmente, Workbench admite los siguientes tipos de kernels.

### <a name="local-python-kernel"></a>Kernel de Python local
Este kernel de Python admite la ejecución en el equipo local. Se integra con el soporte técnico del historial de ejecución de Azure Machine Learning. El nombre del kernel suele ser "my_project_name local".

>[!NOTE]
>No use el kernel "Python 3". Se trata de un kernel independiente que proporciona Jupyter de forma predeterminada. No se integra con las funcionalidades de Azure Machine Learning.

### <a name="python-kernel-in-docker-local-or-remote"></a>Kernel de Python en Docker (local o remoto)
Este kernel de Python se ejecuta en un contenedor de Docker en el equipo local o en una VM remota de Linux. El nombre del kernel suele ser "my_project docker". El archivo `docker.runconfig` asociado tiene el campo `Framework` establecido en `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Kernel de PySpark en Docker (local o remoto)
Este kernel de PySpark ejecuta secuencias de comandos en un contexto de Spark que se ejecutan dentro del contenedor de Docker, en el equipo local o en una VM remota de Linux. El nombre del kernel suele ser "my_project docker". El archivo `docker.runconfig` asociado tiene el campo `Framework` establecido en `PySpark`.

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>Kernel de PySpark en el clúster de HDInsight
Este kernel se ejecuta en el clúster remoto de HDInsight que se adjunta como un destino de proceso para el proyecto. El nombre del kernel suele ser "my_project my_hdi". 

>[!IMPORTANT]
>En el archivo `.compute` del destino de proceso de HDI, debe cambiar el campo `yarnDeployMode` a `client` (el valor predeterminado es `cluster`) para poder usar este kernel. 

## <a name="start-jupyter-server-from-the-workbench"></a>Iniciar el servidor de Jupyter desde Workbench
Desde Azure Machine Learning Workbench, los blocs de notas son accesibles a través de la pestaña **Blocs de notas** de Workbench. El proyecto de ejemplo _Classifying Iris_ (Clasificación Iris) incluye un bloc de notas `iris.ipynb` de ejemplo.

![pestaña blocs de notas](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Cuando se abre un bloc de notas en Azure Machine Learning Workbench, se muestra en su propia pestaña de documentos en **Modo de vista previa**. Se trata de una vista de solo lectura que no requiere un servidor ni un kernel de Jupyter en ejecución.

![vista previa de bloc de notas](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Al hacer clic en el botón **Start Notebook Server** (Iniciar servidor del bloc de notas) se inicia el servidor de Jupyter y se cambia el bloc de notas al **modo de edición**. La conocida interfaz de usuario de Jupyter Notebook aparece incrustada en Workbench. Ahora puede establecer un kernel desde el menú **Kernel** e iniciar la sesión interactiva del bloc de notas. 

>[!NOTE]
>Tenga en cuenta que los kernels no locales, pueden tardar un minuto o dos en iniciarse si es la primera vez que los usa. Puede ejecutar el comando `az ml experiment prepare` desde la ventana de la CLI para preparar el destino de proceso y que el kernel pueda iniciarse mucho más rápido una vez preparado.

![modo de edición](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Se trata de una experiencia de Jupyter Notebook totalmente interactiva. Desde esta ventana se admiten todas las operaciones y los métodos abreviados de teclado normales del bloc de notas, con la excepción de algunas operaciones de archivo, ya que es posible hacerlas a través de la pestaña **Blocs de notas** y **Archivo** de Workbench.

## <a name="start-jupyter-server-from-command-line"></a>Iniciar el servidor de Jupyter desde la línea de comandos
También puede iniciar una sesión de bloc de notas mediante la emisión de `az ml notebook start` desde la ventana de línea de comandos:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
El explorador predeterminado se inicia automáticamente con el servidor de Jupyter señalando al directorio de inicio del proyecto. También puede utilizar la dirección URL y el token que se muestran en la ventana de la CLI para iniciar otras ventanas del explorador localmente. 

![panel de proyecto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Ahora puede hacer clic en un archivo del bloc de notas `.ipynb`, abrirlo y establecer el kernel (si no se ha establecido) e iniciar una sesión interactiva.

![panel de proyecto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Usar comandos mágicos para administrar experimentos

Puede usar los [comandos mágicos](http://ipython.readthedocs.io/en/stable/interactive/magics.html) de las celdas del bloc de notas para realizar un seguimiento del historial de ejecución y guardar los resultados, como modelos o conjuntos de datos.

Para realizar un seguimiento de las ejecuciones individuales de las celdas del bloc de notas, use el comando mágico "%azureml history on". Después de activar el historial, cada ejecución de celda aparecerá como una entrada en el historial de ejecución.

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Para desactivar el seguimiento de las ejecuciones de las celdas, use el comando mágico "%azureml history off".

Puede usar el comando mágico "%azureml upload" para guardar los archivos de datos y el modelo de la ejecución. Los objetos guardados se muestran como resultados en la vista del historial de ejecuciones de una determinada ejecución.

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Los resultados deben guardarse en una carpeta denominada "outputs".

## <a name="next-steps"></a>Pasos siguientes
- Para obtener información acerca de cómo usar Jupyter Notebook, consulte la [documentación oficial de Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Para obtener una descripción más detallada del entorno de ejecución de Experimentación de Azure Machine Learning, consulte [Overview of Azure Machine Learning experimentation service](experimentation-service-configuration.md) (Información general del servicio Experimentación de Azure Machine Learning)

