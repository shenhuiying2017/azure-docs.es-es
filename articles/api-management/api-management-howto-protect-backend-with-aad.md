---
title: Protección de una API mediante OAuth 2.0 con Azure Active Directory API Management | Microsoft Docs
description: Obtenga información sobre cómo proteger un back-end de API web con Azure Active Directory y API Management.
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
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928043"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Protección de una API mediante OAuth 2.0 con Azure Active Directory API Management

En esta guía se muestra cómo configurar la instancia de Azure API Management para proteger una API mediante el protocolo OAuth 2.0 con Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>requisitos previos
Para seguir los pasos de este artículo, debe tener:
* Una instancia de API Management
* Una API que se vaya a publicar y que use la instancia de API Management
* Un inquilino de Azure AD

## <a name="overview"></a>Información general

Aquí se muestra una introducción rápida de los pasos:

1. Registre una aplicación (aplicación back-end) en Azure AD que represente la API.
2. Registre otra aplicación (aplicación cliente) en Azure AD que represente una aplicación de cliente que deba llamar a la API.
3. En Azure AD, conceda permisos para que la aplicación cliente llame a la aplicación back-end.
4. Configure la consola del desarrollador para utilizar la autorización de usuario OAuth 2.0.
5. Agregue la directiva **validate-jwt** para validar el token de OAuth para todas las solicitudes entrantes.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registro de una aplicación en Azure AD que represente la API

Para proteger una API con Azure AD, el primer paso es registrar una aplicación en Azure AD que la represente. 

1. Vaya a su inquilino de Azure AD y, después, a **Registros de aplicaciones**.

2. Seleccione **Nuevo registro de aplicaciones**. 

3. Proporcione el nombre de la aplicación. (En este ejemplo, el nombre es `backend-app`).  

4. Elija **Aplicación web o API** como **Tipo de aplicación**. 

5. Como **URL de inicio de sesión**, puede usar `https://localhost` como marcador de posición.

6. Seleccione **Crear**.

Una vez creada la aplicación, tome nota del **Id. de aplicación** para usarlo en un paso posterior. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registro de otra aplicación en Azure AD que represente una aplicación cliente

Todas las aplicaciones cliente que llamen a la API deben registrarse también como aplicación en Azure AD. En este ejemplo, la aplicación cliente de ejemplo es la consola del desarrollador en el portal para desarrolladores de API Management. A continuación se indica cómo registrar otra aplicación en Azure AD que represente la consola del desarrollador.

1. Seleccione **Nuevo registro de aplicaciones**. 

2. Proporcione el nombre de la aplicación. (En este ejemplo, el nombre es `client-app`).

3. Elija **Aplicación web o API** como **Tipo de aplicación**.  

4. Como **URL de inicio de sesión**, puede usar `https://localhost` como marcador de posición o la dirección URL de inicio de sesión de la instancia de API Management. (En este ejemplo, la dirección URL es `https://contoso5.portal.azure-api.net/signin`).

5. Seleccione **Crear**.

Una vez creada la aplicación, tome nota del **Id. de aplicación** para usarlo en un paso posterior. 

Ahora, cree un secreto de cliente para esta aplicación que usaremos en un paso posterior.

1. Seleccione **Configuración** de nuevo y vaya a **Claves**.

2. En **Contraseñas**, proporcione una **Descripción de la clave**. Elija cuando debe expirar la clave y seleccione **Guardar**.

Anote el valor de la clave. 

## <a name="grant-permissions-in-azure-ad"></a>Concesión de permisos en Azure AD

Ahora que hemos registrado dos aplicaciones que representan la API y la consola del desarrollador, es necesario conceder permisos para que la aplicación cliente pueda llamar a la aplicación back-end.  

1. Vaya a **Registros de aplicación**. 

2. Seleccione `client-app` y vaya a **Configuración**.

3. Seleccione **Permisos necesarios** > **Agregar**.

4. Seleccione **Seleccionar una API** y busque `backend-app`.

5. En **Permisos delegados**, seleccione `Access backend-app`. 

6. Seleccione **Seleccionar** y, después **Listo**. 

> [!NOTE]
> Si **Azure Active Directory** no aparece en los permisos para otras aplicaciones, haga clic en **Agregar** y agréguelo desde la lista.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Habilitación de la autorización de usuario OAuth 2.0 en la consola del desarrollador

En este punto ya se han creado nuestras aplicaciones en Azure AD y se han concedido los permisos pertinentes para que la aplicación cliente pueda llamar a la aplicación back-end. 

En este ejemplo, la consola del desarrollador es la aplicación cliente. En los pasos siguientes se describe la habilitación de la autorización de usuario OAuth 2.0 en la consola del desarrollador. 

1. Vaya a la instancia de API Management.

2. Seleccione **OAuth 2.0** > **Agregar**.

3. Proporcione un **Nombre para mostrar** y una **Descripción**.

4. Como **URL de la página de registro de cliente**, escriba un valor de marcador de posición como `http://localhost`. La **URL de la página de registro de cliente** señala a la página que los usuarios pueden utilizar para crear y configurar sus propias cuentas para proveedores de OAuth 2.0 que admiten esto. En este ejemplo, los usuarios no crean ni configuran sus propias cuentas, por lo que se usa un marcador de posición en su lugar.

