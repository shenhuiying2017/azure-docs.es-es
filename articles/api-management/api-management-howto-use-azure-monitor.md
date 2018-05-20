---
title: Supervisión de las API publicadas en Azure API Management | Microsoft Docs
description: Siga los pasos de este tutorial para aprender a supervisar su API en Azure API Management.
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
ms.openlocfilehash: 658588b29e65c9b1cd2f9d82c1c4528929875b2f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="monitor-published-apis"></a>Supervisión de las API publicadas

Con Azure Monitor, puede visualizar, consultar, enrutar, archivar y realizar acciones en las métricas o los registros procedentes de los recursos de Azure.

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

## <a name="prerequisites"></a>requisitos previos

+ Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, completar el tutorial siguiente: [Importación y publicación de la primera API](import-and-publish.md).

## <a name="view-metrics-of-your-apis"></a>Visualización de las métricas de las API

API Management emite métricas cada minuto, lo que le ofrece visibilidad casi en tiempo real sobre el estado y el mantenimiento de las API. El siguiente es un resumen de algunas de las métricas disponibles:

* Capacidad (versión preliminar): ayuda a tomar decisiones acerca de cómo actualizar o degradar los servicios de APIM. La métrica se emite por minuto y refleja la capacidad de la puerta de enlace en el momento del informe. La métrica oscila entre 0 y 100, y se calcula en función de los recursos de la puerta de enlace, como el uso de la CPU y de la memoria.
* Solicitudes de puerta de enlace en total: el número de solicitudes API en el período. 
* Solicitudes de puerta de enlace correctas: el número de solicitudes de API que recibieron códigos de respuesta HTTP correctos, como 304, 307 y los menores de 301 (por ejemplo, 200).
* Solicitudes de puerta de enlace con error: el número de solicitudes de API que recibieron códigos de respuesta HTTP erróneos, como 400 y cualquiera mayor que 500.
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
7. Intente llamar a la Conference API sin una clave de API. Como propietario de este servicio API Management, recibirá una alerta por correo electrónico. 

    > [!TIP]
    > La regla de alerta también puede llamar a un webhook o una aplicación lógica de Azure cuando se desencadena.

    ![configurar alerta](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="activity-logs"></a>Registros de actividad

Los registros de actividad proporcionan información sobre las operaciones llevadas a cabo en los servicios API Management. Con los registros de actividades, puede determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) llevadas a cabo en los servicios API Management.

> [!NOTE]
> Los registros de actividad no incluyen las operaciones de lectura (GET) ni las realizadas en Azure Portal o mediante las API de administración originales.

Puede acceder a registros de actividad en el servicio API Management o a los registros de todos los recursos de Azure en Azure Monitor. 

Para ver los registros de actividad:

1. Seleccione la instancia del servicio APIM.
2. Haga clic en **Registro de actividad**.

## <a name="diagnostic-logs"></a>Registros de diagnóstico

Los registros de diagnóstico proporcionan información valiosa acerca de las operaciones y los errores que son importantes para la auditoría, así como para solucionar problemas. Los registros de diagnóstico son diferentes de los registros de actividad. Los registros de actividad proporcionan información sobre las operaciones llevadas a cabo en los recursos de Azure. Los registros de diagnóstico proporcionan información detallada acerca de las operaciones que el recurso ha realizado.

Para configurar registros de diagnóstico:

1. Seleccione la instancia del servicio APIM.
2. Haga clic en **Registro de diagnóstico**.
3. Haga clic en **Activar diagnóstico**. Los registros de diagnóstico se pueden archivar junto con las métricas en una cuenta de almacenamiento, transmitirlos en secuencias a un centro de eventos o enviarlos a Log Analytics. 

