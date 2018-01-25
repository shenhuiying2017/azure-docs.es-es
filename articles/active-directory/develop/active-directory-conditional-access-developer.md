---
title: Instrucciones para desarrolladores para Acceso condicional de Azure Active Directory
description: Instrucciones para desarrolladores y escenarios para el acceso condicional de Azure AD
services: active-directory
keywords: 
author: danieldobalian
manager: mtillman
editor: PatAltimore
ms.author: dadobali
ms.date: 07/19/2017
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 8ad1ed1b8a178cb8906e6233e6bd78f50d01c50c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Instrucciones para desarrolladores para Acceso condicional de Azure Active Directory

Azure Active Directory (AD) ofrece varias de maneras para proteger la aplicación y los servicios.  Una de estas características únicas es el acceso condicional.  El acceso condicional permite que los desarrolladores y clientes empresariales protejan los servicios de diversas formas, entre las que se incluyen las siguientes:

* Multi-Factor Authentication
* Autorización para que solo los dispositivos inscritos en Intune accedan a servicios específicos
* Restricción de ubicaciones de usuario e intervalos IP

Para más información sobre las funcionalidades completas del acceso condicional, consulte [Acceso condicional en Azure Active Directory](../active-directory-conditional-access-azure-portal.md). 

En este artículo, nos centramos en el significado que tiene el acceso condicional para los desarrolladores que compilan aplicaciones para Azure AD.  En él se supone que tiene conocimientos de aplicaciones de inquilino [único](active-directory-integrating-applications.md) y [multiinquilino](active-directory-devhowto-multi-tenant-overview.md), además de los [patrones comunes de autenticación](active-directory-authentication-scenarios.md).

Analizaremos en profundidad el impacto de acceder a recursos sobre los cuales no tiene control y en los que se puedan aplicar directivas de acceso condicional.  Además, exploraremos las implicaciones del acceso condicional en el flujo en el nombre de otra persona y aplicaciones web, el acceso a Microsoft Graph y las llamadas a API.

## <a name="how-does-conditional-access-impact-an-app"></a>¿Cómo el acceso condicional impacta a una aplicación?

### <a name="app-types-impacted"></a>Tipos de aplicación afectados

En los casos más comunes, el acceso condicional no cambia el comportamiento de una aplicación ni requiere ningún cambio por parte del desarrollador.  Solo en ciertos casos en los que una aplicación, de manera indirecta o silenciosa, solicita un token para un servicio, una aplicación requiere cambios en el código para controlar los "desafíos" del acceso condicional.  Puede ser tan sencillo como realizar una solicitud de inicio de sesión interactiva. 

En concreto, los escenarios siguientes requieren código para controlar los "desafíos" del acceso condicional: 

* Aplicaciones que acceden a Microsoft Graph
* Aplicaciones que realizan el flujo "en nombre de"
* Aplicaciones que acceden a varios servicios o recursos
* Aplicaciones de una sola página que usan ADAL.js
* Aplicaciones web que llaman a un recurso

Las directivas de acceso condicional se pueden aplicar a la aplicación, pero también se pueden aplicar a una API web a la que accede la aplicación. Para más información sobre cómo configurar una directiva de acceso condicional, consulte [Introducción al acceso condicional de Azure Active Directory](../active-directory-conditional-access-azure-portal-get-started.md).

Según el escenario, un cliente empresarial puede aplicar y quitar directivas de acceso condicional en cualquier momento.  Con el fin de que la aplicación siga funcionando cuando se aplica una directiva nueva, es necesario implementar el control de "desafíos". En los ejemplos siguientes se ilustra el control de desafíos. 

### <a name="conditional-access-examples"></a>Ejemplos de acceso condicional

En algunos escenarios se requieren cambios en el código para controlar el acceso condicional, mientras que en otros se trabaja tal cual.  Estos son algunos escenarios que usan el acceso condicional para realizar la autenticación multifactor que proporciona información sobre la diferencia.

