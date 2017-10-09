---
title: Marcos de aprendizaje profundo y de AI - Azure | Microsoft Docs
description: Marcos de aprendizaje profundo y de AI
keywords: "herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 46662c15374f781a6527bb1435d883ae060c63f4
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="deep-learning-and-ai-frameworks"></a>Marcos de aprendizaje profundo y de AI
[Data Science Virtual Machine](http://aka.ms/dsvm) (DSVM) y [Deep Learning VM](http://aka.ms/dsvm/deeplearning) admiten varios marcos de aprendizaje profundo para compilar aplicaciones de inteligencia artificial (AI) con capacidades de análisis predictivo y capacidades cognitivas, como la comprensión de imágenes y de lenguajes. 

A continuación se muestra la información de todos los marcos de aprendizaje profundo disponibles en DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | Microsoft Cognitive Toolkit (CNTK) se instala en Python 2.7, en el entorno _root_, así como en Python 3.5, en el entorno _py35_.   |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo.     |
| Herramientas relacionadas en DSVM      | Keras      |
| ¿Cómo se usa/ejecuta?    | Abra Jupyter y busque la carpeta CNTK  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | En Linux, TensorFlow se instala en el entorno Python 2.7 (_root_), así como en el entorno Python 3.5 (_py35_). En Windows, TensorFlow se instala en el entorno Python 3.5 (_py35_).  |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo.     |
| Herramientas relacionadas en DSVM      | Keras      |
| ¿Cómo se usa/ejecuta?    | Abra Jupyter y busque la carpeta TensorFlow.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | Keras se instala en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_).   |
| Vínculos a ejemplos      | https://github.com/fchollet/keras/tree/master/examples      |
| Herramientas relacionadas en DSVM      | Microsoft Cognitive Toolkit, TensorFlow y Theano      |
| ¿Cómo se usa/ejecuta?    | Descargue los ejemplos desde la ubicación de Github, cópielos en un directorio en ~/notebooks y ábralos en Jupyter   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | Caffe se instala en `/opt/caffe`.    |
| Vínculos a ejemplos      | En `/opt/caffe/examples` se incluyen ejemplos.      |
| Herramientas relacionadas en DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>¿Cómo se usa/ejecuta?  

Use X2Go para iniciar sesión en la máquina virtual, inicie un terminal nuevo y escriba:

```
cd /opt/caffe/examples
jupyter notebook
```

Se abre una nueva ventana del explorador con blocs de notas de ejemplo.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | Caffe2 se instala en `/opt/caffe2`. También está disponible para el entorno conda de Python 2.7 (_root_).     |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo     |
| Herramientas relacionadas en DSVM      | Caffe      |
| ¿Cómo se usa/ejecuta?    | Abra Jupyter y desplácese al directorio Caffe2 para buscar blocs de notas de ejemplo. Algunos blocs de notas requieren que se establezca la raíz de Caffe2 en el código de Python. Escriba /opt/caffe2.   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | Chainer se instala en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_). También se instalan ChainerRL y ChainerCV.   |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo.      |
| Herramientas relacionadas en DSVM      | Caffe      |

### <a name="how-to-use--run-it"></a>¿Cómo se usa/ejecuta?  

En un terminal, active la versión de Python que quiera (_root_ o _py35_), ejecute _python_ e importe Chainer. En Jupyter, seleccione el kernel de Python 2.7 o 3.5 e importe Chainer.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo para H2O      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | Deep Water se instala en `/dsvm/tools/deep_water`.   |
| Vínculos a ejemplos      | Los ejemplos están disponibles a través del servidor de Deep Water.      |
| Herramientas relacionadas en DSVM      | H2o y Sparkling Water      |

### <a name="how-to-use--run-it"></a>¿Cómo se usa/ejecuta?  

Conéctese a la máquina virtual con X2Go. En un terminal, inicie el servidor de Deep Water:

    java -jar /dsvm/tools/deep_water/h2o.jar

Luego, abra un explorador y conéctese a `http://localhost:54321`.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | MXNet se instala en `C:\dsvm\tools\mxnet` en Windows y en `/dsvm/tools/mxnet` en Linux. Los enlaces de Python se instalan en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_). También se instalan los enlaces de R.   |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo.    |
| Herramientas relacionadas en DSVM      | Keras      |
| ¿Cómo se usa/ejecuta?    | Abra Jupyter y busque la carpeta mxnet  |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Sistema de aprendizaje profundo de NVIDIA para entrenar modelos de aprendizaje profundo de forma rápida      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | DIGITS se instala en `/dsvm/tools/DIGITS` y hay disponible un servicio denominado _digits_.   |
### <a name="how-to-use--run-it"></a>¿Cómo se usa/ejecuta?  

Inicie sesión en la máquina virtual con X2Go. En un terminal, inicie el servicio:

    sudo systemctl start digits

El servicio tarda cerca de un minuto en iniciarse. Abra un explorador web y vaya a `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Herramienta NVIDIA para consultar la actividad GPU      |
| Ediciones compatibles de DSVM      | Windows, Linux     |
| ¿Cómo se configura/instala en DSVM?  | _nvidia-smi_ está disponible en la ruta de acceso del sistema.   |
| ¿Cómo se usa/ejecuta? | Inicie un símbolo del sistema (en Windows) o un terminal (en Linux) y ejecute _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | Theano se instala en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_).   |
| Herramientas relacionadas en DSVM      | Keras      |
| ¿Cómo se usa/ejecuta?    | En un terminal, active la versión de Python que quiera (root o py35), ejecute Python e importe Theano. En Jupyter, seleccione el kernel de Python 2.7 o 3.5 e importe Theano.  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Marco de aprendizaje profundo      |
| Ediciones compatibles de DSVM      | Linux     |
| ¿Cómo se configura/instala en DSVM?  | Torch se instala en `/dsvm/tools/torch`. PyTorch se instala en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_).   |
| Vínculos a ejemplos      | Los ejemplos de Torch están en `/dsvm/samples/torch`. Los ejemplos de PyTorch están en `/dsvm/samples/pytorch`.      |