5. En **Tipos de concesión de autorización**, seleccione **Código de autorización**.

6. Especifique la **URL del punto de conexión de autorización** y la **URL del punto de conexión de token**. Recupere estos valores desde la página **Puntos de conexión** del inquilino de Azure AD. Vaya a la página **Registros de aplicaciones** de nuevo y seleccione **Puntos de conexión**.

7. Copie el **punto de conexión de autorización de OAuth 2.0** y péguelo en el cuadro de texto **URL de punto de conexión de autorización**.

8. Copie el **punto de conexión de token de OAuth 2.0** y péguelo en el cuadro de texto **URL de punto de conexión del token**. Además de pegarlo en el punto de conexión del token, agregue un parámetro de cuerpo denominado **resource**. Como valor de este parámetro, use el **Id. de aplicación** para la aplicación de back-end.

9. A continuación, especifique las credenciales del cliente. Estas son las credenciales para la aplicación cliente.

10. Como **Id. de cliente**, use el **Id. de aplicación** de la aplicación cliente.

11. Como **secreto de cliente**, use la clave que creó para la aplicación cliente. 

12. Inmediatamente después del secreto del cliente se encuentra **redirect_url** como tipo de concesión de código de autorización. Anote esta dirección URL.

13. Seleccione **Crear**.

14. Vuelva a la página **Configuración** de la aplicación cliente.

15. Seleccione **URL de respuesta** y pegue el valor de **redirect_url** en la primera fila. En este ejemplo, ha reemplazado `https://localhost` por la dirección URL en la primera fila.  

Ahora que ha configurado un servidor de autorización de OAuth 2.0, la consola del desarrollador puede obtener tokens de acceso de Azure AD. 

El paso siguiente consiste en habilitar la autorización de usuario de OAuth 2.0 para la API. Esto permite que la consola del desarrollador sepa que debe obtener un token de acceso en nombre del usuario antes de realizar llamadas a la API.

1. Vaya a la instancia de API Management y a **API**.

2. Seleccione la API que desea proteger. En este ejemplo, puede usar `Echo API`.

3. Vaya a **Configuración**.

4. En **Seguridad**, elija **OAuth 2.0** y seleccione el servidor OAuth 2.0 que se configuró anteriormente. 

5. Seleccione **Guardar**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Llamada correcta a la API desde el portal para desarrolladores

Ahora que la autorización de usuario OAuth 2.0 está habilitada en `Echo API`, la consola del desarrollador obtiene un token de acceso para el usuario antes de llamar a la API.

1. Vaya a cualquier operación de `Echo API` en el portal para desarrolladores y, a continuación, seleccione **Probar**. Aparecerá la consola del desarrollador.

2. Tenga en cuenta el nuevo elemento de la sección **Autorización** correspondiente al servidor de autorización que acaba de agregar.

3. Seleccione **Código de autorización** de la lista desplegable de autorizaciones y se le pedirá que inicie sesión en el inquilino de Azure AD. Si ya ha iniciado sesión con la cuenta, es posible que no se le indique nada.

4. Al iniciar sesión correctamente, un encabezado `Authorization` se agregará a la solicitud con un token de acceso de Azure AD. El siguiente es un token de ejemplo (con codificación Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Seleccione **Enviar** y podrá llamar a la API correctamente.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar una directiva de validación de JWT para autorizar previamente las solicitudes

En este momento, si un usuario intenta realizar una llamada desde la consola del desarrollador, se le pide al usuario que inicie sesión. La consola del desarrollador obtiene un token de acceso en nombre del usuario.

Sin embargo, ¿qué ocurre si alguien llama a la API sin un token o con uno no válido? Por ejemplo, puede seguir llamando a la API incluso si elimina el encabezado `Authorization`. La razón es que API Management no valida el token de acceso en este momento. Simplemente pasa el encabezado `Authorization` a la API de back-end.

La directiva de [validación de JWT](api-management-access-restriction-policies.md#ValidateJWT) se puede usar para autorizar previamente las solicitudes en API Management, al validarse los tokens de acceso de cada solicitud entrante. Si una solicitud no tiene un token válido, API Management la bloquea. Por ejemplo, puede agregar la directiva siguiente a la sección de la directiva `<inbound>` de `Echo API`. Comprueba la notificación de audiencia en un token de acceso y devuelve un mensaje de error si el token no es válido. Para información sobre cómo configurar directivas, consulte el artículo sobre [edición o establecimiento de directivas](set-edit-policies.md).

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

En esta guía, se utiliza la consola del desarrollador en API Management como la aplicación de cliente de ejemplo para llamar a `Echo API` protegido por OAuth 2.0. Para más información sobre cómo compilar una aplicación e implementar OAuth 2.0, consulte [Ejemplos de código de Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre [Escenarios de autenticación para Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
* Consulte más [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre la administración de API.
* Para conocer otras formas de proteger el servicio back-end, consulte [Autenticación de certificado mutua](api-management-howto-mutual-certificates.md).

* [Creación de una instancia del servicio de API Management](get-started-create-service-instance.md).

* [Administración de su primera API en Administración de API de Azure](import-and-publish.md).
