---
title: ALM en Azure Machine Learning| Microsoft Docs
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
ms.openlocfilehash: 9d1fcc761115c64fafb811d6ca1c2389babfdc15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Administración del ciclo de vida de las aplicaciones en Azure Machine Learning Studio
Azure Machine Learning Studio es una herramienta para desarrollar experimentos de Machine Learning y que se hacen operativos en la plataforma de nube de Azure. Es como IDE de Visual Studio y servicio en la nube escalable combinados en una sola plataforma. Es posible incorporar a Azure Machine Learning Studio prácticas de administración del ciclo de vida de las aplicaciones (ALM) estándar, desde el control de versiones de diversos recursos a la ejecución e implementación automatizadas. En este artículo se analizan algunas de las opciones y enfoques.

## <a name="versioning-experiment"></a>Control de versiones del experimento
Se recomiendan dos formas de aplicar el control de versiones a los experimentos. Puede confiar en el historial de ejecución integrado o exportar el experimento en formato de notación de objetos JavaScript (JSON) y administrarlo de forma externa. Cada enfoque tiene sus ventajas e inconvenientes.

### <a name="experiment-snapshots-using-run-history"></a>Instantáneas del experimento mediante el historial de ejecución
En el modelo de ejecución del experimento de aprendizaje de Azure Machine Learning Studio, cada vez que hace clic en el botón **Ejecutar** del editor de experimentos, se envía una instantánea inmutable del experimento al programador de trabajos. Si desea ver esta lista de instantáneas, haga clic en el botón **Historial de ejecución** en la barra de comandos de la vista del editor de experimentos.

![Botón Historial de ejecución](./media/version-control/runhistory.png)

A continuación, puede abrir la instantánea en modo de bloqueo haciendo clic en el nombre del experimento en el momento en que este se envió para su ejecución y se tomó la instantánea. Tenga en cuenta que solo el primer elemento de la lista, que representa el experimento actual, está en estado editable. Tenga en cuenta además que cada instantánea puede tener también diversos estados, incluidos completado (ejecución parcial), de error, de error (ejecución parcial) o de borrador.

![Lista del historial de ejecuciones](./media/version-control/runhistorylist.png)

Una vez que se abre, puede guardar el experimento de la instantánea como experimento nuevo para luego modificarlo. Si la instantánea del experimento contiene recursos como modelo entrenado, transformación o un conjunto de datos que tienen versiones actualizadas, la instantánea conserva las referencias a la versión original cuando se tomó. Si guarda la instantánea bloqueada como experimento nuevo, Azure Machine Learning Studio detecta la existencia de una versión más reciente de estos recursos y los actualiza automáticamente en el experimento nuevo.

Si elimina el experimento, se eliminan todas las instantáneas de dicho experimento.

