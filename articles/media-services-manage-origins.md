<properties linkid="scripting-center-index" urlDisplayName="index" pageTitle="How to Manage Origins in a Media Services Account" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Origins in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>Administración de orígenes en una cuenta de los Servicios multimedia

Servicios multimedia le permite agregar varios orígenes de transmisión por secuencias a su cuenta y configurarlos. Cada cuenta de Servicios multimedia tiene asociado al menos un origen de transmisión por secuencias llamado **default** (predeterminado).

## Adición y eliminación de orígenes

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.
2.  Seleccione la página ORIGINS.
3.  Haga clic en el botón ADD o DELETE en la parte inferior de la página. Tenga en cuenta que no es posible eliminar el origen predeterminado.
4.  Haga clic en el botón START para iniciar el origen.
5.  Si desea configurar el origen, haga clic en el nombre del origen.

    ![Página de orígenes][Página de orígenes]

## Configuración del origen

La pestaña CONFIGURE le permite realizar las siguientes configuraciones:

1.  Definir el período máximo de almacenamiento en caché que se especificará en el encabezado de control de caché de las respuestas HTTP. Este valor no sobrescribirá el valor máximo de caché que se definió explícitamente en el contenido de blobs.

2.  Especificar las direcciones IP que podrán conectarse al extremo de transmisión por secuencias publicado. Si no se especificaron direcciones IP, cualquier dirección IP se podrá conectar.

3.  Especifique la configuración para la autenticación del encabezado de firma de Akamai.

    ![Configuración del origen][Configuración del origen]

    La configuración en esta página solo se aplicará a orígenes que tengan, al menos, una unidad reservada. Para reservar las unidades reservadas de transmisión por secuencias a petición, vaya a la pestaña ESCALA. Para obtener información detallada acerca de las unidades reservadas, consulte [Escalación de Servicios multimedia][Escalación de Servicios multimedia].

  [Portal de administración]: https://manage.windowsazure.com/
  [Página de orígenes]: ./media/media-services-manage-origins/media-services-origins-page.png
  [Configuración del origen]: ./media/media-services-manage-origins/media-services-origins-configure.png
  [Escalación de Servicios multimedia]: http://go.microsoft.com/fwlink/?LinkID=275847&clcid=0x409/
