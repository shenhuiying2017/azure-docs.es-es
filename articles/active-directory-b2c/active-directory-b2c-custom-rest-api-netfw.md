---
title: "Azure Active Directory B2C: integración de intercambios de notificaciones de API de REST en el recorrido del usuario de Azure AD B2C como validación de la entrada del usuario"
description: "Integración de intercambios de notificaciones de API de REST en el recorrido del usuario de Azure AD B2C como validación de la entrada del usuario."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: fd9c95ae78590aa772fde10c8c80914c905767a8
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integración de intercambios de notificaciones de API de REST en el recorrido del usuario de Azure AD B2C como validación de la entrada del usuario
El marco de experiencia de identidad, que subyace a Azure Active Directory B2C (Azure AD B2C), le permite realizar la integración con una API RESTful en un recorrido del usuario. En este tutorial, aprenderá de qué forma Azure AD B2C con los servicios REST de .NET Framework (API web).

## <a name="introduction"></a>Introducción
Mediante Azure AD B2C puede agregar su propia lógica de negocios a un recorrido del usuario mediante una llamada a su propio servicio REST. El marco de experiencia de identidad envía datos al servicio REST en una colección *Notificaciones entrantes* y recibe datos de RESTful en una colección *Notificaciones salientes*. Con la integración del servicio RESTful, puede hacer lo siguiente:

* **Validar datos de entrada de usuario**: esta acción impide que se conserven en Azure AD datos con formato incorrecto. Si el valor del usuario no es válido, el servicio REST devuelve el mensaje de error que indica al usuario que proporcione una entrada. Por ejemplo, puede comprobar que la dirección de correo electrónico que proporciona el usuario existe en la base de datos del cliente.
* **Sobrescribir notificaciones de entrada**: por ejemplo, si un usuario escribe el nombre con mayúsculas o minúsculas, se le puede dar un formato en el que solo la primera letra esté en mayúscula.
* **Enriquecer los datos de usuario mediante su integración adicional con las aplicaciones de línea de negocio corporativas**: el servicio REST puede recibir la dirección de correo electrónico del usuario, consultar la base de datos del cliente y devolver el número de fidelidad del usuario a Azure AD B2C. Las notificaciones de devolución se pueden almacenar en la cuenta de Azure AD del usuario, evaluar en los siguientes *pasos de orquestación* o incluir en el token de acceso.
* **Ejecutar una lógica de negocios personalizada**: puede enviar notificaciones push, actualizar las bases de datos corporativas, ejecutar un proceso de migración de usuarios, administrar permisos, auditar bases de datos y realizar otras acciones.

La integración con los servicios REST se pueden diseñar de las siguientes maneras:

* **Perfil técnico de validación**: la llamada al servicio REST tiene lugar dentro del perfil técnico de validación del perfil técnico especificado. El perfil técnico de validación valida los datos de que proporciona el usuario antes de que el recorrido del usuario avance. Con el perfil técnico de validación, puede realizar las siguientes tareas:
   * Enviar notificaciones de entrada.
   * Validar las notificaciones de entrada y generar mensajes de error personalizados.
   * Devolver notificaciones de salida.

* **Intercambio de notificaciones**: este diseño es similar al perfil técnico de validación, pero se produce dentro de un paso de la orquestación. Esta definición se limita a:
   * Enviar notificaciones de entrada.
   * Devolver notificaciones de salida.

## <a name="restful-walkthrough"></a>Tutorial de RESTful
En este tutorial, se desarrolla una API web de .NET Framework que valida la entrada del usuario y proporciona un número de fidelidad del usuario. Por ejemplo, la aplicación puede conceder acceso a *beneficios Platinum* en función del número de fidelidad.

Información general:
* Desarrollo del servicio REST (API web de .NET Framework).
* Uso del servicio REST en el recorrido del usuario.
* Envío de notificaciones de entrada y su lectura en el código.
* Validación del nombre del usuario.
* Devolución de un número de fidelidad. 
* Incorporación del número de fidelidad a JSON Web Token (JWT).

## <a name="prerequisites"></a>requisitos previos
Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Paso 1: Creación de ASP.NET Web API

1. Cree un proyecto en Visual Studio, para lo que debe seleccionar **Archivo** >  **Nuevo** > **Proyecto**.

