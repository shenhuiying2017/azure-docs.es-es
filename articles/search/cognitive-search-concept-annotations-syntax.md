---
title: Hacer referencia a una anotación de entradas y salidas en una canalización de Cognitive Search en Azure Search| Microsoft Docs
description: Aquí se explica la sintaxis de la anotación y cómo hacer referencia a una anotación en las entradas y salidas de un conjunto de aptitudes en una canalización de Cognitive Search en Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 2e838e9c94d5b19565bea3d02890fe6164bb37d0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786794"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Cómo hacer referencia a las anotaciones en un conjunto de aptitudes de Cognitive Search

En este artículo aprenderá a hacer referencia a las anotaciones de las definiciones de aptitudes; para ello, usaremos ejemplos para ilustrar varios escenarios. Como el contenido de un documento fluye a través de un conjunto de aptitudes, este se enriquece con anotaciones. Las anotaciones se pueden utilizar como entradas para conseguir un mayor enriquecimiento de niveles inferiores, o se pueden asignar a un campo de salida en un índice. 
 
Los ejemplos de este artículo se basan en el campo de *contenido* que crearon automáticamente los [indexadores de Azure Blob](search-howto-indexing-azure-blob-storage.md) como parte de la fase de descifrado de documentos. Cuando consulte los documentos de un contenedor de blobs, use un formato como `"/document/content"`, donde el campo de *contenido* forma parte del *documento*. 

## <a name="background-concepts"></a>Conceptos de fondo

Antes de revisar la sintaxis, repasemos algunos conceptos importantes para comprender mejor los ejemplos que se proporcionan más adelante en este artículo.

| Término | DESCRIPCIÓN |
|------|-------------|
| Documento enriquecido | Un documento enriquecido es una estructura interna que la canalización crea y usa para guardar todas las anotaciones relacionadas con un documento. Imagine un documento enriquecido como un árbol de anotaciones. En general, una anotación creada a partir de una anotación anterior se convierte en una anotación secundaria.<p/>Los documentos enriquecidos solo existen durante la ejecución del conjunto de aptitudes. Una vez que el contenido se asigna al índice de búsqueda, ya no se necesita el documento enriquecido. Aunque no interactúa directamente con documentos enriquecidos, resulta útil tener un modelo mental de los documentos al crear un conjunto de aptitudes. |
| Contexto de enriquecimiento | Es el contexto en el que tiene lugar el enriquecimiento, teniendo en cuenta qué elemento se va a enriquecer. De forma predeterminada, el contexto de enriquecimiento se establece en el nivel `"/document"`, dentro del alcance de los documentos individuales. Cuando se ejecuta una aptitud, los resultados de la misma se convierten en [propiedades del contexto definido](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Ejemplo 1: referencia de anotación simple

En Azure Blob Storage, supongamos que tiene ciertos archivos que contienen referencias a nombres de personas que quiere extraer mediante el reconocimiento de entidades con nombre. En la definición de aptitudes que tiene a continuación, `"/document/content"` es la representación textual de todo el documento, y "personas" es una extracción de nombres completos de las entidades identificadas como personas.

Debido a que el contexto predeterminado es `"/document"`, ahora se puede hacer referencia a la lista de personas como `"/document/people"`. En este caso específico, `"/document/people"` es una anotación que puede asignarse a un campo en un índice, o usarse en otra aptitud en el mismo conjunto de aptitudes.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Ejemplo 2: hacer referencia a una matriz dentro de un documento

En este ejemplo, que se basa en el anterior, le mostramos cómo invocar varias veces un paso de enriquecimiento en el mismo documento. Suponga que en el ejemplo anterior generó una matriz de cadenas con los nombres de 10 personas de un solo documento. El próximo paso lógico es crear un segundo enriquecimiento que extraiga el apellido de un nombre completo. Como hay 10 nombres, le interesa realizar este paso 10 veces en el documento; esto es, una vez por cada persona. 

Para invocar el número correcto de iteraciones, establezca el contexto como `"/document/people/*"`, donde el asterisco (`"*"`) representa todos los nodos del documento enriquecido como descendientes de `"/document/people"`. Aunque esta aptitud solo se define una vez en el conjunto de aptitudes, se llama a cada miembro del documento hasta que se procesen todos ellos.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Cuando las anotaciones son matrices o colecciones de cadenas, tal vez quiera trabajar con miembros específicos en lugar de con la matriz completa. El ejemplo anterior se genera una anotación llamada `"last"` en cada nodo que representa el contexto. Si quiere hacer referencia a esta familia de anotaciones, puede usar la sintaxis `"/document/people/*/last"`. Si por el contrario quiere hacer referencia a una anotación en particular, puede usar un índice explícito "`"/document/people/1/last`" para hacer referencia al apellido de la primera persona identificada en el documento. Tenga en cuenta que en esta sintaxis las matrices están "indexadas en 1".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Ejemplo 3: miembros de referencia de una matriz

A veces es necesario agrupar todas las anotaciones de un tipo en particular para pasarlas a una aptitud en particular. Pongamos como ejemplo una habilidad hipotética personalizada que identifica el apellido más común de todos los apellidos extraídos en el Ejemplo 2. Para proporcionar solo los apellidos a esa habilidad personalizada, debe especificar el contexto como `"/document"` y la entrada como `"/document/people/*/lastname"`.

Tenga en cuenta que la cardinalidad de `"/document/people/*/lastname"` es mayor que la del documento. Hay 10 nodos de apellido, mientras que solo hay un nodo de documento para este documento. En ese caso, el sistema creará automáticamente una matriz de `"/document/people/*/lastname"` que contenga todos los elementos del documento.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Otras referencias
+ [Cómo integrar una aptitud personalizada en una canalización de enriquecimiento](cognitive-search-custom-skill-interface.md)
+ [Cómo definir un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Crear un conjunto de aptitudes (REST)](ref-create-skillset.md)
+ [Asignar campos enriquecidos a un índice](cognitive-search-output-field-mapping.md)
