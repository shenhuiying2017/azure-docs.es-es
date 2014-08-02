<properties linkid="develop-media-services-how-to-guides-manage-assets" urlDisplayName="Manage Assets in Media Services" pageTitle="How to Manage Assets in Media Services - Azure" metaKeywords="" description="Learn how to manage assets on Media Services. You can also manage jobs, tasks, access policies, locators, and more. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="migree" solutions="" manager="" editor="" />

Administración de recursos en el almacenamiento
===============================================

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [Protección de un recurso](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409).

Después de crear recursos multimedia y de cargarlos en Servicios multimedia, puede obtener acceso a los recursos y administrarlos en el servidor. También puede administrar otros objetos en el servidor que forman parte de Servicios multimedia, como trabajos, tareas, directivas de acceso, localizadores y otros objetos.

El ejemplo siguiente muestra cómo realizar una consulta de un recurso con assetld.

``` {}
static IAsset GetAsset(string assetId)
{
    // Usar una consulta de selección LINQ para obtener un recurso.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Hacer una referencia al recurso como un IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
```

Para mostrar todos los recursos disponibles en el servidor, puede usar el método siguiente que se itera a través de toda la colección de recursos, y muestra detalles de cada recurso.

``` {}
 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Crear un Stringbuilder para almacenar la lista construida. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Mostrar la colección de recursos.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Mostrar los archivos asociados a cada recurso. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Mostrar el resultado en la consola.
    Console.Write(builder.ToString());
}
```

El siguiente fragmento de código elimina todos los recursos de la cuenta de Servicios multimedia.

``` {}
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
```

Para obtener más información acerca de la administración de recursos, consulte:

-   [Administración de recursos con el SDK de Media Services para .NET](http://msdn.microsoft.com/en-us/library/jj129589.aspx)
-   [Administración de recursos con la API de REST de Media Services](http://msdn.microsoft.com/en-us/library/jj129583.aspx)

Pasos siguientes
----------------

Ahora que sabe cómo administrar recursos, consulte el tema [Entrega de un recurso mediante descarga](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409).

