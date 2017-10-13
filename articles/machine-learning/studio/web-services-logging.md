---
title: Registro para servicios web Machine Learning | Microsoft Docs
description: "Aprenda cómo habilitar el registro para los servicios web de Aprendizaje automático. El registro proporciona información adicional para ayudar a solucionar las API."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.author: raymondl;garye
ms.openlocfilehash: 31497bcae5889590a2261b716574ec4eae21852d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="enable-logging-for-machine-learning-web-services"></a>Habilitar el registro para los servicios web de Aprendizaje automático
Este documento proporciona información sobre la funcionalidad de registro de los servicios web Machine Learning. El registro ofrece información adicional, más allá de simplemente un número de error y un mensaje, que puede ayudarle a solucionar problemas con las llamadas a las API de Machine Learning.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Habilitación del registro para un servicio web

El registro se habilita desde el portal de [servicios web de Azure Machine Learning](https://services.azureml.net). 

1. Inicie sesión en el portal de servicios web de Machine Learning en [https://services.azureml.net](https://services.azureml.net). Para servicios web clásicos, también puede llegar al portal si hace clic en **New Web Services Experience** (Nueva experiencia de servicios web) en la página de servicios web de Machine Learning en Machine Learning Studio.

   ![Nuevo vínculo a la experiencia de servicios web](./media/web-services-logging/new-web-services-experience-link.png)

2. En la barra de menús superior, haga clic en **Servicios web** si es un nuevo servicio web o en **Classic Web Services** (Servicios web clásicos) si es un servicio web clásico.

   ![Selección de servicios web nuevos o clásicos](./media/web-services-logging/select-web-service.png)

3. Si es un nuevo servicio web, haga clic en el nombre del servicio web. Si es un servicio web clásico, haga clic en el nombre del servicio web y, a continuación, en la siguiente página, haga clic en el punto de conexión adecuado.

4. En la barra de menús superior, haga clic en **Configurar**.

5. Establezca la opción **Habilitar registro** en *Error* (para registrar solo los errores) o en *Todo* (para realizar un registro completo).

   ![Selección del nivel de registro](./media/web-services-logging/enable-logging.png)

6. Haga clic en **Guardar**.

7. En servicios web clásicos, cree el contenedor **ml-diagnostics**.

   Todos los registros de servicios web se mantienen en un contenedor de blobs llamado **ml-diagnostics** en la cuenta de almacenamiento asociada con el servicio web. En nuevos servicios web, este contenedor se crea la primera vez que se accede al servicio web. En servicios web clásicos, debe crear el contenedor si aún no existe. 

   1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento asociada con el servicio web.

   2. En **Blob service**, haga clic en **Contenedores**.

   3. Si el contenedor **ml-diagnostics** no existe, haga clic en **+Contenedor**, proporcione al contenedor el nombre "ml-diagnostics" y seleccione "Blob" como el **Tipo de acceso**. Haga clic en **Aceptar**.

      ![Selección del nivel de registro](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> En servicios web clásicos, el panel de servicios web de Machine Learning Studio tiene también un conmutador para habilitar el registro. Sin embargo, puesto que el registro se administra ahora mediante el portal de servicios web, deberá habilitarlo en dicho portal, como se describe en este artículo. Si ya habilitó el registro en Studio, en el portal de servicios web deshabilítelo y vuelva a habilitarlo.


## <a name="the-effects-of-enabling-logging"></a>Consecuencias de habilitar el registro
Cuando el registro está habilitado, el diagnóstico y los errores del punto de conexión de servicio Web se registran en el contenedor de blobs **ml-diagnostics** en la cuenta de Azure Storage vinculada al área de trabajo del usuario. Este contenedor incluye toda la información de diagnóstico de todos los puntos de conexión de servicio Web relativos a todas las áreas de trabajo asociadas a esta cuenta de almacenamiento.

Los registros pueden verse mediante cualquiera de las diversas herramientas disponibles para explorar una cuenta de Azure Storage. Lo más sencillo puede ser desplazarse a la cuenta de almacenamiento en el portal de Azure, hacer clic en **Contenedores** y luego en el contenedor **ml-diagnostics**.  

## <a name="log-blob-detail-information"></a>Información detallada sobre el blob de registro
Cada blob del contenedor incluye la información de diagnóstico de exactamente una de las siguientes acciones:

* Una ejecución del método Batch-Execution  
* Una ejecución del método Request-Response  
* Inicialización de un contenedor Request-Response

El nombre de cada blob tiene un prefijo con la forma siguiente: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Donde el _tipo de registro_ es uno de los valores siguientes:  

* proceso por lotes  
* puntuación/solicitudes  
* puntuación/inic  

