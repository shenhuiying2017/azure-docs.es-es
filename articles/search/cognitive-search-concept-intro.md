---
title: Cognitive Search para extracción de datos y procesamiento de lenguaje natural en Azure Search | Microsoft Docs
description: Extracción de datos, procesamiento de lenguaje natural (NLP) y procesamiento de imágenes para crear contenido de búsqueda en la indexación de Azure Search a través de aptitudes cognitivas.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: cce10ceb190ac90b57e77bfa5903b30b2c249a2c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942166"
---
# <a name="what-is-cognitive-search"></a>¿Qué es Cognitive Search?

Cognitive Search es una característica en versión preliminar de [Azure Search](search-what-is-azure-search.md), disponible en todos los niveles en las regiones Centro y Sur de EE. UU. y Europa Occidental, que agrega inteligencia artificial a las cargas de trabajo de indexación. La extracción de datos, el procesamiento de lenguaje natural y el procesamiento de imágenes durante la indexación encuentran la información latente en contenido no estructurado ni de búsqueda y lo transforma en contenido en el que se pueden realizar búsquedas en Azure Search.

La integración de la inteligencia artificial se realiza a través de *aptitudes cognitivas* que enriquecen los documentos de origen mediante procesos secuenciales en la ruta a un índice de búsqueda. 

![Diagrama de la canalización de Cognitive Search](./media/cognitive-search-intro/cogsearch-architecture.png "Diagrama de la canalización de Cognitive Search")

Las aptitudes que se usan durante la indexación puede ser predefinidas o personalizadas:

+ Las [aptitudes predefinidas](cognitive-search-predefined-skills.md) se basan en los mismos algoritmos de inteligencia artificial que se usan en Cognitive Services APIs: [Reconocimiento de entidades con nombre](cognitive-search-skill-named-entity-recognition.md), [Extracción de frases clave](cognitive-search-skill-keyphrases.md) y [OCR](cognitive-search-skill-ocr.md), por nombrar algunas. 

+ Las [aptitudes personalizadas](cognitive-search-create-custom-skill-example.md) se pueden desarrollar para cualquier procesamiento especializado que se requiera. Algunos ejemplos de aptitudes personalizadas pueden ser un módulo de entidad personalizado o un clasificador de documentos que tiene como destino un dominio específico, como finanzas, publicaciones científicas o medicina.

> [!NOTE]
> Cognitive Search se encuentra en versión preliminar pública y la ejecución del conjunto de aptitudes se ofrece de forma gratuita. Más adelante, se anunciarán los precios de esta funcionalidad.

## <a name="components-of-cognitive-search"></a>Componentes de Cognitive Search

La canalización de Cognitive Search se basa en los [*indexadores* de Azure Search ](search-indexer-overview.md) que rastrean los orígenes de datos y proporcionan el procesamiento completo de los índices. Las aptitudes ahora se adjuntan a los indexadores, lo que permite interceptar y enriquecer los documentos en función del conjunto de aptitudes que defina. Una vez que se indexa, puede acceder al contenido mediante solicitudes de búsqueda a través de todos los [tipos de consulta compatibles con Azure Search](search-query-overview.md).  Si es la primera vez que usa los indexadores, esta sección lo guiará a través de los pasos.

### <a name="source-data-and-document-cracking-phase"></a>Fase de descifrado de documentos y datos de origen

Al comienzo de la canalización, hay texto no estructurado o contenido que no es de texto, como archivos JPEG de documentos escaneados e imágenes. Los datos deben estar en un servicio de almacenamiento de datos de Azure al que un indexador puede acceder. Los indexadores pueden "descifrar" los documentos de origen para extraer texto de los datos de origen.

