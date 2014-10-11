<properties linkid="manage-services-mediaservices-manage-media-services-content" urlDisplayName="How to manage media content" pageTitle="How to manage media content - Azure Media Services" metaKeywords="" description="Learn how to manage your media content in Azure Media Services." metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Administración de contenido en Servicios multimedia

El contenido de Servicios multimedia de Azure le permite administrar contenido multimedia para la cuenta de Servicios multimedia.

Actualmente, puede realizar las siguientes operaciones de contenido directamente desde el portal:

-   Consulta de información del contenido, como el estado publicado, la dirección URL publicada, el tamaño y la fecha y hora de la última actualización
-   Carga de nuevo contenido
-   contenido
-   Reproducción de vídeo de contenido
-   Publicación/cancelación de publicación de contenido
-   Eliminación de contenido

## Carga de contenido

1.  En el [Portal de administración][], haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2.  Seleccione la página CONTENT.
3.  Haga clic en el botón **Upload** en la página o en la parte inferior del portal.
4.  En el cuadro de diálogo **Upload content**, diríjase al archivo del recurso deseado. Haga clic en el archivo y, a continuación, haga clic en **Open** o presione **Entrar**.

    ![Cuadro de diálogo de carga de contenido][]

5.  En el cuadro de diálogo de carga de contenido, haga clic en el botón de comprobación para aceptar el archivo y el nombre de contenido.
6.  La carga comenzará y podrá realizar un seguimiento del proceso desde la parte inferior del portal.

    ![Estado del trabajo][]

Una vez que la carga se haya completado, verá el nuevo recurso en la lista de contenido. Se establecerá una convención en la que el nombre tendrá anexo "**-Source**" en la parte inferior para ayudar a realizar un seguimiento del nuevo contenido como contenido de origen para las tareas de codificación.

Si el valor del tamaño del archivo no se actualiza después de que se detenga el proceso de carga, presione el botón **Sync Metadata**. De esta forma, se sincroniza el tamaño de archivo del recurso con el tamaño del archivo real en el almacenamiento y se actualiza el valor en la página de contenido.

## Codificación de contenido

1.  En el [Portal de administración][], haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2.  Haga clic en la página CONTENT en la parte superior de la página.
3.  Haga clic en el vídeo de origen deseado para el trabajo de codificación y, a continuación, haga clic en **Encode** en la parte inferior de la página.
4.  En el cuadro de diálogo del codificador multimedia de Azure, seleccione uno de los valores predefinidos de codificación avanzados o comunes.

    **Valores predefinidos comunes**

    -   Reproducción en PC/Mac (a través de Flash/Silverlight)\*\*. Este valor predefinido produce un recurso de transmisión suave con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con codificación a 96 kbps mediante AAC y CBR de vídeo de 720p con codificación con una velocidad de bits de 6 de 3400 kbps a 400 kbps mediante el perfil principal H.264 y GOP de dos segundos.
    -   Reproducción a través de HTML5 (IE/Chrome/Safari)\*\*. Este valor predefinido produce un archivo MP4 único con las siguientes características: 44,1 kHz 16 bits/CBR de audio estéreo de ejemplo con una codificación a 128 kbps mediante AAC y CBR de vídeo de 720p con una codificación a 4500 kbps mediante el perfil principal H.264.
    -   Reproducción en dispositivos iOS y PC/Mac\*\*. Este valor predefinido produce un recurso con las mismas características de recurso de transmisión suave (descrito anteriormente), pero en un formato que puede usarse para proporcionar transmisiones de HLS de Apple en dispositivos iOS.

    **Valores predefinidos avanzados**

    -   El tema [Valores predefinidos del sistema Media Services Encoder][] explica qué significa cada valor predefinido en la lista de valores predefinidos avanzados.

    ![Cuadro de diálogo del codificador][]

    Actualmente, el portal no es compatible con todos los formatos de codificación compatibles que admite el codificador multimedia. Tampoco es compatible con el cifrado/descifrado de recursos multimedia. Puede realizar estas tareas mediante programación. Para obtener más información, consulte [Creación de aplicaciones con el SDK de Media Services para .NET][] y [Valores predefinidos del sistema Media Services Encoder][].

5.  En el cuadro de diálogo del codificador multimedia de Azure, especifique el nombre descriptivo del contenido de salida o acepte el valor predeterminado. A continuación, haga clic en el botón de comprobación para iniciar la operación de codificación y podrá realizar un seguimiento del progreso desde la parte inferior del portal.

Una vez que se haya realizado la codificación, la vista debe tener un aspecto similar a la captura de pantalla siguiente.

    ![PortalViewUploadCompleted][portaloverview]

Si el valor del tamaño de archivo no se actualiza después de que la codificación se haya realizado, presione el botón **Sync Metadata**. De esta forma, se sincroniza el tamaño de archivo de salida del recurso con el tamaño de archivo real en el almacenamiento y se actualiza el valor en la página de contenido.

## Publicación de contenido

1.  En el [Portal de administración][], haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2.  Haga clic en la página CONTENT en la parte superior de la página.
3.  Haga clic en un recurso que no se haya publicado. Haga clic en el botón de publicación para publicar en una dirección URL pública. Una vez que se haya publicado el contenido en una dirección URL, un reproductor de clientes con capacidad para mostrar el contenido codificado puede abrir la dirección URL.

![Contenido publicado][]

## Reproducción de contenido desde el portal

1.  En el [Portal de administración][], haga clic en **Servicios multimedia** y, a continuación, haga clic en el nombre de cuenta de Servicios multimedia.
2.  Haga clic en la página CONTENT en la parte superior de la página.
3.  Haga clic en el contenido de vídeo deseado y haga clic en el botón **Play** en la parte inferior del portal. Solo el contenido publicado puede reproducirse desde el portal. Además, la codificación debe ser compatible con el explorador.

<!-- Images -->

  [Portal de administración]: http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409
  [Cuadro de diálogo de carga de contenido]: ./media/media-services-manage-content/UploadContent.png
  [Estado del trabajo]: ./media/media-services-manage-content/Status.png
  [Valores predefinidos del sistema Media Services Encoder]: http://go.microsoft.com/fwlink/?LinkId=270865
  [Cuadro de diálogo del codificador]: ./media/media-services-manage-content/EncoderDialog2.png
  [Creación de aplicaciones con el SDK de Media Services para .NET]: http://go.microsoft.com/fwlink/?LinkId=270866
  [Contenido publicado]: ./media/media-services-manage-content/media-services-upload-content-published.png
