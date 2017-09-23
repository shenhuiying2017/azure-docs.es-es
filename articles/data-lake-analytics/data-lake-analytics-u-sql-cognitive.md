---
title: Uso de las funcionalidades de Cognitive en U-SQL para Azure Data Lake Analytics | Microsoft Docs
description: "Obtenga información sobre cómo usar la inteligencia de las funcionalidades de Cognitive en U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f77329f9838d6e824afa7234de90f62257a004de
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017

---

# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Tutorial: Introducción a las funcionalidades de Cognitive de U-SQL

Las funcionalidades de Cognitive para U-SQL permiten a los desarrolladores usar inteligencia en sus programas de macrodatos. El proceso general es sencillo:

* Utilice la instrucción de REFERENCE ASSEMBLY para habilitar las características de Cognitive para el script de U-SQL.
* Llame a la operación PROCESS para utilizar las funcionalidades de Cognitive 

## <a name="imaging-scenarios"></a>Escenarios de imágenes

### <a name="example-image-tagging"></a>Ejemplo: etiquetado de imágenes

En el ejemplo siguiente se muestra un uso integral de las funcionalidades relacionadas con las imágenes para detectar objetos dentro de estas.

    REFERENCE ASSEMBLY ImageCommon;
    REFERENCE ASSEMBLY FaceSdk;
    REFERENCE ASSEMBLY ImageEmotion;
    REFERENCE ASSEMBLY ImageTagging;
    REFERENCE ASSEMBLY ImageOcr;

    @imgs =
        EXTRACT FileName string, ImgData byte[]
        FROM @"/images/{FileName:*}.jpg"
        USING new Cognition.Vision.ImageExtractor();

    // Extract the number of objects on each image and tag them 
    @objects =
        PROCESS @imgs 
        PRODUCE FileName,
                NumObjects int,
                Tags string
        READONLY FileName
        USING new Cognition.Vision.ImageTagger();


### <a name="extract-emotions-from-human-faces"></a>Extracción de emociones de caras humanas 

    @emotions =
        PROCESS @imgs
        PRODUCE FileName string,
                NumFaces int,
                Emotion string
        READONLY FileName
        USING new Cognition.Vision.EmotionAnalyzer();

### <a name="estimate-age-and-gender-for-human-faces"></a>Cálculo de la edad y el sexo de caras humanas

    @faces = 
            PROCESS @imgs
            PRODUCE FileName,
                    NumFaces int,
                    FaceAge string,
                    FaceGender string
            READONLY FileName
            USING new Cognition.Vision.FaceDetector();

### <a name="detect-text-in-images-ocr"></a>Detección de texto en imágenes (OCR)

    @ocrs =
            PROCESS @imgs
            PRODUCE FileName,
                    Text string
            READONLY FileName
            USING new Cognition.Vision.OcrExtractor();

## <a name="text-scenarios"></a>Escenarios de texto

### <a name="input-data"></a>Datos de entrada

Suponga que tenemos una entrada que consta de "Guerra y Paz" por León Tolstói.

    REFERENCE ASSEMBLY [TextCommon];
    REFERENCE ASSEMBLY [TextSentiment];
    REFERENCE ASSEMBLY [TextKeyPhrase];

    @WarAndPeace =
        EXTRACT No int,
                Year string,
                Book string,
                Chapter string,
                Text string
        FROM @"/usqlext/samples/cognition/war_and_peace.csv"
        USING Extractors.Csv();

### <a name="extract-key-phrases-for-each-paragraph"></a>Extracción de las frases clave de cada párrafo

    @keyphrase =
        PROCESS @WarAndPeace
        PRODUCE No,
                Year,
                Book,
                Chapter,
                Text,
                KeyPhrase string
        READONLY No,
                Year,
                Book,
                Chapter,
                Text
        USING new Cognition.Text.KeyPhraseExtractor();

    // Tokenize the key phrases.
    @kpsplits =
        SELECT No,
            Year,
            Book,
            Chapter,
            Text,
            T.KeyPhrase
        FROM @keyphrase
            CROSS APPLY
                new Cognition.Text.Splitter("KeyPhrase") AS T(KeyPhrase);
    
### <a name="perform-sentiment-analysis-on-each-paragraph"></a>Realización del análisis de sentimiento de cada párrafo

    @sentiment =
        PROCESS @WarAndPeace
        PRODUCE No,
                Year,
                Book,
                Chapter,
                Text,
                Sentiment string,
                Conf double
        READONLY No,
                Year,
                Book,
                Chapter,
                Text
        USING new Cognition.Text.SentimentAnalyzer(true);


