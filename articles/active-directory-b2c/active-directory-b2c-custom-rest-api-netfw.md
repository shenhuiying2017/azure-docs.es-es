---
title: "Azure Active Directory B2C: integración de intercambios de notificaciones de API de REST en el recorrido del usuario de Azure AD B2C como validación en entradas de usuario"
description: "Ejemplo de cómo integrar intercambios de notificaciones de API de REST en el recorrido del usuario de Azure AD B2C como validación en entradas de usuario"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: da7b8326a89fc6eb0bc3e7365c522d4a3adc809b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Azure Active Directory B2C: integración de intercambios de notificaciones de API de REST en el recorrido del usuario de Azure AD B2C como validación en entradas de usuario
El marco de experiencia de identidad (IEF) subyacente a Azure Active Directory B2C (Azure AD B2C) le permite integrar con una API de RESTful en un recorrido del usuario. En este tutorial, vamos a mostrarle cómo interactúa B2C con los servicios RESTful de .NET Framework (API Web).

## <a name="introduction"></a>Introducción
Azure AD B2C le permite agregar su propia lógica de negocios en un recorrido del usuario mediante una llamada a su propio servicio RESTful. El marco de experiencia de identidad envía datos al servicio RESTful en la colección **Notificaciones de entrada** y recibe datos de RESTful de la colección **Notificaciones de salida**. Con la integración del servicio RESTful, puede hacer lo siguiente:

* Validar datos de entrada de usuario e impedir que se conserven datos con formato incorrecto en Azure AD. Si el valor del usuario no es válido, el servicio RESTful devuelve el mensaje de error que indica al usuario que proporcione una entrada. Por ejemplo, puede comprobar que la dirección de correo electrónico proporcionada por el usuario ya exista en la base de datos de los clientes.
* Sobrescribir notificaciones de entrada. Por ejemplo, si el usuario escribe el nombre en minúsculas o mayúsculas, puede dar formato al nombre y poner en mayúsculas solo la primera letra.
* Enriquecer datos de usuario mediante integración adicional con la aplicación de línea de negocio corporativa. El servicio RESTful puede recibir la dirección de correo electrónico del usuario, consultar la base de datos de clientes y devolver el número de usuario de fidelidad a B2C. Se pueden almacenar las notificaciones de devolución en la cuenta de AAD del usuario, que se evalúan en los siguientes **pasos de orquestación** o se incluyen en el token de acceso.
* Ejecutar lógica de negocios personalizada: enviar notificaciones push, actualizar bases de datos corporativas, ejecutar proceso de migración de usuario, administración de permisos, auditoría, etc.

La integración con los servicios RESTful puede diseñarse como intercambio de notificaciones o como perfil técnico de validación:

* **Perfil técnico de validación**: la llamada al servicio RESTful tiene lugar dentro de ValidationTechnicalProfile de un TechnicalProfile determinado. El perfil técnico de validación valida los datos de proporcionados por el usuario antes de que avance el recorrido del usuario. Con el perfil técnico de validación, puede hacer lo siguiente:
  * Enviar notificaciones de entrada
  * Validar las notificaciones de entrada y generar mensajes de error personalizados
  * Devolver notificaciones de salida

* **Intercambio de notificaciones**: similar al anterior pero ocurre dentro de un paso de la orquestación. Esta definición se limita a:
   * Enviar notificaciones de entrada
   * Devolver notificaciones de salida

## <a name="restful-walkthrough"></a>Tutorial de RESTful
En este tutorial, se desarrolla una API web de .NET Framework que valida la entrada del usuario y proporciona el número de fidelidad del usuario. Por ejemplo, la aplicación puede conceder acceso a "beneficios Platinum" en función del número de fidelidad.

Información general:
*   Desarrollo del servicio RESTful (API Web de .NET Framework)
*   Uso del servicio RESTful en el recorrido del usuario
*   Envío de notificaciones de entrada y su lectura en el código
*   Validación del nombre del usuario
*   Devolución del número de fidelidad 
*   Incorporación del número de fidelidad a JSON Web Token (JWT)

