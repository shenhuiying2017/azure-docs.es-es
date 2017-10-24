---
title: "Diagnóstico de proxy inverso de Azure Service Fabric | Microsoft Docs"
description: Aprenda a supervisar y diagnosticar el procesamiento de solicitudes en el proxy inverso.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: 1c62d2390709577bfde6225b783642fb55396a6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Supervisión y diagnóstico del procesamiento de solicitudes en el proxy inverso.

A partir de la versión 5.7 de Service Fabric, los eventos de proxy inverso están disponibles para la recopilación. Los eventos están disponibles en dos canales, uno que solo contiene eventos de error relacionados con los errores de procesamiento de solicitudes en el proxy inverso y el segundo canal que contiene eventos detallados con registros tanto para las solicitudes correctas como para las incorrectas.

Consulte [Recopilación de eventos de proxy inverso](service-fabric-diagnostics-event-aggregation-wad.md#collect-reverse-proxy-events) para habilitar la recopilación de eventos de estos canales en clústeres de Azure Service Fabric y locales.

## <a name="troubleshoot-using-diagnostics-logs"></a>Solución de problemas mediante la herramienta de diagnóstico
Estos son algunos ejemplos sobre cómo interpretar los registros de error comunes que pueden encontrarse:

1. El proxy inverso devuelve el código de estado de respuesta 504 (tiempo de espera agotado).

    Un motivo podría ser que el servicio no responda dentro del período de tiempo de espera de la solicitud.
El primer evento a continuación registra los detalles de la solicitud recibida en el proxy inverso. El segundo evento indica que la solicitud no se pudo completar al reenviarse al servicio, debido al "error interno = ERROR_WINHTTP_TIMEOUT" 

    La carga incluye:

    *  **traceId**: este GUID se puede utilizar para poner en correlación todos los eventos correspondientes a una única solicitud. En los siguientes dos eventos, traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, lo que implica que pertenecen a la misma solicitud.
    *  **requestUrl**: la dirección URL (URL de proxy inverso) a la que se envió la solicitud.
    *  **verb**: verbo HTTP.
    *  **remoteAddress**: dirección del cliente que envía la solicitud.
    *  **resolvedServiceUrl**: dirección URL del punto de conexión del servicio para el que se resolvió la solicitud entrante. 
    *  **errorDetails**: información adicional sobre el error.

    ```
    {
      "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
      "ProviderName": "Microsoft-ServiceFabric",
      "Id": 51477,
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
      "ProcessId": 57696,
      "Level": "Informational",
      "Keywords": "0x1000000000000021",
      "EventName": "ReverseProxy",
      "ActivityID": null,
      "RelatedActivityID": null,
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
        "verb": "GET",
        "remoteAddress": "::1",
        "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
        "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
      }
    }

    {
      "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
      ...
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
      ...
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "statusCode": 504,
        "description": "Reverse Proxy Timeout",
        "sendRequestPhase": "FinishSendRequest",
        "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
      }
    }
    ```

2. El proxy inverso devuelve el código de estado de respuesta 404 (no encontrado). 
    
    Este es un evento de ejemplo donde el proxy inverso devuelve 404 ya que no se pudo encontrar el punto de conexión del servicio de búsqueda correspondiente.
    Las entradas de carga que interesan aquí son:
    *  **processRequestPhase**: indica la fase durante el procesamiento de la solicitud cuando se produjo el error, es decir, ***TryGetEndpoint***, al intentar obtener el punto de conexión del servicio al que reenviar. 
    *  **errorDetails**: enumera los criterios de búsqueda del punto de conexión. Aquí puede ver que el listenerName especificado = **FrontEndListener**, mientras que la lista de puntos de conexión de réplica solo contiene un agente de escucha con el nombre **OldListener**.
    
    ```
    {
      ...
      "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
      "ProcessId": 57696,
      "Level": "Warning",
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
        ...
        "processRequestPhase": "TryGetEndoint",
        "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
      }
    }
    ```
    Otro ejemplo donde el proxy inverso puede devolver 404 (no encontrado) es cuando el valor de ApplicationGateway o el parámetro de configuración de HTTP **SecureOnlyMode** está establecido en true con el proxy inverso escuchando en **HTTPS**, sin embargo, todos los puntos de conexión de réplica son inseguros (escucha en HTTP).
    El proxy devuelve 404, ya que no puede encontrar un punto de conexión que escuche HTTPS para reenviar la solicitud. Analizar los parámetros en la carga del evento ayuda a definir el problema:
    
    ```
        "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
    ```

3. La solicitud para el proxy inverso genera un error de tiempo de espera. 
    Los registros de eventos contienen un evento con los detalles de la solicitud recibida (no mostrados aquí).
    El siguiente evento muestra que el servicio responde con un código de estado 404 y el proxy inverso inicia una repetición de la resolución. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Al recopilar todos los eventos, verá una cola de eventos que muestra cada resolución y cada intento de reenvío.
    El último evento de la serie muestra que el procesamiento de la solicitud no se pudo realizar en el tiempo de espera, junto con el número de intentos de resolución correctos.
    
    > [!NOTE]
    > Se recomienda mantener la recopilación de eventos de canal detallados deshabilitada de forma predeterminada y habilitarla para solucionar el problema según sus necesidades.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Si solo está habilitada la recolección de eventos críticos o de error, observa un evento con detalles sobre el tiempo de espera y el número de intentos de resolución. 
    
    Los servicios que tengan la intención de reenviar un código de estado 404 al usuario, deben agregar un encabezado "X-ServiceFabric" en la respuesta. Una vez que el encabezado se agrega a la respuesta, el servidor proxy inverso reenvía el código de estado al cliente.  

4. Casos cuando el cliente ha desconectado la solicitud.

    El siguiente evento se registra cuando el servidor proxy inverso está reenviando la respuesta al cliente, pero este se desconecta:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. El servidor proxy inverso devuelve 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    El error FABRIC_E_SERVICE_DOES_NOT_EXIST se devuelve si el esquema URI no se especifica para el punto de conexión de servicio en el manifiesto de servicio.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Para resolver el problema, especifique el esquema URI en el manifiesto.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Actualmente no se registran los eventos relacionados con el procesamiento de la solicitud de websocket. Se agregará esta funcionalidad en la próxima versión.

## <a name="next-steps"></a>Pasos siguientes
* [Agregación de eventos y recopilación mediante Microsoft Azure Dignostics](service-fabric-diagnostics-event-aggregation-wad.md) para habilitar la recopilación de registros en clústeres de Azure.
* Para ver los eventos de Service Fabric en Visual Studio, consulte [Supervisión y diagnóstico locales](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Consulte [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) (Configuración del proxy inverso para conectarse a servicios seguros) para ver las muestras de plantillas de Azure Resource Manager y para configurar el proxy inverso seguro con diferentes opciones de validación de certificados de servicio.
* Para más información, lea [Proxy inverso de Service Fabric](service-fabric-reverseproxy.md).
