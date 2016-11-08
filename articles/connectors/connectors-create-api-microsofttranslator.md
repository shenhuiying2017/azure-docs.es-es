---
title: Incorporación de Microsoft Translator en aplicaciones lógicas | Microsoft Docs
description: Información general del conector de Microsoft Translator con los parámetros de la API de REST
services: ''
suite: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia

---
# Introducción al conector de Microsoft Translator
Conéctese a Microsoft Translator para traducir el texto, detectar un idioma, etc. Con Microsoft Translator, puede:

* Compilar el flujo de negocio en función de los datos que obtiene de Microsoft Translator.
* Usar acciones para traducir texto, detectar un idioma, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se crea un nuevo archivo en Dropbox, puede convertir el texto del archivo a otro idioma mediante Microsoft Translator.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Microsoft Translator incluye las siguientes acciones. No hay desencadenadores.

| Desencadenadores | Acciones |
| --- | --- |
| None |<ul><li>Detectar idioma</li><li>Texto a voz</li><li>Traducir texto</li><li>Obtener idiomas</li><li>Obtener idiomas de voz</li></ul> |

Todos los conectores admiten datos en formato JSON y XML.

## Creación de una conexión a Microsoft Translator
> [!INCLUDE [Pasos para crear una conexión a Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Detectar idioma
Detecta el idioma de origen de un texto concreto. ```GET: /Detect```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |Ninguna |Texto cuyo idioma se identificará |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### Texto a voz
Convierte un texto dado en voz en forma de secuencia de audio en formato de onda. ```GET: /Speak```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |Ninguna |Texto que desea convertir |
| language |string |yes |query |Ninguna |Código de idioma para generar voz (ejemplo: ' es-es') |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### Traducir texto
Traduce texto a un idioma especificado mediante Microsoft Translator. ```GET: /Translate```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |Ninguna |Texto para traducir |
| languageTo |string |yes |query |Ninguna |Código de idioma de destino (ejemplo: 'fr') |
| languageFrom |string |no |query |Ninguna |Idioma de origen; si no se proporciona, Microsoft Translator intentará detectarlo automáticamente. (Ejemplo: es) |
| categoría |string |no |query |general |Categoría de traducción (predeterminada: 'general') |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### Obtener idiomas
Recupera todos los idiomas que admite Microsoft Translator. ```GET: /TranslatableLanguages```

No hay parámetros para esta llamada.

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### Obtener idiomas de voz
Recupera los idiomas disponibles para la síntesis de voz. ```GET: /SpeakLanguages```

No hay parámetros para esta llamada.

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## Definiciones de objeto
#### Idioma: modelo de idioma para idiomas de Microsoft Translator traducibles
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Código |string |no |
| Nombre |string |no |

## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

Volver a la [lista de API](apis-list.md).

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0824_2016-->