---
title: "Reentrenamiento de un servicio web clásico | Microsoft Docs"
description: "Obtenga información acerca de cómo volver a entrenar un modelo y actualizar el servicio web mediante programación para utilizar el modelo recién entrenado en el Aprendizaje automático de Azure."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 04e019501be6880fcc7e92de690a9f31195282e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-a-classic-web-service"></a>Reentrenamiento de un servicio web clásico
El servicio web predictivo que implementó es el punto de conexión de puntuación predeterminado. Los puntos de conexión predeterminados se mantienen sincronizados con los experimentos de entrenamiento y puntuación originales y, por tanto, el modelo entrenado de un punto de conexión predeterminado no se puede reemplazar. Para reciclar el servicio web, debe agregar un nuevo punto de conexión al servicio web. 

## <a name="prerequisites"></a>Requisitos previos
Debe haber configurado un experimento de entrenamiento y un experimento predictivo tal como se muestra en los [modelos de reciclaje de Machine Learning mediante programación](retrain-models-programmatically.md). 

> [!IMPORTANT]
> El experimento predictivo debe implementarse como un servicio web Machine Learning clásico. 
> 
> 

Para obtener más información sobre la implementación de servicios web, vea el artículo sobre [implementación de un servicio web Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Adición de un nuevo punto de conexión
El servicio web predictivo que implementó contiene un punto de conexión de puntuación predeterminado que se mantiene sincronizado con la formación original y el modelo entrenado de experimentos de puntuación. Para actualizar el servicio web a un nuevo modelo entrenado, debe crear un nuevo punto de conexión para la puntuación. 

Para crear un nuevo punto de conexión de puntuación, en el servicio web predictivo que se puede actualizar con el modelo entrenado:

> [!NOTE]
> Asegúrese de agregar el punto de conexión al servicio web predictivo y no al de entrenamiento. Si ha implementado correctamente un servicio web predictivo y otro de entrenamiento, debería ver dos servicios web independientes. El servicio web predictivo debe terminar con "[predictive exp.]".
> 
> 

Hay tres formas en que puede agregar un nuevo punto de conexión a un servicio web:

1. De manera programática
2. Uso del portal de servicios web de Microsoft Azure
3. Uso del Portal de Azure clásico

### <a name="programmatically-add-an-endpoint"></a>Incorporación de un punto de conexión mediante programación
También puede agregar puntos de conexión de puntuación mediante el código de ejemplo proporcionado en este [repositorio de GitHub](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Uso del portal de servicios web de Microsoft Azure para agregar un punto de conexión
1. En Machine Learning Studio, en la columna de navegación izquierda, haga clic en Servicios web.
2. En la parte inferior del panel de servicios web, haga clic en **Manage endpoints preview**(Administrar versión preliminar de puntos de conexión).
3. Haga clic en **Agregar**.
4. Escriba un nombre y una descripción para el nuevo punto de conexión. Seleccione el nivel de registro y si los datos de ejemplo están habilitados. Para más información sobre los registros, vea [Habilitar el registro para los servicios web de Aprendizaje automático](web-services-logging.md).

### <a name="use-the-azure-classic-portal-to-add-an-endpoint"></a>Uso del Portal de Azure clásico para agregar un punto de conexión
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Aprendizaje automático**en el menú izquierdo.
3. En Nombre, haga clic en el área de trabajo y, a continuación, haga clic en **Servicios web**.
4. En Nombre, haga clic en **Census Model [predictive exp.]** (Modelo de censo [exp. predictivo]).
5. Haga clic en **Agregar extremo**en la parte inferior de la página. Para más información sobre la incorporación de puntos de conexión, consulte [Creación de puntos de conexión](create-endpoint.md). 

## <a name="update-the-added-endpoints-trained-model"></a>Actualización del modelo entrenado del punto de conexión agregado
Para completar el proceso de reentrenamiento, debe actualizar el modelo entrenado del nuevo punto de conexión que ha agregado.

* Si ha agregado el nuevo punto de conexión mediante el Portal de Azure clásico, puede hacer clic en su nombre y luego en el vínculo **UpdateResource** para obtener la dirección URL que necesitará para actualizar el modelo del punto de conexión.
* Si agregó el punto de conexión mediante el código de ejemplo, esto incluye la ubicación de la dirección URL de ayuda identificada por el valor *HelpLocationURL* de la salida.

Para recuperar la dirección URL de la ruta de acceso:

1. Copie y pegue la URL en el explorador.
2. Haga clic en el vínculo Actualizar recurso.
3. Copie la dirección URL de POST de la solicitud PATCH. Por ejemplo:
   
     PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Ahora puede usar el modelo entrenado para actualizar el punto de conexión de puntuación que creó anteriormente.

El código de ejemplo siguiente muestra cómo utilizar *BaseLocation*, *RelativeLocation*, *SasBlobToken* y el valor de PATCH URL para actualizar el punto de conexión.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

Se puede obtener *apiKey* y *endpointUrl* para la llamada desde el panel del punto de conexión.

El valor del parámetro *Name* de *Resources* debe coincidir con el nombre del recurso del modelo entrenado guardado en el experimento predictivo. Para obtener el nombre del recurso:

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Aprendizaje automático**en el menú izquierdo.
3. En Nombre, haga clic en el área de trabajo y, a continuación, haga clic en **Servicios web**.
4. En Nombre, haga clic en **Census Model [predictive exp.]** (Modelo de censo [exp. predictivo]).
5. Haga clic en el nuevo punto de conexión que ha agregado.
6. En el panel del punto de conexión, haga clic en **Actualizar recurso**.
7. En la página de documentación de la API de actualización de recurso para el servicio web, encontrará el **nombre del recurso** en **Updatable Resources** (Recursos actualizables).

Si el token SAS expira antes de que termine de actualizar el punto de conexión, deberá realizar una operación GET con el identificador de trabajo para obtener un nuevo token.

Si el código se ha ejecutado correctamente, el nuevo punto de conexión debería comenzar a utilizar el modelo reentrenado en aproximadamente 30 segundos.

## <a name="summary"></a>Resumen
Mediante el uso de las API de reentrenamiento, puede actualizar el modelo entrenado de un servicio web predictivo habilitando escenarios como:

* Reentrenamiento de modelos periódicos con nuevos datos.
* Distribución de un modelo entre los clientes con el fin de permitirles reentrenar el modelo mediante sus propios datos.

## <a name="next-steps"></a>Pasos siguientes
[Solución de problemas del reentrenamiento de un servicio web clásico de Aprendizaje automático de Azure](troubleshooting-retraining-models.md)

