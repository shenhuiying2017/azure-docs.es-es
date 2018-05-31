---
title: Aptitud de búsqueda cognitiva Opinión (Azure Search) | Microsoft Docs
description: Extraiga la opinión del texto en una canalización de enriquecimiento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ddbba5b881cd05a997cd24a9396d5b722376e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786764"
---
#   <a name="sentiment-cognitive-skill"></a>Aptitud cognitiva Opinión

La aptitud **Opinión** evalúa el texto no estructurado a lo largo de una continuidad positiva-negativa y, para cada registro, devuelve un valor numérico entre 0 y 1. Las puntuaciones próximas a 1 indican una opinión positiva y las puntuaciones próximas a 0 indican una opinión negativa.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe tener 5000 caracteres, medido por `String.Length`. Si tiene que dividir los datos antes de enviarlos al analizador de opiniones, use la [aptitud División de texto](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro |                      |
|----------------|----------------------|
| defaultLanguageCode | (Opcional) Es el código de idioma que se aplicará a los documentos que no especifiquen el lenguaje de forma explícita. <br/> Vea [Full list of supported languages](../cognitive-services/text-analytics/text-analytics-supported-languages.md) (Lista completa de idiomas admitidos). |

## <a name="skill-inputs"></a>Entradas de la aptitud 

| Nombre de entrada | DESCRIPCIÓN |
|--------------------|-------------|
| text | Texto que se va a analizar.|
| languageCode  |  (Opcional) Cadena que indica el idioma de los registros. Si esta no se especifica este parámetro, se usa el valor predeterminado "en". <br/>Vea [Full list of supported languages](../cognitive-services/text-analytics/text-analytics-supported-languages.md) (Lista completa de idiomas admitidos).|

## <a name="skill-outputs"></a>Salidas de la aptitud

| Nombre de salida | DESCRIPCIÓN |
|--------------------|-------------|
| de la aplicación | Un valor entre 0 y 1 que representa la opinión del texto analizado. Valores próximos a 0 tienen opiniones negativo, los valores próximos a 0,5 tienen opiniones neutras y los valores próximos a 1 tienen opiniones positivas.|


##  <a name="sample-definition"></a>Definición de ejemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Notas
Si no hay nada, no se devuelve una puntuación de la opinión para dichos registros.

## <a name="error-cases"></a>Casos de error
Si no se admite un idioma, se genera un error y no se devuelve ninguna puntuación de opinión.

## <a name="see-also"></a>Otras referencias

+ [Aptitudes predefinidas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)