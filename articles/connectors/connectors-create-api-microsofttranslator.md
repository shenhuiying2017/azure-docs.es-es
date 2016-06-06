<properties
    pageTitle="Incorporación de Microsoft Translator a PowerApps Enterprise o aplicaciones lógicas | Microsoft Azure"
    description="Información general del conector de Microsoft Translator con los parámetros de la API de REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/18/2016"
   ms.author="mandia"/>

# Introducción al conector de Microsoft Translator
Conéctese a Microsoft Translator para traducir el texto, detectar un idioma, etc. El conector de Microsoft Translator pueden usarse desde:

- Aplicaciones lógicas 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/connectors-create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

Con Microsoft Translator, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de Microsoft Translator. 
- Usar acciones para traducir texto, detectar un idioma, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se crea un nuevo archivo en Dropbox, puede convertir el texto del archivo a otro idioma mediante Microsoft Translator.
- Agregar el conector de Microsoft Translator a PowerApps Enterprise. Así, los usuarios pueden utilizar este conector en sus aplicaciones. 

Si desea obtener información sobre cómo agregar un conector a PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Microsoft Translator incluye las siguientes acciones. No hay desencadenadores.

Desencadenadores | Acciones
--- | ---
None | <ul><li>Detectar idioma</li><li>Texto a voz</li><li>Traducir texto</li><li>Obtener idiomas</li><li>Obtener idiomas de voz</li></ul>

Todos los conectores admiten datos en formato JSON y XML.


## Creación de una conexión a Microsoft Translator

>[AZURE.INCLUDE [Pasos para crear una conexión a Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Detectar idioma    
Detecta el idioma de origen de un texto dado. ```GET: /Detect```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto cuyo idioma se identificará|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Texto a voz    
Convierte un texto dado en voz en forma de secuencia de audio en formato de onda. ```GET: /Speak```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que desea convertir|
|language|cadena|yes|query|Ninguna |Código de idioma para generar voz (ejemplo: ' es-es')|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Traducir texto    
Traduce texto a un idioma especificado mediante Microsoft Translator. ```GET: /Translate```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto para traducir|
|languageTo|cadena|yes|query| Ninguna|Código de idioma de destino (ejemplo: 'fr')|
|languageFrom|cadena|no|query|Ninguna |Idioma de origen; si no se proporciona, Microsoft Translator intentará detectarlo automáticamente. (Ejemplo: es)|
|categoría|cadena|no|query|general |Categoría de traducción (predeterminada: 'general')|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener idiomas    
Recupera todos los idiomas que admite Microsoft Translator. ```GET: /TranslatableLanguages```

No hay parámetros para esta llamada.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener idiomas de voz    
Recupera los idiomas disponibles para la síntesis de voz. ```GET: /SpeakLanguages```

No hay parámetros para esta llamada.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

## Definiciones de objeto

#### Idioma: modelo de idioma para idiomas de Microsoft Translator traducibles

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|Código|cadena|no|
|Nombre|cadena|no|


## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Vuelta a la [lista de API](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0525_2016-->