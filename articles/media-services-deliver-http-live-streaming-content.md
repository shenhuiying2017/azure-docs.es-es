<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="How to Deliver Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="migree" solutions="" manager="" editor="" />

Entrega de contenido por secuencias HLS de Apple
================================================

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [Entrega de contenido por secuencias](http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409).

En este tema se explica cómo crear un localizador para el contenido por secuencias mediante HTTP Live Streaming (HLS) de Apple en un servidor de origen de Servicios multimedia. Use este enfoque para crear una dirección URL para el contenido HLS de Apple y proporciónela a los dispositivos iOS de Apple para la reproducción. El enfoque básico para generar la dirección URL del localizador es el mismo. Genere un localizador para la ruta de acceso del recurso de transmisión de HLS de Apple en un servidor de origen y, a continuación, genere una dirección URL completa que se vincule al manifiesto para el contenido de transmisión.

En el siguiente ejemplo de código se asume que ya ha obtenido una referencia a un recurso de transmisión de HLS y que se hace referencia a la variable con nombre **assetToStream** en el código. Una vez que haya ejecutado este código para generar un localizador de origen en el recurso, puede usar la dirección URL resultante para reproducir el contenido de transmisión en un dispositivo iOS como un iPad o un iPhone.

Para generar un localizador para contenido de transmisión de HLS de Apple:

1.  Obtenga una referencia al archivo de manifiesto del recurso.
2.  Defina una directiva de acceso.
3.  Cree el localizador de origen mediante una llamada a CreateLocator.
4.  Compile una URL al archivo de manifiesto.

En el siguiente código se muestra cómo implementar los pasos:

``` {}
static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
{
    // Obtener una referencia al recurso que desea transmitir.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Obtener una referencia al archivo de manifiesto por secuencias de HLS desde  
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
    policy = _context.AccessPolicies.Create("Streaming HLS Policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

    // Crear una URL para el archivo de manifiesto por secuencias de HLS. Úsela para la reproducción
    // en los clientes por secuencias de iOS de Apple.
    string urlForClientStreaming = originLocator.Path
        + manifestFile.Name + "/manifest(format=m3u8-aapl)";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Devolver el localizador. 
    return originLocator;
}
```

Para obtener más información acerca de la entrega de recursos, consulte:

-   [Entrega de recursos con los Servicios multimedia para .NET](http://msdn.microsoft.com/en-us/library/jj129575.aspx)
-   [Entrega de recursos con la API de REST de Media Services](http://msdn.microsoft.com/en-us/library/jj129578.aspx)

Pasos siguientes
----------------

Con este tema concluyen los temas acerca del uso de los Servicios multimedia de Azure. Hemos tratado la configuración de la máquina para el desarrollo de Servicios multimedia y la realización de tareas de programación típicas. Para obtener más información acerca de la programación de Servicios multimedia, consulte los siguientes recursos:

-   [Media Services](http://go.microsoft.com/fwlink/?linkid=245437)
-   [Guía de inicio rápido: Usar el SDK de Media Services para .NET](http://go.microsoft.com/fwlink/?linkid=252966)
-   [Creación de aplicaciones con el SDK de Media Services para .NET](http://go.microsoft.com/fwlink/?linkid=247821)
-   [Entrega de contenido](http://go.microsoft.com/fwlink/?linkid=252967)
-   [Foro de Servicios multimedia](http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads)
-   [Supervisión de una cuenta de Servicios multimedia](http://www.windowsazure.com/en-us/manage/services/media-services/how-to-monitor-a-media-services-account/)
-   [Administración de contenido en Servicios multimedia](http://www.windowsazure.com/en-us/manage/services/media-services/how-to-manage-content-in-media-services/)

