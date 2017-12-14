---
title: "Autenticación entre servicios de Azure AD mediante la especificación provisional On-Behalf-Of de OAuth 2.0 | Microsoft Docs"
description: "En este artículo se describe cómo usar los mensajes HTTP para implementar la autenticación entre servicios mediante el flujo en nombre de OAuth 2.0."
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: 
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bb3e01b1b8741253a459a41cfff27da558573551
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Llamadas entre servicios mediante la identidad de usuarios delegada en el flujo de On-Behalf-Of
El flujo en nombre de OAuth 2.0 se usa en los casos en que una aplicación invoca un servicio o API web que a su vez debe llamar a otro servicio o API web. La idea es propagar la identidad y los permisos del usuario delegado a través de la cadena de solicitud. Para que el servicio de nivel intermedio realice solicitudes autenticadas al servicio de bajada, debe proteger un token de acceso de Azure Active Directory (Azure AD) en nombre del usuario.

## <a name="on-behalf-of-flow-diagram"></a>Diagrama del flujo de On-Behalf-Of
Suponga que el usuario se ha autenticado en una aplicación mediante el [flujo de concesión del código de autorización de OAuth 2.0](active-directory-protocols-oauth-code.md). En este punto, la aplicación tiene un token de acceso (token A) con las notificaciones del usuario y su consentimiento para tener acceso a la API web de nivel intermedio (API A). Ahora, la API A debe realizar una solicitud autenticada a la API web de bajada (API B).

Los pasos siguientes constituyen el "flujo en nombre de" y se explican con la ayuda del diagrama siguiente.

