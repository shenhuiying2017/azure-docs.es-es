---
title: "Módulo de PowerShell para Machine Learning | Microsoft Docs"
description: "El módulo de PowerShell para Aprendizaje automático de Azure está disponible en versión preliminar pública. Use PowerShell para crear y administrar áreas de trabajo, experimentos, servicios web y mucho más."
keywords: "experimento,regresión lineal,algoritmos de aprendizaje automático,tutorial de aprendizaje automático,técnicas de modelado predictivo,experimento de ciencia de datos"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: garye;haining
ms.openlocfilehash: 47d005e584b6cb87d27a77f56ff619d342481aaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Módulo de PowerShell para Aprendizaje automático de Microsoft Azure
El módulo de PowerShell para Azure Machine Learning es una herramienta eficaz que permite usar Windows PowerShell para administrar áreas de trabajo, experimentos, conjuntos de datos, servicios web clásicos y mucho más.

Puede ver la documentación y descargar el módulo, junto con el código fuente completo, en [https://aka.ms/amlps](https://aka.ms/amlps). 

> [!NOTE]
> El módulo de PowerShell de Azure Machine Learning está actualmente en modo de versión preliminar. Este módulo se seguirá mejorando y ampliando durante este período de versión preliminar. Esté al tanto de más información y noticias en [Cortana Intelligence and Machine Learning Blog](https://blogs.technet.microsoft.com/machinelearning/) (Blog sobre Cortana Intelligence y Machine Learning).

## <a name="what-is-the-machine-learning-powershell-module"></a>¿Qué es el módulo de PowerShell de Aprendizaje automático?
El módulo de PowerShell Machine Learning es un módulo DLL basado en .NET que permite administrar totalmente áreas de trabajo de Azure Machine Learning, experimentos, conjuntos de datos, servicios web y puntos de conexión de servicio web desde Windows PowerShell. 

Junto con el módulo, puede descargar el código fuente completo que incluye un [nivel de API de C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs) claramente delimitado. Esto significa que puede hacer referencia a este archivo DLL desde su propio proyecto .NET y administrar Azure Machine Learning por medio de código. NET. Además, el archivo DLL depende de las API de REST subyacentes que puede usar directamente desde su cliente favorito.

## <a name="what-can-i-do-with-the-powershell-module"></a>¿Qué puedo hacer con el módulo de PowerShell?
Estas son algunas de las tareas que puede realizar con este módulo de PowerShell. Consulte la [documentación completa](https://aka.ms/amlps) sobre estas funciones y muchas otras.

* Aprovisionar una nueva área de trabajo mediante un certificado de administración ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Exportar e importar un archivo JSON que representa un gráfico de experimento ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Ejecutar un experimento ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Crear un servicio web a partir de un experimento de predicción ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Crear un punto de conexión en un servicio web publicado ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Invocar un punto de conexión de servicio web RRS o BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

A continuación, se ofrece un ejemplo rápido de cómo usar PowerShell para ejecutar un experimento existente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Para ver un caso de uso más detallado, consulte este artículo sobre cómo usar el módulo de PowerShell para automatizar una tarea solicitada habitualmente: [Creación de varios modelos de Machine Learning y puntos de conexión de servicio web a partir de un experimento mediante PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>¿Cómo empiezo?
Para empezar a usar PowerShell para Aprendizaje automático, descargue el [paquete de versión](https://github.com/hning86/azuremlps/releases) en GitHub y siga las [instrucciones de instalación](https://github.com/hning86/azuremlps/blob/master/README.md). Estas instrucciones explican cómo desbloquear el archivo DLL descargado y descomprimido y, después, importarlo en el entorno de PowerShell. La mayoría de los cmdlets requieren que se proporcione el identificador de área de trabajo, el token de autorización del área de trabajo y la región de Azure donde se encuentra el área de trabajo. La forma más sencilla de proporcionar estos valores es mediante un archivo config.json predeterminado. Las instrucciones también explican cómo configurar este archivo. 

Si lo desea, puede clonar el árbol git, modificar el código y compilarlo localmente mediante Visual Studio.

## <a name="next-steps"></a>Pasos siguientes
Puede encontrar la documentación completa para el módulo de PowerShell en [https://aka.ms/amlps](https://aka.ms/amlps). 

Para obtener un ejemplo más detallado extended sobre cómo usar el módulo en un escenario del mundo real, consulte [Creación de varios modelos de Machine Learning y puntos de conexión de servicio web a partir de un experimento mediante PowerShell](create-models-and-endpoints-with-powershell.md).
