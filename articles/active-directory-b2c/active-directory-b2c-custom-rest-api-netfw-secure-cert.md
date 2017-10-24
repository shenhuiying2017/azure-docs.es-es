---
title: "Azure Active Directory B2C: protección de los servicios RESTful con certificados de cliente"
description: "Ejemplo de cómo proteger los intercambios de notificaciones de la API de REST personalizada en Azure AD B2C mediante certificados de cliente"
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
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C: protección de los servicios RESTful con certificados de cliente
Después de [crear un servicio RESTful](active-directory-b2c-custom-rest-api-netfw.md) para interactuar con Azure AD B2C, le mostraremos cómo restringir el acceso a la aplicación web de Azure (API de RESTful) con un certificado de cliente. Este mecanismo se denomina autenticación mutua de TLS o **autenticación de certificado de cliente**.  Solo los servicios, como Azure AD B2C, con el certificado adecuado podrán tener acceso a su servicio.

> [!NOTE]
>
>También puede proteger el servicio RESTful con la [autenticación básica HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Sin embargo, debe saber que la autenticación básica HTTP se considera menos segura con un certificado de cliente. Nuestra recomendación es proteger el servicio RESTful mediante la autenticación de certificado de cliente tal y como se describe en este artículo.

En este artículo se detalla cómo:
1. Configurar la aplicación web para usar la autenticación de certificado de cliente.
1. Cargar el certificado en las claves de directiva de Azure AD B2C.
1. Configurar la directiva personalizada para usar el certificado de cliente.

## <a name="prerequisites"></a>Requisitos previos
* Complete los pasos anteriores del artículo [Integración de intercambios de notificaciones de API de REST](active-directory-b2c-custom-rest-api-netfw.md).
* Debe tener un certificado válido (archivo .pfx con clave privada).

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>Paso 1: Configuración de la aplicación web para la autenticación de certificado de cliente
Para configurar **Azure App Service** y que requiera certificados de cliente, la configuración del sitio `clientCertEnabled` de la aplicación web debe ser true. Esta configuración no está actualmente disponible a través de la experiencia de administración en Azure Portal y deberá usarse la API de REST para realizar este cambio.

> [!NOTE]
>
>Asegúrese de que su plan de Azure App Service sea Estándar o superior. Para más información, consulte [Introducción detallada sobre los planes de Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Use [Azure Resource Explorer (Preview)](https://resources.azure.com) (Azure Resource Explorer [versión preliminar]) para cambiar la propiedad clientCertEnabled a true. La siguiente captura de pantalla muestra cómo establecer el valor clientCertEnabled ![configurando clientCertEnabled en Azure Resource Explorer](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png).

> [!NOTE]
>
>Para más información acerca de cómo establecer el atributo clientCertEnabled, consulte [Configuración de la autenticación mutua de TLS para una aplicación web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth).


> [!TIP]
>
>También puede usar la [herramienta ARMClient](https://github.com/projectkudu/ARMClient) para simplificar la creación de la llamada a la API de REST.

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Paso 2: Carga del certificado en las claves de directiva de Azure AD B2C.
Después de establecer `clientCertEnabled` en `true`, la comunicación con la API de RESTful requiere un certificado de cliente. Para ello, debe cargar y almacenar el certificado de cliente en el inquilino de Azure AD B2C.   
1.  Vaya a su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.
2.  Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3.  Haga clic en **+Agregar**.
4.  En **Opciones**, use **Cargar**.
5.  En **Nombre**, use `B2cRestClientCertificate`.  
    El prefijo `B2C_1A_` se agrega automáticamente.
6.  En **Carga de archivos**, seleccione el archivo .pfx de certificado con clave privada. Proporcione también la **contraseña** del certificado.

    ![Carga de la clave de directiva](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  Haga clic en **Crear**
8.  Para confirmar que ha creado la clave `B2C_1A_B2cRestClientCertificate`, seleccione **Claves de directiva** para ver las claves disponibles en el inquilino.

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>Paso 3: Cambio de `TechnicalProfile` para que admita la autenticación de certificado de cliente en la directiva de extensión
1.  Abra el archivo de directiva de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo.
2.  Busque el nodo `<TechnicalProfile>` que se incluye con `Id="REST-API-SignUp"`.
3.  Localice el elemento `<Metadata>`.
4.  Cambie `AuthenticationType` a `ClientCertificate`.

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  Agregue el siguiente fragmento de código XML inmediatamente después del cierre del elemento `<Metadata>`:  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

Después de agregar los fragmentos de código XML, `TechnicalProfile` debería ser similar a:

![Configuración de los elementos XML de la autenticación ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Paso 4: Carga de la directiva en el inquilino

1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y haga clic en **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Haga clic en **Todas las directivas**.
4.  Seleccione **Cargar directiva**
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Paso 5: Prueba de la directiva personalizada con Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

    >[!NOTE]
    >
    >    **Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. 
    >    Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.

3.  Intente escribir "Test" en el campo **Nombre propio**, B2C muestra un mensaje de error en la parte superior de la página.

    ![Prueba de la API de identidad](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  Intente escribir un nombre (distinto de "Test") en el campo **Nombre propio**. B2C registra al usuario y envía loyaltyNumber a la aplicación. Observe el número de este JWT en este ejemplo.

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

   > [!NOTE]
   >Si aparece el mensaje de error anterior, significa que Azure AD B2C llama correctamente a su servicio RESTful mientras presenta el certificado de cliente. El siguiente paso es validar el certificado.

## <a name="step-6-adding-certificate-validation"></a>Paso 6: Adición de validación de certificados
El certificado de cliente que Azure AD B2C envía al servicio RESTful no pasa por ninguna validación de la plataforma Azure Web Apps (excepto para comprobar que existe el certificado). La validación del certificado es responsabilidad de la aplicación web. Este es el código de ASP.NET de ejemplo que valida las propiedades del certificado para la autenticación.

> [!NOTE]
>Para más información sobre cómo configurar Azure App Service para la autenticación de certificado de cliente, consulte [Configuración de la autenticación mutua de TLS para una aplicación web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1 Incorporación de la configuración de la aplicación al archivo web.config del proyecto
Abra el proyecto de Visual Studio que creó anteriormente y agregue la siguiente configuración de la aplicación al archivo web.config en el elemento `appSettings`.

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Reemplace los valores del certificado:
* Nombre del firmante
* Nombre del emisor
* Huella digital de certificado

### <a name="62-add-isvalidclientcertificate-function"></a>6.2 Incorporación de la función IsValidClientCertificate
Abra el archivo Controllers\IdentityController.cs y agregue la siguiente función a la clase de controlador `Identity`. 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check issuer name of certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

En el anterior **ejemplo**, solo se acepta el certificado como válido si se cumplen todas las condiciones:
1. El certificado no ha expirado y está activo durante la hora actual en el servidor.
2. El nombre `Subject` del certificado tiene el nombre común igual al valor de configuración de la aplicación `ClientCertificate:Subject`.
3. El nombre `Issuer` del certificado tiene el nombre común igual al valor de configuración de la aplicación `ClientCertificate:Issuer`.
4. `thumbprint` del certificado es igual al valor de configuración de la aplicación `ClientCertificate:Thumbprint`.

> [!IMPORTANT]
>
>En función de la confidencialidad de su servicio, quizá deba agregar más validaciones. Por ejemplo: comprobación de si el certificado se encadena a una entidad de certificación raíz de confianza, validación del nombre de la organización emisora de certificado, etc.

### <a name="63-add-isvalidclientcertificate-function"></a>6.3 Incorporación de la función IsValidClientCertificate
Abra Controllers\IdentityController.cs y, al principio de la función `SignUp()`, agregue las líneas de código siguientes. 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Después de agregar los fragmentos de código, el controlador `Identity` debería ser similar a:

![Incorporación de código de validación de certificados](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>Paso 7: Publicación en Azure y prueba
1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Contoso.AADB2C.API** y seleccione **Publicar**.
2. Repita el paso 6 y pruebe la directiva personalizada (de nuevo) con la validación del certificado. Intente ejecutar la directiva y asegúrese de que todo funcione después de agregar la validación.
3. Ahora, en el archivo web.config, cambie el valor de `ClientCertificate:Subject` al valor **invalid**. Vuelva a ejecutar la directiva y verá el mensaje de error.
4. Cambie el valor de nuevo al valor **valid** y asegúrese de que la directiva pueda llamar a la API de REST
5. Solucionar problemas [recopilando registros con Application Insights](active-directory-b2c-troubleshoot-custom.md)

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Opcional] Descarga de los archivos y código de la directiva completos
* Se recomienda que, en lugar de usar estos archivos de ejemplo, cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas.  [Archivos de directiva de ejemplo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* Puede descargar aquí el código completo [Ejemplo de solución de Visual Studio para referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)