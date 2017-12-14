---
title: Uso de GPU para Azure Machine Learning | Microsoft Docs
description: "En este artículo se describe cómo usar unidades de procesamiento gráfico (GPU) para entrenar redes neuronales profundas en Azure Machine Learning Workbench."
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: ce1557aed09384b0d7a0b65aabd473fe72ab740c
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Uso de GPU en Azure Machine Learning
La unidad de procesamiento gráfico (GPU) es comúnmente usada para procesar tareas que consumen muchos recursos informáticos que se pueden producir normalmente al entrenar determinados modelos de redes neuronales. Mediante las GPU, puede reducir considerablemente el tiempo de entrenamiento de los modelos. En este documento, aprenderá a configurar Azure ML Workbench para usar [DSVM (Data Science Virtual Machine)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) equipada con GPU como destino de ejecución. 

## <a name="prerequisites"></a>Requisitos previos
- Para seguir los pasos de esta guía, primero debe [instalar Azure ML Workbench](quickstart-installation.md).
- Debe tener acceso a los equipos equipados con GPU NVidia.
    - Puede ejecutar los scripts directamente en la máquina local (Windows o macOS) con las GPU.
    - También puede ejecutar scripts de un contenedor de Docker en una máquina con GPU.

## <a name="execute-in-local-environment-with-gpus"></a>Ejecución en el entorno _local_ con GPU
Puede instalar Azure ML Workbench en un equipo que incorpora GPU y ejecutarlo en el entorno _local_. Puede ser:
- Una máquina física Windows o macOS.
- Una máquina virtual (VM) Windows, como una DSVM Windows se aprovisiona con plantillas de máquina virtual de la serie Azure NC.

En este caso, no se necesita ninguna configuración especial en Azure ML Workbench. Asegúrese de que tiene todos los controladores necesarios, kits de herramientas y bibliotecas de aprendizaje automático habilitadas para GPU instalados localmente. Solo tiene que ejecutar la máquina en el entorno _local_  donde el tiempo de ejecución de Python pueda acceder directamente al hardware de GPU local.

1. Abra AML Workbench. Vaya a **Archivo** y **Open Command Prompt** (Abrir símbolo del sistema). 
2. En la línea de comandos, instale el marco de Deep Learning habilitado para GPU, como el kit de herramientas de Microsoft Cognitive, TensorFlow, etc. Por ejemplo:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Escriba código de Python que aproveche las bibliotecas de Deep Learning.
4. Elija _local_ como entorno de proceso y ejecute el código Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Ejecución en el entorno de _Docker_ en una máquina virtual Linux con GPU
Azure ML Workbench admite también la ejecución en Docker en una máquina virtual Linux de Azure. Esta es una buena opción para ejecutar trabajos que consumen muchos recursos informáticos en un poderoso trozo de hardware virtual que se puede apagar cuando haya terminado y así pagar únicamente por el uso. Aunque en principio es posible usar GPU en cualquier máquina virtual Linux, la DSVM basada en Ubuntu-incluye los controladores y bibliotecas CUDA necesarios ya instalados, lo que facilita mucho la configuración. Siga los pasos siguientes:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Creación de una instancia de Data Science Virtual Machine de Linux basada en Ubuntu en Azure
1. Abra el explorador web y vaya a [Azure Portal](https://portal.azure.com).

2. Seleccione **+ Nuevo** a la izquierda del portal.

3. Busque "Data Science Virtual Machine for Linux (Ubuntu)" en Marketplace.

4. Haga clic en **Crear** para crear una DSVM de Ubuntu.

5. Rellene el formulario **Aspectos básicos** con la información necesaria.
Al seleccionar la ubicación de la máquina virtual, tenga en cuenta que las máquinas virtuales de GPU solo están disponibles en determinadas regiones de Azure, por ejemplo, **Centro y Sur de EE. UU.** Consulte los [productos de proceso disponibles por región](https://azure.microsoft.com/regions/services/).
Haga clic en Aceptar para guardar la información de **Aspectos básicos**.

6. Elija el tamaño de la máquina virtual. Seleccione uno de los tamaños de las máquinas virtuales con prefijo NC, que están equipadas con chips GPU de NVidia.  Haga clic en **Ver todos** para ver la lista completa, según sea necesario. Más información sobre las [máquinas virtuales de Azure equipadas con GPU](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. Finalice el resto de la configuración y revise la información de compra. Haga clic en Comprar para crear la máquina virtual. Anote la dirección IP asignada a la máquina virtual. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Creación de un nuevo proyecto en Azure ML Workbench 
Puede usar el ejemplo _Classifying MNIST using TensorFlow_ o el ejemplo _Classifying MNIST dataset with CNTK_.

### <a name="create-a-new-compute-target"></a>Creación de un nuevo destino de proceso
Inicie la línea de comandos desde Azure ML Workbench. Escriba el comando siguiente: Reemplace el texto de marcador de posición del ejemplo siguiente por sus propios valores de nombre, dirección IP, nombre de usuario y contraseña. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Configuración de Azure ML Workbench para acceder a GPU
Vuelva al proyecto, abra **Ver archivo**y elija el botón **Actualizar**. Ahora puede ver dos nuevos archivos de configuración `my_dsvm.compute` y `my_dsvm.runconfig`.
 
Abra `my_dsvm.compute`. Cambiar `baseDockerImage` por `microsoft/mmlspark:plus-gpu-0.7.9` y agregue una nueva línea `nvidiaDocker: true`. Así que el archivo debe tener estas dos líneas:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Ahora, abra `my_dsvm.runconfig` y cambie el valor `Framework` de `PySpark` a `Python`. Si no ve esta línea, agréguela, puesto que el valor predeterminado sería `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Marco de Deep Learning de referencia 
Abra el archivo `conda_dependencies.yml` y asegúrese de que utiliza la versión de GPU de los paquetes de Python del marco de Deep Learning. Los proyectos de ejemplo enumeran las versiones de CPU, por lo que necesita realizar este cambio.

Ejemplo de TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Ejemplo del kit de herramientas de Microsoft Cognitive:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

También puede usar la versión de SGD de 1 bit del kit de herramientas de Microsoft Cognitive que proporciona mejoras de rendimiento en máquinas virtuales de varias GPU. Tenga en cuenta [el requisito de licencia de SGD de 1 bit](https://docs.microsoft.com/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Ejecución
Ahora está listo para ejecutar los scripts de Python. Puede ejecutarlos en Azure ML Workbench mediante el contexto `my_dsvm`, o puede iniciarlo desde la línea de comandos:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Para comprobar que se usa la GPU, examine el resultado de ejecución para ver algo parecido a lo siguiente:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

¡Enhorabuena! El script aprovecha la potencia de GPU a través de un contenedor de Docker.

## <a name="next-steps"></a>Pasos siguientes
Vea un ejemplo de uso de GPU para acelerar el aprendizaje de red neuronal profundo en la galería de Azure ML.
