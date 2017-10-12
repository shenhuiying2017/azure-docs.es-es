---
title: Reciclaje de un nuevo servicio web Azure Machine Learning con PowerShell | Microsoft Docs
description: "Obtenga información sobre cómo reciclar un modelo y actualizar el servicio web para utilizar el modelo recién entrenado en Azure Machine Learning con los cmdlets de PowerShell para administración de aprendizaje automático."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 77bc78e7ed27f1566e5e5f6a3539c93c9aa73e2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Reciclaje de un servicio web nuevo basado en Resource Manager mediante los cmdlets de PowerShell para administración de Machine Learning
Al volver a entrenar un servicio web nuevo, actualice la definición del servicio web predictiva para hacer referencia al nuevo modelo entrenado.  

## <a name="prerequisites"></a>Requisitos previos
Debe configurar un experimento de entrenamiento y predictivo tal como se muestra en los [modelos de reciclaje de Machine Learning mediante programación](retrain-models-programmatically.md). 

> [!IMPORTANT]
> El experimento predictivo debe implementarse como un servicio web Machine Learning basado en Azure Resource Manager (nuevo). Para implementar un nuevo servicio web, debe tener permisos suficientes en la suscripción en la que lo implementa. Para obtener más información, consulte [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](manage-new-webservice.md). 

Para obtener más información sobre la implementación de servicios web, vea el artículo sobre [implementación de un servicio web Azure Machine Learning](publish-a-machine-learning-web-service.md).

Este proceso requiere haber instalado los cmdlets de Azure Machine Learning. Para obtener información de instalación de los cmdlets de Machine Learning, vea la referencia sobre los [cmdlets de Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) en MSDN.

La siguiente información se copia desde la salida reentrenamiento:

* BaseLocation
* RelativeLocation

Los pasos son:

1. Inicie sesión en la cuenta de Azure Resource Manager
2. Obtención de la definición de servicio web
3. Exporte la definición de servicio web como JSON
4. Actualice la referencia al blob ilearner en JSON
5. Importe JSON en una definición de servicio web
6. Actualice el servicio web con la nueva definición de servicio web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Inicie sesión en la cuenta de Azure Resource Manager
Primero debe iniciar sesión en la cuenta de Azure en el entorno de PowerShell mediante el cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Obtenga la definición de servicio web
A continuación, obtenga el servicio web, llamando al cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . La definición de servicio web es una representación interna del modelo entrenado del servicio web y no es modificable directamente. Asegúrese de que va a recuperar la definición de servicio web para el experimento predictivo y no para el experimento de entrenamiento.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar el nombre del grupo de recursos de un servicio web existente, ejecute el cmdlet Get-AzureRmMlWebService sin ningún parámetro para mostrar los servicios web en la suscripción. Busque el servicio web y luego examine su identificador de servicio web. El nombre del grupo de recursos es el cuarto elemento del identificador, justo después del elemento *resourceGroups* . En el ejemplo siguiente, el nombre del grupo de recursos es Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Como alternativa, para determinar el nombre del grupo de recursos de un servicio web existente, inicie sesión en el portal de servicios web de Microsoft Azure Machine Learning. Seleccione el servicio web. El nombre del grupo de recursos es el quinto elemento de la dirección URL del servicio web, justo después del elemento *resourceGroups* . En el ejemplo siguiente, el nombre del grupo de recursos es Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exporte la definición de servicio web como JSON
Para modificar la definición para el modelo entrenado para usar el modelo recién entrenado, primero debe usar el cmdlet [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportar a un archivo con formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Actualice la referencia al blob ilearner en JSON
En los recursos, busque [modelo entrenado], actualice el valor de *uri* en el nodo *locationInfo* con el identificador URI del blob ilearner. El identificador URI se genera mediante la combinación de *BaseLocation* y *RelativeLocation* a partir del resultado de la llamada de reentrenamiento de BES. Esto actualiza la ruta de acceso para hacer referencia al nuevo modelo entrenado.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>Importe JSON en una definición de servicio web
Debe utilizar el cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) para convertir el archivo JSON modificado en una definición de servicio web que puede usar para actualizar la definición del servicio web.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Actualice el servicio web con la nueva definición de servicio web
Por último, utilice el cmdlet [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) para actualizar la definición del servicio web.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Resumen
Con el uso de los cmdlets de administración de PowerShell para Machine Learning, puede actualizar el modelo entrenado de un servicio web predictivo habilitando escenarios como:

* Reentrenamiento de modelos periódicos con nuevos datos.
* Distribución de un modelo entre los clientes con el fin de permitirles reentrenar el modelo mediante sus propios datos.