Actualmente, API Management proporciona registros de diagnóstico (de los que se crean lotes cada hora) de una solicitud de API individual con cada entrada que tenga el esquema siguiente:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Propiedad  | Escriba | DESCRIPCIÓN |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | True si la solicitud HTTP se completó con el código de estado de respuesta dentro del intervalo 2xx o 3xx |
| Twitter en tiempo | date-time | Marca de tiempo de la recepción de la solicitud HTTP por parte de la puerta de enlace |
| operationName | string | Valor constante "Microsoft.ApiManagement/GatewayLogs" |
| categoría | string | Valor constante "GatewayLogs" |
| durationMs | integer | Número de milisegundos transcurridos desde el momento en que la puerta de enlace recibió la solicitud hasta que se envió toda la respuesta |
| callerIpAddress | string | Dirección IP del llamador inmediato de la puerta de enlace (puede ser un intermediario) |
| correlationId | string | Identificador único de la solicitud HTTP asignado por API Management |
| location | string | Nombre de la región de Azure donde se encontraba la puerta de enlace que procesó la solicitud |
| httpStatusCodeCategory | string | Categoría del código de estado de la respuesta http: Correcto (301 o menos, 304 o 307), No autorizado (401, 403, 429), Erróneo (400, entre 500 y 600), Otro |
| ResourceId | string | "Identificador del recurso de API Management/SUBSCRIPTIONS/<subscription>/RESOURCEGROUPS/<grupoDeRecursos>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/<name> |
| propiedades | objeto | Propiedades de la solicitud actual |
| estático | string | Método HTTP de la solicitud entrante |
| URL | string | Dirección URL de la solicitud entrante |
| clientProtocol | string | Versión del protocolo HTTP de la solicitud entrante |
| responseCode | integer | Código de estado de la respuesta HTTP enviada a un cliente |
| backendMethod | string | Método HTTP de la solicitud enviada a un back-end |
| backendUrl | string | Dirección URL de la solicitud enviada a un back-end |
| backendResponseCode | integer | Código de la respuesta HTTP recibida de un back-end |
| backendProtocol | string | Versión del protocolo HTTP de la solicitud enviada a un back-end | 
| requestSize | integer | Número de bytes recibidos de un cliente durante el procesamiento de solicitudes | 
| responseSize | integer | Número de bytes enviados a un cliente durante el procesamiento de solicitudes | 
| cache | string | Estado de participación de la caché de API Management en el procesamiento de la solicitud (es decir, acierto, error, ninguno) | 
| cacheTime | integer | Número de milisegundos empleados en la E/S de la caché global de API Management (conexión, envío y recepción de bytes) | 
| backendTime | integer | Número de milisegundos empleados en la E/S global de back-end (conexión, envío y recepción de bytes) | 
| clientTime | integer | Número de milisegundos empleados en la E/S global del cliente (conexión, envío y recepción de bytes) | 
| apiId | string | Identificador de la entidad de la API de la solicitud actual | 
| operationId | string | Identificador de la entidad de la operación de la solicitud actual | 
| productId | string | Identificador de la entidad del producto de la solicitud actual | 
| userId | string | Identificador de la entidad del usuario de la solicitud actual | 
| apimSubscriptionId | string | Identificador de la entidad de la suscripción de la solicitud actual | 
| backendId | string | Identificador de la entidad del back-end de la solicitud actual | 
| LastError | objeto | Último error de procesamiento de la solicitud | 
| elapsed | integer | Número de milisegundos transcurridos desde que la puerta de enlace recibió una solicitud hasta el momento en que se produjo el error | 
| de origen | string | Nombre del controlador interno del procesamiento o de la directiva que produjo el error | 
| ámbito | string | Ámbito del documento de directiva que contiene la directiva que produjo el error | 
| section | string | Sección del documento de directiva que contiene la directiva que produjo el error | 
| reason | string | Motivo del error | 
| Mensaje | string | Mensaje de error | 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Visualización de registros de actividad
> * Visualización de los registros de diagnóstico
> * Visualización de las métricas de las API
> * Configurar una regla de alerta cuando la API recibe llamadas no autorizadas

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Seguimiento de llamadas](api-management-howto-api-inspector.md)
