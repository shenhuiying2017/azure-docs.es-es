---
title: Depuración de las API con el seguimiento de solicitudes en Azure API Management | Microsoft Docs
description: Siga los pasos de este tutorial para aprender a supervisar los pasos del procesamiento de solicitudes en Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 01a9120af2fb240eed1463613f7a6f946a9c16ed
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="debug-your-apis-using-request-tracing"></a>Depuración de las API con el seguimiento de solicitudes

Este tutorial describe cómo inspeccionar el procesamiento de solicitudes para ayudarle con la depuración y la solución de problemas de una API. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Realizar el seguimiento de una llamada

![API inspector](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>requisitos previos

+ Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, completar el tutorial siguiente: [Import and publish your first API](import-and-publish.md) (Importación y publicación de la primera API).

## <a name="trace-a-call"></a>Realizar el seguimiento de una llamada

1. Seleccione **API**.
2. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
3. Seleccione la operación **GetSpeakers**.
4. Cambie a la pestaña **Prueba**.
5. Asegúrese de incluir un encabezado HTTP denominado **Ocp-Apim-Trace** con el valor establecido en **true**.
6. Haga clic en **"Enviar"** para realizar una llamada API. 
7. Espere a que se complete la llamada. 
8. Vaya a la pestaña de **seguimiento** en el **consola de la API**. Puede hacer clic en cualquiera de los siguientes vínculos para saltar a la información de seguimiento detallada: **entrada**, **back-end**, **salida**.

    En la sección de **entrada**, verá la solicitud original que API Management recibió del autor de la llamada y todas las directivas aplicadas a la solicitud, que incluyen directivas de límite de velocidad y de set-header que se agregaron en el paso 2.

    En la sección **back-end**, verá las solicitudes que API Management envió al back-end de la API y la respuesta que recibió.
    
    En la sección de **salida**, verá todas las directivas que se aplican a la respuesta antes de enviarla de vuelta al autor de la llamada.
 
    > [!TIP]
    > Cada paso muestra también el tiempo transcurrido desde que API Management recibe la solicitud.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Realizar el seguimiento de una llamada

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Uso de revisiones](api-management-get-started-revise-api.md)
