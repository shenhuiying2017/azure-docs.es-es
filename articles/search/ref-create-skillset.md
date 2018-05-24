---
title: Crear un conjunto de aptitudes (versión-api-REST=2017-11-11-Versión preliminar) - Azure Search | Microsoft Docs
description: Un conjunto de aptitudes es una colección de conocimientos cognitivos que forman una canalización de enriquecimiento.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786914"
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>Crear un conjunto de aptitudes (versión-api=2017-11-11-Versión preliminar)

**Se aplica a:** versión-api-2017-11-11-Versión preliminar

Un conjunto de aptitudes es una colección de conocimientos cognitivos que se usan para el procesamiento de lenguaje natural y otros tipos de transformaciones. Las aptitudes incluyen la extracción de entidades con nombre, la extracción de frases clave, la fragmentación de texto en páginas lógicas, entre otros.

Para usar el conjunto de aptitudes, haga referencia a él en un indizador de Azure Search y ejecute el indizador para importar datos, invocar transformaciones y enriquecimiento y asignar los campos de salida a un índice. Un conjunto de aptitudes es recurso de alto nivel, pero solo está operativo en un procesamiento de indizador. Como recurso de alto nivel, puede diseñar un conjunto de aptitudes una vez y luego hacer referencia a él en varios indizadores. 

Los conjuntos de aptitudes se expresan en Azure Search con una solicitud HTTP PUT o POST. En el caso de PUT, el cuerpo de la solicitud es un esquema JSON que especifica las aptitudes que se invocan. Las aptitudes están conectadas entre sí mediante asociaciones de entrada y salida, donde la salida de una transformación se convierte en la entrada de otra.

Un conjunto de aptitudes deben tener al menos una aptitud. No hay ningún límite teórico para el número máximo de aptitudes, pero lo habitual es una configuración de entre tres y cinco aptitudes.  


> [!NOTE]
> Cognitive Search se encuentra en versión preliminar pública y la ejecución del conjunto de aptitudes se ofrece de forma gratuita. Más adelante, se anunciarán los precios de esta funcionalidad.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>Solicitud  
 HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Crear conjunto de aptitudes** se puede crear mediante un método PUT, con el nombre del conjunto de aptitudes como parte de la dirección URL. Si el conjunto de aptitudes no existe, se crea. Si ya existe, se actualiza a la nueva definición. Tenga en cuenta que solo se puede colocar (PUT) un conjunto de aptitudes a la vez.  

 El nombre del conjunto de aptitudes debe cumplir los siguientes requisitos:

- Debe estar en minúsculas
- Debe empezar y terminar por una letra o un número
- No debe tener barras ni puntos
- Debe tener menos de 128 caracteres 

Después de iniciar el nombre del conjunto de aptitudes por una letra o un número, el resto del nombre puede incluir cualquier letra, número y guiones, siempre que los guiones no aparezcan de manera consecutiva.  

El parámetro **api-version** es obligatorio. La única versión disponible es `2017-11-11-Preview`. Vea [Versiones de API en Azure Search](https://go.microsoft.com/fwlink/?linkid=834796) para obtener una lista de todas las versiones. 


### <a name="request-headers"></a>Encabezados de solicitud  

 En la siguiente tabla se describen los encabezados de solicitud obligatorios y opcionales.  

|Encabezado de solicitud|DESCRIPCIÓN|  
|--------------------|-----------------|  
|*Content-Type:*|Necesario. Establézcalo en `application/json`|  
|*api-key:*|Necesario. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Crear conjunto de aptitudes** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).|  

También necesita el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio de Azure Portal. Consulte [Crear un servicio Azure Search en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.  

### <a name="request-body-syntax"></a>Sintaxis del cuerpo de la solicitud  

El cuerpo de la solicitud contiene la definición del conjunto de aptitudes, que consta de una o varias aptitudes totalmente especificadas, así como parámetros de nombre y de descripción opcionales.  

La sintaxis para estructurar la carga de la solicitud es la siguiente. Más adelante en este artículo, así como en [How to define a skillset](cognitive-search-defining-skillset.md) (Cómo definir un conjunto de aptitudes), se proporciona una solicitud de ejemplo.  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>Ejemplo de solicitud
 En el ejemplo siguiente se crea un conjunto de aptitudes que se usa para enriquecer una colección de documentos financieros.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

El cuerpo de solicitud es un documento JSON. Este conjunto de aptitudes en concreto usa dos aptitudes de forma asincrónica, independientemente del procesamiento de la sustancia de los `contents` como dos transformaciones diferentes. Como alternativa, puede dirigir la salida de una transformación para que sea la entrada de otra. Para más información, vea [How to define a skillset](cognitive-search-defining-skillset.md) (Cómo definir un conjunto de aptitudes).

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>Response  

 Para una solicitud correcta, debería ver el código de estado "201 Created".  

 De forma predeterminada, el cuerpo de la respuesta contendrá el JSON de la definición del conjunto de aptitudes que se creó, pero si se establece el encabezado de la solicitud Prefer en return=minimal, el cuerpo de respuesta quedará vacío y el código de estado correcto será "204 Sin contenido", en lugar de "201 Creado". Esto es cierto independientemente de si se usa PUT o POST para crear el conjunto de aptitudes.   

## <a name="see-also"></a>Otras referencias

+ [Introducción a la búsqueda cognitiva](cognitive-search-concept-intro.md)
+ [Inicio rápido: probar la búsqueda cognitiva](cognitive-search-quickstart-blob.md)
+ [Tutorial: Indización enriquecida de blobs de Azure](cognitive-search-tutorial-blob.md)
+ [Cómo definir un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Cómo asignar campos](cognitive-search-output-field-mapping.md)
+ [Cómo definir una interfaz personalizada](cognitive-search-custom-skill-interface.md)
+ [Ejemplo: creación de una aptitud personalizada](cognitive-search-create-custom-skill-example.md)
+ [Aptitudes predefinidas](cognitive-search-predefined-skills.md)