* Se genera una aplicación iOS de inquilino único y se aplica una directiva de acceso condicional.  La aplicación inicia la sesión de un usuario y no solicita acceso a una API.  Cuando el usuario inicia sesión, la directiva se invoca automáticamente y el usuario debe realizar la autenticación multifactor (MFA). 
* Crea una aplicación web multiinquilino que usa Microsoft Graph para acceder a Exchange, entre otros servicios.  Un cliente empresarial que adopta esta aplicación establece una directiva en Exchange.  Cuando la aplicación web solicita un token para MS Graph, la aplicación no será requerida a cumplir con la directiva.  El usuario final ha iniciado sesión con tokens válidos. Cuando la aplicación intenta utilizar este token en Microsoft Graph para tener acceso a datos de Exchange, se devuelve una notificación "challenge" a la aplicación web a través del encabezado ```WWW-Authenticate```.  La aplicación puede utilizar ```claims``` en una nueva solicitud y se pedirá al usuario final que cumpla con las condiciones. 
* Está creando una aplicación nativa que utiliza un servicio de nivel intermedio para tener acceso a una API de nivel inferior.  Un cliente empresarial de la empresa que usa esta aplicación aplica una directiva a la API de nivel inferior.  Cuando un usuario final inicia sesión, la aplicación nativa solicita acceso al nivel intermedio y envía el token.  El nivel intermedio realiza el flujo "en nombre de" para solicitar acceso a la API de nivel inferior.  En este punto, se presenta un "desafío" de notificaciones al nivel intermedio. El nivel intermedio envía el desafío de vuelta a la aplicación nativa, la que necesita cumplir con la directiva de acceso condicional.

### <a name="complying-with-a-conditional-access-policy"></a>Cumplimiento con una directiva de acceso condicional

En el caso de varias topologías de aplicaciones distintas, se evalúa una directiva de acceso condicional cuando se establece la sesión.  Si bien una directiva de acceso condicional opera en la granularidad de aplicaciones y servicios, el punto en que se invoca depende en gran medida del escenario que intenta lograr.

Cuando la aplicación intenta acceder a un servicio con una directiva de acceso condicional, podría encontrar un desafío de acceso condicional.  Este desafío se codifica en el parámetro `claims` que se incluye en una respuesta de Azure AD o Microsoft Graph.  Este es un ejemplo de este parámetro de desafío: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Los desarrolladores pueden tomar este desafío y anexarlo a una solicitud nueva a Azure AD.  Cuando se pasa este estado, se solicita al usuario final que realice cualquier acción necesaria para cumplir con la directiva de acceso condicional. En los escenarios siguientes, se explican los detalles específicos del error y cómo extraer el parámetro. 

## <a name="scenarios"></a>Escenarios

### <a name="prerequisites"></a>requisitos previos

