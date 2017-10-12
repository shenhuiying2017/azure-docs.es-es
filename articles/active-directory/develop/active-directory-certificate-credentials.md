---
title: Credenciales de certificado en Azure AD | Microsoft Docs
description: "Este artículo describe el registro y el uso de credenciales de certificados para la autenticación de aplicaciones"
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 08bb5140bb35bbd120aaa506afeab8ad247f81e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="certificate-credentials-for-application-authentication"></a>Credenciales de certificado para la autenticación de aplicaciones

Azure Active Directory permite que una aplicación use sus propias credenciales para la autenticación, por ejemplo, en el flujo de concesión de credenciales de cliente de OAuth 2.0 y el flujo en nombre de otro.
Un formato de credencial que se puede utilizar es una aserción de JSON Web Token (JWT) firmada con un certificado que la aplicación posea.

## <a name="format-of-the-assertion"></a>Formato de la aserción
Para calcular la aserción, es posible que desee utilizar una de las muchas bibliotecas de [JSON Web Token](https://jwt.io/) en el idioma que prefiera. La información que lleva el token es:

#### <a name="header"></a>Encabezado

| Parámetro |  Comentario |
| --- | --- | --- |
| `alg` | Debe ser **RS256** |
| `typ` | Debe ser **JWT** |
| `x5t` | Debe ser la huella digital SHA-1 del certificado X.509 |

#### <a name="claims-payload"></a>Notificaciones (carga útil)

| Parámetro |  Comentario |
| --- | --- | --- |
| `aud` | Público: debe ser **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Fecha de expiración: la fecha en que el token expira. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento en que la validez del token expira.|
| `iss` | Emisor: debe ser el identificador client_id (identificador de la aplicación del servicio de cliente) |
| `jti` | GUID: el identificador de JWT |
| `nbf` | No antes de: fecha antes de la cual el token no se puede usar. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento en que se emitió el token. |
| `sub` | Asunto: al igual que para `iss`, debe ser el identificador client_id (identificador de la aplicación del servicio de cliente) |

#### <a name="signature"></a>Firma
La firma se calcula aplicando el certificado como se describe en la [especificación RFC7519 de JSON Web Token](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>Ejemplo de una aserción de JWT descodificada
```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

### <a name="example-of-an-encoded-jwt-assertion"></a>Ejemplo de una aserción de JWT codificada
La cadena siguiente es un ejemplo de aserción codificada. Si observa detenidamente, verá tres secciones separadas por puntos (.).
La primera sección codifica el encabezado, la segunda la carga útil y la última es la firma calculada con los certificados a partir del contenido de las primeras dos secciones.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Registro del certificado con Azure AD
Para asociar las credenciales del certificado con la aplicación de cliente en Azure AD, debe editar el manifiesto de la aplicación.
Si tiene un certificado, debe calcular:
- `$base64Thumbprint`, que es la codificación base64 del hash del certificado
- `$base64Value`, que es la codificación base64 de los datos sin procesar del certificado

También deberá proporcionar un GUID para identificar la clave en el manifiesto de la aplicación (`$keyId`)

En el registro de aplicación de Azure para la aplicación cliente, abra el manifiesto de aplicación y reemplace la propiedad *keyCredentials* con la información del nuevo certificado usando el siguiente esquema:
```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
]
```

Guarde las modificaciones en el manifiesto de aplicación y cárguelo en Azure AD. La propiedad keyCredentials es multivalor, por lo que puede cargar varios certificados para una administración de claves más eficaz.
