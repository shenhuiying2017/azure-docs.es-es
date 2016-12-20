---
title: "Incorporación del conector de Twilio a Logic Apps | Microsoft Docs"
description: "Información general del conector de Twilio con parámetros de la API de REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0999d96b4819d6bf03faa843750c5c4b19649774


---
# <a name="get-started-with-the-twilio-connector"></a>Introducción al conector de Twilio
Conectarse a Twilio para enviar y recibir mensajes SMS, MMS y IP globales.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Con Twilio, puede:

* Compilar el flujo de negocio en función de los datos que obtiene de Twilio. 
* Usar acciones que obtienen un mensaje, enumeran mensajes y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando reciba un nuevo mensaje de Twilio, puede usarlo en un flujo de trabajo de Service Bus. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector de Twilio incluye las siguientes acciones. No hay desencadenadores. 

| Desencadenadores | Acciones |
| --- | --- |
| None |<ul><li>Obtener mensaje</li><li>Enumerar mensajes</li><li>Enviar mensaje</li></ul> |

Todos los conectores admiten datos en formato JSON y XML. 

## <a name="create-a-connection-to-twilio"></a>Creación de una conexión a Twilio
Cuando agregue este conector a las aplicaciones lógicas, escriba los siguientes valores de Twilio:

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| Id. de cuenta |Sí |Escriba el identificador de cuenta de Twilio |
| Token de acceso |Sí |Escriba el token de acceso de Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Si no tiene ninguno, consulte [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) para crearlo.

> [!TIP]
> Puede usar esta misma conexión de Twilio en otras aplicaciones lógicas.
> 
> 

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de Swagger
#### <a name="this-documentation-is-for-version-10"></a>Esta documentación corresponde a la versión: 1.0
### <a name="get-message"></a>Obtener mensaje
Devuelve un único mensaje especificado por el identificador de mensaje proporcionado.  
```GET: /Messages/{MessageId}.json```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| MessageId |cadena |Sí |path |Ninguna |Id. de mensaje |

### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 404 |Mensaje no encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

### <a name="list-messages"></a>Enumerar mensajes
Devuelve una lista de mensajes asociados a su cuenta.  
```GET: /Messages.json```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| Para |cadena |no |query |None |Al número de teléfono |
| De |cadena |no |query |None |Desde número de teléfono |
| DateSent |cadena |no |query |None |Mostrar solo los mensajes enviados en esa fecha (en formato GMT), como AAAA-MM-DD. Ejemplo: DateSent=2009-07-06. También puede especificar la desigualdad, como DateSent <=AAAA-MM-DD para los mensajes enviados en o antes de medianoche en una fecha y DateSent>=AAAA-MM-DD para los mensajes enviados en o después de medianoche en una fecha. |
| PageSize |integer |no |query |50 |La cantidad de recursos para devolver en cada página de lista. Valor predeterminado: 50 |
| Page |integer |no |query |0 |Número de página. El valor predeterminado es 0. |

### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

### <a name="send-message"></a>Enviar mensaje
Envíe un nuevo mensaje a un número de teléfono móvil.  
```POST: /Messages.json```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |Sí |body |None |Mensaje para enviar |

### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest: modelo de solicitud para la operación de envío de mensajes
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| De |cadena |Sí |
| Para |cadena |Sí |
| body |cadena |yes |
| media_url |array |no |
| status_callback |string |no |
| messaging_service_sid |string |no |
| application_sid |string |no |
| max_price |string |no |

#### <a name="message-model-for-message"></a>Message: modelo de mensaje
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| body |cadena |no |
| De |cadena |no |
| Para |cadena |no |
| status |cadena |no |
| sid |cadena |no |
| account_sid |string |no |
| api_version |string |no |
| num_segments |string |no |
| num_media |string |no |
| date_created |string |no |
| date_sent |string |no |
| date_updated |string |no |
| dirección |cadena |no |
| error_code |string |no |
| error_message |string |no |
| price |cadena |no |
| price_unit |string |no |
| uri |cadena |no |
| subresource_uris |array |no |
| messaging_service_sid |string |no |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList: modelo de respuesta para la operación de enumeración de mensajes
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| messages |array |no |
| Page |integer |no |
| page_size |integer |no |
| num_pages |integer |no |
| uri |cadena |no |
| first_page_uri |string |no |
| next_page_uri |string |no |
| total |integer |no |
| previous_page_uri |string |no |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList: modelo de respuesta para la operación de enumeración de mensajes
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| incoming_phone_numbers |array |no |
| Page |integer |no |
| page_size |integer |no |
| num_pages |integer |no |
| uri |cadena |no |
| first_page_uri |string |no |
| next_page_uri |string |no |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest: modelo de solicitud para la operación de agregar un número entrante
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| PhoneNumber |cadena |Sí |
| AreaCode |cadena |no |
| FriendlyName |cadena |no |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber: número de teléfono entrante
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| sid |cadena |no |
| account_sid |string |no |
| date_created |string |no |
| date_updated |string |no |
| capabilities |not defined |no |
| status_callback |string |no |
| status_callback_method |string |no |
| api_version |string |no |

#### <a name="capabilities-phone-number-capabilities"></a>Capabilities: funcionalidades de número de teléfono
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| mms |boolean |no |
| sms |boolean |no |
| voice |boolean |no |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers: números de teléfono disponibles
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| lata |cadena |no |
| latitude |cadena |no |
| longitude |cadena |no |
| postal_code |string |no |
| rate_center |string |no |
| region |cadena |no |
| mms |boolean |no |
| sms |boolean |no |
| voice |boolean |no |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords: clase de registros de uso
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| categoría |cadena |no |
| usage |cadena |no |
| usage_unit |string |no |
| Descripción |cadena |no |
| price |número |no |
| price_unit |string |no |
| count |cadena |no |
| count_unit |string |no |
| start_date |string |no |
| end_date |string |no |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


