<properties
   pageTitle="Metadatos de federación"
   description="Una descripción de los extremos de metadatos y el contenido del documento de metadatos que deben usar los servicios que aceptan tokens de Azure AD."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/30/2015"
   ms.author="mbaldwin"/>

# Metadatos de federación
Azure Active Directory (Azure AD) publica un documento de metadatos de federación para los servicios que están configurados para aceptar los tokens de seguridad que emite Azure Active Directory. El formato del documento de metadatos de federación se describe en el [lenguaje de federación de servicios web (WS-Federation) versión 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que extiende los [metadatos para el lenguaje de marcado de aserción de seguridad (SAML) de OASIS V2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

En este tema se enumeran los extremos de metadatos y se explica el contenido del documento de metadatos que necesitan usar los servicios que aceptan tokens de Azure AD.

##Extremos de metadatos específicos del inquilino e independientes del inquilino

Azure AD publica los extremos específicos del inquilino e independientes del inquilino. Los extremos específicos del inquilino están diseñados para un inquilino determinado. Los metadatos de federación específicos del inquilino incluyen información sobre el inquilino, incluida la información sobre el emisor y el extremo específica del inquilino. Las aplicaciones que restringen el acceso a un solo inquilino utilizan extremos específicos del inquilino.

Los extremos independientes del inquilino proporcionan información que es común a todos los inquilinos de Azure AD. Esta información se aplica a los inquilinos hospedados en *login.windows.net* y se comparte entre los inquilinos. Los extremos independientes del inquilino se recomiendan para aplicaciones de varios inquilinos, ya que no están asociados a ningún inquilino en particular.

## Extremos de metadatos de federación

Azure AD publica los metadatos de federación en *https://login.windows.net/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml*, donde el valor de <TenantDomainName> puede ser "común" o un valor específico del inquilino. Los extremos son direccionables; por tanto, puede ir al sitio de la dirección para ver los metadatos de federación para un inquilino.

En el caso de los **extremos específicos del inquilino**, el <TenantDomainName> puede ser uno de los siguientes tipos:

- Un nombre de dominio registrado de un inquilino de Azure AD, como contoso.onmicrosoft.com.

- El identificador del inquilino inmutable del dominio, como 72f988bf-86f1-41af-91ab-2d7cd011db45.

En el caso de los **extremos independientes del inquilino **, el <TenantDomainName> es **común**. Este nombre indica que los elementos de metadatos de federación que son comunes a todos los inquilinos de Azure AD son los únicos que se hospedan en login.windows.net.

Por ejemplo, un extremo específico del inquilino podría ser *https://login.windows.net/contoso.onmicrosoft.comFederationMetadata/2007-06/FederationMetadata.xml*. El extremo independiente del inquilino es *https://login.windows.net/common/FederationMetadata/2007-06/FederationMetadata.xml*.

## Contenido de los metadatos de federación

La siguiente sección proporciona la información necesaria para los servicios que consumen los tokens emitidos por Azure AD.

### EntityID

El elemento **EntityDescriptor** contiene un atributo **EntityID**. El valor del atributo **EntityID** representa al emisor; es decir, al servicio de token de seguridad (STS) que emitió el token. Es importante validar al emisor para poder confirmar qué inquilino emitió un token.

Los metadatos siguientes muestran un ejemplo de un elemento **EntityDescriptor** específico del inquilino con un elemento **EntityID**.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b" 
    entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">

El **EntityID** en el extremo independiente del inquilino proporciona una plantilla que puede utilizarse para generar un valor **EntityID** específico del inquilino. Reemplace el literal "{inquilino}" del extremo independiente del inquilino por el valor de la notificación **TenantID** de un token. El valor resultante será el mismo que el emisor del token. Esta estrategia permite a una aplicación de varios inquilinos validar al emisor de un inquilino determinado.

Los metadatos siguientes muestran un ejemplo de un elemento **EntityID** independiente del inquilino. En este elemento, {inquilino} es un literal, no un marcador de posición.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd" 
    entityID="https://sts.windows.net/{tenant}/">

### Certificado de firma de tokens
Cuando un servicio recibe un token emitido por un inquilino de Azure AD, la firma del token debe validarse con una clave de firma que se publica en el documento de metadatos de federación.

Los metadatos de federación incluyen la parte pública de los certificados que utilizan los inquilinos para la firma de tokens. Los bytes sin formato del certificado aparecen en el elemento **KeyDescriptor**. El certificado de la firma del token es válido para la firma solo cuando el valor del atributo **use** es **signing**.

Un documento de metadatos de federación publicado por Azure AD puede tener varias claves de firma, como en aquellos casos en que Azure AD se está preparando para actualizar el certificado de firma. Cuando un documento de metadatos de federación incluye más de un certificado, un servicio que está validando los tokens debe admitir todos los certificados del documento.

Los metadatos siguientes muestran un ejemplo del elemento **KeyDescriptor** con una clave de firma.

    <KeyDescriptor use="signing">
    <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
    <X509Certificate>
    MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
    </X509Certificate>
    </X509Data>
    </KeyInfo>
    </KeyDescriptor>

El elemento **KeyDescriptor** aparece en dos lugares en el documento de metadatos de federación; en la sección específica de WS-Federation y la sección específica de SAML. Los certificados publicados en ambas secciones serán el mismo.

En la sección específica de WS-Federation, un lector de metadatos de WS-Federation leería los certificados de un elemento **RoleDescriptor** con el tipo **SecurityTokenServiceType**.

Los metadatos siguientes muestran un ejemplo del elemento **RoleDescriptor**.

    <RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706"
    xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">`

En la sección específica de SAML, un lector de metadatos de WS-Federation leería los certificados de un elemento **IDPSSODescriptor**.

Los metadatos siguientes muestran un ejemplo del elemento **IDPSSODescriptor**.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">

No existen diferencias en el formato de los certificados específicos del inquilino e independientes del inquilino.

### Dirección URL del extremo de WS-Federation
Los metadatos de federación incluyen la dirección URL que utiliza Azure AD para el inicio de sesión único y el cierre de sesión único en el protocolo WS-Federation. Este extremo aparece en el elemento **PassiveRequestorEndpoint**.

Los metadatos siguientes muestran un ejemplo del elemento **PassiveRequestorEndpoint** para un extremo específico del inquilino.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

Para el extremo independiente del inquilino, la dirección URL de WS-Federation aparece en el extremo de WS-Federation, como se muestra en el ejemplo siguiente.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/common/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

### Dirección URL del extremo de protocolo SAML

Los metadatos de federación incluyen la dirección URL que utiliza Azure AD para el inicio de sesión único y el cierre de sesión único en el protocolo SAML 2.0. Estos extremos aparecen en el elemento **IDPSSODescriptor**.

Las direcciones URL de inicio de sesión y cierre de sesión aparecen en los elementos **SingleSignOnService** y **SingleLogoutService**. Los metadatos siguientes muestran un ejemplo de **PassiveResistorEndpoint** para un extremo específico del inquilino.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https:// login.windows.net/contoso.onmicrosoft.com /saml2" />
    </IDPSSODescriptor>

Del mismo modo, los extremos comunes del protocolo SAML 2.0 se publican en los metadatos de federación independientes del inquilino, tal como se muestra en el ejemplo siguiente.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    </IDPSSODescriptor>

## Otras referencias
[Protocolos de autenticación de Azure Active Directory](active-directory-authentication-protocols.md)

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

<!---HONumber=July15_HO4-->