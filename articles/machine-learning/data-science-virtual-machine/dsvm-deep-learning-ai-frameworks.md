---
title: Marcos de aprendizaje profundo y de AI - Azure | Microsoft Docs
description: Marcos de aprendizaje profundo y de AI
keywords: herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: d73869d1371247a269b6601c35b1a938d89176c0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Marcos de aprendizaje profundo y de AI
[Data Science Virtual Machine](http://aka.ms/dsvm) (DSVM) y [Deep Learning VM](http://aka.ms/dsvm/deeplearning) admiten varios marcos de aprendizaje profundo para compilar aplicaciones de inteligencia artificial (AI) con capacidades de análisis predictivo y capacidades cognitivas, como la comprensión de imágenes y de lenguajes. 

A continuación se muestra la información de todos los marcos de aprendizaje profundo disponibles en DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | Microsoft Cognitive Toolkit (CNTK) se instala en Python 3.5 en [Linux y Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), y en Python 3.6 en [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo.     |
| Herramientas relacionadas en DSVM      | Keras      |
| ¿Cómo se usa o ejecuta?    | * En un terminal: active el entorno correcto y ejecute Python. <br/>
 * En Jupyter: conéctese a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y luego abra el directorio CNTK para acceder a los ejemplos. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | TensorFlow se instala en Python 3.5 en [Linux y Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), y en Python 3.6 en [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo.     |
| Herramientas relacionadas en DSVM      | Keras      |
| ¿Cómo se usa o ejecuta?    | * En un terminal: active el entorno correcto y ejecute Python. <br/>
 * En Jupyter: conéctese a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y luego abra el directorio TensorFlow para acceder a los ejemplos.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo distribuido para TensorFlow      |
| Ediciones compatibles de DSVM      | Ubuntu     |
| ¿Cómo se configura/instala en DSVM?  | Horovod se instala en Python 3.5 en [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Vínculos a ejemplos      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Herramientas relacionadas en DSVM      | TensorFlow      |
| ¿Cómo se usa o ejecuta?    | En un terminal: active el entorno correcto y ejecute Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | TensorFlow se instala en Python 3.5 en [Linux y Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), y en Python 3.6 en [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Vínculos a ejemplos      | https://github.com/fchollet/keras/tree/master/examples      |
| Herramientas relacionadas en DSVM      | Microsoft Cognitive Toolkit, TensorFlow y Theano      |
| ¿Cómo se usa o ejecuta?    | * En un terminal: active el entorno correcto y ejecute Python. <br/>
 * En Jupyter: descargue los ejemplos desde la ubicación de Github, conéctese a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y luego abra el directorio de ejemplos. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Ubuntu     |
| ¿Cómo se configura/instala en DSVM?  | Caffe se instala en `/opt/caffe`.    |
| Vínculos a ejemplos      | En `/opt/caffe/examples` se incluyen ejemplos.      |
| Herramientas relacionadas en DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>¿Cómo se usa o ejecuta?  

Use X2Go para iniciar sesión en la máquina virtual, inicie un terminal nuevo y escriba:

```
cd /opt/caffe/examples
jupyter notebook
```

Se abre una nueva ventana del explorador con blocs de notas de ejemplo.

Los binarios se instalan en /opt/caffe/build/install/bin. 

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Ubuntu     |
| ¿Cómo se configura/instala en DSVM?  | Caffe2 se instala en el [entorno de Conda de Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). El origen está en `/opt/caffe2`. |
| Vínculos a ejemplos      | Se incluyen blocs de notas de ejemplo en JupyterHub. |
| Herramientas relacionadas en DSVM      | Caffe      |
| ¿Cómo se usa o ejecuta?    | * En el terminal: active el [entorno de Python root](dsvm-languages.md#python-linux-and-windows-server-2012-edition), inicie Python e importe caffe2. <br/> * En JupyterHub: [conéctese a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y desplácese al directorio Caffe2 para buscar blocs de notas de ejemplo. Algunos blocs de notas requieren que se establezca la raíz de Caffe2 en el código de Python. Escriba /opt/caffe2. |
| Notas sobre compilación | Caffe2 se compila a partir de código fuente en Linux e incluye CUDA, cuDNN e Intel MKL. La confirmación actual es 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, que se ha elegido para estabilidad en todas las GPU y muestras analizadas. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | Chainer se instala en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). También se instalan ChainerRL y ChainerCV.   |
| Vínculos a ejemplos      | Se incluyen blocs de notas de ejemplo en JupyterHub. |
| Herramientas relacionadas en DSVM      | Caffe      |
| ¿Cómo se usa o ejecuta?  | * En el terminal: active el entorno de [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), ejecute _python_ y luego importe Chainer. <br/>
* * En JupyterHub: [conéctese a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y desplácese al directorio Chainer para buscar blocs de notas de ejemplo.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo para H2O      |
| Ediciones compatibles de DSVM      | Ubuntu     |
| ¿Cómo se configura/instala en DSVM?  | Deep Waterse instala en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) y también está disponible en `/dsvm/tools/deep_water`.   |
| Vínculos a ejemplos      | Se incluyen blocs de notas de ejemplo en JupyterHub.      |
| Herramientas relacionadas en DSVM      | H2O y Sparkling Water      |

### <a name="how-to-use--run-it"></a>¿Cómo se usa o ejecuta?  

Deep Water requiere CUDA 8 con cuDNN 5.1. No está en la ruta de acceso de la biblioteca de forma predeterminada, ya que otros marcos de aprendizaje profundo utilizan CUDA 9 y cuDNN 7. Para usar CUDA 8 + cuDNN 5.1 para Deep Water:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Para utilizar Deep Water:
* * En el terminal: active el entorno de [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) y luego ejecute _python_. <br/>
* * En JupyterHub: [conéctese a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y desplácese al directorio deep_water para buscar blocs de notas de ejemplo.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | MXNet se instala en `C:\dsvm\tools\mxnet` en Windows y en `/dsvm/tools/mxnet` en Linux. Los enlaces de Python se instalan en Python 3.5 en [Linux y Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), y en Python 3.6 en [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Los enlaces de R también se instalan en Ubuntu.   |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo.    |
| Herramientas relacionadas en DSVM      | Keras      |
| ¿Cómo se usa o ejecuta?    | * En un terminal: active el entorno correcto y ejecute Python. <br/>
 * En Jupyter: conéctese a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y luego abra el directorio mxnet para acceder a los ejemplos.  |
 | Notas sobre compilación | MXNet se compila a partir de código fuente en Linux. Esta compilación incluye CUDA, cuDNN, NCCL y MKL. |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Sistema de aprendizaje profundo de NVIDIA para entrenar modelos de aprendizaje profundo de forma rápida      |
| Ediciones compatibles de DSVM      | Ubuntu     |
| ¿Cómo se configura/instala en DSVM?  | DIGITS se instala en `/dsvm/tools/DIGITS` y hay disponible un servicio denominado _digits_.   |
### <a name="how-to-use--run-it"></a>¿Cómo se usa o ejecuta?  

Inicie sesión en la máquina virtual con X2Go. En un terminal, inicie el servicio:

    sudo systemctl start digits

El servicio tarda cerca de un minuto en iniciarse. Abra un explorador web y vaya a `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Herramienta NVIDIA para consultar la actividad GPU      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | _nvidia-smi_ está disponible en la ruta de acceso del sistema.   |
| ¿Cómo se usa o ejecuta? | Inicie un símbolo del sistema (en Windows) o un terminal (en Linux) y ejecute _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Ubuntu     |
| ¿Cómo se configura/instala en DSVM?  | Theano se instala en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_).   |
| Herramientas relacionadas en DSVM      | Keras      |
| ¿Cómo se usa o ejecuta?    | En un terminal, active la versión de Python que quiera (root o py35), ejecute Python e importe Theano. <br/> 
* En Jupyter, seleccione el kernel de Python 2.7 o 3.5 e importe Theano.  
<br/>
Para solucionar de forma alternativa un error de MKL reciente, primero debe establecer la capa de subprocesos MKL:<br/><br/>
_export MKL_THREADING_LAYER=GNU_
|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Ubuntu     |
| ¿Cómo se configura/instala en DSVM?  | Torch se instala en `/dsvm/tools/torch`. PyTorch se instala en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_).   |
| Vínculos a ejemplos      | Los ejemplos de Torch están en `/dsvm/samples/torch`. Los ejemplos de PyTorch están en `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | PyTorch se instala en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Vínculos a ejemplos      | Se incluyen ejemplos de Jupyter Notebooks y también se pueden encontrar ejemplos en /dsvm/samples/pytorch.      |
| Herramientas relacionadas en DSVM      | Torch      |
| ¿Cómo se usa o ejecuta? | 
* En un terminal: active el entorno correcto y ejecute Python. <br/>
 * * En Jupyter: conéctese a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y luego abra el directorio PyTorch para acceder a los ejemplos.  |

## <a name="mxnet-model-server"></a>MXNet Model Server

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Un servidor para crear puntos de conexión de HTTP para los modelos MXNet y ONNX      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | _mxnet-model-server_ está disponible en el terminal.   |
| Vínculos a ejemplos      | Busque ejemplos actualizados en la [página de MXNet Model Server](https://github.com/awslabs/mxnet-model-server).    |
| Herramientas relacionadas en DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow Serving

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Un servidor para ejecutar inferencia en un modelo de TensorFlow      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | _tensorflow_model_server_ está disponible en el terminal.   |
| Vínculos a ejemplos      | Otros ejemplos están disponibles [en línea](https://www.tensorflow.org/serving/).      |
| Herramientas relacionadas en DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Un servidor de inferencia de aprendizaje profundo de NVIDIA. |
| Ediciones compatibles de DSVM      | Ubuntu     |
| ¿Cómo se configura/instala en DSVM?  | TensorRT se instala como un paquete _apt_.   |
| Vínculos a ejemplos      | Otros ejemplos están disponibles [en línea](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Herramientas relacionadas en DSVM      | TensorFlow Serving, MXNet Model Server  |



