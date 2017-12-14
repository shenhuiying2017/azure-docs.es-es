---
title: Carga de activos en Azure Media Clipper | Microsoft Docs
description: Pasos para cargar activos en Azure Media Clipper
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a479218ff8bd5addf4273b23c06380859e0ea08
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="loading-assets-into-azure-media-clipper"></a>Carga de activos en Azure Media Clipper
Los activos se pueden cargar en Azure Media Clipper mediante dos métodos:
1. El paso estático de una biblioteca de archivos
2. La generación dinámica de una lista de activos mediante API

## <a name="statically-load-videos-into-clipper"></a>Carga estática de vídeos en Clipper
Cargue vídeos de manera estática en Clipper para permitir que los usuarios finales creen clips sin seleccionar vídeos del panel de selección de activos.

En este caso, se pasa un conjunto estático de activos a Clipper. Cada activo incluye un id. de filtro/activo AMS, el nombre y la dirección URL del streaming publicado. Si corresponde, se puede pasar un token de autenticación de protección del contenido o una matriz de las direcciones URL en miniatura. Si se pasan, las miniaturas se rellenan en la interfaz. En los escenarios donde la biblioteca de activos es estática y pequeña, puede pasar el contrato de activo para cada activo de la biblioteca.

> [!NOTE]
> Cuando se cargan activos de manera estática en Clipper, los activos se agregan **directamente a la escala de tiempo** y **no se representa el panel de recursos**. El primer activo se agrega a la escala de tiempo y el resto de los activos se apila al lado derecho de la escala de tiempo.

Para cargar una biblioteca de activos estática, use el método **load** para pasar una representación JSON de cada activo. En el ejemplo de código siguiente se muestra la representación JSON de un activo.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> Se recomienda encadenar la llamada al método load() con el método ready(handler), tal como se muestra en el ejemplo anterior. En el ejemplo anterior se garantiza que el widget está listo antes de cargar los activos.

> [!NOTE]
> Para que las direcciones URL en miniatura funcionen según lo previsto en la escala de tiempo de Clipper, deben estar distribuidas de manera uniforme en todo el vídeo (basado en la duración) y en orden cronológico dentro de la matriz. Puede usar el fragmento de código JSON preestablecido siguiente como una referencia de ejemplo para generar imágenes con el procesador "Media Encoder Standard":

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Carga dinámica de vídeos en Clipper
Cargue vídeos de manera dinámica en Clipper para permitir que los usuarios seleccionen vídeos desde el panel de selección de activos para crear clips.

De manera alternativa, puede cargar dinámicamente los activos a través de una devolución de llamada. En los escenarios donde los activos se generan de manera dinámica o en que la biblioteca es grande, debe cargar a través de la devolución de llamada. Para cargar dinámicamente los activos, debe implementar la función de devolución de llamada onLoadAssets opcional. Esta función se pasa a Clipper en la inicialización. Los activos resueltos deben respetar el mismo contrato que los activos cargados de manera estática. El ejemplo de código siguiente muestra la firma del método y la entrada y la salida previstas.

> [!NOTE]
> Cuando se cargan activos de manera dinámica en Clipper, los activos se representan en el **panel de selección de activos**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```