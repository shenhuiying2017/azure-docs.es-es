<properties 
	pageTitle="Administración de activos en Servicios multimedia de Azure" 
	description="Aprenda a administrar activos en Servicios multimedia. También puede administrar trabajos, tareas, directivas de acceso, localizadores y mucho más. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>




<h1>  Administración de recursos en el almacenamiento</h1>

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [ Protección de un recurso](media-services-protect-asset.md).

Después de crear recursos multimedia y de cargarlos en Servicios multimedia, puede obtener acceso a los recursos y administrarlos en el servidor. También puede administrar otros objetos en el servidor que forman parte de Servicios multimedia, como trabajos, tareas, directivas de acceso, localizadores y otros objetos.

El ejemplo siguiente muestra cómo realizar una consulta de un recurso con assetld. 
<pre><code>
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre> 

Para mostrar todos los recursos disponibles en el servidor, puede usar el método siguiente que se itera a través de toda la colección de recursos, y muestra detalles de cada recurso.
<pre><code> 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
</code></pre>
El siguiente fragmento de código elimina todos los recursos de la cuenta de Servicios multimedia.
<pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

Para obtener más información acerca de la administración de recursos, consulte:
<ul>
<li><a href="http://msdn.microsoft.com/library/jj129589.aspx">Administración de recursos con el SDK de Media Services para .NET</a></li>
<li><a href="http://msdn.microsoft.com/library/jj129583.aspx">Administración de recursos con la API de REST de Media Services</a></li></ul>


<h2>Pasos siguientes</h2>
Ahora que sabe cómo administrar recursos, consulte el tema [Entrega de un recurso mediante descarga](media-services-deliver-asset-download.md).


<!--HONumber=42-->
