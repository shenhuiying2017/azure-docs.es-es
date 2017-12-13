---
title: "Supervisión de las API publicadas en Azure API Management | Microsoft Docs"
description: Siga los pasos de este tutorial para aprender a supervisar su API en Azure API Management.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: bdca9d4968e9e68314f350787907f15e417821f7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="monitor-published-apis"></a>Supervisión de las API publicadas

Azure Monitor es un servicio de Azure que proporciona un único origen para la supervisión de todos los recursos de Azure. Con Azure Monitor, puede visualizar, consultar, redirigir y archivar las métricas y los registros procedentes de recursos de Azure como API Management, así como tomar medidas relacionadas. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Visualización de registros de actividad
> * Visualización de los registros de diagnóstico
> * Visualización de las métricas de las API 
> * Configurar una regla de alerta cuando la API recibe llamadas no autorizadas

En el vídeo siguiente se muestra cómo supervisar API Management con Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>Requisitos previos

+ Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, completar el tutorial siguiente: [Import and publish your first API](import-and-publish.md) (Importación y publicación de la primera API).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>Visualización de registros de actividad

Los registros de actividad proporcionan información sobre las operaciones llevadas a cabo en los servicios API Management. Con los registros de actividades, puede determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) llevadas a cabo en los servicios API Management. 

> [!NOTE]
> Los registros de actividad no incluyen las operaciones de lectura (GET) ni las realizadas en el portal para editores clásico o mediante las API de administración originales.

Puede acceder a registros de actividad en el servicio API Management o a los registros de todos los recursos de Azure en Azure Monitor. 

Para ver los registros de actividad:

1. En su instancia de **API Management** haga clic en **Registro de actividad**.

## <a name="view-diagnostic-logs"></a>Visualización de los registros de diagnóstico

Los registros de diagnóstico proporcionan información valiosa acerca de las operaciones y los errores que son importantes para la auditoría, así como para solucionar problemas. Los registros de diagnóstico son diferentes de los registros de actividad. Los registros de actividad proporcionan información sobre las operaciones llevadas a cabo en los recursos de Azure. Los registros de diagnóstico proporcionan información detallada sobre las operaciones que el mismo recurso realiza.

Para acceder a los registros de diagnóstico:

1. En su instancia de **API Management**, haga clic en **Registro de diagnóstico**.

## <a name="view-metrics-of-your-apis"></a>Visualización de las métricas de las API

API Management emite métricas cada minuto, lo que le ofrece visibilidad casi en tiempo real sobre el estado y el mantenimiento de las API. El siguiente es un resumen de algunas de las métricas disponibles:

* Capacidad (versión preliminar): ayuda a tomar decisiones acerca de cómo actualizar o degradar los servicios de APIM. La métrica se emite por minuto y refleja la capacidad de la puerta de enlace en el momento del informe. Su valor oscila entre 0 y 100 y se calcula en función de los recursos de puerta de enlace, como el uso de CPU y memoria.
* Solicitudes de puerta de enlace en total: el número de solicitudes API en el período. 
* Solicitudes de puerta de enlace correctas: el número de solicitudes API que recibieron correctamente códigos de respuesta HTTP, como 304, 307 y los menores de 301 (por ejemplo, 200). 
* Solicitudes de puerta de enlace con error: el número de solicitudes API que recibieron códigos de respuesta HTTP erróneos, como 400 y cualquiera mayor que 500.
* Solicitudes de puerta de enlace no autorizadas: el número de solicitudes API que recibieron códigos de respuesta HTTP, incluidos 401, 403 y 429. 
* Otras solicitudes de puerta de enlace: el número de solicitudes API que recibieron códigos de respuesta HTTP que no pertenecen a ninguna de las categorías anteriores (por ejemplo, 418).

Para acceder a la métrica:

1. Seleccione **Métricas** en el menú situado cerca de la parte inferior de la página.
2. En la lista desplegable, seleccione las métricas que le interesan (puede agregar varias métricas). 
    
    Por ejemplo, seleccione **Total Gateway Requests** (Solicitudes totales de puerta de enlace) y **Failed Gateway Requests** (Solicitudes de puerta de enlace con error).
3. El gráfico muestra el número total de llamadas API. También muestra el número de llamadas API que no se pudieron realizar. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configuración de una regla de alerta para solicitudes no autorizadas

Puede configurar la recepción de alertas en función de métricas y registros de actividad. Azure Monitor permite configurar una alerta que haga lo siguiente cuando se desencadena:

* Enviar una notificación por correo electrónico
* Llamar a un webhook
* Invocar una aplicación lógica de Azure

Para configurar alertas:

1. Seleccione **Reglas de alertas** en la barra de menús cerca de la parte inferior de la página.
2. Seleccione **Agregar alerta de métrica**.
3. Escriba un **nombre** para esta alerta.
4. Seleccione **Unauthorized Gateway Requests** (Solicitudes de puerta de enlace no autorizadas), que es la métrica que quiere supervisar.
5. Seleccione **Lectores, contribuyentes y propietarios de correo electrónico**.
6. Presione **Aceptar**.
7. Intente llamar a nuestra Conference API sin una clave de API. Como propietario de este servicio API Management, recibirá una alerta por correo electrónico. 

    > [!TIP]
    > La regla de alerta también puede llamar a un webhook o una aplicación lógica de Azure cuando se desencadena.

    ![configurar alerta](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Visualización de registros de actividad
> * Visualización de los registros de diagnóstico
> * Visualización de las métricas de las API 
> * Configurar una regla de alerta cuando la API recibe llamadas no autorizadas

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Seguimiento de llamadas](api-management-howto-api-inspector.md)