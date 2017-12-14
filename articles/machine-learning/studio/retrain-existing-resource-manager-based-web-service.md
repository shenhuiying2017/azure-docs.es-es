---
title: Reciclaje de un servicio web predictivo existente | Microsoft Docs
description: "Obtenga información sobre cómo reciclar un modelo y actualizar el servicio web para utilizar el modelo recién entrenado en Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: v-donglo
ms.openlocfilehash: 6d96d173928fa18a3ab4f3086e5971ad1927df89
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="retrain-an-existing-predictive-web-service"></a>Reciclaje de un servicio web predictivo existente
En este documento se describe el proceso de reciclaje para el escenario siguiente:

* Dispone de un experimento de entrenamiento y un experimento predictivo que se ha implementado como un servicio web de operaciones.
* Tiene nuevos datos que desea que el servicio web predictivo utilice para realizar su puntuación.

> [!NOTE] 
> Para implementar un nuevo servicio web, debe tener permisos suficientes en la suscripción en la que lo implementa. Para obtener más información, consulte [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](manage-new-webservice.md). 

A partir de su servicio web existente y los experimentos, debe seguir estos pasos:

1. Actualizar el modelo.
   1. Modificar el experimento de entrenamiento para permitir salidas y entradas del servicio web.
   2. Implementar el experimento de entrenamiento como un servicio web de reciclaje.
   3. Utilizar el servicio de ejecución por lotes (BES) del experimento de entrenamiento para reciclar el modelo.
2. Usar los cmdlets de PowerShell de Azure Machine Learning para actualizar el experimento predictivo.
   1. Inicie sesión en la cuenta de Azure Resource Manager
   2. Obtenga la definición de servicio web.
   3. Exporte la definición de servicio web como JSON.
   4. Actualice la referencia al blob ilearner en JSON
   5. Importe JSON en una definición de servicio web.
   6. Actualice el servicio web con la nueva definición de servicio web.

## <a name="deploy-the-training-experiment"></a>Implementación del experimento de entrenamiento
Para implementar el experimento de entrenamiento como servicio web de reciclaje, debe agregar entradas y salidas de servicio web al modelo. Si conecta un módulo de *Salida de servicio web* en el módulo del *[modelo de entrenamiento][train-model]*, puede activar el experimento de entrenamiento para producir un nuevo modelo de entrenamiento o que puede usar en el experimento predictivo. Si tiene un módulo *Evaluar modelo*, puede adjuntar un resultado del servicio web para obtener los resultados de evaluación como salida.

Para actualizar el experimento de entrenamiento:

1. Conecte un módulo *Entrada de servicio web* en la entrada de datos (por ejemplo, un módulo *Limpiar datos que faltan*). Normalmente, quiere asegurarse de que los datos de entrada se procesen de la misma forma que los datos de entrenamiento original.
2. Conecte un módulo *Salida de servicio web* a la salida del módulo *Modelo de entrenamiento*.
3. Si tiene un módulo *Evaluar modelo* y desea los resultados de la evaluación, conecte n módulo *Salida del servicio web* a la salida de su módulo *Evaluar modelo*.

Ejecute el experimento.

Después, debe implementar el experimento de entrenamiento como un servicio web que genera un modelo entrenado y resultados de evaluación del modelo.  

En la parte inferior del lienzo del experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y después seleccione **Deploy Web Service [New]** (Implementar servicio web [nuevo]). El portal de servicios web de Azure Machine Learning se abre en la página **Deploy Web service** (Implementar servicio web). Escriba un nombre para el servicio web y elija un plan de pago y después haga clic en **Implementar**. Solo puede usar el método Ejecución de lotes para crear modelos de entrenamiento.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Reciclaje del modelo con nuevos datos mediante BES
En este ejemplo, se utiliza C# para crear la aplicación de reciclado. También puede utilizar el código de ejemplo de Python o R para realizar esta tarea.

Para llamar a las API de reciclado:

1. Cree una nueva aplicación de consola de C# en Visual Studio: **Nuevo** > **Proyecto** > **Visual C#** > **Escritorio clásico de Windows** > **Aplicación de consola (.NET Framework)**.
2. Inicie sesión en el portal de servicio web Machine Learning.
3. Haga clic en el servicio web con el que está trabajando.
4. Haga clic en **Consume**(Consumo).
5. En la parte inferior de la página **Consume** (Consumo), en la sección **Código de ejemplo**, haga clic en **Batch**.
6. Copie el código C# de ejemplo para la ejecución por lotes y péguelo en el archivo Program.cs. Asegúrese de que el espacio de nombres permanece intacto.

