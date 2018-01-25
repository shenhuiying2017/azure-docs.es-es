---
title: "Azure Active Directory B2C: protección de los servicios REST mediante certificados de cliente"
description: Proteja los intercambios de notificaciones de la API de REST personalizada en Azure AD B2C mediante certificados de cliente
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
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 582aadd35821779e307ac285804e3b7fe5c24abd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Proteja los servicios REST mediante certificados de cliente
En un artículo relacionado se [crea un servicio REST](active-directory-b2c-custom-rest-api-netfw.md) que interactúa con Azure Active Directory B2C (Azure AD B2C).

En este artículo, aprenderá a restringir el acceso a la aplicación web de Azure (API RESTful ) mediante el uso de un certificado de cliente. Este mecanismo se denomina autenticación mutua de TLS o *autenticación de certificado de cliente*. Solo pueden acceder al servicio los servicios que tienen certificados adecuados, como Azure AD B2C.

>[!NOTE]
>El servicio REST también se puede proteger ful con la [autenticación HTTP básica](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Sin embargo, la autenticación HTTP básica se considera menos segura con un certificado de cliente. Nuestra recomendación es proteger el servicio REST mediante la autenticación de certificado de cliente como se describe en este artículo.

En este artículo se detalla cómo:
* Configurar una aplicación web para que use la autenticación del certificado de cliente.
* Cargar el certificado en las claves de la directiva de Azure AD B2C.
* Configurar una directiva personalizada para usar el certificado de cliente.

## <a name="prerequisites"></a>requisitos previos
* Complete los pasos del artículo [Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C como un paso de orquestación](active-directory-b2c-custom-rest-api-netfw.md).
* Obtener tener un certificado válido (un archivo .pfx con clave privada).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Paso 1: Configuración de la aplicación web para la autenticación del certificado de cliente
Para configurar **Azure App Service** para que solicite certificados de cliente, el valor del sitio `clientCertEnabled` de la aplicación web debe ser *true*. Para realizar este cambio, debe utilizar la API de REST. El valor está disponible a través de la experiencia de administración de Azure Portal. Para buscar el valor, en el menú **Configuración** de la aplicación RESTful, en **Herramientas de desarrollo**, seleccione **Resource Explorer**.

>[!NOTE]
>Asegúrese de que el plan de Azure App Service es Estándar, o superior. Para más información, consulte [Introducción detallada sobre los planes de Azure App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Use [Azure Resource Explorer (Preview)](https://resources.azure.com) (Azure Resource Explorer [versión preliminar]) para establecer la propiedad **clientCertEnabled** en *true*, como se muestra en la imagen siguiente:

![Establecer clientCertEnabled a través de Azure Resource Explorer](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

>[!NOTE]
>Para más información acerca de cómo establecer la propiedad [clientCertEnabled](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth), consulte **Configuración de la autenticación mutua de TLS para una aplicación web**.

>[!TIP]
>Como alternativa, para simplificar la creación de la llamada a la API de REST se puede usar la herramienta [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Paso 2: Carga del certificado en las claves de directiva de Azure AD B2C.
Después de establecer `clientCertEnabled` en *true*, la comunicación con la API RESTful requiere un certificado de cliente. Para obtener, cargar y almacenar el certificado de cliente en el inquilino de Azure AD B2C, siga estos pasos: 
1. En su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.

2. Para ver las claves disponibles en el inquilino, seleccione **Policy Keys** (Claves de directiva).

3. Seleccione **Agregar**.  
    Se abre la ventana **Crear una clave**.

4. En el cuadro **Opciones**, seleccione **Cargar**.

5. En el cuadro **Nombre**, escriba **B2cRestClientCertificate**.  
    El prefijo *B2C_1A_* se agrega automáticamente.

6. En el cuadro **Carga de archivos**, seleccione el archivo .pfx del certificado con una clave privada.

7. En el cuadro **Contraseña**, escriba la contraseña del certificado.

    ![Carga de la clave de directiva](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Seleccione **Crear**.

8. Para ver las claves que están disponibles en su inquilino y confirmar que ha creado la clave `B2C_1A_B2cRestClientCertificate`, seleccione **Claves de directiva**.

## <a name="step-3-change-the-technical-profile"></a>Paso 3: Cambio del perfil técnico
Para admitir la autenticación de certificado de cliente en la directiva personalizada, cambie el perfil técnico como se indica a continuación:

1. Abra el archivo de directiva de extensión *TrustFrameworkExtensions.xml* desde el directorio de trabajo.

2. Busque el nodo `<TechnicalProfile>` que incluya `Id="REST-API-SignUp"`.

3. Busque el elemento `<Metadata>`.

4. Cambie el valor de *AuthenticationType* a *ClientCertificate*, como se muestra a continuación:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Agregue el siguiente fragmento de código XML inmediatamente después del elemento `<Metadata>` de cierre: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Tras agregar el fragmento de código, su perfil técnico debe ser similar al código XML siguiente:

    ![Configuración de los elementos XML de la autenticación ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Paso 4: Carga de la directiva en el inquilino

1. En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y seleccione **Azure AD B2C**.

2. Seleccione **Marco de experiencia de identidad**.

3. Seleccione **Todas las directivas**.

4. Seleccione **Cargar directiva**.

5. Active la casilla **Sobrescribir la directiva si existe**.

6. Cargue el archivo *TrustFrameworkExtensions.xml* y asegúrese de que pasa la validación.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Paso 5: Prueba de la directiva personalizada con Ejecutar ahora
1. Abra **Configuración de Azure AD B2C** y seleccione **Marco de experiencia de identidad**.

    >[!NOTE]
    >La función Ejecutar ahora requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado, y seleccione **Ejecutar ahora**.

3. Pruebe el proceso escribiendo **Test** en el cuadro **Nombre propio**.  
    Azure AD B2C muestra un mensaje de error en la parte superior de la ventana.    

    ![Prueba de la API de identidad](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. En el campo **Nombre propio**, escriba un nombre (que no sea "Test").  
    Azure AD B2C registra al usuario y envía un número de fidelidad a la aplicación. Observe el número de este ejemplo de JWT:

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

   >[!NOTE]
   >Si recibe el mensaje de error *The name is not valid, please provide a valid name* (El nombre no es válido, especifique un nombre válido), significa que Azure AD B2C ha llamado correctamente a su servicio REST mientras presentaba el certificado de cliente. El siguiente paso es validar el certificado.

## <a name="step-6-add-certificate-validation"></a>Paso 6: Incorporación de validación de certificados
El certificado de cliente que Azure AD B2C envía al servicio REST no pasa por ninguna validación de la plataforma Azure Web Apps, excepto comprobar que existe el certificado. La validación del certificado es responsabilidad de la aplicación web. 

En esta sección se añade código de ASP.NET de ejemplo que valida las propiedades del certificado para la autenticación.

> [!NOTE]
>Para más información acerca de cómo configurar Azure App Service para la autenticación de certificado de cliente, consulte [Configuración de la autenticación mutua de TLS para una aplicación web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Adición de la configuración de la aplicación al archivo web.config del proyecto
En el proyecto de Visual Studio que ha creado, agregue la siguiente configuración de la aplicación al archivo *web.config* después del elemento `appSettings`:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Reemplace los valores de **Nombre del sujeto**, **Nombre del emisor** y **Huella digital de certificado** por sus valores de certificado.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 Incorporación de la función IsValidClientCertificate
Abra el archivo *Controllers\IdentityController.cs* y agregue la siguiente función a la clase de controlador `Identity`: 

```csharp
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

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
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
    
    // 3. Check the issuer name of the certificate
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

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
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

En el código de ejemplo anterior, se acepta el certificado como válido solo si se cumplen todas las condiciones siguientes:
* El certificado no ha expirado y está activo durante la hora actual en el servidor.
* El nombre `Subject` del certificado es igual al valor de la configuración de la aplicación `ClientCertificate:Subject`.
* El nombre `Issuer` del certificado es igual al valor de la configuración de la aplicación `ClientCertificate:Issuer`.
* El valor de `thumbprint` del certificado es igual al valor de la configuración de la aplicación `ClientCertificate:Thumbprint`.

>[!IMPORTANT]
>En función de la confidencialidad del servicio, quizá deba agregar más validaciones. Por ejemplo, es posible que necesite comprobar si el certificado se encadena a una entidad de certificación raíz de confianza, la validación del nombre de la organización emisora de certificado, etc.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 Llamada a la función IsValidClientCertificate
Abra el archivo *Controllers\IdentityController.cs* y, después, al principio de la función `SignUp()`, agregue el siguiente fragmento de código: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Tras agregar el fragmento de código, su controlador `Identity` debería ser similar al código siguiente:

![Incorporación de código de validación de certificados](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Paso 7: Publicación de un proyecto en Azure y realización de pruebas del mismo
1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Contoso.AADB2C.API** y seleccione **Publicar**.

2. Repita el "paso 6" y vuelva a probar la directiva personalizada con la validación del certificado. Intente ejecutar la directiva y asegúrese de que todo funciona después de agregar la validación.

3. En el archivo *web.config*, cambie el valor de `ClientCertificate:Subject` a **invalid**. Vuelva a ejecutar la directiva y verá un mensaje de error.

4. Cambie el valor de nuevo a **valid** y asegúrese de que la directiva pueda llamar a la API de REST.

Si necesita solucionar problemas de este paso, consulte [Recopilación de registros mediante Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Descargue el código y los archivos de la directiva completos
* Una vez completado el tutorial [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md), le recomendamos que compile su escenario mediante sus archivos de directiva personalizados. Hemos proporcionado [archivos de directiva de ejemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert) para que los tenga como referencia.
* El código completo se puede descargar en [Ejemplo de solución de Visual Studio para referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
