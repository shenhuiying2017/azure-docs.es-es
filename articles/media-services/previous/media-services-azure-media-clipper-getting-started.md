---
title: "Introducción a Azure Media Clipper | Microsoft Docs"
description: "Introducción a Azure Media Clipper, una herramienta para crear clips de vídeo a partir de activos de AMS"
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Creación de clips con Azure Media Clipper
En esta sección se muestran los pasos básicos para comenzar a trabajar con Azure Media Clipper. En las secciones siguientes se proporcionan detalles sobre cómo configurar Azure Media Clipper.

- En primer lugar, agregue los siguientes vínculos para Azure Media Player y Azure Media Clipper al encabezado del documento. Se recomienda especificar explícitamente una versión de Clipper y de Azure Media Player en las direcciones URL. No use la versión más reciente de estos recursos en producción, dado que están sujetos a cambios a petición.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- A continuación, agregue las siguientes clases al elemento div donde desea crear una instancia de Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcionalmente, para habilitar el tema oscuro, agregue la clase dark-skin:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- A continuación, cree una instancia de Clipper con la siguiente llamada API:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Los parámetros para la llamada al método de inicialización son:
- `selector` {REQUIRED, string}: selector de CSS del elemento HTML coincidente donde se debe representar el widget.
- `restVersion` {REQUIRED, string}: la versión de la API de REST de Azure Media Services de destino. La versión de REST define el formato de la salida generada por el widget. Actualmente, solo se admite la 2.0.
- `submitSubclipCallback` {REQUIRED, promise}: la función de devolución de llamada que se invoca cuando se hace clic en el botón "enviar" del widget. La función de devolución de llamada debe esperar la salida generada por el widget (una configuración del trabajo de representación o una definición de filtro). Para más información, consulte la devolución de llamada de envío del subclip.
- `logLevel` {OPTIONAL, {"info", "warn", "error"}}: el nivel de registro que se muestra en la consola del explorador. Valor predeterminado: error
- `minimumMarkerGap` {OPTIONAL, int}: el tamaño mínimo de un subclip (en segundos). Nota: El valor debe ser mayor o igual que 6, que también es el valor predeterminado.
- `singleBitrateMp4Profile` {OPTIONAL, JSON object}: el perfil de mp4 de velocidad de bits única para usar en la configuración del trabajo de representación generado por el widget. Si no se proporciona, se usa el [perfil de MP4 de velocidad de bits única predeterminado](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {OPTIONAL, JSON object}: el perfil de mp4 de velocidad de bits múltiple para usar en la configuración del trabajo de representación generado por el widget. Si no se proporciona, se usa el [perfil de MP4 de velocidad de bits múltiple predeterminado](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {OPTIONAL, json object}: permite la personalización de los métodos abreviados de teclado del widget. Para más información, consulte los [métodos abreviados de teclado personalizables](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {OPTIONAL, promise}: la función de devolución de llamada invocada para cargar (de manera asincrónica) una nueva página de recursos en el panel de recursos cada vez que el usuario se desplaza a la parte inferior del panel. Para más información, consulte la devolución de llamada del cargador de paneles de recursos.
- `height` {OPTIONAL, number}: el alto total del widget (el alto mínimo es 600 píxeles sin el panel de recursos y 850 píxeles con el panel de recursos).
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'}): los modos de creación de subclips permitidos. El valor predeterminado es todos.
- `filterAssetsTypes` (OPTIONAL, bool): filterAssetsTypes le permite mostrar u ocultar la lista desplegable de filtros del panel de recursos. El valor predeterminado es true.
- `speedLevels` (OPTIONAL, array): speedLevels permite definir diferentes niveles de velocidad para el reproductor de vídeo. Para más información, consulte la [documentación de Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions).
- `resetOnJobDone` (OPTIONAL, bool): resetOnJobDone permite que Clipper restablezca el creador de subclips a un estado inicial cuando un trabajo se envía correctamente.
- `autoplayVideo` (OPTIONAL, bool): autoplayVideo permite que el creador de subclips reproduzca automáticamente el vídeo cuando se carga. El valor predeterminado es true.
- `language` {OPTIONAL, string}: el idioma establece el idioma del widget. Si no se especifica, el widget intenta localizar los mensajes basándose en el idioma del explorador. Si no se detecta ningún idioma en el explorador, el widget adopta como predeterminado el inglés. Para más información, consulte la sección de [configuración de la localización](media-services-azure-media-clipper-localization.md).
- `languages` {OPTIONAL, JSON}: el parámetro de idiomas sustituye el diccionario predeterminado de idiomas por un diccionario personalizado que define el usuario. Para más información, consulte la sección de [configuración de la localización](media-services-azure-media-clipper-localization.md).
- `extraLanguages` (OPTIONAL, JSON): el parámetro extraLanaguages agrega nuevos idiomas al diccionario predeterminado. Para más información, consulte la sección de [configuración de la localización](media-services-azure-media-clipper-localization.md).

## <a name="typescript-definition"></a>Definición de TypeScript
Se puede encontrar un archivo de definición de [TypeScript](https://www.typescriptlang.org/) para Clipper [aquí](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API de Azure Media Clipper
En esta sección se describe la superficie de la API proporcionada por Clipper.

- `ready(handler)`: ofrece una manera de ejecutar JavaScript tan pronto como Clipper esté completamente cargado y listo para usar.
- `load(assets)`: carga una lista de activos en la escala de tiempo del widget (no se debe usar en combinación con assetsPanelLoaderCallback). Consulte este [artículo](media-services-azure-media-clipper-load-assets.md) para más información sobre cómo cargar los recursos en Clipper.
- `setLogLevel(level)`: establece el nivel de registro que se mostrará en la consola del explorador. Los valores posibles son `info`, `warn` y `error`.
- `setHeight(height)`: establece el alto total del widget en píxeles (el alto mínimo es 600 píxeles sin el panel de recursos y 850 píxeles con el panel recursos).
- `version`: obtiene la versión del widget.

## <a name="next-steps"></a>Pasos siguientes
Consulte los pasos siguientes para configurar Azure Media Clipper:
- [Carga de recursos en Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Configuración de métodos abreviados de teclado personalizados](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Envío de trabajos de clips desde Clipper](media-services-azure-media-clipper-submit-job.md)
- [Configuración de la localización](media-services-azure-media-clipper-localization.md)