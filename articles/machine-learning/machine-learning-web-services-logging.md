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
ms.date: 01/12/2017
ms.author: raymondl;garye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f307a26bfbb55b395f4073f4368432ae69b867ae


---
# <a name="enable-logging-for-machine-learning-web-services"></a>Habilitar el registro para los servicios web de Aprendizaje automático
Este documento aporta información sobre la función de registro de los servicios web clásicos. Habilitar el registro en los servicios web ofrece información adicional, más allá de simplemente un número de error y un mensaje, que puede ayudarle a solucionar problemas con las llamadas a las API de Machine Learning.  

Para habilitar el registro en los servicios web del Portal de Azure clásico:   

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. En la columna de características de la izquierda, haga clic en **MACHINE LEARNING**.
3. Haga clic en el nombre del área de trabajo y luego en **SERVICIOS WEB**.
4. En la lista de servicios web, haga clic en el nombre del servicio web.
5. En la lista de puntos de conexión, haga clic en el nombre del punto de conexión.
6. Haga clic en **CONFIGURAR**.
7. Establezca **NIVEL DE SEGUIMIENTO DE DIAGNÓSTICO** en *Error* o *Todos* y luego haga clic en **GUARDAR**.

Instrucciones para habilitar el registro en el portal Servicios web Azure Machine Learning.

1. Inicie sesión en el portal [Servicios web Azure Machine Learning](https://services.azureml.net).
2. Haga clic en Classic Web Services (Servicios web clásicos).
3. En la lista de servicios web, haga clic en el nombre del servicio web.
4. En la lista de puntos de conexión, haga clic en el nombre del punto de conexión.
5. Haga clic en **Configurar**.
6. Establezca **Registro** en *Error* o *Todos* y luego haga clic en **GUARDAR**.

## <a name="the-effects-of-enabling-logging"></a>Consecuencias de habilitar el registro
Cuando el registro está habilitado, todos los diagnósticos y errores del punto de conexión seleccionado se registran en la cuenta de almacenamiento de Azure Storage vinculada al área de trabajo del usuario. Puede ver esta cuenta de almacenamiento en la vista de panel del Portal de Azure clásico (parte inferior de la sección Vista rápida) de su área de trabajo.  

Los registros pueden verse mediante cualquiera de las diversas herramientas disponibles para "explorar" una cuenta de almacenamiento de Azure Storage. Lo más sencillo puede ser simplemente ir a la cuenta de almacenamiento en el Portal de Azure clásico y luego hacer clic en **CONTENEDORES**. A continuación, verá un contenedor denominado **ml-diagnostics**. Este contenedor contiene toda la información de diagnóstico de todos los puntos de conexión de servicio web relativos a todas las áreas de trabajo asociadas a esta cuenta de almacenamiento. 

## <a name="log-blob-detail-information"></a>Información detallada sobre el blob de registro
Cada blob del contenedor contiene la información de diagnóstico para exactamente uno de los siguientes elementos:

* Una ejecución del método Batch-Execution  
* Una ejecución del método Request-Response  
* Inicialización de un contenedor Request-Response

El nombre de cada blob tiene un prefijo con la forma siguiente: 

{Id. del área de trabajo}-{id. del servicio web}-{id. del punto de conexión}/{tipo de registro}  

Donde el tipo de registro es uno de los valores siguientes:  

* lote  
* puntuación/solicitudes  
* puntuación/inic  




<!--HONumber=Nov16_HO3-->


