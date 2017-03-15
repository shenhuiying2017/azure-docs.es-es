---
title: "Uso de la captura de paquetes para realizar la supervisión proactiva de redes con Azure Functions | Microsoft Docs"
description: "En este artículo se describe cómo crear una captura de paquetes desencadenada mediante alertas con Azure Network Watcher."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>Uso de la captura de paquetes para realizar la supervisión proactiva de redes con Azure Functions

La captura de paquetes de Network Watcher crea sesiones de captura para realizar el seguimiento del tráfico dentro y fuera de una máquina virtual. El archivo de captura puede tener un filtro que se define para realizar el seguimiento solo del tráfico que se quiere supervisar. Estos datos se almacenan luego en un blob de almacenamiento o de forma local en la máquina invitada. Esta funcionalidad se puede iniciar de forma remota desde otros escenarios de automatización, como Azure Functions. La captura de paquetes ofrece la funcionalidad de ejecutar capturas proactivas en función de las anomalías de red definidas. Otros usos son la recopilación de estadísticas de red, la obtención de información sobre las intrusiones de red y la depuración de las comunicaciones cliente-servidor, entre otros.

Los recursos implementados en Azure se ejecutan las 24 horas, los 7 días de la semana. Usted o su personal no pueden supervisar activamente el estado de todos los recursos de manera ininterrumpida. ¿Qué ocurre si un problema se produce a las 2 a.m.?

Con Network Watcher, la característica de alertas y Functions del ecosistema de Azure, puede responder de manera proactiva a los problemas de su red con los datos y las herramientas para resolverlos.

## <a name="before-you-begin"></a>Antes de empezar

En este ejemplo, la máquina virtual está enviando más segmentos TCP de lo habitual, y le gustaría recibir alertas al respecto. Los segmentos TCP se usan como ejemplo, pero podría usar cualquier condición de alerta. Al recibir una alerta, quiere tener datos de nivel de paquete para comprender por qué la comunicación ha aumentado y así poder realizar los pasos para devolver la máquina a la comunicación normal.
En este escenario se supone que tiene una instancia existente de Network Watcher y que se va a usar un grupo de recursos con una máquina virtual válida.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Escenario

Para automatizar este proceso, crearemos y conectaremos una alerta en nuestra máquina virtual para que se active cuando se produzca el incidente y una función de Azure para llamar en Network Watcher.

En este escenario:

* Creará una función de Azure que inicie una captura de paquetes.
* Creará una regla de alerta en una máquina virtual.
* Configurará la regla de alerta para llamar a la función de Azure.

## <a name="creating-an-azure-function-and-overview"></a>Creación de una función de Azure e información general

El primer paso es crear una función de Azure para procesar la alerta y crear una captura de paquetes. 

En la lista siguiente se muestra información general sobre el flujo de trabajo que tiene lugar.

1. Se desencadena una alerta en la máquina virtual.
1. La alerta llama a la función de Azure a través de un webhook.
1. La función de Azure procesa la alerta y se inicia una sesión de captura de paquetes de Network Watcher.
1. La captura de paquetes se ejecuta en la máquina virtual y recopila el tráfico. 
1. El archivo de captura se carga en una cuenta de almacenamiento para su revisión y diagnóstico. 

La creación de una función de Azure se puede realizar en el portal siguiendo las instrucciones del artículo [Creación de su primera función de Azure](../azure-functions/functions-create-first-azure-function.md). En este ejemplo, elegimos una función de tipo HttpTrigger CSharp. 

> [!NOTE]
> Aunque se proporcionan otros lenguajes con Azure Functions, puede que todavía estén en fase experimental y no sean completamente compatibles, como PowerShell y Python.

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>Procesamiento de la alerta e inicio de una sesión de captura de paquetes

Ahora es el momento de realizar llamadas en Network Watcher dentro de la función de Azure. La implementación de esta función es diferente, según los requisitos. Sin embargo, el flujo general del código es el siguiente:

1. Procesar los parámetros de entrada.
2. Consultar las capturas de paquetes existentes para comprobar los límites y resolver los conflictos de nombres.
3. Crear una captura de paquetes con los parámetros adecuados.
4. Sondear la captura de paquetes periódicamente hasta que finalice.
5. Notificar al usuario que la sesión de captura de paquetes ha finalizado.

El ejemplo siguiente es con C#, que se puede usar en la función de Azure. Los valores de suscripción, id. de cliente, id. de inquilino y secreto de cliente, deben reemplazarse.

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

Cuando haya creado la función, debe configurar la alerta para que llame a la dirección URL asociada a la función. Para obtener este valor, copie la dirección URL de la función de la aplicación de función.

![búsqueda de la url de función][2]

Si necesita propiedades personalizadas en la carga de la solicitud POST del webhook, consulte [Configuración de un webhook en una alerta de métrica de Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Configuración de una alerta en una máquina virtual

Se pueden configurar alertas para que avisen a los usuarios cuando una métrica determinada supere un umbral que se le ha asignado. En este ejemplo, la alerta se encuentra en los segmentos TCP enviados, pero se puede activar para muchas otras métricas. En este ejemplo, se configura una alerta para llamar a un webhook que llama a la función.

### <a name="create-the-alert-rule"></a>Creación de la regla de alerta

Vaya a una máquina virtual existente y agregue una regla de alerta. Se puede encontrar documentación más detallada sobre la configuración de alertas en [Creación de alertas en Azure Monitor para servicios de Azure - Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md). 

![agregar regla de alerta de vm a una máquina virtual][1]

> [!NOTE]
> Algunas métricas no están habilitadas de forma predeterminada. Para encontrar más información sobre cómo habilitar la adición de métricas, visite [Habilitación de supervisión y diagnóstico](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

Por último, pegue la dirección URL del paso anterior en el cuadro de texto de webhook de la alerta. Haga clic en **Aceptar** para guardar la regla de alerta.

![pegar la dirección url en la regla de alerta][3]

## <a name="downloading-and-viewing-the-capture-file"></a>Descarga y visualización del archivo de captura

Si guarda la captura en una cuenta de almacenamiento, se puede descargar el archivo de captura a través del portal o mediante programación. Si el archivo de captura se almacena localmente, para recuperarlo se debe iniciar sesión en la máquina virtual. 

Para más instrucciones acerca de cómo descargar archivos desde cuentas de Azure Storage, consulte [Introducción a Azure Blob Storage mediante .NET](../storage/storage-dotnet-how-to-use-blobs.md). Otra herramienta que se puede utilizar es el Explorador de Storage. Encontrará más información acerca del Explorador de Storage en el siguiente vínculo: [Explorador de Storage](http://storageexplorer.com/).

Cuando se descarga la captura, puede verla con cualquier herramienta que pueda leer archivos **cap.**. A continuación se muestran vínculos a dos de estas herramientas:

[Analizador de mensajes de Microsoft](https://technet.microsoft.com/en-us/library/jj649776.aspx).  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>Pasos siguientes

Para saber cómo ver las capturas de paquetes, visite [Análisis de capturas de paquetes con Wireshark](network-watcher-alert-triggered-packet-capture.md)

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

