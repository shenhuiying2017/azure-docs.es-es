---
title: Ejemplos y tutoriales para Data Science Virtual Machine en Azure | Microsoft Docs
description: Ejemplos y tutoriales para Data Science Virtual Machine.
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
ms.openlocfilehash: 89bb5d255db9ab266d04169a3101e2b694236029
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Ejemplos de Data Science Virtual Machines (DSVM)

Las instancias de DSVM incluyen ejemplos totalmente desarrollados en formato de Jupyter Notebooks y otros que no se basan en Jupyter. Puede acceder a Jupyter haciendo clic en el icono `Jupyter` en el menú de escritorio o de una aplicación.  
> [!NOTE]
> Hacer referencia a la sección [Acceso a Jupyter](#access-jupyter) para habilitar Jupyter Notebooks en su instancia de DSVM.

## <a name="quick-reference-of-samples"></a>Referencia rápida de muestras
| Categoría de ejemplos | Descripción | Ubicaciones |
| ------------- | ------------- | ------------- |
| Lenguaje **R**  | Ejemplos en **R** que explican escenarios como la conexión con los almacenes de datos en la nube de Azure, comparación de R de código abierto y Microsoft R y puesta en marcha de modelos en Microsoft R Server o SQL Server. <br/> [Captura de pantalla](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Lenguaje **Python**  | Ejemplos de **Python** que explican escenarios como la conexión con los almacenes de datos en nube de Azure y el uso con **Azure Machine Learning**.  <br/> [Captura de pantalla](#python-language) | <br/>`~notebooks` <br/><br/>|
| Lenguaje **Julia**  | Ejemplo en **Julia** que ofrece detalles sobre el trazado en Julia, el aprendizaje profundo en Julia, llamar a C y Python desde Julia etc. <br/> [Captura de pantalla](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Los ejemplos de aprendizaje profundo publicados por el equipo de Cognitive Toolkit en Microsoft.  <br/> [Captura de pantalla](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXnet** Notebooks  | Ejemplos de aprendizaje profundo usando las redes neuronales basadas en **MXnet**. Hay una variedad de blocs de notas que van desde escenarios principiantes hasta avanzados.  <br/> [Captura de pantalla](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | Interacción con **Azure Machine Learning Studio** y creación de puntos de conexión de servicio web a partir de modelos entrenados localmente, para los flujos de trabajo de puntuación basada en la nube. <br/> [Captura de pantalla](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | Ejemplos de aprendizaje profundo usando las redes neuronales basadas en **caffe2**. Hay varios blocs de notas diseñados para que los usuarios se familiaricen con caffe2 y cómo utilizarlo de forma eficiente, incluidos ejemplos como la creación de conjuntos de datos de procesamiento previo de imagen, creación de conjuntos de datos, regresión y el uso de modelos previamente entrenados. <br/> [Captura de pantalla](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Ejemplos basados en Python que usan **H2O** para numerosos problemas de escenarios reales. <br/> [Captura de pantalla](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Lenguaje **SparkML**  | Ejemplo de uso de características y funcionalidades del kit de herramientas **MLlib** de Spark s a través de **pySpark 2.0** en **Apache Spark 2.0**.  <br/> [Captura de pantalla](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| Lenguaje **MMLSpark**  | Una variedad de ejemplos de uso de **MMLSpark - Microsoft Machine Learning para Apache Spark**, que es una plataforma que proporciona una serie de herramientas de aprendizaje profundo y ciencia de datos para **Apache Spark**. <br/> [Captura de pantalla](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Varios ejemplos diferentes de red neuronal y técnicas implementadas mediante la plataforma **TensorFlow**. <br/> [Captura de pantalla](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Ejemplos de Machine Learning estándar en **XGBoost** para escenarios como clasificación, regresión etc. <br/> [Captura de pantalla](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acceso a Jupyter 

Visite la página de inicio de Jupyter yendo a **`https://localhost:9999`** en Windows o **`https://localhost:8000`** en Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Habilitación del acceso de Jupyter desde el explorador

**Windows DSVM**

Ejecute **`Jupyter SetPassword`** desde el acceso directo del escritorio y siga el símbolo del sistema para establecer o restablecer la contraseña para Jupyter e iniciar el proceso de Jupyter. 
<br/>![Habilitación de la excepción de Jupyter](./media/jupyter-setpassword.png)<br/>
Puede acceder a la página de inicio de Jupyter una vez que el proceso de Jupyter se haya iniciado correctamente en su máquina virtual visitando **`https://localhost:9999`** en el explorador. Consulte la captura de pantalla para agregar la excepción y habilitar el acceso de Jupyter en el explorador
<br/>![Habilitación de la excepción de Jupyter](./media/windows-jupyter-exception.png)<br/>
Inicie sesión con la nueva contraseña que acaba de establecer.
<br/>
**Linux DSVM**

Puede tener acceso a la página de inicio de Jupyter en la máquina virtual visitando **`https://localhost:8000`** en el explorador. Consulte la captura de pantalla para agregar la excepción y habilitar el acceso de Jupyter en el explorador.
<br/>![Habilitación de la excepción de Jupyter](./media/ubuntu-jupyter-exception.png)<br/>
Inicie sesión con la misma contraseña que ha usado para el inicio de sesión de DSVM.
<br/>

**Página de inicio de Jupyter**
<br/>![Página de inicio de Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Lenguaje R 
<br/>![Ejemplos de R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Lenguaje Python
<br/>![Ejemplos de Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Lenguaje Julia 
<br/>![Ejemplos de Julia](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![Ejemplos de CNTK](./media/cntk-samples2.png)<br/>
<br/>![Ejemplos de CNTK](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXnet
<br/>![Ejemplos de MXnet](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Ejemplos de AzurekML](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![Ejemplos de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Ejemplos de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Ejemplos de SparkML](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Ejemplos de TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Ejemplos de XGBoost](./media/xgboost-samples.png)<br/>

