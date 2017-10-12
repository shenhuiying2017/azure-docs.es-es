---
title: "Creación de varios modelos a partir de un experimento | Microsoft Docs"
description: "Use PowerShell para crear varios modelos de Aprendizaje automático y puntos de conexión de servicio web con el mismo algoritmo pero con conjuntos de datos de entrenamiento distintos."
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: garye;haining
ms.openlocfilehash: cc938fdaa6843f7c9e974d9b88a9b682b4678493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Creación de varios modelos de aprendizaje automático y puntos de conexión de servicio web a partir de un experimento mediante PowerShell
Este es un problema común del aprendizaje automático: quiere crear muchos modelos que tienen el mismo flujo de trabajo de entrenamiento y utilizan el mismo algoritmo, pero tienen conjuntos de datos de entrenamiento distintos como entrada. Este artículo muestra cómo hacer esto a escala en Estudio de aprendizaje automático de Azure simplemente con un solo experimento.

Por ejemplo, digamos que posee una empresa de franquicias de alquiler de bicicletas global. Desea crear un modelo de regresión para predecir la demanda de alquiler basada en datos históricos. Tiene 1000 ubicaciones de alquiler en todo el mundo y ha recopilado un conjunto de datos para cada ubicación que incluye características importantes como fecha, hora, meteorología y tráfico que son específicas de cada ubicación.

Puede entrenar el modelo una vez usando una versión combinada de todos los conjuntos de datos en todas las ubicaciones. Pero dado que cada una de las ubicaciones tiene un entorno único, un mejor enfoque sería entrenar el modelo de regresión por separado mediante el conjunto de datos de cada ubicación. De este modo, cada modelo entrenado podría tener en cuenta los diferentes tamaños de tienda, el volumen, la geografía, la población, el entorno de tráfico preparado para bicicletas, *etc*.

Ese puede que sea el mejor enfoque, pero no desea crear 1000 experimentos de entrenamiento en Aprendizaje automático de Azure cada uno de los cuales representando una ubicación única. Además de ser una tarea abrumadora, también parece bastante ineficaz, ya que cada experimento tendría exactamente los mismos componentes, excepto el conjunto de datos de entrenamiento.

Por suerte, podemos lograrlo con la [API para volver a entrenar de Aprendizaje automático de Azure](retrain-models-programmatically.md) y automatizando la tarea con [PowerShell de Aprendizaje automático de Azure](powershell-module.md).

