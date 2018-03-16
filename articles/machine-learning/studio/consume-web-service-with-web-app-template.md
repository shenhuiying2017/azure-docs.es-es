---
title: "Consumo de un servicio web Machine Learning con una plantilla de aplicación web | Microsoft Docs"
description: "Use una plantilla de aplicación web en Azure Marketplace para consumir un servicio web predictivo en Azure Machine Learning."
keywords: "servicio web,operacionalización,API de REST,aprendizaje automático"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: raymondl
ms.openlocfilehash: f7efa647fa6afc247509cd4a52066c0459f75ca3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Consumo de un servicio web Azure Machine Learning con una plantilla de aplicación web

También puede desarrollar un modelo predictivo e implementarlo como un servicio web de Azure mediante el uso de:
- Azure Machine Learning Studio.
- Herramientas como R o Python. 

Después de eso, puede acceder al modelo de operaciones mediante el uso de una API de REST.

Hay varias maneras de usar la API de REST y tener acceso al servicio web. Por ejemplo, puede escribir una aplicación en C#, R o Python con el código de ejemplo que se genera automáticamente cuando se implementa el servicio web. (El código de ejemplo está disponible en el [portal de servicios web Azure Machine Learning](https://services.azureml.net/quickstart) o en el panel del servicio web en Machine Learning Studio). O bien, puede usar el libro de Microsoft Excel de ejemplo que se creó al mismo tiempo.

