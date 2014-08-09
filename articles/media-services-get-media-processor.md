<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

Obtención de una instancia de procesador multimedia
===================================================

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de la [Creación de un recurso codificado y carga en el almacenamiento](http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409).

En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

<table data-morhtml="true" border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead data-morhtml="true">
    <tr data-morhtml="true">
       <th data-morhtml="true">Nombre de procesador multimedia</th>
       <th data-morhtml="true">Descripci&oacute;n</th>
	<th data-morhtml="true">M&aacute;s informaci&oacute;n</th>
    </tr>
  </thead>
  <tbody data-morhtml="true">
    <tr data-morhtml="true">
       <td data-morhtml="true">Codificador multimedia de Azure</td>
       <td data-morhtml="true">Le permite ejecutar tareas de codificaci&oacute;n con el Codificador multimedia.</td>
       <td data-morhtml="true"><a data-morhtml="true" href="http://msdn.microsoft.com/es-es/library/jj129582.aspx"> Valores predefinidos del sistema Media Services Encoder</a></td>
    </tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Azure Media Packager</td>
        <td data-morhtml="true">Le permite convertir recursos multimedia de .mp4 a un formato de Smooth Streaming. Adem&aacute;s, le permite convertir recursos multimedia de Smooth Streaming al formato Apple HTTP Live Streaming (HLS).</td>
		<td data-morhtml="true"><a data-morhtml="true" href="http://msdn.microsoft.com/es-es/library/hh973635.aspx">Valores predefinidos del sistema Azure Media Packager</a></td>
    </tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Azure Media Encryptor</td>
        <td data-morhtml="true">Le permite cifrar recursos multimedia con la protecci&oacute;n PlayReady.</td>
        <td data-morhtml="true"><a data-morhtml="true" href="http://msdn.microsoft.com/es-es/library/hh973610.aspx">Valores predefinidos del sistema Azure Media Packager</a></td>
    </tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Storage Decryption</td>
        <td data-morhtml="true">Le permite descifrar recursos multimedia que se cifraron con el cifrado de almacenamiento.</td>
		<td data-morhtml="true">N/D</td>
    </tr>
  </tbody>
</table>

El siguiente método muestra cómo obtener una instancia del procesador multimedia. El ejemplo de código supone el uso de una variable de nivel de módulo llamado **\_context** para hacer referencia al contexto de servidor tal como se describe en la sección [Conexión con los Servicios multimedia mediante programación](http://www.windowsazure.com/es-es/develop/media-services/how-to-guides/set-up-computer-for-media-services).

``` {}
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

Pasos siguientes
----------------

Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema [Codificar un recurso](http://go.microsoft.com/fwlink/?LinkId=301753), que le mostrará cómo utilizar el Codificador multimedia de Azure para codificar un recurso.

