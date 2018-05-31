---
title: Habilidades predefinidas de extracción de datos, lenguaje natural y procesamiento de imágenes (Azure Search) | Microsoft Docs
description: Las habilidades cognitivas de extracción de datos, lenguaje natural y procesamiento de imágenes agregan semántica y estructura al contenido sin procesar de una canalización de Azure Seach.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 870cf9629c7af8faee0ce5709199b64910b27ffb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786704"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Habilidades predefinidas para el enriquecimiento del contenido (Azure Search)

En este artículo se obtiene información sobre las habilidades cognitivas que proporciona la búsqueda cognitiva. Una *habilidad cognitiva* es una operación que transforma el contenido de alguna manera. Normalmente es un componente que extrae datos o deduce la estructura y, por tanto, aumenta la comprensión de los datos de entrada. Casi siempre la salida se basa en texto. Un *conjunto de habilidades* es una colección de habilidades que definen la canalización de enriquecimiento. 

## <a name="predefined-skills"></a>Habilidades predefinidas

Hay varias habilidades que son flexibles con respecto a lo que usan o producen. En general, la mayoría de las habilidades se basan en modelos aprendidos previamente, lo que significa que no es posible aprender el modelo con sus propios datos de aprendizaje. Para obtener instrucciones sobre cómo crear una habilidad personalizada, vea [How to define a custom interface](cognitive-search-custom-skill-interface.md) (Cómo definir una interfaz personalizada) y [Example: creating a custom skill](cognitive-search-create-custom-skill-example.md) (Ejemplo: crear una habilidad personalizada). En la tabla siguiente se enumeran y se describen las habilidades proporcionadas por Microsoft. 

| Habilidad | DESCRIPCIÓN |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Esta habilidad usa un modelo aprendido previamente para detectar frases importantes en función de la colocación de términos, las reglas lingüísticas, la proximidad a otros términos y cómo de inusual es el término en los datos de origen. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Esta habilidad usa un modelo aprendido previamente para detectar qué idioma se usa (un identificador de idioma por documento). Si se usan varios idiomas en los mismos segmentos de texto, la salida es el LCID del idioma más usado.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Consolida el texto de una colección de campos en un solo campo.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Esta habilidad usa un modelo aprendido previamente para establecer las entidades de un conjunto fijo de categorías: personas, ubicación, organización. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Esta habilidad usa un modelo aprendido previamente para puntuar opiniones positivas o negativas registro a registro. La puntuación va de 0 a 1. Se otorgan puntuaciones neutras para el caso nulo cuando no se puede detectar la opinión y para el texto que se considera neutro.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Divide el texto en páginas para que se pueda enriquecer o aumentar el contenido de forma incremental. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Esta habilidad usa un algoritmo de detección de imágenes para identificar el contenido de una imagen y generar una descripción de texto. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconocimiento óptico de caracteres. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Asigna la salida a un tipo complejo (un tipo de datos de varias partes que se podría usar para un nombre completo, una dirección de varias líneas o una combinación de apellido e identificador personal). |

## <a name="see-also"></a>Otras referencias

+ [Definición de un conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Custom Skills interface definition](cognitive-search-custom-skill-interface.md) (Definición de interfaz de habilidades personalizadas)
+ [Tutorial: Enriched indexing with cognitive search](cognitive-search-tutorial-blob.md) (Tutorial: Indización enriquecida con búsqueda cognitiva)