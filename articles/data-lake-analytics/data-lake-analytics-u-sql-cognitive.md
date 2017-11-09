---
title: Uso de las funcionalidades de Cognitive en U-SQL para Azure Data Lake Analytics | Microsoft Docs
description: "Obtenga información sobre cómo usar la inteligencia de las funcionalidades de Cognitive en U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: ec48a07af0aba78f2e508bad232f34102f0c2073
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Tutorial: Introducción a las funcionalidades de Cognitive de U-SQL

## <a name="overview"></a>Información general
Las funcionalidades de Cognitive para U-SQL permiten a los desarrolladores usar inteligencia en sus programas de macrodatos. 

Las siguientes funcionalidades cognitivas están disponibles:
* Creación de imágenes: detección de caras
* Creación de imágenes: detección de emociones
* Creación de imágenes: detección de objetos (etiquetado)
* Creación de imágenes: OCR (reconocimientos óptico de caracteres)
* Texto: extracción de frases clave
* Texto: análisis de sentimiento

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>Uso de Cognitive en el script U-SQL

El proceso general es sencillo:

* Utilice la instrucción de REFERENCE ASSEMBLY para habilitar las características de Cognitive para el script de U-SQL.
* Use PROCESS en un conjunto de filas de entrada mediante un UDO de Cognitive para generar un conjunto de filas de salida.

### <a name="detecting-objects-in-images"></a>Detección de objetos en imágenes

En el ejemplo siguiente se muestra cómo usar las funcionalidades cognitivas para detectar objetos en imágenes.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
Para ver más ejemplos, examine los de **U-SQL/Cognitive** en la sección **Pasos siguientes**.

## <a name="next-steps"></a>Pasos siguientes
* [Ejemplos de U-SQL/Cognitive](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-use-window-functions.md)
