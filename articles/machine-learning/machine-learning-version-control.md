---
title: ALM en Azure ML | Microsoft Docs
description: "Aplicar procedimientos recomendados de administración del ciclo de vida de las aplicaciones en Azure Machine Learning Studio"
keywords: "ALM, AML, Azure ML, administración del ciclo de vida de las aplicaciones, control de versiones"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: haining
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 614aa45b365abe90fcc2e75fcde50f1c62a95ed8


---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Administración del ciclo de vida de las aplicaciones en Azure Machine Learning Studio
Azure Machine Learning Studio es una herramienta para desarrollar experimentos de Machine Learning y hacerlos operativos en la nube de Azure. Es como IDE de Visual Studio y servicios de hospedaje de servicio web escalables mezclados en una sola plataforma. Por lo tanto, solo es lógico/natural incorporar procedimientos de ALM (administración del ciclo de vida de las aplicaciones) estándar, desde control de versiones de diversos recursos a ejecución e implementación automatizadas. en Azure Machine Learning Studio. Este artículo pretende cubrir algunas de las opciones y los enfoques. 

## <a name="versioning-experiment"></a>Control de versiones del experimento
Se recomiendan dos formas de aplicar el control de versiones a los experimentos. Puede confiar en el historial de ejecución integrado o exportar el experimento en formato JSON y administrarlo de forma externa. Cada enfoque tiene sus ventajas e inconvenientes.

### <a name="experiment-snapshots-using-run-history"></a>Instantáneas del experimento mediante el historial de ejecución
El modelo de ejecución del experimento de Azure Machine Learning es que cada vez que hace clic en el botón Ejecutar del editor de experimentos, se envía una instantánea inmutable del experimento al programador de trabajos. Puede ver esta lista de instantáneas haciendo clic en el botón "HISTORIAL DE EJECUCIÓN" de la barra de comandos de la vista del editor de experimentos.

![Botón Historial de ejecución](media/machine-learning-version-control/runhistory.png)

A continuación, puede abrir una instantánea en modo de bloqueo haciendo clic en el nombre del experimento en el momento en que este se envió para su ejecución y se tomó la instantánea. Tenga en cuenta que solo el primer elemento de la lista, que representa el experimento actual, está en estado editable. Tenga en cuenta además que cada instantánea puede estar también en diversos estados, incluidos completado (ejecución parcial), de error, de error (ejecución parcial) o de borrador.

![Lista de HISTORIAL DE EJECUCIÓN](media/machine-learning-version-control/runhistorylist.png)

Al abrirse, puede guardar el experimento de la instantánea como nuevo experimento y modificarlo a continuación. Una advertencia es que si la instantánea del experimento contiene recursos como modelo entrenado, transformación, conjunto de datos, etc., que desde ese momento tienen versiones actualizadas, la instantánea conserva las referencias a la versión original cuando se tomó. Sin embargo, si guarda la instantánea bloqueada como nuevo experimento, ML Studio detecta la existencia de una versión más reciente de estos recursos, actualizándolos a esta. 

Tenga en cuenta además que si elimina el experimento, se eliminan también todas las instantáneas de dicho experimento.