Pero la manera más rápida y fácil de acceder al servicio web es a través de las plantillas de aplicación web disponibles en [Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Plantillas de aplicación web de Azure Machine Learning
Las plantillas de aplicación web disponibles en Azure Marketplace pueden crear una aplicación web personalizada que conoce los datos de entrada del servicio web y los resultados esperados. Todo lo que necesita hacer es conceder el acceso de la aplicación web al servicio web y los datos, y la plantilla se encarga del resto.

Existen dos plantillas:

* [Plantilla de aplicación web Request-Response Service de Aprendizaje automático de Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Plantilla de aplicación web Batch Execution Service de Aprendizaje automático de Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada plantilla crea una aplicación de ASP.NET de ejemplo mediante el URI de la API y la clave para el servicio web. Después, la plantilla implementa la aplicación como un sitio web en Azure. 

La plantilla Request-Response Service (RRS) crea una aplicación web que puede usar para enviar una sola fila de datos al servicio web para obtener un resultado único. La plantilla Batch Execution Service (BES) crea una aplicación web que puede usar para enviar muchas filas de datos para obtener varios resultados.

No es necesaria ninguna codificación para usar estas plantillas. Simplemente, proporcione el URI y la clave de API, y la plantilla compilará la aplicación.

Para obtener la clave de API y el URI de solicitud de un servicio web:

1. En el [portal de servicios web](https://services.azureml.net/quickstart), seleccione **servicios web** en la parte superior. O bien, para un servicio web clásico, seleccione **Servicios web clásicos**.
2. Seleccione el servicio web al que desea acceder.
3. Para un servicio web clásico, seleccione el punto de conexión al que desea acceder.
4. Seleccione **Consumir** en la parte superior.
5. Copie la clave principal o secundaria y guárdela.
6. Si crea una plantilla RRS, copie el URI de **solicitud-respuesta** y guárdelo. Si crea una plantilla BES, copie el URI de las **solicitudes por lotes** y guárdelo.


## <a name="how-to-use-the-request-response-service-template"></a>Uso de la plantilla Request-Response Service
Siga estos pasos para usar la plantilla de aplicación web de RRS, tal como se indica en el diagrama siguiente.

![Proceso para usar la plantilla web RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Nuevo**, busque y seleccione **Azure ML Request-Response Service Web App** y después seleccione **Crear**. 
3. En el panel **Crear**:
   
   * Asigne un nombre único a la aplicación web. La dirección URL de la aplicación web será este nombre seguido de **.azurewebsites.net**. Un ejemplo es **http://carprediction.azurewebsites.net**.
   * Seleccione los servicios y la suscripción de Azure en los que se ejecuta el servicio web.
   * Seleccione **Crear**.
     
   ![Crear una aplicación web][image5]

4. Cuando Azure termine de implementar la aplicación web, seleccione la **dirección URL** en la página de configuración de la aplicación web en Azure o escriba la dirección URL en un explorador web. Por ejemplo, escriba **http://carprediction.azurewebsites.net**.
5. Cuando se ejecute la aplicación web por primera vez, le pedirá la **dirección URL del mensaje de API** y la **clave de API**. Escriba los valores que guardó anteriormente (URI de solicitud y Clave de API, respectivamente). Seleccione **Submit** (Enviar).
     
   ![Escriba el URI del mensaje y la clave de API][image6]

6. La aplicación web muestra su página **Configuración de la aplicación web** con la configuración actual del servicio web. Aquí puede realizar cambios en la configuración usada por la aplicación web.
   
   > [!NOTE]
   > El cambio de estas opciones solo las cambia para esta aplicación web. No cambia la configuración predeterminada del servicio web. Por ejemplo, si cambia el texto de la **Descripción**, aquí no cambia la descripción mostrada en el panel del servicio web en Machine Learning Studio.
   > 
   > 
   
    Cuando termine, seleccione **Guardar cambios** y, luego, **Ir a la página principal**.

7. En la página principal puede escribir valores para enviar al servicio web. Cuando termine, seleccione **Enviar** y se generará el resultado.

Si desea volver a la página **Configuración**, vaya a la página **setting.aspx** de la aplicación web. Por ejemplo, vaya a **http://carprediction.azurewebsites.net/setting.aspx**. Se le pedirá que vuelva a escribir la clave de API. Es necesario que acceda a la página y que actualice la configuración.

Puede detener, reiniciar o eliminar la aplicación web en el Portal de Azure como cualquier otra aplicación web. Mientras se esté ejecutando, puede ir a la dirección web de inicio y escribir los nuevos valores.

## <a name="how-to-use-the-batch-execution-service-template"></a>Uso de la plantilla Batch Execution Service
Puede usar la plantilla de aplicación web BES de la misma forma que la plantilla RRS. La diferencia es que puede usar la aplicación web creada para enviar varias filas de datos y recibir varios resultados.

Los valores de entrada de un servicio web de ejecución de lotes pueden provenir de Azure Storage o un archivo local. Los resultados se almacenan en un contenedor de Azure Storage. Por lo tanto, necesita un contenedor de Azure Storage para almacenar los resultados que la aplicación web devuelve. También debe tener preparados los datos de entrada.

![Proceso para usar la plantilla web BES][image2]

1. Siga el mismo procedimiento para crear la aplicación web BES que para la plantilla RRS. Vaya a [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir la plantilla BES en Azure Marketplace. Seleccione **Crear aplicación web**.

2. Para especificar dónde desea que se almacenen los resultados, escriba la información del contenedor de destino en la página principal de la aplicación web. Especifique también dónde puede obtener los valores de entrada la aplicación web, en un archivo local o en un contenedor de Azure Storage.
   Seleccione **Submit** (Enviar).
   
   ![Información de almacenamiento][image7]

La aplicación web muestra una página con el estado del trabajo. Una vez completado el trabajo, se le proporciona la ubicación de los resultados en Azure Blob Storage. También tiene la opción de descargar los resultados en un archivo local.

## <a name="for-more-information"></a>Para obtener más información
Para más información sobre:

* la creación de un experimento de aprendizaje automático con Azure Machine Learning Studio, vea [Creación del primer experimento en Azure Machine Learning Studio](create-experiment.md),
* la implementación del experimento de aprendizaje automático como un servicio web, vea [Implementación de un servicio web de Azure Machine Learning](publish-a-machine-learning-web-service.md),
* otras formas de acceder al servicio web, vea [Consumo de servicios web de Azure Machine Learning](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