2. En la ventana **Nuevo proyecto**, seleccione **Visual C#** > **Web** > **Aplicación web ASP.NET (.NET Framework)**.

3. En el cuadro **Nombre**, escriba el nombre de la aplicación (por ejemplo, *Contoso.AADB2C.API*) y seleccione **Aceptar**.

    ![Creación de un nuevo proyecto de Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. En la ventana **Nueva aplicación web ASP.NET**, seleccione una plantilla de **API web** o de **aplicación de API de Azure**.

    ![Seleccionar una plantilla de API web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Asegúrese de seleccionar **Sin autenticación** en el campo Autenticación.

6. Haga clic en **Aceptar** para crear el proyecto.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Paso 2: Preparación del punto de conexión de API de REST

### <a name="step-21-add-data-models"></a>Paso 2.1: Incorporación de modelos de datos
Los modelos representan los datos de notificaciones de entrada y de notificaciones de salida en el servicio RESTful. El código lee los datos de entrada, para lo que deserializa el modelo de notificaciones de entrada de una cadena JSON en un objeto de C# (el modelo). ASP.NET Web API deserializa automáticamente el modelo de notificaciones de salida en JSON y luego escribe los datos serializados en el cuerpo del mensaje de respuesta HTTP. 

Cree un modelo que represente las notificaciones de entrada, para lo que debe seguir estos pasos:

1. Si el Explorador de soluciones no está abierto, seleccione **Ver** > **Explorador de soluciones**. 
2. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta **Modelos**, seleccione **Agregar** y **Clase**.

    ![Agregar modelo](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Asigne a la clase el nombre `InputClaimsModel` y, después, agregue las siguientes propiedades a la clase `InputClaimsModel`:

    ```csharp
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

4. Cree un nuevo modelo, `OutputClaimsModel`, y agregue las siguientes propiedades a la clase `OutputClaimsModel`:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Cree un modelo más, `B2CResponseContent`, que se usa para generar mensajes de error de la validación de los datos de entrada. Agregue las siguientes propiedades a la clase `B2CResponseContent`, proporcione las referencias que faltan y guarde el archivo:

    ```csharp
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
En la API web, un _controlador_ es un objeto que controla las solicitudes HTTP. El controlador devuelve notificaciones de salida, o bien, si el nombre no es válido, genera un mensaje de error de HTTP en conflicto.

1. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta **Controladores**, y seleccione **Agregar** y **Controlador**.

    ![Incorporación de nuevo controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. En la ventana **Agregar scaffold**, seleccione **Controlador de Web API: en blanco** y seleccione **Agregar**.

    ![Selección de Controlador de Web API 2 - en blanco](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. En la ventana **Agregar controlador**, asigne un nombre al controlador **IdentityController**y seleccione **Agregar**.

    ![Escribir el nombre del controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    La técnica de scaffolding crea un archivo denominado *IdentityController.cs* en la carpeta *Controllers*.

4. Si el archivo *IdentityController.cs* no está abierto, haga doble clic en él y reemplace el código por el siguiente:

    ```csharp
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
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Paso 3: Publicación del proyecto en Azure
1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **Contoso.AADB2C.API** y seleccione **Publicar**.

    ![Publicación en Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. En la ventana **Publicar**, seleccione **Microsoft Azure App Service** y, después, haga clic en **Publicar**.

    ![Creación de nueva aplicación en Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Se abre la ventana **Crear servicio de aplicaciones**. En ella, cree todos los recursos de Azure necesarios para ejecutar la aplicación web ASP.NET en Azure.

    > [!NOTE]
    >Para más información acerca de la publicación, consulte [Creación de una aplicación web ASP.NET en Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. En el cuadro **Nombre de aplicación web**, escriba un nombre de aplicación único [los caracteres válidos son a-z, A-Z, 0-9 y guión(-)]. La dirección URL de la aplicación web es http://<nombre_aplicación>.azurewebsites.NET, donde *nombre_aplicación* es el nombre de su aplicación web. Puede aceptar el nombre generado automáticamente, que es único.

    ![Entrada de las propiedades de App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Para empezar a crear recursos de Azure, seleccione **Crear**.  
    Una vez que se ha creado la aplicación web ASP.NET, el asistente la publica en Azure y la inicia en el explorador predeterminado.

6. Copie la dirección URL de la aplicación web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Paso 4: Incorporación de la nueva notificación `loyaltyNumber` al esquema del archivo TrustFrameworkExtensions.xml
La notificación `loyaltyNumber` aún no se ha definido en nuestro esquema. Agregue una definición al elemento `<BuildingBlocks>`, que puede encontrará al principio del archivo *TrustFrameworkExtensions.xml*.

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

## <a name="step-5-add-a-claims-provider"></a>Paso 5: Incorporación de un proveedor de notificaciones 
Todos los proveedores de notificaciones deben tener uno o varios perfiles técnicos, que determinan los puntos de conexión y los protocolos necesarios para comunicarse con el proveedor de notificaciones. 

Un proveedor de notificaciones puede tener varios perfiles técnicos por distintas razones. Por ejemplo, se pueden definir varios perfiles técnicos porque el proveedor de notificaciones admita varios protocolos, los puntos de conexión pueden tener con distintas funcionalidades o las versiones pueden contener notificaciones que tienen diferentes niveles de seguridad. Puede ser aceptable liberar notificaciones confidenciales en un recorrido del usuario, pero no en otro. 

El siguiente fragmento de código de XML contiene un proveedor de reclamaciones con dos perfiles técnicos:

* **TechnicalProfile Id="REST-API-SignUp"**: define el servicio REST. 
   * `Proprietary` se describe como protocolo de un proveedor basado en REST. 
   * `InputClaims` define las notificaciones que se enviarán desde Azure AD B2C al servicio REST. 

   En este ejemplo, el contenido de la notificación `givenName` se envía al servicio REST como `firstName`, el contenido de la notificación `surname` se envía al servicio REST como `lastName` y `email` se envía tal cual. El elemento `OutputClaims` define las notificaciones que se recuperan del servicios REST para Azure AD B2C.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"**: agrega un perfil técnico de validación a un perfil técnico existente (definido en la directiva de base). Durante el recorrido del registro, el perfil técnico de validación invoca el perfil técnico anterior. Si el servicio REST devuelve un error HTTP 409 (un error de conflicto), el mensaje de error se muestra al usuario. 

Busque el nodo `<ClaimsProviders>` y agregue el siguiente fragmento de código XML al nodo `<ClaimsProviders>`:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
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
Edite el archivo del usuario de confianza (RP) *SignUpOrSignIn.xml* y modifique el elemento TechnicalProfile Id="PolicyProfile" para agregar lo siguiente: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Después de agregar la nueva notificación, el código del usuario de confianza será como el siguiente:

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

1. En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra **Azure AD B2C**.

2. Seleccione **Marco de experiencia de identidad**.

3. Abra **Todas las directivas**. 

4. Seleccione **Cargar directiva**.

5. Active la casilla **Sobrescribir la directiva si existe**.

6. Cargue el archivo TrustFrameworkExtensions.xml y asegúrese de que supera la validación.

7. Repita el paso anterior con el archivo SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Paso 8: Prueba de la directiva personalizada con Ejecutar ahora
1. Seleccione **Configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

    > [!NOTE]
    > **Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado, y seleccione **Ejecutar ahora**.

    ![La ventana de B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Pruebe el proceso escribiendo **Test** en el cuadro **Nombre propio**.  
    Azure AD B2C muestra un mensaje de error en la parte superior de la ventana.

    ![Prueba de la directiva](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  En el campo **Nombre propio**, escriba un nombre (que no sea "Test").  
    Azure AD B2C registra al usuario y envía un número de fidelidad a la aplicación. Observe el número de este JWT.

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Descargue el código y los archivos de la directiva completos
* Una vez completado el tutorial [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md), le recomendamos que compile su escenario mediante sus archivos de directiva personalizados. Hemos proporcionado [archivos de directiva de ejemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw) para que los tenga como referencia.
* El código completo se puede descargar en [Ejemplo de solución de Visual Studio para referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>pasos siguientes
* [Secure your RESTful API with basic authentication (username and password)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Protección de las API de RESTful con autenticación básica [nombre de usuario y contraseña])
* [Secure your RESTful API with client certificates](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) (Protección de las API de RESTful con certificados de cliente)
