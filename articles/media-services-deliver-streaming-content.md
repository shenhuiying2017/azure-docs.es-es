<properties urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="Cómo proporcionar contenido de transmisión por secuencias desde Servicios multimedia en Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="juliako" manager="dwrede" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#Entrega de contenido de streaming

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior era [ Entrega de un recurso mediante descarga](../media-services-deliver-asset-download/).

Además de descargar el contenido multimedia de los Servicios multimedia, puede usar la transmisión de velocidad de bits adaptable para entregar contenido. Por ejemplo, puede crear una URL directa, lo que se conoce como localizador, al contenido por secuencias de un servidor de origen de los Servicios multimedia. Las aplicaciones cliente como Microsoft Silverlight pueden reproducir el contenido por secuencias directamente desde el localizador.

En el siguiente ejemplo se muestra cómo crear un localizador de origen para un recurso de salida generado por un trabajo. En el ejemplo se asume que ya ha obtenido un referencia a un recurso que contiene archivos por secuencias suave y en el código se hace referencia a la variable con nombre **assetToStream**. 

Para crear un localizador de origen para el contenido por secuencias:

   1. Obtenga una referencia al archivo de manifiesto por secuencias (.ism) del recurso. 
   2. Defina una directiva de acceso.
   3. Cree el localizador de origen mediante una llamada al método CreateLocator. 
   4. Compile una URL al archivo de manifiesto. 

En el siguiente código se muestra cómo implementar los pasos:
<pre><code>
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the streaming manifest file from the  
    // collection of files in the asset. 
    var theManifest =
                        from f in assetToStream.AssetFiles
                        where f.Name.EndsWith(".ism")
                        select f;

    // Cast the reference to a true IAssetFile type. 
    IAssetFile manifestFile = theManifest.First();
    IAccessPolicy policy = null;
    ILocator originLocator = null;
            
    // Create a 30-day readonly access policy. 
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;
}
</code></pre>

Para obtener más información acerca de la entrega de recursos, consulte:
<ul>
<li><a href="http://msdn.microsoft.com/en-us/library/jj129575.aspx">Entrega de recursos con los Servicios multimedia para .NET</a></li>
<li><a href="http://msdn.microsoft.com/en-us/library/jj129578.aspx">Entrega de recursos con la API de REST de Media Services</a></li>
</ul>

<h2>Pasos siguientes</h2>
Hasta el momento, hemos tratado la entrega de contenido multimedia mediante descarga desde la plataforma Almacenamiento de Azure y con el uso de Smooth Streaming. En el tema siguiente[Entrega de contenido HLS](../media-services-deliver-http-live-streaming-content/) se analiza la entrega de contenido de streaming usando Apple HTTP Live Streaming (HLS).

<!--HONumber=35.1-->