## <a name="prerequisites"></a>Requisitos previos
Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Paso 1: Creación de ASP.NET Web API
1.  Cree un proyecto nuevo en Visual Studio seleccionando **Archivo > Nuevo > Proyecto**.
2.  En el cuadro de diálogo **Nuevo proyecto**, seleccione **Visual C# > Web > Aplicación web ASP.NET (.NET Framework)**.
3.  Llame a la aplicación, por ejemplo, Contoso.AADB2C.API y seleccione **Aceptar**.

    ![Creación de un nuevo proyecto de Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4.  Seleccione una plantilla de **API web** o de **Aplicación de API de Azure**.
5.  Asegúrese de establecer la autenticación en **Sin autenticación**.

    ![Selección de la plantilla API Web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

6.  Haga clic en **Aceptar** para crear el proyecto.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Paso 2: Preparación del punto de conexión de API de REST

### <a name="step-21-add-data-models"></a>Paso 2.1: Incorporación de modelos de datos
Los modelos representan los datos de notificaciones de entrada y de notificaciones de salida en el servicio RESTful. El código lee los datos de entrada deserializando el modelo de notificaciones de entrada de la cadena JSON para el objeto de C# (el modelo). ASP.NET Web API deserializa automáticamente el modelo de notificaciones de salida en JSON y escribe los datos serializados en el cuerpo del mensaje de respuesta HTTP. Comencemos por crear un modelo que representa notificaciones de entrada.

1.  Si el Explorador de soluciones no está visible, haga clic en el menú **Ver** y seleccione **Explorador de soluciones**. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta Models. En el menú contextual, seleccione **Agregar** y, después, haga clic en **Clase**.

    ![Agregar modelo](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

2.  Asigne `InputClaimsModel` como nombre de clase y agregue las propiedades siguientes a la clase `InputClaimsModel`.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

3.  Cree un nuevo modelo `OutputClaimsModel` y agregue las propiedades siguientes a la clase `OutputClaimsModel`.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

4.  Cree otro modelo más `B2CResponseContent`. Este modelo se usa para producir mensajes de error de validación de entrada. Agregue las siguientes propiedades para la clase `B2CResponseContent`, proporcione las referencias que faltan y guarde el archivo.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Paso 2.2: Incorporación de un controlador
En la API Web, un _controlador_ es un objeto que controla las solicitudes HTTP. Agregamos un controlador que devuelve las notificaciones de salida, o bien, si el nombre no es válido, produce el mensaje de error HTTP de conflicto.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta Controllers. Seleccione **Agregar** y, luego, **Controlador**.

    ![Incorporación de nuevo controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2.  En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de API Web: en blanco**. Haga clic en **Agregar**.

    !![Selección de Controlador de Web API 2 - en blanco](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3.  En el cuadro de diálogo **Agregar controlador**, asigne el nombre `IdentityController` al controlador y seleccione **Agregar**.

    ![Escribir el nombre del controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    La técnica de scaffolding crea un archivo denominado IdentityController.cs en la carpeta Controllers.

4.  Si este archivo no está abierto, haga doble clic en el archivo para abrirlo. Reemplace el contenido de este archivo por las siguientes líneas de código:

    ```C#
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If not data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create output claims object and set the loyalty number with random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-to-azure"></a>Paso 3: Publicación en Azure
1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Contoso.AADB2C.API** y seleccione **Publicar**.

    ![Publicación en Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2.  Asegúrese de que **Microsoft Azure App Service** está seleccionado y seleccione **Publicar**.

    ![Creación de nueva aplicación en Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Con ello se abrirá el cuadro de diálogo **Crear App Service** que le ayudará a crear todos los recursos de Azure necesarios para ejecutar la aplicación web de ASP.NET en Azure.

> [!NOTE]
>Para más información sobre la publicación, consulte [Creación de una aplicación web de ASP.NET en Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3.  En **Nombre de la aplicación web**, escriba un nombre único de aplicación (los caracteres válidos son `a-z`, `0-9` y `-`). La dirección URL de la aplicación web es `http://<app_name>.azurewebsites.NET`, donde `<app_name>` es el nombre de la aplicación web. Puede aceptar el nombre generado automáticamente, que es único.

    Seleccione **Crear** para comenzar a crear los recursos de Azure.

    ![Entrada de las propiedades de App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4.  Una vez completado el asistente, publica la aplicación web ASP.NET en Azure y, a continuación, inicia la aplicación en el explorador predeterminado. Copie la dirección URL.

## <a name="step-4-add-the-new-claim-loyaltynumber-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Paso 4: Incorporación de la nueva notificación `loyaltyNumber` al esquema del archivo TrustFrameworkExtensions.xml
La notificación `loyaltyNumber` todavía no se ha definido en ninguna parte de nuestro esquema. Por lo tanto, debe agregar una definición dentro del elemento `<BuildingBlocks>`. Encontrará dicho elemento al principio del archivo TrustFrameworkExtensions.xml.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-adding-claims-provider"></a>Paso 5: Adición del proveedor de notificaciones 
Cada proveedor de notificaciones debe tener uno o varios perfiles técnicos que determina los puntos de conexión y los protocolos necesarios para comunicarse con el proveedor de notificaciones. Un proveedor de notificaciones puede tener varios perfiles técnicos por distintas razones. Por ejemplo, se pueden definir varios perfiles técnicos porque el proveedor de notificaciones considerado admite varios protocolos, varios puntos de conexión con distintas funcionalidades o libera distintas notificaciones en diferentes niveles de seguridad. Puede ser aceptable liberar notificaciones confidenciales en un recorrido del usuario pero no en otro. 

El fragmento de código XML siguiente contiene el nodo `ClaimsProvider` con dos `TechnicalProfile`:
* `<TechnicalProfile Id="REST-API-SignUp">` define el servicio RESTful. `Proprietary` se describe como protocolo para un proveedor basado en RESTful. El elemento `InputClaims` define las notificaciones que se enviarán desde B2C al servicio REST. En este ejemplo, el contenido de la notificación `givenName` se envía al servicio REST como `firstName`, el contenido de la notificación `surename` se envía al servicio REST como `lastName` y `email` se envía tal cual. El elemento `OutputClaims` define las notificaciones que se recuperan desde los servicios RESTful hacia B2C.

* `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` agrega un perfil técnico de validación al perfil técnico existente (definido en la directiva de base). Durante el recorrido del registro, el perfil técnico de validación invoca el perfil técnico anterior. Si un servicio RESTful devuelve el error HTTP 409 (conflicto), el mensaje de error se presenta al usuario. 

Agregue el nodo `<ClaimsProviders>` y el siguiente fragmento de código XML en el nodo `<ClaimsProviders>`:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://yourdomain.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Paso 6: Adición de la notificación `loyaltyNumber` al archivo de directiva de usuario de confianza para que la notificación se envíe a la aplicación
Edite el archivo de usuario de confianza (RP) SignUpOrSignIn.xml y modifique el elemento `<TechnicalProfile Id="PolicyProfile">` para agregar lo siguiente: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Después de agregar la nueva notificación, `RelyingParty` tendrá el siguiente aspecto:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Paso 7: Carga de la directiva en el inquilino
1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Abra **Todas las directivas**. 
4.  Seleccione **Cargar directiva**
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.
7.  Repita el último paso y cargue el archivo SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Paso 8: Prueba de la directiva personalizada con Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

> [!NOTE]
>
>**Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.


2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.

    ![](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3.  Intente escribir "Test" en el campo **Nombre propio**, B2C muestra un mensaje de error en la parte superior de la página.

    ![Prueba de la directiva](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  Intente escribir un nombre (distinto de "Test") en el campo **Nombre propio**. B2C registra al usuario y envía loyaltyNumber a la aplicación. Observe el número de este JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Opcional] Descarga de los archivos y código de la directiva completos
* Se recomienda que, en lugar de usar estos archivos de ejemplo, cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas.  [Archivos de directiva de ejemplo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)
* Puede descargar aquí el código completo [Ejemplo de solución de Visual Studio para referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)
    
## <a name="next-steps"></a>Pasos siguientes
1.  [Secure your RESTful API with basic authentication (username and password)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Protección de las API de RESTful con autenticación básica [nombre de usuario y contraseña])
2.  [Secure your RESTful API with client certificate](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) (Protección de las API de RESTful con certificado de cliente)