![Flujo en nombre de OAuth 2.0](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. La aplicación cliente realiza una solicitud a la API A con el token A.
2. La API A se autentica en el punto de conexión de emisión de tokens de Azure AD y solicita un token para obtener acceso a la API B.
3. El punto de conexión de emisión de tokens de Azure AD valida las credenciales de la API A con el token A y emite el token de acceso para la API B (token B).
4. El token B se establece en el encabezado de autorización de la solicitud a la API B.
5. La API B devuelve los datos del recurso protegido.

## <a name="register-the-application-and-service-in-azure-ad"></a>Registro de la aplicación y el servicio en Azure AD
Registre la aplicación cliente y el servicio de nivel intermedio en Azure AD.
### <a name="register-the-middle-tier-service"></a>Registro del servicio de nivel intermedio
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta y, en la lista **Directorio**, elija el inquilino de Active Directory en el que desee registrar la aplicación.
3. Haga clic en **Más servicios** en el panel de navegación izquierdo y elija **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y elija **Nuevo registro de aplicaciones**.
5. Escriba un nombre descriptivo para la aplicación y seleccione el tipo de aplicación. Según el tipo de aplicación, establezca la dirección URL de redireccionamiento o de inicio de sesión en la URL base. Haga clic en **Crear** para crear la aplicación.
6. Mientras sigue en Azure Portal, elija la aplicación y haga clic en **Configuración**. En el menú Configuración, elija **Claves** y agregue una clave: seleccione una duración de 1 o 2 años. Al guardar esta página, el valor de clave se mostrar; copie y guarde el valor en una ubicación segura. Necesitará esta clave más adelante para configurar las opciones de la aplicación en su implementación: este valor de clave no se volverá a mostrar ni recuperar por otro medio, así que regístrelo en cuanto esté visible en Azure Portal.

### <a name="register-the-client-application"></a>Registro del tipo de aplicación cliente
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta y, en la lista **Directorio**, elija el inquilino de Active Directory en el que desee registrar la aplicación.
3. Haga clic en **Más servicios** en el panel de navegación izquierdo y elija **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y elija **Nuevo registro de aplicaciones**.
5. Escriba un nombre descriptivo para la aplicación y seleccione el tipo de aplicación. Según el tipo de aplicación, establezca la dirección URL de redireccionamiento o de inicio de sesión en la URL base. Haga clic en **Crear** para crear la aplicación.
6. Configuración de los permisos de la aplicación: en el menú de configuración, elija la sección **Permisos necesarios**, haga clic en **Agregar**, luego, en **Seleccionar una API** y, finalmente, escriba el nombre del servicio de nivel intermedio en el cuadro de texto. Después, haga clic en **Seleccionar permisos** y seleccione "Acceder a *nombre del servicio*".

### <a name="configure-known-client-applications"></a>Configuración de aplicaciones cliente conocidas
En este caso, el servicio de nivel intermedio no tiene interacción con el usuario para obtener el consentimiento del usuario para obtener acceso a la API de bajada. Por lo tanto, la opción para conceder acceso a la API de bajada debe presentarse inicialmente como parte del paso de autorización durante la autenticación.
Para ello, siga estos pasos para enlazar explícitamente el registro de la aplicación cliente en Azure AD con el registro del servicio de nivel intermedio, que combina el consentimiento requerido por el cliente y el nivel intermedio en un único cuadro de diálogo.
1. Navegue hasta el registro del servicio de nivel medio y haga clic en **Manifiesto** para abrir el editor de manifiestos.
2. En el manifiesto, busque la propiedad de matriz `knownClientApplications` y agregue el identificador de cliente de la aplicación cliente como un elemento.
3. Guarde el manifiesto haciendo clic en el botón de guardado.

## <a name="service-to-service-access-token-request"></a>Solicitud de token de acceso entre servicios
Para solicitar un token de acceso, realice una solicitud HTTP POST al punto de conexión específico del inquilino de Azure AD con los parámetros siguientes.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Se pueden dar dos casos en función de si la aplicación cliente elige un secreto compartido o un certificado para su protección.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primer caso: solicitud de token de acceso con un secreto compartido
Cuando se utiliza un secreto compartido, una solicitud de token de acceso entre servicios contiene los parámetros siguientes:

| Parámetro |  | Descripción |
| --- | --- | --- |
| grant_type |requerido | Tipo de la solicitud de token. En el caso de una solicitud mediante un JWT, el valor debe ser **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Aserción |requerido | Valor del token usado en la solicitud. |
| client_id |requerido | El identificador de aplicación asignado al servicio de llamada durante el registro con Azure AD. Para buscar el identificador del identificador, en el Portal de administración de Azure, haga clic sucesivamente en **Active Directory**, en el directorio y en el nombre de la aplicación. |
| client_secret |requerido | La clave registrada para el servicio de llamada de Azure AD. Este valor debe haberse anotado en el momento del registro. |
| resource |requerido | El URI del identificador de la aplicación del servicio de recepción (recurso seguro). Para buscar el URI del identificador, en el Portal de administración de Azure, haga clic sucesivamente en **Active Directory**, en el directorio, en el nombre de la aplicación, en **Toda la configuración** y, luego, en **Propiedades**. |
| requested_token_use |requerido | Especifica cómo se debe procesar la solicitud. En el "flujo en nombre de", el valor debe ser **on_behalf_of**. |
| ámbito |requerido | Lista de ámbitos separados por un espacio para la solicitud de token. Para OpenID Connect, el ámbito **openid** debe especificarse.|

#### <a name="example"></a>Ejemplo
El siguiente HTTP POST solicita un token de acceso para la API web https://graph.windows.net. El parámetro `client_id` permite identificar el servicio que solicita el token de acceso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitud de token de acceso con un certificado
Una solicitud de token de acceso entre servicios con un certificado contiene los parámetros siguientes:

| Parámetro |  | Descripción |
| --- | --- | --- |
| grant_type |requerido | Tipo de la solicitud de token. En el caso de una solicitud mediante un JWT, el valor debe ser **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Aserción |requerido | Valor del token usado en la solicitud. |
| client_id |requerido | El identificador de aplicación asignado al servicio de llamada durante el registro con Azure AD. Para buscar el identificador del identificador, en el Portal de administración de Azure, haga clic sucesivamente en **Active Directory**, en el directorio y en el nombre de la aplicación. |
| client_assertion_type |requerido |El valor debe ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |requerido | Aserción (un JSON Web Token) que debe crear y firmar con el certificado que ha registrado como credenciales de la aplicación.  Lea el artículo sobre las [credenciales de certificado](active-directory-certificate-credentials.md) para obtener información sobre cómo registrar el certificado y el formato de la aserción.|
| resource |requerido | El URI del identificador de la aplicación del servicio de recepción (recurso seguro). Para buscar el URI del identificador, en el Portal de administración de Azure, haga clic sucesivamente en **Active Directory**, en el directorio, en el nombre de la aplicación, en **Toda la configuración** y, luego, en **Propiedades**. |
| requested_token_use |requerido | Especifica cómo se debe procesar la solicitud. En el "flujo en nombre de", el valor debe ser **on_behalf_of**. |
| ámbito |requerido | Lista de ámbitos separados por un espacio para la solicitud de token. Para OpenID Connect, el ámbito **openid** debe especificarse.|

Tenga en cuenta que los parámetros son casi iguales que en el caso de la solicitud con un secreto compartido, salvo que el parámetro client_secret se sustituye por dos parámetros: client_assertion_type y client_assertion.

#### <a name="example"></a>Ejemplo
El siguiente HTTP POST solicita un token de acceso para la API web https://graph.windows.net con un certificado. El parámetro `client_id` permite identificar el servicio que solicita el token de acceso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Respuesta de token de acceso entre servicios
Una respuesta correcta es una respuesta de OAuth 2.0 de JSON con los parámetros siguientes.

| Parámetro | Descripción |
| --- | --- |
| token_type |Indica el valor de tipo de token. El único tipo que admite Azure AD es el **portador**. Para obtener más información sobre los tokens de portador, vea [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) [Marco de autorización de OAuth 2.0: uso del token de portador (RFC 6750)]. |
| ámbito |Ámbito de acceso concedido en el token. |
| expires_in |Período de validez del token de acceso (en segundos). |
| expires_on |La hora a la que expira el token de acceso. La fecha se representa como el número de segundos desde 1970-01-01T0:0:0Z UTC hasta la fecha de expiración. Este valor se utiliza para determinar la duración de los tokens almacenados en caché. |
| resource |El URI del identificador de la aplicación del servicio de recepción (recurso seguro). |
| access_token |El token de acceso solicitado. El servicio de llamada puede usar este token para autenticarse en el servicio de recepción. |
| ID_token |El token de identificador solicitado. Puede usar el servicio de llamada para comprobar la identidad del usuario y comenzar una sesión con el usuario. |
| refresh_token |Token de actualización para el token de acceso solicitado. El servicio de llamada puede usar este token para solicitar otro token de acceso después de que expire el token de acceso actual. |

### <a name="success-response-example"></a>Ejemplo de respuesta correcta
En el ejemplo siguiente se muestra una respuesta correcta a una solicitud de un token de acceso para la API web https://graph.windows.net.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Ejemplo de respuesta de error
El punto de conexión del token de Azure AD devuelve una respuesta de error cuando intenta adquirir un token de acceso para la API de bajada si dicha API tiene establecida una directiva de acceso condicional, como la autenticación multifactor. El servicio de nivel intermedio debe exponer el error a la aplicación cliente para que esta pueda proporcionar la interacción del usuario necesaria para cumplir la directiva de acceso condicional.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Usar el token de acceso para obtener acceso al recurso protegido
Ahora, el servicio de nivel intermedio puede usar el token adquirido anteriormente para realizar solicitudes autenticadas a la API web de bajada mediante el establecimiento del token en el encabezado `Authorization`.

### <a name="example"></a>Ejemplo
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el protocolo OAuth 2.0 y conozca otra manera de realizar la autenticación entre servicios con las credenciales del cliente.
* [Autenticación entre servicios mediante la concesión de credenciales de cliente de OAuth 2.0 en Azure AD](active-directory-protocols-oauth-service-to-service.md)
* [OAuth 2.0 en Azure AD](active-directory-protocols-oauth-code.md)
