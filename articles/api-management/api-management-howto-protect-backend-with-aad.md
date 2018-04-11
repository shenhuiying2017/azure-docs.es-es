---
title: Protección de una API mediante OAuth 2.0 con Azure Active Directory API Management | Microsoft Docs
description: Obtenga información sobre cómo proteger un back-end de API web con Azure Active Directory y la administración de API
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: 2c05407d761a8848f9e032aa219960cd7ea6fa93
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Protección de una API mediante OAuth 2.0 con Azure Active Directory API Management

En esta guía se muestra cómo configurar la instancia de API Management (APIM) para proteger una API mediante el protocolo OAuth 2.0 con Azure Active Directory (AAD). 

## <a name="prerequisite"></a>Requisito previo
Para seguir los pasos de este artículo, debe tener:
* Una instancia de APIM
* Una API que se publique mediante la instancia de APIM
* Un inquilino de Azure AD

## <a name="overview"></a>Información general

En esta guía se muestra cómo se protege una API mediante OAuth 2.0 en APIM. En este artículo, se usa Azure AD como servidor de autorización (servidor de OAuth). A continuación se muestra una introducción rápida de los pasos:

1. Registre una aplicación (aplicación back-end) en Azure AD que represente la API
2. Registre otra aplicación (aplicación cliente) en Azure AD que represente una aplicación de cliente que deba llamar a la API
3. En Azure AD, conceda permisos para que la aplicación cliente llame a aplicación back-end
4. Configure la consola del desarrollador para utilizar la autorización de usuario OAuth 2.0
5. Agregue la directiva validate-jwt para validar el token de OAuth para todas las solicitudes entrantes

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registro de una aplicación en Azure AD que represente la API

Para proteger una API con Azure AD, el primer paso es registrar una aplicación en Azure AD que la represente. 

Vaya a su inquilino de Azure AD y a **Registros de aplicaciones**.

Seleccione **Nuevo registro de aplicaciones**. 

Proporcione el nombre de la aplicación. En este ejemplo, se usa `backend-app`.  

Elija **Aplicación web o API** como **Tipo de aplicación**. 

Como **URL de inicio de sesión**, puede usar `https://localhost` como marcador de posición.

Haga clic en **Crear**.

Una vez creada la aplicación, tome nota del **Id. de aplicación** para usarlo en un paso posterior. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registro de otra aplicación en Azure AD que represente una aplicación cliente

Todas las aplicaciones cliente que deban llamar a la API deben registrarse también como aplicación en Azure AD. En esta guía, se usará la consola del desarrollador en el portal para desarrolladores de APIM como aplicación cliente de ejemplo. 

Debemos registrar otra aplicación en Azure AD que represente la consola del desarrollador.

Haga clic en **Nuevo registro de aplicaciones**. 

Proporcione el nombre de la aplicación y elija **Aplicación web o API** como **Tipo de aplicación**. En este ejemplo, se usa `client-app`.  

Como **URL de inicio de sesión**, puede usar `https://localhost` como marcador de posición o la dirección URL de inicio de sesión de la instancia de APIM. En este ejemplo, se usa `https://contoso5.portal.azure-api.net/signin`.

Haga clic en **Crear**.

Una vez creada la aplicación, tome nota del **Id. de aplicación** para usarlo en un paso posterior. 

Ahora debemos crear un secreto de cliente para esta aplicación que usaremos en un paso posterior.

Haga clic en **Configuración** de nuevo y vaya a **Claves**.

En **Contraseñas**, proporcione una **Descripción de la clave**, elija la fecha de expiración de la clave y haga clic en **Guardar**.

Anote el valor de la clave. 

## <a name="grant-permissions-in-aad"></a>Concesión de permisos en AAD

Ahora que hemos registrado dos aplicaciones que representan la API (la aplicación back-end) y la consola del desarrollador (la aplicación cliente), es necesario conceder permisos para que la aplicación cliente pueda llamar a la aplicación back-end.  

Vaya a **Registros de aplicaciones** de nuevo. 

Haga clic en `client-app` y vaya a **Configuración**.

Haga clic en **Permisos necesarios** y en **Agregar**.

Haga clic en **Seleccionar una API** y busque `backend-app`.

Marque `Access backend-app` en **Permisos delegados**. 

Haga clic en **Seleccionar** y en **Listo**. 

> [!NOTE]
> Si **Windows** **Azure Active Directory** no aparece en los permisos para otras aplicaciones, haga clic en **Agregar** y agréguelo de la lista.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Habilitación de la autorización de usuario OAuth 2.0 en la consola del desarrollador

En este punto ya se han creado nuestras aplicaciones en Azure AD y se han concedido los permisos pertinentes para que la aplicación cliente pueda llamar a la aplicación back-end. 

En esta guía, se usará la consola del desarrollador como aplicación cliente. Con los pasos siguientes se describe la habilitación de la autorización de usuario OAuth 2.0 en la consola del desarrollador 

Vaya a la instancia de APIM.

Haga clic en **OAuth 2.0** y en **Agregar**.

Proporcione un **Nombre para mostrar** y una **Descripción**.

