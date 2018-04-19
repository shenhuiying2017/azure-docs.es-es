---
title: "Artículo de inicio rápido de Visual Studio Tools para Machine Learning en Azure | Microsoft Docs"
description: "En este artículo se describe cómo empezar a usar Visual Studio Tools para Machine Learning, desde la creación de un experimento y el entrenamiento de un modelo, hasta la operacionalización de un servicio web."
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
ms.openlocfilehash: bbcb2ea5a7ceeb976f590393608cc29c67d9a49e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools para AI
Visual Studio Tools para AI es una extensión de desarrollo para compilar, probar e implementar soluciones de Deep Learning y AI. Ofrece una integración sin problemas con Azure Machine Learning, principalmente, una vista del historial de ejecución que detalla el rendimiento de aprendizajes anteriores y métricas personalizadas. Ofrece una vista de explorador de ejemplos que permite examinar y arrancar un proyecto nuevo con [Microsoft Cognitive Toolkit (anteriormente, CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) y otro marco de aprendizaje profundo. Por último, proporciona un explorador para destinos de proceso que permite enviar trabajos para entrenar modelos en entornos remotos, como máquinas virtuales de Azure o servidores Linux con GPU. También facilita el acceso a [Azure Batch AI (versión preliminar)](https://docs.microsoft.com/azure/batch-ai/).
 
## <a name="getting-started"></a>Introducción 
Para comenzar, descargue e instale [Visual Studio](https://www.visualstudio.com/downloads/). Una vez abierto Visual Studio, siga los pasos que se describen a continuación:
1. Haga clic en la barra de menús de Visual Studio y seleccione "Extensiones y actualizaciones..."
2. Haga clic en la pestaña "En línea" y seleccione "Search Visual Studio Marketplace" (Buscar en Visual Studio Marketplace).
3. Busque "Visual Studio para AI". 
3. Haga clic en el botón **Descargar** . 
4. Tras la instalación, reinicie Visual Studio. 

Después de volver a cargar Visual Studio, la extensión está activa. [Más información sobre cómo buscar extensiones](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> Visual Studio Tools para AI necesita Visual Studio 2015 o 2017, edición profesional o empresa. No admite la versión de Apple OSX. 


## <a name="exploring-project-samples"></a>Explorar ejemplos de proyecto
Visual Studio Tools para AI incluye un explorador de ejemplos. El explorador de ejemplos facilita la detección de ejemplos y permite probarlos con unos pocos clics. Para abrir el explorador, haga lo siguiente:   
1. En la barra de menús, haga clic en **AI Tools**.
2. Haga clic en "Azure Machine Learning Gallery" (Galería de Azure Machine Learning).

Se abre una pestaña con todos los ejemplos de Azure ML.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Crear un proyecto nuevo desde el explorador de ejemplos 
Puede examinar los distintos ejemplos y obtener más información sobre ellos. A continuación, buscaremos el ejemplo "Classifying Iris" (Clasificación Iris). Para crear un nuevo proyecto basado en este ejemplo, siga estos pasos:
1. Haga clic en el botón **Instalar** en el proyecto de ejemplo y se abrirá un cuadro de diálogo nuevo. 
2. Seleccione un grupo de recursos, una cuenta y un área de trabajo.
3. Puede dejar el tipo de proyecto como General.
4. Escriba una ruta de acceso al proyecto y un nombre para el proyecto y, a continuación, presione Entrar. 
5. Se abre un cuadro de diálogo que le pregunta si quiere guardar la solución. Haga clic en Guardar. 

Una vez completado, se abre un nuevo proyecto en una nueva instancia de Visual Studio. 

> [!TIP]
> Debe tener la sesión iniciada para acceder a su recurso de Azure. Desde el terminal incrustado, escriba "az login" y siga las instrucciones. 

## <a name="submitting-experiment-with-the-new-project"></a>Enviar un experimento con el nuevo proyecto
El nuevo proyecto que se abre en Visual Studio envía un trabajo a un destino de cálculo (local o máquina virtual con Docker).
Para enviar el trabajo, haga lo siguiente: 
1. En el Explorador de soluciones, haga clic con el botón derecho en el archivo que quiere enviar y seleccione **Establecer como archivo de inicio**.
2. Seleccione el nombre del proyecto, haga clic con el botón derecho y seleccione **Enviar trabajo...**
3. Se abre un nuevo cuadro de diálogo que le permite elegir el clúster (o destino de proceso) para ejecutar el script.
4. Haga clic en **Enviar**.

Una vez enviado el trabajo, el terminal incrustado muestra el progreso de las ejecuciones.

## <a name="view-list-of-jobs"></a>Ver lista de trabajos
Una vez enviado el trabajo, puede mostrar la lista de trabajos desde el historial de ejecución.
1. En **Explorador de servidores**, haga clic en **AI Tools**.
2. A continuación, seleccione **Azure Machine Learning**
3. Haga clic en el menú **Trabajos**.

El explorador de trabajos muestra todos los experimentos enviados para este proyecto. 

## <a name="view-job-details"></a>Ver detalles del trabajo
Con la vista del explorador de trabajos abierta, haga clic en la primera ejecución de la lista.
Esta acción carga el panel Resumen de trabajos y el panel Logs and Outputs (Registros y salidas).

## <a name="next-steps"></a>pasos siguientes
> [!div class="nextstepaction"]
> [Cómo configurar Azure Machine Learning para que funcione con un IDE](./how-to-configure-your-IDE.md)