### <a name="exportimport-experiment-in-json-format"></a>Exportar o importar experimento en formato JSON
Aunque las instantáneas de historial de ejecución conservan una versión inmutable del experimento en ML Studio cada vez que se envía para su ejecución, a veces desea guardar de todas formas una copia local del experimento y quizás consultarla en su sistema de control de código fuente favorito, como Team Foundation Server, y volver a crear posteriormente un experimento de ese archivo local. Puede usar el commandlet [Azure ML PowerShell](http://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) para realizar esto.

Tenga en cuenta, sin embargo, que el archivo JSON es una representación textual del gráfico del experimento, en la que puede hacerse referencia a recursos del área de trabajo como conjunto de datos o modelos entrenados. Pero NO contiene una versión serializada de dichos recursos. De modo que, si intenta importar el documento JSON de nuevo en el área de trabajo, esos recursos a los que se hace referencia deben existir ya con los mismos identificadores de recurso a los que se hace referencia en el experimento, ya que de lo contrario no podrá tener acceso al experimento importado.

## <a name="versioning-trained-model"></a>Control de versiones de un modelo entrenado
Un modelo entrenado de Azure ML se serializa en un formato conocido como archivo .iLearner y se almacena en la cuenta de Azure Blob Storage asociada al área de trabajo. Una forma de obtener una copia del archivo iLearner es a través de la API de reciclaje. En este [artículo](machine-learning-retrain-models-programmatically.md) se explica con mucho más detalle el funcionamiento de la API de reciclaje. Pero los pasos de alto nivel son los siguientes:

1. Actualice el experimento de entrenamiento.
2. Agregue un puerto de salida de servicio web al módulo Entrenar modelo o al módulo que produce el modelo entrenado, como el módulo Optimizar el modelo Hiperparámetros o el módulo Crear modelo R.
3. Ejecute el experimento de entrenamiento y, a continuación, impleméntelo como servicio web de entrenamiento de modelos. 
4. Llame al punto de conexión BES del servicio web de entrenamiento y especifique el nombre de archivo .iLearner deseado y la ubicación de la cuenta de Azure Blob Storage donde se almacenará.
5. Recopile el archivo .iLearner producido una vez finalizada la llamada a BES.

Otra forma de recuperar el archivo .iLearner es a través del commandlet de PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Esto podría ser más fácil si solo desea obtener una copia del archivo .iLearner sin necesidad de reciclar el modelo de manera programática.

Una vez que tenga el archivo .iLearner que contiene el modelo entrenado, podrá emplear su propia estrategia de control de versiones, que abarca desde cosas tan sencillas como aplicar un prefijo o sufijo como convención de nomenclatura y simplemente dejar el archivo .iLearner en Azure Blob Storage o copiarlo o importarlo en su sistema de control de versión.

El archivo .iLearner guardado se puede usar para puntuar a través de servicios web implementados.

## <a name="versioning-web-service"></a>Control de versiones de un servicio web
Puede implementar dos tipos de servicios web a partir de un experimento de Azure ML. El servicio web clásico está estrechamente vinculado con el experimento, así como con el área de trabajo. El nuevo servicio web aprovecha el marco de administración de recursos de Azure y ya no está vinculado con el experimento original ni con el área de trabajo. 

### <a name="classic-web-service"></a>Servicio web clásico
Para aplicar control de versiones a un servicio web clásico, puede aprovechar la construcción del punto de conexión de servicio web. Este es un flujo típico:

1. A partir de su experimento predictivo, implementa un nuevo servicio web clásico, que contiene un punto de conexión predeterminado.
2. A continuación, crea un nuevo punto de conexión llamado ep2, que muestra la versión actual del experimento o modelo entrenado.
3. Después vuelve y actualiza su experimento predictivo y modelo entrenado.
4. Luego puede volver a implementar el experimento predictivo, que actualizará entonces el punto de conexión predeterminado. Sin embargo, esto no modificará ep2.
5. Ahora puede crear un punto de conexión ep3 adicional, que muestra la nueva versión del experimento y modelo entrenado. 
6. Vuelva al paso 3 si es necesario.

Con el tiempo, es posible que se creen muchos puntos de conexión en el mismo servicio web, representando cada uno una copia a un momento dado del experimento que contiene la versión a un momento dado del modelo entrenado. Podrás usar entonces lógica externa para determinar el punto de conexión al que llamar, lo que efectivamente significa seleccionar una versión del modelo entrenado para la ejecución de la puntuación.

También puede crear muchos puntos de conexión de servicio web idénticos y, a continuación, aplicar revisiones de diversas versiones del archivo .iLearner al punto de conexión para lograr un efecto similar. En este [artículo](machine-learning-create-models-and-endpoints-with-powershell.md) se explica con más detalle cómo hacerlo.

### <a name="new-web-service"></a>Servicio web nuevo
Si crea un nuevo servicio web basado en Azure Resource Manager, la construcción del punto de conexión dejará de estar disponible. En su lugar, puede generar archivos WSD (definición del servicio web), en formato JSON, a partir de su experimento predictivo mediante el commandlet de PowerShell [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment), o bien mediante el commandlet de PowerShell [*Export-AzureRmMlWebservice*](https://msdn.microsoft.com/library/azure/mt767935.aspx) a partir de un servicio web basado en Resource Manager ya implementado. 

Una vez que tenga exportados la versión y el archivo WSD, contrólelos. También puede implementar WSD como nuevo servicio web en un plan de servicio web y una región de Azure diferentes. Basta con que se asegure de proporcionar la configuración adecuada de la cuenta de almacenamiento, así como el identificador del nuevo plan de servicio web. Para aplicar revisiones en diversos archivos iLearner, puede modificar el archivo WSD y actualizar la referencia de ubicación del modelo entrenado, además de implementar WSD como nuevo servicio web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar la ejecución e implementación del experimento
Un aspecto importante de ALM es poder automatizar el proceso de ejecución e implementación de la aplicación. En Azure ML, puede hacerlo mediante el [módulo de PowerShell](http://aka.ms/amlps). Este es un ejemplo de pasos completos que son pertinentes para un proceso de ejecución e implementación automatizado de ALM estándar mediante el [módulo de Azure ML Studio PowerShell](http://aka.ms/amlps). Cada paso está vinculado a uno o varios commandlets de PowerShell que puede aprovechar para realizar ese paso.

1. [Cargue un conjunto de datos](https://github.com/hning86/azuremlps#upload-amldataset). 
2. Copie un experimento de entrenamiento en el área de trabajo a partir de un [área de trabajo](https://github.com/hning86/azuremlps#copy-amlexperiment) o de [Galería](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), o [importe](https://github.com/hning86/azuremlps#import-amlexperimentgraph) un experimento [exportado](https://github.com/hning86/azuremlps#export-amlexperimentgraph) desde el disco local.
3. [Actualice el conjunto de datos](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) en el experimento de entrenamiento.
4. [Ejecute el experimento de entrenamiento](https://github.com/hning86/azuremlps#start-amlexperiment)
5. [Promocione el modelo entrenado](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copie un experimento predictivo](https://github.com/hning86/azuremlps#copy-amlexperiment) en el área de trabajo.
7. [Actualice el modelo entrenado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) en el experimento predictivo.
8. [Ejecute el experimento predictivo](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Implemente un servicio web](https://github.com/hning86/azuremlps#new-amlwebservice) a partir del experimento predictivo.
10. Pruebe el punto de conexión [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) o [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) de servicio web 

## <a name="next-steps"></a>Pasos siguientes
* Descargue el módulo de [Azure ML Studio PowerShell](http://aka.ms/amlps) y empiece a automatizar sus tareas de ALM.
* Obtenga información sobre cómo [crear y administrar un gran número de modelos de ML con solo un único experimento](machine-learning-create-models-and-endpoints-with-powershell.md) a través de PowerShell y la API de reciclaje.
* Obtenga más información sobre cómo [implementar Servicios web de Azure ML](machine-learning-publish-a-machine-learning-web-service.md).




<!--HONumber=Nov16_HO3-->


