---
title: "Incorporación del conector de usuarios de Office 365 a sus Logic Apps | Microsoft Docs"
description: "Información general del conector de Usuarios de Office 365 con parámetros de la API de REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2e4af1c9369a97d518d027dc4679b9f01ca53d4c


---
# <a name="get-started-with-the-office-365-users-connector"></a>Introducción al conector de Usuarios de Office 365
Conéctese a Usuarios de Office 365 para obtener perfiles, buscar usuarios, etc. 

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Con Usuarios de Office 365, puede:

* Compilar el flujo de su negocio en función de los datos que obtiene de Usuarios de Office 365. 
* Usar acciones que obtienen informes directos, obtienen el perfil de usuario de un administrador, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, obtenga informes directos de la persona y, luego, tome esta información y actualice una base de datos de SQL Azure. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector de Usuarios de Office 365 tiene las siguientes acciones disponibles: No hay desencadenadores.

| Desencadenadores | Acciones |
| --- | --- |
| None |<ul><li>Obtener administrador</li><li>Obtener mi perfil</li><li>Obtener informes directos</li><li>Obtener perfil de usuario</li><li>Buscar usuarios</li></ul> |

Todos los conectores admiten datos en formato JSON y XML. 

## <a name="create-a-connection-to-office-365-users"></a>Creación de una conexión a Usuarios de Office 365
Al agregar este conector a las aplicaciones lógicas, debe iniciar sesión en su cuenta de Usuarios de Office 365 y permitir que estas se conecten a su cuenta.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Después de crear la conexión, especifique las propiedades de Usuarios de Office 365, como el identificador de usuario. En la **referencia de la API de REST** de este tema se describen estas propiedades.

> [!TIP]
> Esta misma conexión de Usuarios de Office 365 se puede usar en otras aplicaciones lógicas.
> 
> 

## <a name="office-365-users-rest-api-reference"></a>Referencia de API de REST de Usuarios de Office 365
Se aplica a la versión: 1.0.

### <a name="get-my-profile"></a>Obtener mi perfil
Recupera el perfil del usuario actual.  
```GET: /users/me``` 

No hay parámetros para esta llamada.

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 202 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="get-user-profile"></a>Obtener perfil de usuario
Recupera un perfil de usuario específico.  
```GET: /users/{userId}``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| userId |cadena |Sí |path |None |Nombre principal o identificador de correo electrónico del usuario |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 202 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="get-manager"></a>Obtener administrador
Recupera el perfil de usuario del administrador del usuario especificado.  
```GET: /users/{userId}/manager``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| userId |cadena |Sí |path |None |Nombre principal o identificador de correo electrónico del usuario |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 202 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="get-direct-reports"></a>Obtener informes directos
Obtiene informes directos.  
```GET: /users/{userId}/directReports``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| userId |cadena |Sí |path |None |Nombre principal o identificador de correo electrónico del usuario |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 202 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="search-for-users"></a>Buscar usuarios
Recupera los resultados de búsqueda de los perfiles de usuario.  
```GET: /users``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| searchTerm |cadena |no |query |None |Cadena de búsqueda (se aplica a: nombre para mostrar, nombre dado, apellidos, correo, sobrenombre de correo y nombre principal del usuario) |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 202 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 403 |Prohibido |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
#### <a name="user-user-model-class"></a>Usuario: clase de modelo de usuario.
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| DisplayName |cadena |no |
| GivenName |cadena |no |
| Surname |cadena |no |
| Mail |cadena |no |
| MailNickname |cadena |no |
| TelephoneNumber |cadena |no |
| AccountEnabled |boolean |no |
| Id |cadena |yes |
| UserPrincipalName |cadena |no |
| Departamento |cadena |no |
| JobTitle |cadena |no |
| mobilePhone |cadena |no |

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

Volver a la [lista de API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG



<!--HONumber=Jan17_HO3-->


