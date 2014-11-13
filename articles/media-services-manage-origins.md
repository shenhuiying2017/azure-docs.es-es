<properties urlDisplayName="index" pageTitle="Administraci&oacute;n de or&iacute;genes en una cuenta de Servicios multimedia" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Administraci&oacute;n de extremos de streaming en una cuenta de Servicios multimedia" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>Administración de extremos de streaming en una cuenta de Servicios multimedia

Servicios multimedia le permite agregar varios extremos de streaming a su cuenta y configurarlos. Cada cuenta de Servicios multimedia tiene asociado al menos un extremo de streaming llamado **default**.

> [WACOM.NOTE] Extremos de streaming usados para conocerse como orígenes.

## Adición y eliminación de extremos de streaming

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.
2.  Seleccione la página EXTREMOS DE STREAMING.
3.  Haga clic en el botón ADD o DELETE en la parte inferior de la página. Tenga en cuenta que no es posible eliminar el extremo de streaming predeterminado.
4.  Haga clic en el botón INICIAR para iniciar el extremo de streaming.
5.  Haga clic en el nombre del extremo de streaming para configurarlo.

    ![Página de orígenes][Página de orígenes]

## Configuración del extremo de streaming

La pestaña CONFIGURE le permite realizar las siguientes configuraciones:

1.  Definir el período máximo de almacenamiento en caché que se especificará en el encabezado de control de caché de las respuestas HTTP. Este valor no sobrescribirá el valor máximo de caché que se definió explícitamente en el contenido de blobs.

2.  Especificar las direcciones IP que podrán conectarse al extremo de transmisión por secuencias publicado. Si no se especificaron direcciones IP, cualquier dirección IP se podrá conectar.

3.  Especifique la configuración para la autenticación del encabezado de firma de Akamai.

    ![Configuración del origen][Configuración del origen]

    La configuración que se describe en esta página solo se aplica a los extremos de streaming que tienen al menos una unidad reservada. Para reservar las unidades reservadas de transmisión por secuencias a petición, vaya a la pestaña ESCALA. Para obtener información detallada acerca de las unidades reservadas, consulte [Escalación de Servicios multimedia][Escalación de Servicios multimedia].

  [Portal de administración]: https://manage.windowsazure.com/
  [Página de orígenes]: ./media/media-services-manage-origins/media-services-origins-page.png
  [Configuración del origen]: ./media/media-services-manage-origins/media-services-origins-configure.png
  [Escalación de Servicios multimedia]: ../media-services-how-to-scale/
