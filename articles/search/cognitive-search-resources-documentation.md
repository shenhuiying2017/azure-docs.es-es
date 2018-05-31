---
title: Recursos de documentación de búsqueda cognitiva (Azure Search) | Microsoft Docs
description: Una lista anotada de artículos, tutoriales, ejemplos y entradas de blog relacionados con cargas de trabajo de búsqueda cognitiva en Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: a96b66f61b19d218c5708a0ce967e0033ba26627
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786684"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Recursos de documentación para las cargas de trabajo de búsqueda cognitiva

Búsqueda cognitiva, ahora en versión preliminar, es una nueva capa de enriquecimiento de la indexación de Azure Search que busca información latente en orígenes no textuales y texto sin diferenciar, y la transforma en contenido de búsqueda de texto completo en Azure Search.

Los artículos siguientes son la documentación completa para la búsqueda cognitiva.

## <a name="getting-started"></a>Introducción
+ [What is cognitive search?](cognitive-search-concept-intro.md) (¿Qué es la búsqueda cognitiva?)
+ [Inicio rápido: crear una canalización de Cognitive Search mediante aptitudes y datos de ejemplo](cognitive-search-quickstart-blob.md)
+ [Tutorial: Procedimiento para llamar a Cognitive Search API (versión preliminar)](cognitive-search-tutorial-blob.md)
+ [Ejemplo: creación de una aptitud personalizada](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Guía de procedimientos
+ [Definición de un conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [How to reference annotations in a skillset](cognitive-search-concept-annotations-syntax.md) (Referencia a las anotaciones de un conjunto de aptitudes)
+ [How to map fields to an index](cognitive-search-output-field-mapping.md) (Asignación de campos enriquecidos a un índice)
+ [How to process and extract information from images](cognitive-search-concept-image-scenarios.md) (Procesamiento y extracción de información de imágenes)
+ [How to rebuild an Azure Search index](search-howto-reindex.md) (Reconstrucción de un índice de Azure Search)
+ [How to define a custom skills interface](cognitive-search-custom-skill-interface.md) (Definición de una interfaz de aptitudes personalizada)
+ [Sugerencias para la solución de problemas](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referencia

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [API REST en versión preliminar](search-api-2017-11-11-preview.md)
  + [Create Skillset (api-version=2017-11-11-Preview)](ref-create-skillset.md) (Creación del conjunto de aptitudes [api-version=2017-11-11-Preview])
  + [Create Indexer (api-version=2017-11-11-Preview)](ref-create-indexer.md) (Creación del indexador [api-version=2017-11-11-Preview])

## <a name="see-also"></a>Otras referencias

+ [API REST de Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexadores de Azure Search](search-indexer-overview.md)
+ [¿Qué es Azure Search?](search-what-is-azure-search.md)