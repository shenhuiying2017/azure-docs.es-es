---
title: Referencia de modelo de datos de la plantilla de Azure API Management | Microsoft Docs
description: Aprenda sobre las representaciones de entidad y tipo de elementos comunes que se usan en los modelos de datos en las plantillas de portal para desarrolladores de Azure API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 0f27b6b529c2591e37d48e3386190077fc8efc32
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="azure-api-management-template-data-model-reference"></a>Referencia de modelo de datos de la plantilla de Azure API Management
Este tema describe las representaciones de entidad y tipo de elementos comunes que se usan en los modelos de datos en las plantillas de portal para desarrolladores de Azure API Management.  
  
 Para obtener más información sobre el trabajo con plantillas, consulte [Cómo personalizar el portal para desarrolladores de Azure API Management mediante plantillas](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [API](#API)  
-   [Resumen de API](#APISummary)  
-   [Aplicación](#Application)  
-   [Datos adjuntos](#Attachment)  
-   [Código de ejemplo](#Sample)  
-   [Comment](#Comment)  
-   [Filtrado](#Filtering)  
-   [Encabezado](#Header)  
-   [Solicitud HTTP](#HTTPRequest)  
-   [Respuesta HTTP](#HTTPResponse)  
-   [Problema](#Issue)  
-   [operación](#Operation)  
-   [Menú de operaciones](#Menu)  
-   [Elemento de menú de operaciones](#MenuItem)  
-   [Paginación](#Paging)  
-   [Parámetro](#Parameter)  
-   [Producto](#Product)  
-   [Proveedor](#Provider)  
-   [Representación](#Representation)  
-   [Suscripción](#Subscription)  
-   [Resumen de suscripción](#SubscriptionSummary)  
-   [Información de cuenta de usuario](#UserAccountInfo)  
-   [Inicio de sesión de usuario](#UseSignIn)  
-   [Registro de usuario](#UserSignUp)  
  
##  <a name="API"></a> API  
 La entidad `API` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|id|cadena|Identificador de recursos. Identifica de forma única la API de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `apis/{id}`, donde `{id}` es un identificador de API. Esta propiedad es de solo lectura.|  
|Nombre|cadena|Nombre de la API. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|Description|cadena|Descripción de la API. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
|serviceUrl|cadena|Dirección URL absoluta del servicio back-end que implementa esta API.|  
|path|cadena|Dirección URL relativa que identifica de forma única esta API y todas las rutas de acceso a sus recursos dentro de la instancia del servicio API Management. Se anexa a la dirección URL base del punto de conexión de API que se especificó durante la creación de la instancia de servicio para formar una dirección URL pública para esta API.|  
|protocols|matriz de número|Describe en qué protocolos se pueden invocar las operaciones en esta API. Los valores permitidos son `1 - http` y `2 - https`, o ambos.|  
|authenticationSettings|[Configuración de autenticación del servidor de autorización](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Colección de ajustes de autenticación que se incluyen en esta API.|  
|subscriptionKeyParameterNames|objeto|Propiedad opcional que puede utilizarse para especificar nombres personalizados para los parámetros de la consulta o encabezado que contiene la clave de suscripción. Cuando esta propiedad está presente, debe contener al menos una de las dos propiedades siguientes.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> Resumen de API  
 La entidad `API summary` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|id|cadena|Identificador de recursos. Identifica de forma única la API de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `apis/{id}`, donde `{id}` es un identificador de API. Esta propiedad es de solo lectura.|  
|Nombre|cadena|Nombre de la API. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|Description|cadena|Descripción de la API. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
  
##  <a name="Application"></a> Application  
 La entidad `application` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Id|cadena|Identificador único de la aplicación.|  
|Título|cadena|Título de la aplicación.|  
|DESCRIPCIÓN|cadena|Descripción de la aplicación.|  
|URL|URI|Identificador URI de la aplicación.|  
|Versión|cadena|Información de versión de la aplicación.|  
|Requisitos|cadena|Descripción de los requisitos de la aplicación.|  
|Estado|número|Estado actual de la aplicación.<br /><br /> -0 - registrada<br /><br /> -1 - enviada<br /><br /> -2 - publicada<br /><br /> -3 - rechazada<br /><br /> -4 - no publicada|  
|RegistrationDate|Datetime|Fecha y hora a las que se registró la aplicación.|  
|CategoryId|número|Categoría de la aplicación (finanzas, entretenimiento, etcétera).|  
|DeveloperId|cadena|Identificador único del desarrollador que envió la aplicación.|  
|Datos adjuntos|Colección de entidades [Attachment](#Attachment).|Cualquier tipo de datos adjuntos de la aplicación, como capturas de pantalla o iconos.|  
|Icono|[Datos adjuntos](#Attachment)|Icono de la aplicación.|  
  
##  <a name="Attachment"></a> Datos adjuntos  
 La entidad `attachment` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|UniqueId|cadena|Identificador único de los datos adjuntos.|  
|URL|cadena|Dirección URL del recurso.|  
|type|cadena|Tipo de datos adjuntos.|  
|ContentType|cadena|Tipo de medios de los datos adjuntos.|  
  
##  <a name="Sample"></a> Código de ejemplo  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|título|cadena|Nombre de la operación.|  
|snippet|cadena|Esta propiedad está en desuso y no debe utilizarse.|  
|brush|cadena|Especifica qué plantilla de color de sintaxis de código se usará al mostrar el ejemplo de código. Los valores permitidos son `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby` y `csharp`.|  
|template|cadena|Nombre de esta plantilla de ejemplo de código.|  
|Cuerpo|cadena|Un marcador de posición para la parte del ejemplo de código del fragmento de código.|  
|estático|cadena|Método HTTP de la operación.|  
|scheme|cadena|Protocolo que se debe usar en la solicitud de operación.|  
|path|cadena|Ruta de acceso de la operación.|  
|query|cadena|Ejemplo de cadena de consulta con parámetros definidos.|  
|host|cadena|Dirección URL de la puerta de enlace del servicio API Management para la API que contiene esta operación.|  
|encabezados|Colección de entidades [Header](#Header).|Encabezados de esta operación.|  
|parameters|Colección de entidades [Parameter](#Parameter).|Los parámetros definidos para esta operación.|  
  
##  <a name="Comment"></a> Comment  
 La entidad `API` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Id|número|Identificador del comentario.|  
|CommentText|cadena|Cuerpo del comentario. Puede incluir HTML.|  
|DeveloperCompany|cadena|Nombre de la empresa del desarrollador.|  
|PostedOn|Datetime|Fecha y hora de publicación del comentario.|  
  
##  <a name="Issue"></a> Issue  
 La entidad `issue` tiene las siguientes propiedades.  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Id|cadena|Identificador único del problema.|  
|ApiID|cadena|Identificador de la API sobre la que se notificó este problema.|  
|Título|cadena|Título del problema.|  
|DESCRIPCIÓN|cadena|Descripción del problema.|  
|SubscriptionDeveloperName|cadena|Nombre del desarrollador que notificó el problema.|  
|IssueState|cadena|Estado actual del problema. Los valores posibles son Proposed (Propuesto), Opened (Abierto), Closed (Cerrado).|  
|ReportedOn|Datetime|Fecha y hora a las que se notificó el problema.|  
|Comentarios|Colección de entidades [Comment](#Comment).|Comentarios sobre este problema.|  
|Datos adjuntos|Colección de entidades [Attachment](api-management-template-data-model-reference.md#Attachment).|Datos adjuntos al problema.|  
|Services|Colección de entidades [API](#API).|Las API a las que está suscrito el usuario que archivó el problema.|  
  
##  <a name="Filtering"></a> Filtrado  
 La entidad `filtering` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Patrón|cadena|El término de búsqueda actual; o `null` si no hay ningún término de búsqueda.|  
|Placeholder|cadena|Texto que se muestra en el cuadro de búsqueda cuando no hay ningún término de búsqueda especificado.|  
  
##  <a name="Header"></a> Header  
 Esta sección describe la representación de `parameter`.  
  
|Propiedad|DESCRIPCIÓN|type|  
|--------------|-----------------|----------|  
|Nombre|cadena|Nombre del parámetro.|  
|Descripción|cadena|Descripción del parámetro.|  
|value|cadena|Valor del encabezado.|  
|typeName|cadena|Tipo de datos del valor del encabezado.|  
|options|cadena|Opciones.|  
|requerido|boolean|Especifica si el encabezado es necesario.|  
|readOnly|boolean|Especifica si el encabezado es de solo lectura.|  
  
##  <a name="HTTPRequest"></a> HTTP Request  
 Esta sección describe la representación de `request`.  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Descripción|cadena|Descripción de la solicitud de operación.|  
|Encabezados|matriz de entidades [Header](#Header).|Encabezados de solicitud.|  
|parameters|matriz de [Parameter](#Parameter)|Colección de parámetros de solicitud de operación.|  
|representations|matriz de [Representation](#Representation)|Colección de representaciones de solicitud de operación.|  
  
##  <a name="HTTPResponse"></a> HTTP Response  
 Esta sección describe la representación de `response`.  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|statusCode|número entero positivo|Código de estado de la respuesta de la operación.|  
|Descripción|cadena|Descripción de la respuesta de la operación.|  
|representations|matriz de [Representation](#Representation)|Colección de representaciones de respuesta de operación.|  
  
##  <a name="Operation"></a> Operation  
 La entidad `operation` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|id|cadena|Identificador de recursos. Identifica de forma única la operación de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `apis/{aid}/operations/{id}`, donde `{aid}` es un identificador de API y `{id}` es un identificador de operación. Esta propiedad es de solo lectura.|  
|Nombre|cadena|Nombre de la operación. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|Description|cadena|Descripción de la operación. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
|scheme|cadena|Describe en qué protocolos se pueden invocar las operaciones en esta API. Los valores permitidos son `http`, `https` o tanto `http` como `https`.|  
|uriTemplate|cadena|Plantilla de dirección URL relativa identifica el recurso de destino para esta operación. Puede incluir parámetros. Ejemplo: `customers/{cid}/orders/{oid}/?date={date}`|  
|host|cadena|Dirección URL de la puerta de enlace de API Management que hospeda la API.|  
|httpMethod|cadena|Método HTTP de la operación.|  
|request|[Solicitud HTTP](#HTTPRequest)|Una entidad que contiene los detalles de la solicitud.|  
|responses|matriz de [HTTP Response](#HTTPResponse)|Matriz de entidades [HTTP Response](#HTTPResponse) de la operación.|  
  
##  <a name="Menu"></a> Menú de operaciones  
 La entidad `operation menu` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|ApiId|cadena|Identificador de la API actual.|  
|CurrentOperationId|cadena|Identificador de la operación actual.|  
|.|cadena|Tipo de menú.|  
|MenuItems|Colección de entidades [Operation menu item](#MenuItem).|Operaciones de la API actual.|  
  
##  <a name="MenuItem"></a> Elemento de menú de operaciones  
 La entidad `operation menu item` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Id|cadena|Identificador de la operación.|  
|Título|cadena|Descripción de la operación.|  
|HttpMethod|cadena|Método HTTP de la operación.|  
  
##  <a name="Paging"></a> Paginación  
 La entidad `paging` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Page|número|Número de página actual.|  
|PageSize|número|Número máximo de resultados que se mostrarán en una misma página.|  
|TotalItemCount|número|Número de elementos que se muestran.|  
|ShowAll|boolean|Especifica si se deben mostrar todos los resultados en una misma página.|  
|PageCount|número|Número de páginas de resultados.|  
  
##  <a name="Parameter"></a> Parameter  
 Esta sección describe la representación de `parameter`.  
  
|Propiedad|DESCRIPCIÓN|type|  
|--------------|-----------------|----------|  
|Nombre|cadena|Nombre del parámetro.|  
|Descripción|cadena|Descripción del parámetro.|  
|value|cadena|Valor del parámetro.|  
|options|matriz de cadena|Valores definidos para los valores de parámetro de consulta.|  
|requerido|boolean|Especifica si el parámetro es necesario o no.|  
|kind|número|Determina si este parámetro es un parámetro de ruta de acceso (1) o un parámetro de cadena de consulta (2).|  
|typeName|cadena|Tipo de parámetro.|  
  
##  <a name="Product"></a> Product  
 La entidad `product` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Id|cadena|Identificador de recursos. Identifica de forma única el producto en la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `products/{pid}`, donde `{pid}` es un identificador de producto. Esta propiedad es de solo lectura.|  
|Título|cadena|Nombre del producto. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|DESCRIPCIÓN|cadena|Descripción del producto. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
|Términos|cadena|Términos de uso del producto. Los desarrolladores que traten de suscribirse al producto verán y deberán aceptar estos términos para poder completar el proceso de suscripción.|  
|ProductState|número|Especifica si el producto se ha publicado o no. Los desarrolladores pueden consultar los productos publicados en el portal para desarrolladores. Solo los administradores pueden ver los productos que no se han publicado.<br /><br /> Los valores de estado del producto permitidos son:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|boolean|Especifica si un usuario puede tener varias suscripciones a este producto al mismo tiempo.|  
|MultipleSubscriptionsCount|número|Número máximo de suscripciones a este producto que un usuario puede tener al mismo tiempo.|  
  
##  <a name="Provider"></a> Provider  
 La entidad `provider` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Properties (Propiedades)|diccionario de cadenas|Propiedades de este proveedor de autenticación.|  
|AuthenticationType|cadena|Tipo de proveedor. (Azure Active Directory, inicio de sesión de Facebook, cuenta de Google, cuenta de Microsoft, Twitter).|  
|Caption|cadena|Nombre para mostrar del proveedor.|  
  
##  <a name="Representation"></a> Representación  
 En esta sección se describe `representation`.  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|contentType|cadena|Especifica un tipo de contenido personalizado o registrado para esta representación, por ejemplo, `application/xml`.|  
|de Pi|cadena|Un ejemplo de la representación.|  
  
##  <a name="Subscription"></a> Subscription  
 La entidad `subscription` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Id|cadena|Identificador de recursos. Identifica de forma única la suscripción de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `subscriptions/{sid}`, donde `{sid}` es un identificador de suscripción. Esta propiedad es de solo lectura.|  
|ProductId|cadena|El identificador de recurso de producto del producto suscrito. El valor es una dirección URL relativa válida con el formato `products/{pid}`, donde `{pid}` es un identificador de producto.|  
|ProductTitle|cadena|Nombre del producto. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|ProductDescription|cadena|Descripción del producto. No debe estar vacía. Puede incluir etiquetas de formato HTML. La longitud máxima es de 1000 caracteres.|  
|ProductDetailsUrl|cadena|Dirección URL relativa a los detalles del producto.|  
|state|cadena|Estado de la suscripción. Los estados posibles son:<br /><br /> - `0 - suspended`: la suscripción está bloqueada y el suscriptor no puede llamar a ninguna API del producto.<br /><br /> - `1 - active`: la suscripción está activa.<br /><br /> - `2 - expired`: la suscripción ha alcanzado su fecha de expiración y se ha desactivado.<br /><br /> - `3 - submitted`: el desarrollador ha realizado una solicitud de suscripción, pero esta aún no se ha aprobado ni rechazado.<br /><br /> - `4 - rejected`: un administrador ha rechazado la solicitud de suscripción.<br /><br /> - `5 - cancelled`: el desarrollador o el administrador han cancelado la suscripción.|  
|DisplayName|cadena|Nombre para mostrar de la suscripción.|  
|CreatedDate|dateTime|Fecha en la que se creó la suscripción, en formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|boolean|Especifica si el usuario actual puede cancelar la suscripción.|  
|IsAwaitingApproval|boolean|Especifica si la suscripción está en espera de aprobación.|  
|StartDate|dateTime|Fecha de inicio de la suscripción, en formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|dateTime|Fecha de expiración de la suscripción, en formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|NotificationDate|dateTime|Fecha de notificación de la suscripción, en formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|primaryKey|cadena|Clave de suscripción principal. La longitud máxima es de 256 caracteres.|  
|secondaryKey|cadena|Clave de suscripción secundaria. La longitud máxima es de 256 caracteres.|  
|CanBeRenewed|boolean|Especifica si el usuario actual puede renovar la suscripción.|  
|HasExpired|boolean|Especifica si la suscripción ha expirado.|  
|IsRejected|boolean|Especifica si la solicitud de suscripción ha sido denegada.|  
|CancelUrl|cadena|La dirección URL relativa para cancelar la suscripción.|  
|RenewUrl|cadena|La dirección URL relativa para renovar la suscripción.|  
  
##  <a name="SubscriptionSummary"></a> Resumen de suscripción  
 La entidad `subscription summary` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Id|cadena|Identificador de recursos. Identifica de forma única la suscripción de la instancia actual del servicio API Management. El valor es una dirección URL relativa válida con el formato `subscriptions/{sid}`, donde `{sid}` es un identificador de suscripción. Esta propiedad es de solo lectura.|  
|DisplayName|cadena|Nombre para mostrar de la suscripción.|  
  
##  <a name="UserAccountInfo"></a> Información de cuenta de usuario  
 La entidad `user account info` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Nombre|cadena|Nombre. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|Apellidos|cadena|Apellidos. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|Email|cadena|Dirección de correo electrónico. No debe estar vacía y debe ser única dentro de la instancia de servicio. La longitud máxima es de 254 caracteres.|  
|Password|cadena|Contraseña de la cuenta de usuario.|  
|NameIdentifier|cadena|Identificador de cuenta, es igual al correo electrónico del usuario.|  
|ProviderName|cadena|Nombre del proveedor de autenticación.|  
|IsBasicAccount|boolean|Es True si esta cuenta se registró mediante un correo electrónico y una contraseña; False si la cuenta se registró con un proveedor.|  
  
##  <a name="UseSignIn"></a> Inicio de sesión de usuario  
 La entidad `user sign in` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|Email|cadena|Dirección de correo electrónico. No debe estar vacía y debe ser única dentro de la instancia de servicio. La longitud máxima es de 254 caracteres.|  
|Password|cadena|Contraseña de la cuenta de usuario.|  
|ReturnUrl|cadena|Dirección URL de la página donde el usuario hizo clic en el vínculo de inicio de sesión.|  
|RememberMe|boolean|Especifica si se desea guardar la información del usuario actual.|  
|RegistrationEnabled|boolean|Especifica si el registro se ha habilitado.|  
|DelegationEnabled|boolean|Especifica si está habilitado el inicio de sesión delegado.|  
|DelegationUrl|cadena|Dirección URL de inicio de sesión delegado, si está habilitado.|  
|SsoSignUpUrl|cadena|Inicio de sesión único en la dirección URL del usuario, si está presente.|  
|AuxServiceUrl|cadena|Si el usuario actual es un administrador, esto es un vínculo a la instancia de servicio en Azure Portal.|  
|Proveedores|Colección de entidades [Provider](#Provider).|Los proveedores de autenticación de este usuario.|  
|UserRegistrationTerms|cadena|Condiciones que un usuario debe aceptar para poder iniciar sesión.|  
|UserRegistrationTermsEnabled|boolean|Especifica si las condiciones están habilitadas.|  
  
##  <a name="UserSignUp"></a> Suscripción de usuario  
 La entidad `user sign up` tiene las siguientes propiedades:  
  
|Propiedad|type|DESCRIPCIÓN|  
|--------------|----------|-----------------|  
|PasswordConfirm|boolean|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|Password|cadena|Contraseña de la cuenta de usuario.|  
|PasswordVerdictLevel|número|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|UserRegistrationTerms|cadena|Condiciones que un usuario debe aceptar para poder iniciar sesión.|  
|UserRegistrationTermsOptions|número|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|ConsentAccepted|boolean|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|Email|cadena|Dirección de correo electrónico. No debe estar vacía y debe ser única dentro de la instancia de servicio. La longitud máxima es de 254 caracteres.|  
|Nombre|cadena|Nombre. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|Apellidos|cadena|Apellidos. No debe estar vacía. La longitud máxima es de 100 caracteres.|  
|UserData|cadena|Valor utilizado por el control de [sign-up](api-management-page-controls.md#sign-up).|  
|NameIdentifier|cadena|Valor utilizado por el control de inicio de sesión [sign-up](api-management-page-controls.md#sign-up).|  
|ProviderName|cadena|Nombre del proveedor de autenticación.|

## <a name="next-steps"></a>pasos siguientes
Para obtener más información sobre el trabajo con plantillas, consulte [Cómo personalizar el portal para desarrolladores de Azure API Management mediante plantillas](api-management-developer-portal-templates.md).
