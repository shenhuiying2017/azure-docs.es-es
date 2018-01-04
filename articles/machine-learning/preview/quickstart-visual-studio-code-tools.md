---
title: "Artículo de inicio rápido para herramientas de Visual Studio Code para Machine Learning en Azure | Microsoft Docs"
description: "En este artículo se describe cómo empezar a usar las herramientas de Visual Studio Code para Machine Learning, desde la creación de un experimento y el entrenamiento de un modelo, hasta la operacionalización de un servicio web."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: 7ec74dd9901171b7ec62b0bac5bfb1da80fee5ca
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="visual-studio-code-tools-for-ai"></a>Herramientas de Visual Studio Code para AI
Herramientas de Visual Studio Code para AI es una extensión de desarrollo para compilar, probar e implementar soluciones de aprendizaje profundo/AI. Ofrece una integración sin problemas con Azure Machine Learning, principalmente, una vista del historial de ejecución que detalla el rendimiento de aprendizajes anteriores y métricas personalizadas. Ofrece una vista de explorador de ejemplos que permite examinar y arrancar un proyecto nuevo con [Microsoft Cognitive Toolkit (anteriormente, CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) y otro marco de aprendizaje profundo. Por último, proporciona un explorador para destinos de proceso que permite enviar trabajos para entrenar modelos en entornos remotos, como máquinas virtuales de Azure o servidores Linux con GPU. 
 
## <a name="getting-started"></a>Introducción 
Para empezar, descargue e instale [Visual Studio Code](https://code.visualstudio.com/Download). Una vez abierto Visual Studio Code, siga los pasos que se describen a continuación:
1. Haga clic en el icono de extensión de la barra de actividad. 
2. Busque "Herramientas de Visual Studio Code para AI". 
3. Haga clic en el botón **Instalar**. 
4. Al finalizar la instalación, haga clic en el botón **Volver a cargar**. 

Una vez que Visual Studio Code se vuelva a cargar, la extensión estará activa. [Más información acerca de cómo instalar las extensiones](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Explorar ejemplos de proyecto
Herramientas de Visual Studio Code para AI incluye un explorador de ejemplos. El explorador de ejemplos facilita la detección de ejemplos y permite probarlos con unos pocos clics. Para abrir el explorador, haga lo siguiente:   
1. Abra la paleta de comandos (Vista > **Paleta de comandos** o presione **Ctrl + Mayús + P**).
2. Escriba "AI Sample". 
3. Recibirá la recomendación "AI: Open Azure ML Sample Explorer". Selecciónela y presione Entrar. 

También puede hacer clic en el icono del explorador de ejemplos.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Crear un proyecto nuevo desde el explorador de ejemplos 
Puede examinar los distintos ejemplos y obtener más información sobre ellos. A continuación, buscaremos el ejemplo "Classifying Iris" (Clasificación Iris). Para crear un nuevo proyecto basado en este ejemplo, siga estos pasos:
1. Haga clic en el botón Instalar del proyecto de ejemplo y siga los comandos que aparecen para guiarle a lo largo de la creación de un proyecto nuevo. 
2. Elija un nombre para el proyecto; por ejemplo "Iris".
3. Elija una ruta de acceso de carpeta para crear el proyecto y presione Entrar. 
4. Seleccione un área de trabajo y presione Entrar.

Se creará el proyecto.

> [!TIP]
> Debe tener la sesión iniciada para acceder a su recurso de Azure. Desde el terminal incrustado, escriba "az login" y siga las instrucciones. 

## <a name="submitting-experiment-with-the-new-project"></a>Enviar un experimento con el nuevo proyecto
Con el nuevo proyecto abierto en Visual Studio Code, enviamos un trabajo a nuestro destino de proceso distinto (local y VM con Docker).
Herramientas de Visual Studio Code para AI proporciona varias maneras de enviar un experimento. 
1. Menú contextual (clic derecho) - **AI: Submit Job**.
2. Desde la paleta de comandos: "AI: Submit Job".
3. También puede ejecutar el comando directamente mediante Azure CLI o comandos de Machine Learning, con el terminal incrustado.

Abra iris_sklearn.py, haga clic con el botón derecho y seleccione **AI: Submit Job**.
1. Seleccione la plataforma: "Azure Machine Learning".
2. Seleccione la configuración de ejecución: "Docker-Python".

> [!NOTE]
> Si es la primera vez que envía un trabajo, recibirá el mensaje "No Machine Learning configuration found, creating..." (No se encontró ninguna configuración de Machine Learning; se está creando...). Se abre un archivo JSON. Guárdelo (**CTRL+S**).

Una vez enviado el trabajo, el terminal incrustado muestra el progreso de las ejecuciones. 

## <a name="view-list-of-jobs"></a>Ver lista de trabajos
Una vez enviados los trabajos, puede enumerarlos desde el historial de ejecución.
1. Abra la paleta de comandos (Vista > **Paleta de comandos** o presione **Ctrl + Mayús + P**).
2. Escriba "AI List."
3. Obtendrá la recomendación "AI: List Jobs". Selecciónela y presione Entrar.

La vista de lista de trabajos se abre y muestra todas las ejecuciones y alguna información relacionada.

## <a name="view-job-details"></a>Ver detalles del trabajo
Con la vista de lista de trabajos abierta, haga clic en la primera ejecución de la lista.
Para profundizar en los resultados de un trabajo, haga clic en **Id. de trabajo** en la parte superior para ver información detallada. 

## <a name="next-steps"></a>pasos siguientes
> [!div class="nextstepaction"]
> [Cómo configurar Azure Machine Learning para que funcione con un IDE](./how-to-configure-your-IDE.md)