### <a name="exportimport-experiment-in-json-format"></a>Exportar o importar experimento en formato JSON
Las instantáneas del historial de ejecución conservan una versión inmutable del experimento en Azure Machine Learning Studio cada vez que se envía para su ejecución. También puede guardar una copia local del experimento y protegerla en el sistema de control de código fuente de su preferencia, como Team Foundation Server, para luego volver a crear un experimento a partir de ese archivo local. Puede usar los commandlets [Azure Machine Learning PowerShell](http://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) para hacerlo.

El archivo JSON es una representación textual del gráfico del experimento, en la que podría incluirse una referencia a los recursos del área de trabajo, como un conjunto de datos o un modelo entrenado. No contiene una versión serializada del recurso. Si intenta importar el documento JSON de nuevo al área de trabajo, esos recursos a los que se hace referencia ya deben existir con los mismos identificadores de recurso a los que se hace referencia en el experimento. En caso contrario, no podrá tener acceso al experimento importado.

## <a name="versioning-trained-model"></a>Control de versiones de un modelo entrenado
Un modelo entrenado en Azure Machine Learning se serializa en un formato conocido como archivo .iLearner y se almacena en la cuenta de Azure Blob Storage asociada al área de trabajo. Una forma de obtener una copia del archivo .iLearner es a través de la API de reentrenamiento. En [este artículo](retrain-models-programmatically.md) se explica cómo funciona la API de reentrenamiento. Pasos de alto nivel:

1. Actualice el experimento de entrenamiento.
2. Agregue un puerto de salida de servicio web al módulo Entrenar modelo o al módulo que produce el modelo entrenado, como el módulo Optimizar el modelo Hiperparámetros o Crear modelo R.
3. Ejecute el experimento de entrenamiento y, a continuación, impleméntelo como servicio web de entrenamiento de modelos.
4. Llame al punto de conexión BES del servicio web de entrenamiento y especifique el nombre de archivo .iLearner deseado y la ubicación de la cuenta de Blob Storage donde se almacenará.
5. Recopile el archivo .iLearner producido una vez finalizada la llamada a BES.

Otra forma de recuperar el archivo .iLearner es a través del commandlet [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput) de PowerShell. Esto podría ser más fácil si solo desea obtener una copia del archivo .iLearner sin necesidad de volver a entrenar el modelo de manera programática.

Una vez que tiene el archivo .iLearner que contiene el modelo entrado, puede emplear su propia estrategia de control de versiones. La estrategia puede ser tan simple como aplicar un prefijo o postfijo como una convención de nomenclatura y solo dejar el archivo .iLearner en Blob Storage, o bien como copiarlo e importarlo en el sistema de control de versiones.

El archivo .iLearner guardado se puede usar para puntuar a través de servicios web implementados.

## <a name="versioning-web-service"></a>Control de versiones de un servicio web
Puede implementar dos tipos de servicios web a partir de un experimento de Azure Machine Learning. El servicio web clásico está estrechamente vinculado con el experimento, así como con el área de trabajo. El nuevo servicio web usa el marco de Azure Resource Manager y ya no está vinculado con el área de trabajo ni el experimento original.

### <a name="classic-web-service"></a>Servicio web clásico
Para aplicar control de versiones a un servicio web clásico, puede aprovechar la construcción del punto de conexión de servicio web. Este es un flujo típico:

1. A partir de su experimento predictivo, implementa un nuevo servicio web clásico, que contiene un punto de conexión predeterminado.
2. Crea un nuevo punto de conexión llamado ep2, que muestra la versión actual del experimento o modelo entrenado.
3. Vuelve y actualiza el experimento predictivo y el modelo entrenado.
4. Vuelve a implementar el experimento predictivo, que actualizará entonces el punto de conexión predeterminado. Sin embargo, esto no modificará ep2.
5. Crea un punto de conexión adicional llamado ep3, que muestra la nueva versión del experimento y modelo entrenado.
6. Vuelva al paso 3 si es necesario.

Con el tiempo, es posible que se creen muchos puntos de conexión en el mismo servicio web. Cada uno de ellos representa una copia a un momento dado del experimento que contiene la versión a un momento dado del modelo entrenado. Podrás usar entonces lógica externa para determinar el punto de conexión al que llamar, lo que efectivamente significa seleccionar una versión del modelo entrenado para la ejecución de la puntuación.

También puede crear muchos puntos de conexión de servicio web idénticos y, a continuación, aplicar revisiones de diversas versiones del archivo .iLearner al punto de conexión para lograr un efecto similar. En [este artículo](create-models-and-endpoints-with-powershell.md) se explica con más detalla cómo hacerlo.

### <a name="new-web-service"></a>Servicio web nuevo
Si crea un nuevo servicio web basado en Azure Resource Manager, la construcción del punto de conexión dejará de estar disponible. En su lugar, puede generar archivos de definición del servicio web (WSD), en formato JSON, a partir de su experimento predictivo mediante el commandlet [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) de PowerShell, o bien mediante el commandlet [*Export-AzureRmMlWebservice*](https://msdn.microsoft.com/library/azure/mt767935.aspx) de PowerShell a partir de un servicio web ya implementado basado en Resource Manager.

Una vez que tiene el archivo WSD exportado y controla sus versiones, también puede implementar WSD como servicio web nuevo en un plan de servicio web distinto en una región de Azure diferente. Basta con que se asegure de proporcionar la configuración adecuada de la cuenta de almacenamiento, así como el identificador del nuevo plan de servicio web. Para aplicar revisiones en diversos archivos iLearner, puede modificar el archivo WSD y actualizar la referencia de ubicación del modelo entrenado, además de implementarlo como nuevo servicio web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar la ejecución e implementación del experimento
Un aspecto importante de ALM es poder automatizar el proceso de ejecución e implementación de la aplicación. En Azure Machine Learning, puede hacerlo mediante el [módulo de PowerShell](http://aka.ms/amlps). Este es un ejemplo de pasos completos que son pertinentes para un proceso de ejecución e implementación automatizado de ALM estándar mediante el [módulo de Azure Machine Learning Studio PowerShell](http://aka.ms/amlps). Cada paso está vinculado a uno o varios commandlets de PowerShell que puede usar para realizar ese paso.

1. [Cargue un conjunto de datos](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copie un experimento de entrenamiento en el área de trabajo a partir de un [área de trabajo](https://github.com/hning86/azuremlps#copy-amlexperiment) o de [Galería](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), o [importe](https://github.com/hning86/azuremlps#import-amlexperimentgraph) un experimento [exportado](https://github.com/hning86/azuremlps#export-amlexperimentgraph) desde el disco local.
3. [Actualice el conjunto de datos](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) en el experimento de entrenamiento.
4. [Ejecute el experimento de entrenamiento](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promocione el modelo entrenado](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copie un experimento predictivo](https://github.com/hning86/azuremlps#copy-amlexperiment) en el área de trabajo.
7. [Actualice el modelo entrenado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) en el experimento predictivo.
8. [Ejecute el experimento predictivo](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Implemente un servicio web](https://github.com/hning86/azuremlps#new-amlwebservice) a partir del experimento predictivo.
10. Pruebe el punto de conexión [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) o [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) de servicio web.

## <a name="next-steps"></a>Pasos siguientes
* Descargue el módulo [Azure Machine Learning Studio PowerShell](http://aka.ms/amlps) y empiece a automatizar sus tareas de ALM.
* Obtenga información sobre cómo [crear y administrar un gran número de modelos de ML con solo un único experimento](create-models-and-endpoints-with-powershell.md) a través de PowerShell y la API de reentrenamiento.
* Obtenga más información sobre cómo [implementar los servicios web de Azure Machine Learning](publish-a-machine-learning-web-service.md).
