<properties linkid="develop-media-services-how-to-guides-deliver-streaming-content" urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="How to Deliver Streaming Content from Media Services – Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="" />

Entrega de contenido por secuencias
===================================

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior era [Entrega de un recurso mediante descarga](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409).

Además de descargar el contenido multimedia de los Servicios multimedia, puede usar la transmisión de velocidad de bits adaptable para entregar contenido. Por ejemplo, puede crear una URL directa, lo que se conoce como localizador, al contenido por secuencias de un servidor de origen de los Servicios multimedia. Las aplicaciones cliente como Microsoft Silverlight pueden reproducir el contenido por secuencias directamente desde el localizador.

En el siguiente ejemplo se muestra cómo crear un localizador de origen para un recurso de salida generado por un trabajo. En el ejemplo se asume que ya ha obtenido un referencia a un recurso que contiene archivos por secuencias suave y en el código se hace referencia a la variable con nombre **assetToStream**.

Para crear un localizador de origen para el contenido por secuencias:

1.  Obtenga una referencia al archivo de manifiesto por secuencias (.ism) del recurso.
2.  Defina una directiva de acceso.
3.  Cree el localizador de origen mediante una llamada al método CreateLocator.
4.  Compile una URL al archivo de manifiesto.

En el siguiente código se muestra cómo implementar los pasos:

``` {}
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Obtener una referencia al recurso que desea transmitir.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Obtener una referencia al archivo de manifiesto por secuencias desde  
    // la colección de archivos del recurso. 
    var theManifest =
                        from f in assetToStream.AssetFiles
                        where f.Name.EndsWith(".ism")
                        select f;

    // Convertir la referencia en un tipo IAssetFile true. 
    IAssetFile manifestFile = theManifest.First();
    IAccessPolicy policy = null;
    ILocator originLocator = null;
            
    // Crear una directiva de acceso de solo lectura de 30 días. 
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Crear un localizador OnDemandOrigin para el recurso. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Mostrar algunos valores útiles basados en el localizador.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Crear una URL completa al archivo de manifiesto. Usarla para la reproducción
    // en clientes de medios de transmisión por secuencias. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Mostrar el id. del localizador de origen, la directiva de acceso y el recurso.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Devolver el localizador. 
    return originLocator;
}
```

Para obtener más información acerca de la entrega de recursos, consulte:

-   [Entrega de recursos con los Servicios multimedia para .NET](http://msdn.microsoft.com/en-us/library/jj129575.aspx)
-   [Entrega de recursos con la API de REST de Media Services](http://msdn.microsoft.com/en-us/library/jj129578.aspx)

Pasos siguientes
----------------

Hasta el momento, hemos tratado la entrega de contenido multimedia mediante descarga desde la plataforma Almacenamiento de Azure y con el uso de Smooth Streaming. En el siguiente tema [Entrega de contenido HLS](http://go.microsoft.com/fwlink/?LinkId=301817) se habla acerca de la entrega de contenido por secuencias mediante HTTP Live Streaming (HLS) de Apple.

