---
title: "Consumo de un servicio web Machine Learning con una plantilla de aplicación web | Microsoft Docs"
description: "Use una plantilla de aplicación web en Azure Marketplace para consumir un servicio web predictivo en Aprendizaje automático de Azure."
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
ms.author: garye;raymondl
ms.openlocfilehash: 1c182403409966923440f359cb2514af7b7df9f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Consumo de un servicio web de Aprendizaje automático de Azure con una plantilla de aplicación web

Una vez que desarrolla su modelo predictivo y lo implementa como un servicio web de Azure con Estudio de aprendizaje automático o con herramientas como R o Python, puede tener acceso al modelo de operaciones con una API de REST.

Hay varias maneras de usar la API de REST y tener acceso al servicio web. Por ejemplo, puede escribir una aplicación en C#, R o Python con el código de ejemplo que se generó cuando implementó el servicio web (disponible en el [Portal Servicios web de Machine Learning](https://services.azureml.net/quickstart) o en el panel de servicios web en Machine Learning Studio). O bien, puede usar el libro de Microsoft Excel de ejemplo que se creó al mismo tiempo.

Pero la manera más rápida y fácil para tener acceso al servicio web es a través de las plantillas de aplicación web disponibles en [Marketplace de aplicaciones web de Azure](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Plantillas de aplicación web de Aprendizaje automático de Azure
Las plantillas de aplicación web disponibles en Azure Marketplace pueden crear una aplicación web personalizada que conoce los datos de entrada del servicio web y los resultados esperados. Todo lo que necesita hacer es conceder el acceso de la aplicación web al servicio web y los datos, y la plantilla se encarga del resto.

Existen dos plantillas:

* [Plantilla de aplicación web Request-Response Service de Aprendizaje automático de Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Plantilla de aplicación web Batch Execution Service de Aprendizaje automático de Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada plantilla crea una aplicación de ASP.NET de ejemplo mediante el URI de la API y la clave para el servicio web y la implementa como un sitio web en Azure. La plantilla Request-Response Service (RRS) crea una aplicación web que permite enviar una sola fila de datos al servicio web para obtener un resultado único. La plantilla Batch Execution Service (BES) crea una aplicación web que permite enviar muchas filas de datos para obtener varios resultados.

No es necesaria ninguna codificación para usar estas plantillas. Simplemente, proporcione el URI y la clave de API y la plantilla compilará la aplicación.

Para obtener la clave de API y el URI de solicitud de un servicio web:

1. En el [Portal Servicios web](https://services.azureml.net/quickstart), para un servicio web nuevo, haga clic en **Servicios web** en la parte superior. O bien, para un servicio web clásico, haga clic en **Servicios web clásicos**.
2. Haga clic en el servicio web al que desea tener acceso.
3. Para un servicio web clásico, haga clic en el punto de conexión al que desea tener acceso.
4. Haga clic en **Consumir** en la parte superior.
5. Copie la clave **principal** o **secundaria** y guárdela.
6. Si crea una plantilla de servicio de solicitud-respuesta (RRS), copie el URI de **solicitud-respuesta** y guárdelo. Si crea una plantilla de servicio de ejecución de lotes (BES), copie el URI de las **solicitudes de lote** y guárdelo.


## <a name="how-to-use-the-request-response-service-rrs-template"></a>Uso de la plantilla Request-Response Service (RRS)
Siga estos pasos para usar la plantilla de aplicación web de RRS, tal como se indica en el diagrama siguiente.

![Proceso para usar la plantilla web RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Vaya a [Azure Portal](https://portal.azure.com), **Inicio de sesión**, haga clic en **Nuevo**, busque y seleccione **Azure ML Request-Response Service Web App** (Aplicación web de servicio Solicitud-respuesta de Azure ML) y luego haga clic en **Crear**. 
   
   * Asigne un nombre único a la aplicación web. La dirección URL de la aplicación web será este nombre seguido de `.azurewebsites.net.` Por ejemplo, `http://carprediction.azurewebsites.net.`
   * Seleccione los servicios y la suscripción de Azure en los que se ejecuta el servicio web.
   * Haga clic en **Crear**.
     
     ![Crear aplicación web][image5]

4. Cuando Azure termine de implementar la aplicación web, haga clic en la **dirección URL** en la página de configuración de la aplicación web en Azure o escriba la dirección URL en un explorador web. Por ejemplo, `http://carprediction.azurewebsites.net.`
5. Cuando se ejecute la aplicación web por primera vez, le pedirá la **dirección URL del mensaje de API** y **clave de API**.
   Escriba los valores que guardó anteriormente (**URI de solicitud** y **Clave de API**, respectivamente).
     
     Haga clic en **Enviar**.
     
     ![Escriba el URI del mensaje y la clave de API][image6]

6. La aplicación web muestra su página **Configuración de la aplicación web** con la configuración actual del servicio web. Aquí puede realizar cambios en la configuración usada por la aplicación web.
   
   > [!NOTE]
   > El cambio de estas opciones solo las cambia para esta aplicación web. No cambia la configuración predeterminada del servicio web. Por ejemplo, si cambia la **Descripción** aquí no cambia la descripción mostrada en el panel del servicio web en Estudio de aprendizaje automático.
   > 
   > 
   
    Cuando termine, haga clic en **Guardar cambios** y, luego, haga clic en **Ir a la página principal**.

7. En la página principal puede escribir valores para enviar al servicio web. Cuando termine, haga clic en **Enviar** y se generará el resultado.

Si desea volver a la página **Configuración**, vaya a la página `setting.aspx` de la aplicación web. Por ejemplo: `http://carprediction.azurewebsites.net/setting.aspx.` le pedirá que vuelva a escribir la clave de API, lo que es necesario para tener acceso a la página y actualizar la configuración.

Puede detener, reiniciar o eliminar la aplicación web en el Portal de Azure como cualquier otra aplicación web. Mientras se esté ejecutando, puede ir a la dirección web de inicio y escribir los nuevos valores.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Uso de la plantilla Batch Execution Service (BES)
Puede usar la plantilla de aplicación web BES de la misma manera que la plantilla RRS, excepto que la aplicación web que se crea permite enviar varias filas de datos y recibir varios resultados.

Los valores de entrada de un servicio web de ejecución de lotes pueden provenir de Azure Storage o un archivo local; los resultados se almacenan en un contenedor de Azure Storage.
Por lo tanto, necesitará un contenedor de almacenamiento de Azure para guardar los resultados devueltos por la aplicación web, y deberá preparar los datos de entrada.

![Proceso para usar la plantilla web BES][image2]

1. Siga el mismo procedimiento para crear la aplicación web de BES como lo hizo para la plantilla de RRS, excepto que debe ir a [Plantilla de aplicación web Servicio de ejecución de lotes de Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir la plantilla BES en Azure Marketplace y haga clic en **Crear aplicación web**.

2. Para especificar dónde desea que se almacenen los resultados, escriba la información del contenedor de destino en la página principal de la aplicación web. Especifique también dónde puede obtener los valores de entrada la aplicación web, en un archivo local o en un contenedor de almacenamiento de Azure.
   Haga clic en **Enviar**.
   
    ![Información de almacenamiento][image7]

La aplicación web mostrará una página con el estado del trabajo.
Cuando se haya completado el trabajo, se le proporcionará la ubicación de los resultados en el almacenamiento de blobs de Azure. También tiene la opción de descargar los resultados en un archivo local.

## <a name="for-more-information"></a>Para obtener más información
Para obtener más información sobre...

* la creación de un experimento de aprendizaje automático con Estudio de aprendizaje automático, consulte [Creación del primer experimento en Estudio de aprendizaje automático de Azure](create-experiment.md)
* cómo implementar el experimento de aprendizaje automático como un servicio web, consulte [Implementación de un servicio web de Aprendizaje automático de Azure](publish-a-machine-learning-web-service.md)
* otras formas de tener acceso al servicio web, consulte [Consumo de servicios web Azure Machine Learning](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
