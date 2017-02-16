---
title: "Incorporación de Microsoft Translator en Logic Apps | Microsoft Docs"
description: "Información general del conector de Microsoft Translator con los parámetros de la API de REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: add307420b9e16a60b0e57b0ba08188b2509978e


---
# <a name="get-started-with-the-microsoft-translator-connector"></a>Introducción al conector de Microsoft Translator
Conéctese a Microsoft Translator para traducir el texto, detectar un idioma, etc. Con Microsoft Translator, puede: 

* Compilar el flujo de negocio en función de los datos que obtiene de Microsoft Translator. 
* Usar acciones para traducir texto, detectar un idioma, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se crea un nuevo archivo en Dropbox, puede convertir el texto del archivo a otro idioma mediante Microsoft Translator.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Microsoft Translator incluye las siguientes acciones. No hay desencadenadores.

| Desencadenadores | Acciones |
| --- | --- |
| None |<ul><li>Detectar idioma</li><li>Texto a voz</li><li>Traducir texto</li><li>Obtener idiomas</li><li>Obtener idiomas de voz</li></ul> |

Todos los conectores admiten datos en formato JSON y XML.

## <a name="create-a-connection-to-microsoft-translator"></a>Creación de una conexión a Microsoft Translator
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### <a name="detect-language"></a>Detectar idioma
Detecta el idioma de origen de un texto dado.  
```GET: /Detect```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto cuyo idioma se identificará |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="text-to-speech"></a>Texto a voz
Convierte un texto dado en voz en forma de secuencia de audio en formato de onda.  
```GET: /Speak```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto que desea convertir |
| language |cadena |yes |query |Ninguna |Código de idioma para generar voz (ejemplo: ' es-es') |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="translate-text"></a>Traducir texto
Traduce texto a un idioma especificado mediante Microsoft Translator.  
```GET: /Translate```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| query |cadena |yes |query |None |Texto para traducir |
| languageTo |cadena |yes |query |None |Código de idioma de destino (ejemplo: 'fr') |
| languageFrom |cadena |no |query |None |Idioma de origen; si no se proporciona, Microsoft Translator intentará detectarlo automáticamente. (Ejemplo: es) |
| categoría |cadena |no |query |general |Categoría de traducción (predeterminada: 'general') |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-languages"></a>Obtener idiomas
Recupera todos los idiomas que admite Microsoft Translator.  
```GET: /TranslatableLanguages```

No hay parámetros para esta llamada. 

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-speech-languages"></a>Obtener idiomas de voz
Recupera los idiomas disponibles para la síntesis de voz.  
```GET: /SpeakLanguages``` 

No hay parámetros para esta llamada.

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Idioma: modelo de idioma para idiomas de Microsoft Translator traducibles
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Código |cadena |no |
| Nombre |cadena |no |

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

Volver a la [lista de API](apis-list.md).

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png



<!--HONumber=Jan17_HO3-->