![Fase de descifrado de documentos](./media/cognitive-search-intro/document-cracking-phase-blowup.png "descifrado de documentos")

 Entre los orígenes compatibles se incluyen Azure Blob Storage, Azure Table Storage, Azure SQL Database y Azure Cosmos DB. El contenido basado en texto se puede extraer de estos tipos de archivo: PDF, Word, PowerPoint y CSV. Para ver la lista completa, consulte los [formatos compatibles](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Fase de enriquecimiento y aptitudes cognitivas

El enriquecimiento se produce a través de *aptitudes cognitivas* que realizan operaciones atómicas. Por ejemplo, una vez que obtiene el contenido de texto de un archivo PDF, puede aplicar la detección de idioma del reconocimiento de entidades o la extracción de frases clave para generar campos nuevos en el índice que no están disponibles de manera nativa en el origen. La colección completa de aptitudes que usa en la canalización se conoce como un *conjunto de aptitudes*.  

![Fase de enriquecimiento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimiento")

Un conjunto de aptitudes se basa en las [aptitudes cognitivas predefinidas](cognitive-search-predefined-skills.md) o en las [aptitudes predeterminadas](cognitive-search-create-custom-skill-example.md) que proporciona y conecta con el conjunto de aptitudes. Un conjunto de aptitudes puede ser mínimo o altamente complejo y no solo determina el tipo de procesamiento, sino también el orden de las operaciones. Un conjunto de aptitudes más las asignaciones de campos que se definen como parte de un indexador especifica completamente la canalización de enriquecimiento. Para más información sobre cómo unir todos estos elementos, consulte el artículo sobre cómo [definir un conjunto de aptitudes](cognitive-search-defining-skillset.md).

Internamente, la canalización genera una colección de documentos enriquecidos. Puede decidir qué elementos de los documentos enriquecidos se deben asignar a los campos del índice de búsqueda que se pueden indexar. Por ejemplo, si aplicara las aptitudes de reconocimiento de entidades y de extracción de frases clave, esos campos nuevos se volverían parte del documento enriquecido y se podrían asignar a los campos del índice. Consulte las [anotaciones](cognitive-search-concept-annotations-syntax.md) para más información sobre las formaciones de entrada/salida.

### <a name="search-index-and-query-based-access"></a>Índice de búsqueda y acceso basado en consulta

Cuando finalice el procesamiento, tendrá un corpus de búsqueda compuesto de documentos enriquecidos donde se pueden realizar búsquedas completas de texto en Azure Search. Para acceder al contenido enriquecido que la canalización genera, los desarrolladores y usuarios [consultan el índice](search-query-overview.md). 

![Icono de índice con búsqueda](./media/cognitive-search-intro/search-phase-blowup.png "Icono de índice con búsqueda")

El índice es como cualquier otro índice que pudiera crear para Azure Search: puede complementarlo con analizadores personalizados, invocar consultas de búsqueda aproximada, agregar la búsqueda filtrada o experimentar con perfiles de puntuación para volver a dar forma a los resultados de la búsqueda.

Los índices se generan a partir de un esquema de índice que define los campos, atributos y otras construcciones adjuntas a un índice específico, como los perfiles de puntuación y las asignaciones de sinónimos. Una vez que se define y rellena un índice, puede indexar de manera incremental para seleccionar documentos de origen nuevos y actualizados. Algunas de las determinaciones requieren recompilar completamente el índice. Debe usar un conjunto de datos pequeño hasta que el diseño del esquema sea estable. Para más información, consulte el artículo sobre cómo [recompilar un índice](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Características y conceptos principales

| Concepto | DESCRIPCIÓN| Vínculos |
|---------|------------|-------|
| Conjunto de aptitudes | Recurso con nombre de nivel superior que contiene una colección de aptitudes. Un conjunto de aptitudes es la canalización de enriquecimiento. Se invoca durante la indexación por parte del indexador. | [Define a skillset](cognitive-search-defining-skillset.md) (Definición de un conjunto de aptitudes) |
| Aptitud cognitiva | Transformación atómica en una canalización de enriquecimiento. Normalmente, se trata de un componente que extrae o deduce la estructura y, por tanto, aumenta el reconocimiento de los datos de entrada. Casi siempre, la salida se basa en el texto y el procesamiento es el procesamiento de lenguaje natural o el procesamiento de imagen que extrae o genera texto a partir de entradas de imágenes. La salida de una aptitud se puede asignar a un campo en un índice o usar como entrada para el enriquecimiento de nivel inferior. Microsoft predefinida y proporciona una aptitud, o bien puede ser personalizada, es decir, el usuario puede crearla e implementarla. | [Predefined skills](cognitive-search-predefined-skills.md) (Aptitudes predefinidas) |
| Extracción de datos | Abarca una amplia variedad de procesamiento, pero respecto a Cognitive Search, la aptitud de reconocimiento de entidades con nombre se usa principalmente para extraer datos (una entidad) a partir de un origen que no proporciona de manera nativa esa información. | [Named Entity Recognition Skill](cognitive-search-skill-named-entity-recognition.md) (Aptitud de reconocimiento de entidades con nombre)| 
| Procesamiento de imágenes | Deduce texto a partir de una imagen, como la capacidad de reconocer un punto de referencia, o bien extrae texto a partir de una imagen. Algunos ejemplos comunes incluyen OCR para levantar caracteres de un archivo de documento escaneado (JPEG) o reconocer el nombre de una calle en una fotografía que incluye un letrero con el nombre. | [Image Analysis Skill](cognitive-search-skill-image-analysis.md) (Aptitud de análisis de imágenes) o [OCR Skill](cognitive-search-skill-ocr.md) (Aptitud de OCR)
| Procesamiento de lenguaje natural | Procesamiento de texto para obtener conclusiones e información sobre entradas de texto. La detección de idioma, el análisis de sentimiento y la extracción de frases clave son aptitudes que se enmarcan en el procesamiento de lenguaje natural.  | [Key Phrase Extraction Skill](cognitive-search-skill-keyphrases.md) (Aptitud de Extracción de frases clave), [Language Detection Skill](cognitive-search-skill-language-detection.md) (Aptitud de Detección de idioma), [Sentiment Analysis Skill](cognitive-search-skill-sentiment.md) (Aptitud de Análisis de sentimiento) |
| Descifrado de documentos | Proceso de extraer o crear contenido de texto a partir de orígenes que no son de texto durante el indexado. El reconocimiento óptico de caracteres (OCR) es un ejemplo pero, en general, se refiere a la funcionalidad de indexador principal cuando el indexador extrae contenido los archivos de aplicación. El origen de datos que proporciona la ubicación del archivo de origen y la definición del indexador que brinda las asignaciones de campo son factores clave en el descifrado de documentos. | Consulte [Indexers](search-indexer-overview.md) (Indexador) |
| Formas | Consolide los fragmentos de texto en una estructura más grande o, a la inversa, desglose fragmentos de texto de gran tamaño a un tamaño que se pueda administrar para un procesamiento adicional. | [Shaper Skill](cognitive-search-skill-shaper.md) (Aptitud de conformador), [Text Merger Skill](cognitive-search-skill-textmerger.md) (Aptitud de combinación de texto), [Text Split Skill](cognitive-search-skill-textsplit.md) (Aptitud de división de texto) |
| Documentos enriquecidos | Estructura interna transitoria, sin acceso directo en el código. Los documentos enriquecidos se generan durante el procesamiento, pero solo las salidas finales persisten en un índice de búsqueda. Las asignaciones de campos determinan los elementos de datos que se agregan al índice. | Consulte [Accessing enriched documents](cognitive-search-tutorial-blob.md#access-enriched-document) (Acceso a documentos enriquecidos). |
| Indexer |  Un rastreador (crawler) que extrae datos y metadatos utilizables en búsquedas de un origen de datos externo y rellena un índice basado en las asignaciones de un campo a otro entre el índice y su origen de datos para el descifrado de documentos. En el caso de Cognitive Search, el indexador invoca un conjunto de aptitudes y contiene las asignaciones de campos que asocian la salida de enriquecimiento con los campos de destino del índice. La definición del indexador contiene todas las instrucciones y referencias de las operaciones de la canalización y esta se invoca cuando se ejecuta el indexador. | [Indexadores](search-indexer-overview.md) |
| Origen de datos  | Un objeto que un indexador usa para conectarse a un origen de datos externo de tipos compatibles en Azure. | Consulte [Indexers](search-indexer-overview.md) (Indexador) |
| Índice | Corpus de búsqueda persistente en Azure Search, compilado a partir de un esquema de índice que define la estructura y el uso de los campos. | [Indexes in Azure Search](search-what-is-an-index.md) (Índices en Azure Search) | 


## <a name="where-do-i-start"></a>¿Por dónde empiezo?

**Paso 1: Crear un servicio de búsqueda en una región proporcionando las API** 

+ Centro-Sur de EE. UU
+ Europa occidental

**Paso 2: Experiencia práctica para dominar el flujo de trabajo**

+ [Inicio rápido (portal)](cognitive-search-quickstart-blob.md)
+ [Tutorial (solicitudes HTTP)](cognitive-search-tutorial-blob.md)
+ [Aptitudes personalizadas de ejemplo (C#)](cognitive-search-create-custom-skill-example.md)

**Paso 3: Revisar la API (solo REST)**

Actualmente, solo se proporcionan API de REST. Use `api-version=2017-11-11-Preview` en todas las solicitudes. Use las API siguientes para compilar una solución de Cognitive Search. Solo se agregan o extienden dos API para Cognitive Search. Otras API tienen la misma sintaxis que las versiones con disponibilidad general.

| API DE REST | DESCRIPCIÓN |
|-----|-------------|
| [Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source) (Creación de un origen de datos)  | Recurso que identifica un origen de datos externo que proporciona los datos de origen que se usan para crear documentos enriquecidos.  |
| [Create Skillset (api-version=2017-11-11-Preview)](ref-create-skillset.md) (Creación del conjunto de aptitudes [api-version=2017-11-11-Preview])  | Recurso que coordina el uso de las [aptitudes predefinidas](cognitive-search-predefined-skills.md) y las [aptitudes cognitivas personalizadas](cognitive-search-custom-skill-interface.md) que se usan en una canalización de enriquecimiento durante el indexado. |
| [Crear índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Esquema que expresa un índice de Azure Search. Los campos del índice se asignan a los campos de los datos de origen o a los campos fabricados durante la fase de enriquecimiento (por ejemplo, un campo para los nombres de organización creado por el reconocimiento de entidades). |
| [Create Indexer (api-version=2017-11-11-Preview)](ref-create-skillset.md) (Creación de indexador [api-version=2017-11-11-Preview])  | Recurso que define los componentes usados durante la indexación, los que incluyen un origen de datos, un conjunto de aptitudes, asociaciones de campos desde el origen y estructuras de datos intermedias con el índice de destino, además del destino mismo. La ejecución del indexador es el desencadenador del enriquecimiento y la ingesta de datos. La salida es un corpus de búsqueda basado en el esquema del índice, que se rellena con los datos de origen y se enriquece a través de los conjuntos de aptitudes.  |

**Lista de comprobación: un flujo de trabajo típico**

1. Subdivida los datos de origen de Azure en un ejemplo representativo. La indexación toma tiempo, por lo que empiece con un conjunto de datos representativo y pequeño para ir compilando el índice de manera incremental a medida que la solución madura.

1. Cree un [objeto de origen de datos](https://docs.microsoft.com/rest/api/searchservice/create-data-source) en Azure Search para proporciona una cadena de conexión para la recuperación de datos.

1. Cree un [conjunto de aptitudes](ref-create-skillset.md) con pasos de enriquecimiento.

1. Defina el [esquema de índice](https://docs.microsoft.com/rest/api/searchservice/create-index). La colección *Fields* (Campos) incluye campos de los datos de origen. También debe simular campos adicionales para contener valores generados para el contenido creado durante el enriquecimiento.

1. Defina el [indexador](ref-create-skillset.md) que hace referencia al origen de datos, conjunto de aptitudes e índice.

1. Dentro del indexador, agregue *outputFieldMappings*. En esta sección, la salida se asigna desde el conjunto de aptitudes (en el paso 3) a los campos de entrada del esquema de índice (en el paso 4).

1. Envíe la solicitud *Create Indexer* (Crear indexador) que acaba de crear (una solicitud POST con una definición de indexador en el cuerpo de la solicitud) para expresar el indexador en Azure Search. Este paso se refiere a cómo ejecuta el indexador mediante la invocación de la canalización.

1. Ejecute consultas para evaluar los resultados y modifique el código para actualizar la configuración del indexador, el esquema o los conjuntos de aptitudes.

1. [Restablezca el indexador](search-howto-reindex.md) antes de recompilar la canalización.

Para más información sobre preguntas o problemas específicos, consulte las [sugerencias para solucionar problemas](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Pasos siguientes

+ [Documentación de Cognitive Search](cognitive-search-resources-documentation.md)
+ [Quickstart: Try cognitive search in a portal walkthrough](cognitive-search-quickstart-blob.md) (Inicio rápido: pruebe Cognitive Search en un tutorial del portal)
+ [Tutorial: Learn the cognitive search APIs](cognitive-search-tutorial-blob.md) (Tutorial: conozca las API de Cognitive Search)