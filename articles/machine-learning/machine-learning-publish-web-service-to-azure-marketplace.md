---
title: "(obsoleto) Publicación de un servicio web Machine Learning en Azure Marketplace | Microsoft Docs"
description: "(obsoleto) Publicación de un servicio web Machine Learning en Azure Marketplace"
services: machine-learning
documentationcenter: 
author: BharathS
manager: jhubbard
editor: cgronlun
ms.assetid: 68e908be-3a99-4cd7-9517-e2b5f2f341b8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 06/02/2017
ms.author: bharaths
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-gallery-experiments
ms.translationtype: Human Translation
ms.sourcegitcommit: 85a58e09e05fdb50984055ab25c4c2fe520dab4d
ms.openlocfilehash: 2d62966f130f6778c9561393cc7fc338f8903f1e
ms.contentlocale: es-es
ms.lasthandoff: 01/10/2017


---
# <a name="deprecated-publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>(obsoleto) Publicación de un servicio web Machine Learning en Azure Marketplace

> [!NOTE]
> DataMarket y Data Services están en proceso de retirada, y las suscripciones existentes se retirarán y cancelarán a partir del 31 de marzo de 2017. Como resultado, este artículo se ha quedado obsoleto. 
> 
> Aún tiene la posibilidad de publicar sus experimentos de Machine Learning en la [Galería de Cortana Intelligence](https://gallery.cortanaintelligence.com/) en beneficio de la comunidad de la ciencia de datos. Para obtener más información, consulte [Uso compartido y descubrimiento de soluciones en la Galería de Cortana Intelligence](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish).

Azure Marketplace ofrece la posibilidad de publicar servicios web de Aprendizaje automático de Azure como servicios gratuitos o de pago para su consumo por clientes externos. Este artículo proporciona una introducción a ese proceso, junto con vínculos a directrices para comenzar. Mediante este proceso, puede poner a disposición de otros desarrolladores sus servicios web para que los consuman en sus aplicaciones.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Introducción al proceso de publicación
A continuación, se muestran los pasos para publicar un servicio web de Aprendizaje automático de Azure en Azure Marketplace:

1. Crear y publicar un servicio de solicitud-respuesta (RRS) de Aprendizaje automático
2. Implementar el servicio en producción y obtener la información de extremo de OData y la clave de API
3. Usar la URL del servicio web publicado para publicar en [Azure Marketplace (DataMarket)](https://publish.windowsazure.com/workspace/) 
4. Una vez enviada la oferta, se revisa y es necesario aprobarla antes de que los clientes puedan comenzar a comprarla. El proceso de publicación puede tardar varios días laborables. 

## <a name="walk-through"></a>Tutorial
### <a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Paso 1: crear y publicar un servicio de solicitud-respuesta (RRS) de Aprendizaje automático
 Si no lo ha hecho todavía, consulte este [tutorial](machine-learning-walkthrough-5-publish-web-service.md).

### <a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Paso 2: implementar el servicio en producción y obtener la información de extremo de OData y la clave de API
1. En el [Portal de Azure clásico](http://manage.windowsazure.com), elija la opción **Aprendizaje automático** en la barra de navegación de la izquierda y, a continuación, elija el área de trabajo. 
2. Haga clic en la pestaña **Servicios web** y, a continuación, seleccione el servicio web que desee publicar en Marketplace.
   
    ![Azure Marketplace][workspace]
3. Elija el extremo que desea ubicar en Marketplace para su consumo. Si no ha creado ningún extremo adicional, puede elegir el extremo **predeterminado** .
4. Una vez que haya hecho clic en el extremo, podrá ver la **clave de API**. Necesitará esta información más adelante en el paso 3, por lo que se aconseja que la copie.
   
    ![Azure Marketplace][apikey]
5. Haga clic en el método **SOLICITUD/RESPUESTA**. En este momento, no se admite la publicación de servicios de ejecución de lotes en Marketplace. Esto le llevará a la página de Ayuda de la API para el método de solicitud/respuesta.
6. Copie la **dirección de extremo de OData**, ya que necesitará esta información más adelante en el paso 3.
   
    ![Azure Marketplace][odata]

Implementación del servicio en la producción.

### <a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Paso 3: usar la URL del servicio web publicado para realizar la publicación en Azure Marketplace (DataMarket)
1. Acceda a [Azure Marketplace (DataMarket)](http://datamarket.azure.com/home) 
2. Haga clic en el vínculo **Publicar** en la parte superior de la página. Esto le llevará a [Portal de publicación de Microsoft Azure](https://publish.windowsazure.com)
3. Haga clic en la sección **Publicadores** para registrarse como publicador.
4. Cuando cree una nueva oferta, elija **Servicios de datos** y, a continuación, haga clic en **Crear un nuevo servicio de datos**. 
   
   ![Azure Marketplace][image1]
   
   <br />
5. En **Planes** , deberá proporcionar información sobre su oferta, junto con un plan de precios. Decida si ofrecerá un servicio gratuito o de pago. Para que le paguen, deberá proporcionar información de pago, por ejemplo, la cuenta bancaria y los datos fiscales.
6. En **Marketing** , proporcione información acerca de la oferta, como el título y la descripción.
7. En **Precios** , puede establecer el precio de los planes para países específicos, o dejar que el sistema aplique automáticamente el precio de la oferta.
8. En la pestaña **Servicio de datos**, elija **Servicio web** como **Origen de datos**.
   
    ![Azure Marketplace][image2]
9. Obtenga la clave de API y la dirección URL del servicio web en el Portal de Azure clásico, tal y como se explica en el paso 2 anterior.
10. En el cuadro de diálogo de configuración del servicio de datos de Marketplace, pegue la dirección de extremo de OData en el cuadro de texto **Dirección URL del servicio** .
11. En **Autenticación**, elija **Encabezado** como **Esquema de autenticación**.
    
    * Escriba "Autorización" en **Nombre de encabezado**.
    * En **Valor de encabezado**, escriba "Portador" (sin las comillas), haga clic en la barra **Espacio** y, a continuación, pegue la clave de API.
    * Marque la casilla **Este servicio es OData** .
    * Haga clic en **Prueba de conexión** para probar la conexión.
12. En **Categorías**, asegúrese de que **Machine Learning** esté seleccionado.
13. Cuando haya terminado de escribir todos los metadatos acerca de la oferta, haga clic en **Publicar** y, a continuación, haga clic en **Pasar a ensayo**. En este momento, se le notificarán los problemas restantes que deba corregir.
14. Una vez que se haya asegurado de que no quedan problemas pendientes, haga clic en **Solicitar aprobación para pasar a producción**. El proceso de publicación puede tardar varios días laborables. 

[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png