Agregue el paquete NuGet Microsoft.AspNet.WebApi.Client tal como se especifica en los comentarios. Para agregar la referencia a Microsoft.WindowsAzure.Storage.dll, primero debe instalar la [biblioteca de cliente para servicios de Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

La siguiente captura de pantalla muestra la página **Consumo** en el portal de servicios de web de Azure Machine Learning.

![Página Consumo][1]

### <a name="update-the-apikey-declaration"></a>Actualización de la declaración de apikey
Localice la declaración de **apikey**:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

En la sección **Basic consumption info** (Información básica sobre consumo) de la página **Consume** (Consumo), localice la clave principal y cópiela en la declaración de **apikey**.

### <a name="update-the-azure-storage-information"></a>Actualización de la información de Azure Storage
El código de ejemplo de BES carga un archivo desde una unidad local (por ejemplo, "C:\temp\CensusIpnput.csv") en Azure Storage, lo procesa y escribe los resultados de nuevo en Azure Storage.  

Después de ejecutar el experimento, el flujo de trabajo resultante debe ser similar al siguiente:

![Flujo de trabajo resultante después de la ejecución][4]

1. Inicie sesión en el Portal de Azure.
2. En la columna de navegación izquierda, haga clic en **Más servicios**, busque **Cuentas de almacenamiento** y selecciónelo.
3. En la lista de cuentas de almacenamiento, seleccione una para almacenar el modelo reciclado.
4. En la columna de navegación izquierda, haga clic en **Claves de acceso**.
5. Copie y guarde el valor de **Primary Access Key**.
6. En la columna de navegación izquierda, haga clic en **Contenedores**.
7. Seleccione un contenedor existente o cree uno nuevo y guarde el nombre.

Busque las declaraciones *StorageAccountName*, *StorageAccountKey* y *StorageContainerName* y actualice los valores guardados en el portal.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

También deberá asegurarse de que el archivo de entrada está disponible en la ubicación que especifique en el código.

### <a name="specify-the-output-location"></a>Especifique la ubicación de salida.
Al especificar la ubicación de salida en la carga útil de solicitud, la extensión del archivo especificado en *RelativeLocation* debe especificarse como `ilearner`. Consulte el ejemplo siguiente:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

El siguiente es un ejemplo de resultado de reciclaje:

![Salida de reentrenamiento][6]

## <a name="evaluate-the-retraining-results"></a>Evaluación de los resultados de reciclaje
Al ejecutar la aplicación, la salida incluye la dirección URL y el token de firmas de acceso compartido que son necesarios para tener acceso a los resultados de evaluación.

Podrá ver los resultados de rendimiento del modelo reciclado combinando *BaseLocation*, *RelativeLocation* y *SasBlobToken* de los resultados de salida de *output2* (como se muestra en la imagen de la salida de reciclado anterior) y copiando y pegando la dirección URL completa en la barra de direcciones del explorador.  

Revise los resultados para determinar si el modelo recientemente entrenado funciona lo suficientemente bien como para reemplazar el existente.

Copie *BaseLocation*, *RelativeLocation* y *SasBlobToken* de los resultados de salida.

## <a name="retrain-the-web-service"></a>Reciclar el servicio web
Al reciclar un servicio web nuevo, actualice la definición del servicio web predictiva para hacer referencia al nuevo modelo entrenado. La definición de servicio web es una representación interna del modelo entrenado del servicio web y no es modificable directamente. Asegúrese de que va a recuperar la definición de servicio web para el experimento predictivo y no para el experimento de entrenamiento.

## <a name="sign-in-to-azure-resource-manager"></a>Iniciar sesión en Azure Resource Manager
Primero debe iniciar sesión en la cuenta de Azure en el entorno de PowerShell mediante el cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition-object"></a>Obtener el objeto de definición del servicio web
A continuación, obtenga el objeto de definición de servicio web, llamando al cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx).

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar el nombre del grupo de recursos de un servicio web existente, ejecute el cmdlet Get-AzureRmMlWebService sin ningún parámetro para mostrar los servicios web en la suscripción. Busque el servicio web y luego examine su identificador de servicio web. El nombre del grupo de recursos es el cuarto elemento del identificador, justo después del elemento *resourceGroups* . En el ejemplo siguiente, el nombre del grupo de recursos es Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Como alternativa, para determinar el nombre del grupo de recursos de un servicio web existente, inicie sesión en el portal de servicios web de Azure Machine Learning. Seleccione el servicio web. El nombre del grupo de recursos es el quinto elemento de la dirección URL del servicio web, justo después del elemento *resourceGroups* . En el ejemplo siguiente, el nombre del grupo de recursos es Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exportar el objeto de definición de servicio web como JSON
Para modificar la definición para el modelo entrenado para usar el modelo recién entrenado, primero debe usar el cmdlet [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportar a un archivo con formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Actualizar la referencia al blob ilearner
En los recursos, busque [modelo entrenado], actualice el valor de *uri* en el nodo *locationInfo* con el identificador URI del blob ilearner. El identificador URI se genera mediante la combinación de *BaseLocation* y *RelativeLocation* a partir del resultado de la llamada de reentrenamiento de BES.

     "asset3": {
        "name": "Retrain Sample [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importar JSON en un objeto de definición de servicio web
Debe utilizar el cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) para convertir el archivo JSON modificado en un objeto de definición de servicio web que puede usar para actualizar el experimento predictivo.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Actualizar el servicio web
Por último, utilice el cmdlet [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) para actualizar el experimento predictivo.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