> [!NOTE]
> Para que nuestro ejemplo se ejecute más rápido, reduciremos el número de ubicaciones de 1000 a 10. Pero se aplican los mismos principios y procedimientos a 1000 ubicaciones. La única diferencia es que si desea entrenar con 1000 conjuntos de datos, probablemente piense en ejecutar los siguientes scripts de PowerShell en paralelo. Cómo hacerlo queda fuera del ámbito de este artículo, pero puede encontrar ejemplos de subprocesamiento múltiple de PowerShell en Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configuración del experimento de entrenamiento
Vamos a usar un [experimento de entrenamiento](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) de ejemplo que ya hemos creado en la [Galería de Cortana Intelligence](http://gallery.cortanaintelligence.com). Abra este experimento en su área de trabajo [Estudio de aprendizaje automático de Azure](https://studio.azureml.net) .

> [!NOTE]
> Para seguir este ejemplo, puede que le interese usar un área de trabajo estándar en lugar de un área de trabajo gratis. Crearemos un punto de conexión para cada cliente (para un total de 10 puntos de conexión) y que requerirá un área de trabajo estándar, ya que un área de trabajo gratis se limitado a 3 puntos de conexión. Si solo tiene un área de trabajo gratis, solo tiene que modificar los scripts siguientes para permitir solo 3 ubicaciones.
> 
> 

El experimento usa un módulo **Importar datos** para importar el conjunto de datos de entrenamiento *customer001.csv* desde una cuenta de almacenamiento de Azure. Supongamos que hemos recopilado los conjuntos de datos de entrenamiento de todas las ubicaciones de alquiler de bicicletas y los hemos almacenado en la misma ubicación de Blob Storage con nombres de archivo que van desde *rentalloc001.csv* a *rentalloc10.csv*.

![imagen](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Tenga en cuenta que se ha agregado el módulo **Web Service Output** (Resultados de servicio web) al módulo **Train Model** (Entrenar modelo).
Cuando este experimento se implementa como un servicio web, el punto de conexión asociado a esa salida devolverá el modelo entrenado con el formato de un archivo .ilearner.

Observe también que configuramos un parámetro de servicio web para la dirección URL que el módulo **Importar datos** utiliza. Esto nos permite utilizar el parámetro para especificar conjuntos de datos de entrenamiento individuales para entrenar el modelo para cada ubicación.
Podríamos haber hecho esto de otras maneras; por ejemplo, podríamos haber usado una consulta SQL con un parámetro de servicio web para obtener datos de una base de datos SQL Azure o se podría haber usado un módulo **Web Service Input** (Entrada del servicio web) para pasar un conjunto de datos al servicio web.

![imagen](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Ahora, vamos a ejecutar este experimento de entrenamiento utilizando el valor predeterminado *rental001.csv* como el conjunto de datos de entrenamiento. Si ve los resultados del módulo **Evaluate** (Evaluar), después de hacer clic en los resultados y seleccionar **Visualize** (Visualizar), verá que obtenemos un rendimiento aceptable de *AUC* = 0,91. En este momento, estamos listos para implementar un servicio web fuera de este experimento de entrenamiento.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implementación del entrenamiento y puntuación de servicios web
Para implementar el servicio web de entrenamiento, haga clic en el botón **Set Up Web Service** (Configurar servicio web) situado debajo del lienzo del experimento y seleccione **Deploy Web Service** (Implementar servicio web). Llame a este servicio web "Bike Rental Training" (Entrenamiento para alquiler de bicicletas).

Ahora debemos implementar el servicio web de puntuación.
Para ello, podemos hacer clic en **Set Up Web Service** (Configurar servicio web) bajo el lienzo y seleccionar **Predictive Web Service** (Servicio web predictivo). Esto crear un experimento de puntuación.
Necesitamos realizar algunos ajustes menores para que funcione como un servicio web, como puede ser eliminar la columna de etiquetas "cnt" de los datos de entrada y limitar la salida a solo el identificador de instancia y el valor predicho correspondiente.

Para ahorrarse ese trabajo, puede simplemente abrir el [experimento predictivo](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) en la galería que ya se ha preparado.

Para implementar el servicio web, ejecute el experimento predictivo y luego haga clic en el botón **Deploy Web Service** (Implementar servicio web) bajo el lienzo. Asigne el nombre "Bike Rental Scoring" (Puntuación de alquiler de bicicletas) al servicio web de puntuación.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Creación de 10 puntos de conexión de servicio web idénticos con PowerShell
Este servicio web incluye un punto de conexión predeterminado. Pero no estamos más interesados en el punto de conexión predeterminado, ya que no se puede actualizar. Lo que necesitamos hacer es crear 10 puntos de conexión adicionales, uno para cada ubicación. Haremos esto con PowerShell.

En primer lugar, configure nuestro entorno de PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Después, ejecute el siguiente comando de PowerShell:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Ahora hemos creado 10 puntos de conexión y todos ellos contienen el mismo modelo entrenado en *customer001.csv*. Puede verlos en el Portal de administración de Azure.

![imagen](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Actualización de los puntos de conexión para usar conjuntos de datos de entrenamiento independientes mediante PowerShell
El siguiente paso consiste en actualizar los puntos de conexión con modelos entrenados excepcionalmente en datos individuales de cada cliente. Pero primero necesitamos generar estos modelos desde el servicio web **Bike Rental Training** (Entrenamiento para alquiler de bicicletas). Volvamos al servicio web **Bike Rental Training** (Entrenamiento para alquiler de bicicletas). Es necesario llamar a su punto de conexión BES 10 veces con 10 conjuntos de datos de entrenamiento distintos para generar 10 modelos diferentes. Vamos a usar el cmdlet de PowerShell **InovkeAmlWebServiceBESEndpoint** para hacerlo.

También debe proporcionar credenciales para su cuenta de almacenamiento de blobs en `$configContent`, es decir, los campos `AccountName`, `AccountKey` y `RelativeLocation`. El `AccountName` puede ser uno de los nombres de cuenta, como se muestra en el **Portal de administración de Azure clásico** (pestaña*Almacenamiento* ). Cuando haga clic en una cuenta de almacenamiento, su `AccountKey` puede encontrarse presionando el botón **Administrar claves de acceso** , situado en la parte inferior, y copiando la *clave de acceso principal*. El campo `RelativeLocation` es la ruta de acceso relativa al almacenamiento donde se almacenará un nuevo modelo. Por ejemplo, la ruta de acceso `hai/retrain/bike_rental/` en el script siguiente señala a un contenedor denominado `hai` y `/retrain/bike_rental/` son subcarpetas. Actualmente, no puede crear subcarpetas a través del portal de interfaz de usuario, pero hay [varios exploradores de Almacenamiento de Azure](../../storage/common/storage-explorers.md) que le permiten hacerlo. Se recomienda crear un contenedor en el almacenamiento para almacenar los nuevos modelos entrenados (archivos .ilearner) de esta forma: en la página de almacenamiento, haga clic en el botón **Agregar** de la parte inferior y asigne el nombre `retrain` al botón. En resumen, los cambios necesarios para el siguiente script se refieren a `AccountName`, `AccountKey` y `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> El punto de conexión BES es el único modo admitido para esta operación. RRS no se puede usar para generar modelos entrenados.
> 
> 

Como puede ver anteriormente, en lugar de construir 10 archivos JSON de configuración de trabajos BES diferentes, creamos la cadena de configuración dinámicamente en su lugar y la introducimos en el parámetro *jobConfigString* del cmdlet **InvokeAmlWebServceBESEndpoint** , puesto que no es realmente necesario mantener una copia en disco.

Si todo va bien, después de un tiempo verá 10 archivos .ilearner (de *model001.ilearner* a *model010.ilearner*) en la cuenta de Azure Storage. Ahora ya estamos listos para actualizar nuestros 10 puntos de conexión del servicio web de puntuación con estos modelos mediante el cmdlet de PowerShell **Patch AmlWebServiceEndpoint** . Recuerde una vez más que solo podemos revisar los puntos de conexión no predeterminados mediante programación creados anteriormente.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Esto se debe ejecutar bastante rápido. Cuando la ejecución finalice, habremos creado correctamente 10 puntos de conexión de servicio web predictivos, de manera que cada uno de ellos contendrá un modelo entrenado excepcionalmente en el conjunto de datos específico a una ubicación de alquiler, todo ello a partir de un solo experimento de entrenamiento. Para comprobarlo, intente llamar a estos puntos de conexión mediante el cmdlet **InvokeAmlWebServiceRRSEndpoint** , ofreciéndoles los mismos datos de entrada; debe ver diferentes resultados de predicción, ya que los modelos se entrenan con conjuntos de entrenamiento distintos.

## <a name="full-powershell-script"></a>Script de PowerShell completo
Este es el listado del código fuente completo:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
