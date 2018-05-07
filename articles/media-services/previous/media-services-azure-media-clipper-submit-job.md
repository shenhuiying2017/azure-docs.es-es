---
title: "Envío de trabajos de clips de Azure Media Clipper | Microsoft Docs"
description: Pasos para enviar trabajos de clips desde Azure Media Clipper
services: media-services
keywords: "clip;subclip;codificación;multimedia"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8372c405087c0dc7a000a65265bb99c395c3a8d6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Envío de trabajos de clips desde Azure Media Clipper
Azure Media Clipper requiere que se implemente un método **submitSubclipCallback** para gestionar el envío de trabajos de clips. Esta función sirve para implementar un método HTTP POST de la salida de Clipper en un servicio web. Este servicio web es el lugar adonde puede enviar el trabajo de codificación. La salida de Clipper es un valor preestablecido de Media Encoder Standard para trabajos representados o la carga útil de la API de REST para llamadas de filtro de manifiesto dinámico. Este modelo de paso es necesario porque las credenciales de la cuenta de servicios multimedia no son seguras en el explorador del cliente.

En el diagrama de secuencia siguiente se ilustra el flujo de trabajo entre el cliente del explorador, su servicio web y Azure Media Services: ![Diagrama de secuencia de Azure Media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

En el diagrama anterior, las cuatro entidades son: el explorador del usuario final, su servicio web, el extremo de red CDN que hospeda los recursos de Clipper y Azure Media Services. Cuando el usuario final navega a su página web, la página obtiene los recursos CSS y JavaScript de Clipper desde el extremo de red CDN de hospedaje. El usuario final configura el trabajo de creación de clips o la llamada de creación de un filtro de manifiesto dinámico desde el explorador. Cuando el usuario final envía el trabajo o la llamada de creación de filtro, el explorador coloca la carga de trabajo en un servicio web que se debe implementar. A la larga, el servicio web envía el trabajo de creación de clips o la llamada de creación de filtro a Azure Media Services con las credenciales de la cuenta de Media Services.

En el ejemplo siguiente se ilustra un método **submitSubclipCallback** de ejemplo. En este método, se implementa el método HTTP POST del valor preestablecido de codificación de Media Encoder Standard. Si POST se realizó correctamente (**result**), se resuelve **Promise**; de lo contrario, se rechaza con detalles del error.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, retuns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
La salida del envío del trabajo es el valor preestablecido de codificación Media Encoder Standard para el trabajo representado o la carga útil de la API de REST para los filtros de manifiesto dinámico.

## <a name="submitting-encoding-job-to-create-video"></a>Envío del trabajo de codificación para crear vídeo
Puede enviar un trabajo de codificación de Media Encoder Standard para crear un clip de vídeo con precisión a nivel de fotograma. El trabajo de codificación genera vídeos representados, un nuevo archivo MP4 fragmentado.

El contrato de salida del trabajo para el clip representado es un objeto JSON con las siguientes propiedades:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to sumit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Para realizar el trabajo de codificación, envíe el trabajo de codificación de Media Encoder Standard con el valor preestablecido asociado. Consulte este artículo para detalles sobre cómo enviar trabajos de codificación con el [SDK de .NET](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) o la [API de REST](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Creación rápida de clips de vídeo sin codificación
Como alternativa para la creación de un trabajo de codificación, puede usar Azure Media Clipper para crear filtros de manifiesto dinámico. Los filtros no requieren codificación y se pueden crear rápidamente ya que no se crea ningún activo. El contrato de salida de un clip de filtro es un objeto JSON con las siguientes propiedades:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Para enviar la llamada de REST para crear un filtro de manifiesto dinámico, envíe la carga de trabajo de filtro asociada mediante la [API de REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).