Como URL de página de registro de cliente,** escriba un valor de marcador de posición como `http://localhost`.  La **URL de la página de registro de cliente** señala a la página que los usuarios pueden utilizar para crear y configurar sus propias cuentas para proveedores de OAuth 2.0 que admiten la administración de usuarios de las cuentas. En este ejemplo, los usuarios no crean ni configuran sus propias cuentas, por lo que se usa un marcador de posición.

Marque **Código de autorización** como **Tipos de concesión de autorización**.

A continuación, especifique la **URL del punto de conexión de autorización** y la **URL del punto de conexión de token**.

Estos valores se pueden recuperar desde la página **Puntos de conexión** del inquilino de Azure AD. Para acceder a los puntos de conexión, vaya a la página **Registros de aplicaciones** de nuevo y haga clic en **Puntos de conexión**.

Copie el **punto de conexión de autorización de OAuth 2.0** y péguelo en el cuadro de texto **URL de punto de conexión de autorización**.

Copie el **punto de conexión de token de OAuth 2.0** y péguelo en el cuadro de texto **URL de punto de conexión del token**.

Además de pegarlo en el punto de conexión del token, agregue un parámetro de cuerpo adicional denominado **resource** y, como valor, use el **Id. de aplicación** de la aplicación back-end.

A continuación, especifique las credenciales del cliente. Estas son las credenciales para la aplicación cliente.

Como **Id. de cliente**, use el **Id. de aplicación** de la aplicación cliente.

Como **secreto de cliente**, use la clave que creó para la aplicación cliente. 

Inmediatamente después del secreto del cliente se encuentra **redirect_url** como tipo de concesión de código de autorización.

Anote esta dirección URL.

Haga clic en **Crear**.

Vuelva a la página **Configuración** de la aplicación cliente.

Haga clic en **URL de respuesta** y pegue el valor de **redirect_url** en la primera fila. En este ejemplo, se reemplaza `https://localhost` por la dirección URL en la primera fila.  

Ahora que hemos configurado un servidor de autorización de OAuth 2.0, la consola del desarrollador debería poder obtener tokens de acceso de Azure AD. 

El paso siguiente consiste en habilitar la autorización de usuario OAuth 2.0 para la API, para que la consola del desarrollador sepa que debe obtener un token de acceso para el usuario antes de realizar llamadas a la API.

Vaya a la instancia de APIM y a **API**.

Haga clic en la API que desea proteger. En este ejemplo, usaremos `Echo API`.

Vaya a **Configuración**.

En **Seguridad**, elija **OAuth 2.0** y seleccione el servidor OAuth 2.0 que se configuró anteriormente. 

Haga clic en **Save**(Guardar).

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Llamada correcta a la API desde el portal para desarrolladores

Ahora que la autorización de usuario OAuth 2.0 está habilitada en `Echo API`, la consola del desarrollador obtendrá un token de acceso para el usuario antes de llamar a la API.

Vaya a cualquier operación de `Echo API` (en el portal para desarrolladores) y haga clic en **Pruébelo** para ir a la consola del desarrollador.

Tenga en cuenta el nuevo elemento de la sección **Autorización** correspondiente al servidor de autorización que acaba de agregar.

Seleccione **Código de autorización** de la lista desplegable de autorizaciones y se le pedirá que inicie sesión en el inquilino de Azure AD. Si ya ha iniciado sesión con la cuenta, es posible que no se le indique nada.

Al iniciar sesión correctamente, un encabezado `Authorization` se agregará a la solicitud con un token de acceso de Azure AD. 

Un token de ejemplo se parece al siguiente, está codificado en Base64.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Haga clic en **Enviar** y podrá llamar correctamente a la API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar una directiva de validación de JWT para autorizar previamente las solicitudes

En este punto, cuando un usuario intente realizar una llamada desde la consola del desarrollador, se le pedirá que inicie sesión y la consola del desarrollador obtendrá un token de acceso para el usuario. Todo funciona con normalidad. Sin embargo, ¿qué ocurre si alguien llama a la API sin un token o con uno no válido? Por ejemplo, puede probar a eliminar el encabezado `Authorization` y verá que aún puede llamar a la API. La razón es que APIM no valida el token de acceso en este momento. Simplemente pasa el encabezado `Auhtorization` a la API de back-end.

La directiva de [validación de JWT](api-management-access-restriction-policies.md#ValidateJWT) se puede usar para autorizar previamente las solicitudes en APIM, al validarse los tokens de acceso de cada solicitud entrante. Si una solicitud no tiene un token válido, API Management la bloquea y no pasa al back-end. Por ejemplo, podemos agregar la directiva siguiente a la sección de la directiva `<inbound>` de `Echo API`. Comprueba la notificación de audiencia en un token de acceso y devuelve un mensaje de error si el token no es válido. Para información sobre cómo configurar directivas, consulte el artículo sobre [edición o establecimiento de directivas](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>Compilación de una aplicación para llamar a la API

En esta guía, se utiliza la consola de desarrollador en APIM como la aplicación de cliente de ejemplo para llamar a `Echo API` protegido por OAuth 2.0. Para obtener más información sobre cómo compilar una aplicación e implementar el flujo de OAuth 2.0, consulte [Ejemplos de código de Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre [Escenarios de autenticación para Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
* Consulte más [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre la administración de API.
* Para conocer otras formas de proteger el servicio back-end, consulte [Autenticación de certificado mutua](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
