---
title: Aptitud de búsqueda cognitiva División de texto (Azure Search) | Microsoft Docs
description: Divida texto en fragmentos o páginas de texto en función de la longitud de una canalización de enriquecimiento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: dbb9261cfce0a8437cfe76121fa16aa87c4b3393
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786804"
---
#   <a name="text-split-cognitive-skill"></a>Aptitud cognitiva División de texto

La aptitud **División de texto** divide el texto en fragmentos de texto. Puede especificar si desea dividir el texto en oraciones o en páginas de una longitud determinada. Esta aptitud es especialmente útil si hay requisitos de longitud de texto máxima en otras aptitudes de bajada. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro     | DESCRIPCIÓN |
|--------------------|-------------|
| textSplitMode      | "pages" o "sentences" | 
| maximumPageLength | Si textSplitMode se establece en "pages", se refiere a la longitud de página máxima medida por `String.Length`. El valor mínimo es 100. | 
| defaultLanguageCode   | (Opcional) Uno de los siguientes códigos de idioma: `da, de, en, es, fi, fr, it, ko, pt`. El valor predeterminado es inglés (en). Aspectos que se deben tener en cuenta:<ul><li>Si se pasa un formato “código de idioma-código de país”, solo se usa la parte “código de idioma” del formato.</li><li>Si el idioma no está en la lista anterior, la aptitud de división divide el texto en límites de carácter.</li><li>Proporcionar un código de idioma es útil para evitar cortar una palabra por la mitad para idiomas sin espacios como el chino, japonés y coreano.</li></ul>  |


## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de parámetro       | DESCRIPCIÓN      |
|----------------------|------------------|
| text  | Texto que se dividirá en subcadenas. |
| languageCode  | (Opcional) Código de idioma para el documento.  |

## <a name="skill-outputs"></a>Salidas de la aptitud 

| Nombre de parámetro     | DESCRIPCIÓN |
|--------------------|-------------|
| textItems | Una matriz de subcadenas que se han extraído. |


##  <a name="sample-definition"></a>Definición de ejemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Entrada de ejemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Salida de ejemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Casos de error
Si no se admite un idioma, se genera una advertencia y el texto se divide en límites de carácter.

## <a name="see-also"></a>Otras referencias

+ [Aptitudes predefinidas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
