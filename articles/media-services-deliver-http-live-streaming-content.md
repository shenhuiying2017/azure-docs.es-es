<properties urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="C&oacute;mo proporcionar HTTP Live Streaming (HLS) de Apple para Azure" metaKeywords="" description="Aprenda a crear un localizador para el contenido de HTTP Live Streaming (HLS) de Apple en un servidor de origen de Servicios multimedia. Los ejemplos de c&oacute;digo est&aacute;n escritos en C# y utilizan el SDK de Servicios multimedia para .NET." metaCanonical="" services="media-services" documentationCenter="" title="Reproducci&oacute;n de contenido por secuencias HLS de Apple" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Reproducción de contenido por secuencias HLS de Apple

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [Comprobación del contenido por secuencias][Comprobación del contenido por secuencias].

En este tema se explica cómo crear un localizador para el contenido por secuencias mediante HTTP Live Streaming (HLS) de Apple en un servidor de origen de Servicios multimedia. Use este enfoque para crear una dirección URL para el contenido HLS de Apple y proporciónela a los dispositivos iOS de Apple para la reproducción. El enfoque básico para generar la dirección URL del localizador es el mismo. Genere un localizador para la ruta de acceso del recurso de transmisión de HLS de Apple en un servidor de origen y, a continuación, genere una dirección URL completa que se vincule al manifiesto para el contenido de transmisión.

En el siguiente ejemplo de código se asume que ya ha obtenido una referencia a un recurso de transmisión de HLS y que se hace referencia a la variable con nombre **assetToStream** en el código. Una vez que haya ejecutado este código para generar un localizador de origen en el recurso, puede usar la dirección URL resultante para reproducir el contenido de transmisión en un dispositivo iOS como un iPad o un iPhone.

Para generar un localizador para contenido de transmisión de HLS de Apple:

1.  Obtenga una referencia al archivo de manifiesto del recurso.
2.  Defina una directiva de acceso.
3.  Cree el localizador de origen mediante una llamada a CreateLocator.
4.  Compile una URL al archivo de manifiesto.

En el siguiente código se muestra cómo implementar los pasos:

    static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
    {
        // Get a reference to the asset you want to stream.
        IAsset assetToStream = GetAsset(targetAssetID);

        // Get a reference to the HLS streaming manifest file from the  
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
        policy = _context.AccessPolicies.Create("Streaming HLS Policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                    policy,
                    DateTime.UtcNow.AddMinutes(-5));

        // Create a URL to the HLS streaming manifest file. Use this for playback
        // in Apple iOS streaming clients.
        string urlForClientStreaming = originLocator.Path
            + manifestFile.Name + "/manifest(format=m3u8-aapl)";
        Console.WriteLine("URL to manifest for client streaming: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine();

        // Return the locator. 
        return originLocator;
    }

Para obtener más información acerca de la entrega de recursos, consulte:

-   [Entrega de recursos con los Servicios multimedia para .NET][Entrega de recursos con los Servicios multimedia para .NET]
-   [Entrega de recursos con la API de REST de Media Services][Entrega de recursos con la API de REST de Media Services]

</p>
## Pasos siguientes

Con este tema concluyen los temas acerca del uso de los Servicios multimedia de Azure. Hemos tratado la configuración de la máquina para el desarrollo de Servicios multimedia y la realización de tareas de programación típicas. Para obtener más información acerca de la programación de Servicios multimedia, consulte los siguientes recursos:

-   [Media Services][Media Services]
-   [Guía de inicio rápido: Usar el SDK de Media Services para .NET][Guía de inicio rápido: Usar el SDK de Media Services para .NET]
-   [Creación de aplicaciones con el SDK de Media Services para .NET][Creación de aplicaciones con el SDK de Media Services para .NET]
-   [Entrega de contenido][Entrega de contenido]
-   [Foro de Servicios multimedia][Foro de Servicios multimedia]
-   [Supervisión de una cuenta de Servicios multimedia][Supervisión de una cuenta de Servicios multimedia]
-   [Administración de contenido en Servicios multimedia][Administración de contenido en Servicios multimedia]

  [Comprobación del contenido por secuencias]: ../media-services-deliver-streaming-content/
  [Entrega de recursos con los Servicios multimedia para .NET]: http://msdn.microsoft.com/es-es/library/jj129575.aspx
  [Entrega de recursos con la API de REST de Media Services]: http://msdn.microsoft.com/es-es/library/jj129578.aspx
  [Media Services]: http://go.microsoft.com/fwlink/?linkid=245437
  [Guía de inicio rápido: Usar el SDK de Media Services para .NET]: http://go.microsoft.com/fwlink/?linkid=252966
  [Creación de aplicaciones con el SDK de Media Services para .NET]: http://go.microsoft.com/fwlink/?linkid=247821
  [Entrega de contenido]: http://go.microsoft.com/fwlink/?linkid=252967
  [Foro de Servicios multimedia]: http://social.msdn.microsoft.com/Forums/es-es/MediaServices/threads
  [Supervisión de una cuenta de Servicios multimedia]: ../media-services-monitor-services-account/
  [Administración de contenido en Servicios multimedia]: ../media-services-manage-content/
