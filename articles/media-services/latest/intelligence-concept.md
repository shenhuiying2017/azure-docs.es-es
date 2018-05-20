---
title: Inteligencia multimedia de Azure | Microsoft Docs
description: Al usar Azure Media Services, puede analizar el contenido de audio y vídeo con AudioAnalyzerPreset y VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: 804a418f6ee88974d6e74a2c18bc5d01b6adf838
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="media-intelligence"></a>Inteligencia multimedia

API de REST de Azure Media Services v3 permite analizar el contenido de audio y vídeo. Para analizar el contenido, cree una **transformación** y envíe un **trabajo** que use uno de estos valores preestablecidos: **AudioAnalyzerPreset** o **VideoAnalyzerPreset**. 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** permite extraer diversa información de audio de un archivo de audio o vídeo. La salida incluye un archivo JSON (con toda la información) y un archivo VTT para la transcripción del audio. Este valor predeterminado acepta una propiedad que especifica el idioma del archivo de entrada en forma de una cadena [BCP47](https://tools.ietf.org/html/bcp47). La información de audio incluye:

* Transcripción del audio: transcripción de las palabras con marcas de tiempo. Se admiten varios idiomas.
* Indexación del hablante: una asignación de los hablantes y las palabras habladas correspondientes.
* Análisis de opiniones de voz: la salida del análisis de opiniones que se realiza en la transcripción del audio.
* Palabras clave: las palabras clave que se extraen de la transcripción del audio.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** permite extraer diversa información de audio y vídeo desde un archivo de vídeo. La salida incluye un archivo JSON (con toda la información), un archivo VTT para la transcripción del video y una colección de miniaturas de vídeo. Este valor preestablecido también acepta una cadena [BCP47](https://tools.ietf.org/html/bcp47) (que representa el idioma del vídeo) como propiedad. La información de vídeo incluye toda la información de audio mencionada anteriormente y los elementos adicionales siguientes:

* Seguimiento facial: el tiempo durante el cual aparecen rostros en el vídeo. Cada rostro tiene un identificador facial y una colección de miniaturas correspondiente.
* Texto visual: el texto que se detecta mediante el reconocimiento óptico de caracteres. Al texto se le agrega una marca de tiempo y también se usa para extraer palabras clave (además de la transcripción del audio).
* Fotogramas clave: una colección de fotogramas clave que se extrajeron del vídeo.
* Moderación del contenido visual: la parte de los vídeos que se marcó como contenido para adultos o contenido subido de tono.
* Anotación: el resultado de anotar los vídeos en función de un modelo de objetos predefinido.

##  <a name="insightsjson-elements"></a>Elementos insights.json

La saluda incluye un archivo JSON (insights.json) con toda la información que se encontró en el audio o el vídeo. El archivo JSON puede incluir los elementos siguientes:

### <a name="transcript"></a>transcript

|NOMBRE|DESCRIPCIÓN|
|---|---|
|id|El identificador de la línea.|
|text|La transcripción misma.|
|Idioma|El idioma de la transcripción. Diseñado para admitir la transcripción, donde cada línea puede tener un idioma distinto.|
|instances|Una lista de los intervalos de tiempo donde apareció esta línea. Si la instancia es una transcripción, solo tendrá 1 instancia.|

Ejemplo:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|NOMBRE|DESCRIPCIÓN|
|---|---|
|id|El identificador de la línea OCR.|
|text|El texto OCR.|
|confidence|La confiabilidad del reconocimiento.|
|Idioma|El idioma de OCR.|
|instances|Una lista de los intervalos de tiempo donde apareció este OCR (el mismo OCR puede aparecer varias veces).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="keywords"></a>keywords

|NOMBRE|DESCRIPCIÓN|
|---|---|
|id|El identificador de la palabra clave.|
|text|El texto de la palabra clave.|
|confidence|La confiabilidad del reconocimiento de la palabra clave.|
|Idioma|El idioma de la palabra clave (cuando se traduce).|
|instances|Una lista de los intervalos de tiempo donde apareció esta palabra clave (una palabra clave puede aparecer varias veces).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 

```

### <a name="faces"></a>faces

|NOMBRE|DESCRIPCIÓN|
|---|---|
|id|El identificador de rostros.|
|Nombre|El nombre del rostro. Puede ser "Unknown #0", una celebridad identificada o una persona capacitada del cliente.|
|confidence|La confidencialidad de la identificación facial.|
|Descripción|Si se trata de una celebridad, su descripción ("Satya Nadella nació en..."). |
|thumbnalId|El identificador de la miniatura del rostro en cuestión.|
|knownPersonId|Si se trata de alguien conocido, su identificador interno.|
|referenceId|Si se trata de una celebridad de Bing, su identificador de Bing.|
|referenceType|Actualmente, solo Bing.|
|título|Si se trata de una celebridad, su título (por ejemplo, "CEO de Microsoft").|
|imageUrl|Si se trata de una celebridad, la dirección URL de su imagen.|
|instances|Las instancias de cuándo el rostro apareció en el intervalo de tiempo determinado. Cada instancia también tiene su thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="labels"></a>etiquetas

|NOMBRE|DESCRIPCIÓN|
|---|---|
|id|El identificador de la etiqueta.|
|Nombre|El nombre de la etiqueta (por ejemplo, "Equipo", "TV").|
|Idioma|El idioma del nombre de la etiqueta (cuando se traduce). BCP-47|
|instances|Una lista de los intervalos de tiempo donde apareció esta etiqueta (una etiqueta puede aparecer varias veces). Cada instancia tiene un campo de confiabilidad. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="shots"></a>shots

|NOMBRE|DESCRIPCIÓN|
|---|---|
|id|El identificador de la toma.|
|keyFrames|Una lista de los fotogramas clave dentro de la toma (cada uno con un identificador y una lista de intervalos de tiempo de instancias).|
|instances|Una lista de intervalos de tiempo de esta toma (las tomas solo tienen 1 instancia).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="audioeffects"></a>audioEffects

|NOMBRE|DESCRIPCIÓN|
|---|---|
|id|El identificador del efecto de audio.|
|Tipo|El tipo de efecto de audio (por ejemplo, aplausos, voz, silencio).|
|instances|Una lista de los intervalos de tiempo donde apareció este efecto de audio.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```


### <a name="sentiments"></a>sentiments

Las opiniones se agregan según su campo sentimentType (Positive/Neutral/Negative). Por ejemplo, 0-0,1, 0,1-0,2.

|NOMBRE|DESCRIPCIÓN|
|---|---|
|id|El identificador de la opinión.|
|averageScore |El promedio de todas las puntuaciones de todas las instancias de ese tipo de opinión: Positive/Neutral/Negative.|
|instances|Una lista de los intervalos de tiempo donde apareció esta opinión.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Análisis de vídeos con Azure Media Services](analyze-videos-tutorial-with-api.md)