El acceso condicional de Azure AD es una característica que se incluye en [Azure AD Premium](../active-directory-whatis.md#choose-an-edition).  Puede obtener más información sobre los requisitos de licencia en el [informe de uso sin licencia](../active-directory-conditional-access-unlicensed-usage-report.md).  Los desarrolladores pueden unirse a [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), que incluye una suscripción gratis a Enterprise Mobility Suite que, a su vez, incluye Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Consideraciones para escenarios específicos

La información siguiente solo se aplica en estos escenarios de acceso condicional:

* Aplicaciones que acceden a Microsoft Graph
* Aplicaciones que realizan el flujo "en nombre de"
* Aplicaciones que acceden a varios servicios o recursos
* Aplicaciones de una sola página que usan ADAL.js

En las secciones siguientes, describiremos escenarios comunes mucho más complejos.  El principio operativo central es que las directivas de acceso condicional se evalúan en el momento en que se solicita el token para el servicio que tiene aplicada una directiva de acceso condicional, a menos que se acceda a través de Microsoft Graph.

## <a name="scenario-app-accessing-microsoft-graph"></a>Escenario: Aplicación que accede a Microsoft Graph

En este escenario, analizaremos un caso en el que una aplicación web solicita acceso a Microsoft Graph. En este caso, la directiva de acceso condicional se podría asignar a SharePoint, Exchange o algún otro servicio al que se accede como carga de trabajo mediante Microsoft Graph.  En este ejemplo, vamos a suponer que hay una directiva de acceso condicional en SharePoint Online.

![Diagrama de flujo de la aplicación que accede a Microsoft Graph](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

En primer lugar, la aplicación solicita autorización a Microsoft Graph, lo que requiere acceder a una carga de trabajo de bajada sin acceso condicional.  La solicitud se realiza correctamente sin invocar ninguna directiva y la aplicación recibe tokens para Microsoft Graph.  En este punto, la aplicación puede usar el token de acceso en la solicitud de portador del punto de conexión solicitado. Ahora, la aplicación necesita acceder a un punto de conexión de SharePoint Online de Microsoft Graph, por ejemplo: `https://graph.microsoft.com/v1.0/me/mySite`

La aplicación ya tiene un token válido para Microsoft Graph, por lo que puede realizar la solicitud nueva sin que se emita un token nuevo. Esta solicitud genera un error y se emite un desafío de notificaciones desde Microsoft Graph con el formato de HTTP 403 Prohibido con un desafío ```WWW-Authenticate```.
Este es un ejemplo de la respuesta: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

El desafío de notificaciones está dentro del encabezado ```WWW-Authenticate```, que se puede analizar para extraer el parámetro de notificaciones correspondiente a la solicitud siguiente.  Una vez que se anexa a la solicitud nueva, Azure AD sabe evaluar la directiva de acceso condicional cuando el usuario inicia sesión y ahora la aplicación cumple con la directiva de acceso condicional.  La repetición de la solicitud al punto de conexión de SharePoint Online se realiza correctamente.

El encabezado ```WWW-Authenticate``` tiene una estructura única y no es sencillo de analizar para extraer valores.  Este es un método corto para ayudar.

```csharp
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
```

Para ejemplos de código que demuestran cómo controlar el desafío de notificaciones, consulte el [código de ejemplo "en nombre de"](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) para ADAL .NET.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Escenario: Aplicación que realiza el flujo "en nombre de"

En este escenario, analizaremos un caso en el que una aplicación nativa llama a una API o servicio web.  A su vez, este servicio realiza [el flujo "en nombre de"](active-directory-authentication-scenarios.md#application-types-and-scenarios) para llamar a un servicio de bajada.  En este caso, se aplica la directiva de acceso condicional al servicio de bajada (API web 2) y se usa una aplicación nativa en lugar de una aplicación demonio/servidor. 

![Aplicación que realiza el flujo "en nombre de"](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

La solicitud de token inicial para API web 1 no solicita al usuario final que realice la autenticación multifactor, dado que API web 1 no siempre puede alcanzar la API de bajada.  Una vez que API web 1 intenta solicitar un token en nombre del usuario para API web 2, la solicitud genera un error porque el usuario no inició sesión con la autenticación multifactor.

Azure AD devuelve una respuesta HTTP con algunos datos interesantes: 

> [!NOTE]
> En esta instancia se trata de la descripción de un error de autenticación multifactor, sino que es un intervalo amplio de `interaction_required` posiblemente perteneciente al acceso condicional.  

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

En la API web 1, se captura el error `error=interaction_required` y el desafío `claims` se envía de vuelta a la aplicación de escritorio.  En ese momento, la aplicación de escritorio puede realizar una llamada `acquireToken()` nueva y anexa el desafío `claims` como un parámetro de cadena de solicitud adicional.  Esta solicitud nueva requiere que el usuario realice la autenticación multifactor y, luego, envíe el token nuevo de vuelta a la API web 1 y complete el flujo "en nombre de".

Para probar el escenario, consulte el [ejemplo de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Muestra cómo pasar el desafío de notificaciones de vuelta desde API web 1 a la aplicación nativa y construye una solicitud nueva dentro de la aplicación cliente. 

## <a name="scenario-app-accessing-multiple-services"></a>Escenario: Aplicación que accede a varios servicios

En este escenario se describe un caso en el que una aplicación web accede a dos servicios, uno de los cuales tiene asignada una directiva de acceso condicional.  En función de la lógica de la aplicación, es posible que exista una ruta de acceso en la que la aplicación no requiera acceso a ambos servicios web.  En este escenario, el orden en que se solicita un token representa un papel importante en la experiencia del usuario final.

Vamos a suponer que tenemos un servicio web A y B y que el servicio web B tiene aplicada la directiva de acceso condicional.  Si bien la solicitud de autenticación interactiva inicial requiere consentimiento para ambos servicios, la directiva de acceso condicional no se requiere en todos los casos.  Si la aplicación solicita un token para el servicio web B, se invoca la directiva y las solicitudes subsiguientes del servicio web A también se realizan correctamente, como se indica a continuación.

![Diagrama de flujo de la aplicación que accede a varios servicios](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

De manera alternativa, si la aplicación inicialmente solicita un token para el servicio web A, el usuario final no invoca la directiva de acceso condicional.  Esto permite que el desarrollador de la aplicación controle la experiencia del usuario final y no obligue a que la directiva de acceso condicional se invoque en todos los casos. La parte complicada se genera si la aplicación solicita posteriormente un token para el servicio web B. En este punto, el usuario final necesita cumplir con la directiva de acceso condicional.  Si la aplicación intenta `acquireToken`, puede generar el error siguiente (que se ilustra en el diagrama a continuación): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Aplicación que accede a varios servicios que solicitan un token nuevo](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Si la aplicación usa la biblioteca ADAL, la adquisición de un token siempre se reintenta de forma interactiva si se genera un error.  Cuando se produce esta solicitud interactiva, el usuario final tiene la oportunidad de cumplir con el acceso condicional.  Esto se aplica a menos que la solicitud sea `AcquireTokenSilentAsync` o `PromptBehavior.Never`, en cuyo caso la aplicación debe realizar una solicitud ```AcquireToken``` interactiva para dar al usuario final la oportunidad de cumplir con la directiva. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Escenario: Aplicación de una sola página (SPA) que usa ADAL.js

En este escenario, se describe un caso en el que se tiene una aplicación de una sola página (SPA) que usa ADAL.js para llamar a una API web protegida por acceso condicional.  Se trata de una arquitectura simple, pero tiene algunos matices que se deben considerar cuando se desarrollen aplicaciones alrededor del acceso condicional.

En ADAL.js, existen algunas funciones que obtienen tokens: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` y `acquireTokenRedirect(…)`. 

* `login()` obtiene un token de identificador mediante una solicitud de inicio de sesión interactiva, pero no obtiene tokens de acceso para ningún servicio (incluida una API web protegida por acceso condicional).  
* `acquireToken(…)` se puede usar para obtener silenciosamente un token de acceso, lo que significa que no muestra UI en ninguna circunstancia.  
* Tanto `acquireTokenPopup(…)` como `acquireTokenRedirect(…)` se usan para solicitar de manera interactiva un token para un recurso, lo que significa que siempre muestra la UI de inicio de sesión.

Cuando una aplicación necesita un token de acceso para llamar a una API web, intenta un `acquireToken(…)`.  Si la sesión de token expira o es necesario cumplir con una directiva de acceso condicional, la función *acquireToken* genera un error y la aplicación usa `acquireTokenPopup()` o `acquireTokenRedirect()`.

![Diagrama de flujo de la aplicación de una sola página que usa ADAL](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Veamos un ejemplo con el escenario de acceso condicional.  El usuario final acaba de llegar al sitio y no tiene una sesión.  Se realiza una llamada `login()` y se obtiene un token de identificador sin autenticación multifactor.  Luego, el usuario presiona un botón que requiere que la aplicación solicite datos de una API web.  La aplicación intenta realizar una llamada `acquireToken()` pero se genera un error, dado que el usuario todavía no realizó la autenticación multifactor y debe cumplir con la directiva de acceso condicional.

Azure AD envía de vuelta la respuesta HTTP siguiente: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

La aplicación debe capturar `error=interaction_required`.  Luego, la aplicación puede usar `acquireTokenPopup()` o `acquireTokenRedirect()` en el mismo recurso.  Se obliga al usuario a realizar la autenticación multifactor. Una vez que el usuario completa la autenticación multifactor, la aplicación recibe un token de acceso nuevo para el recurso solicitado.

Para probar el escenario, consulte el [ejemplo de código "en nombre de" de SPA de JS](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Este ejemplo de código usa la directiva de acceso condicional y la API web que registró anteriormente con un SPA de JS para mostrar el escenario. Muestra cómo controlar de forma adecuada el desafío de notificaciones y obtener un token de acceso que se puede usar para la API web. De manera alternativa, revise el [ejemplo de código Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) general para información sobre un SPA de Angular


## <a name="see-also"></a>Otras referencias

* Para más información sobre las funcionalidades, consulte [Acceso condicional en Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Para más información sobre los ejemplos de código de Azure AD, consulte [repositorio de GitHub de ejemplos de código](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Para más información sobre el SDK de ADAL y acceso a la documentación de referencia, consulte la [guía de la biblioteca](active-directory-authentication-libraries.md).
* Para más información sobre los escenarios multiinquilino, consulte el artículo sobre el [inicio de sesión de usuarios con el patrón multiinquilino](active-directory-devhowto-multi-tenant-overview.md).
