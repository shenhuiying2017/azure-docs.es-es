---
title: Configuración del flujo de credenciales de contraseña de propietario del recurso en Azure AD B2C | Microsoft Docs
description: Averigüe cómo puede configurar el flujo de credenciales de contraseña de propietario del recurso en Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.openlocfilehash: c1b4d641f6830751e2cb9e66d5052eb20a48d371
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158261"
---
# <a name="configure-the-resource-owner-password-credentials-flow-ropc-in-azure-ad-b2c"></a>Configuración del flujo de credenciales de contraseña de propietario del recurso (ROPC) en Azure AD B2C

El flujo de credenciales de contraseña de propietario del recurso (ROPC) es un flujo de autenticación estándar de OAUTH donde la aplicación, también conocida como usuario de confianza, intercambia credenciales válidas, como el identificador de usuario y la contraseña para un token de identificador, un token de acceso y un token de actualización. 

> [!NOTE]
> Esta característica se encuentra en su versión preliminar.

En Azure AD B2C, se admiten las siguientes opciones:

- **Cliente nativo**: la interacción del usuario durante la autenticación se produce al usar código que se ejecuta en un dispositivo de usuario, que puede ser una aplicación móvil que se ejecute en el sistema operativo nativo, como Android, o en el explorador, como JavaScript.
- **Public client flow (Flujo de cliente público)**: en la llamada de API, solo se envían credenciales de usuario recopiladas por una aplicación. No se envían las credenciales de la aplicación.
- **Add new claims (Agregar nuevas notificaciones)**: se puede cambiar el contenido del token de identificador para agregar nuevas notificaciones. 

Los siguientes flujos no se admiten:

- **De servidor a servidor**: el sistema de protección de identidades (IDPS) necesita una dirección IP de confianza que recopile el autor de la llamada (el cliente nativo) como parte de la interacción.  En una llamada API del lado del servidor, solo se usa la dirección IP del servidor, y el IDPS puede identificar una dirección IP repetida como un atacante si se supera un umbral dinámico de autenticaciones con errores.
- **Confidential client flow (Flujo de cliente confidencial)**: se valida el identificador de cliente de la aplicación, pero no el secreto de la aplicación.

##  <a name="create-a-resource-owner-policy"></a>Creación de una directiva del propietario del recurso

1. Inicie sesión en Azure Portal como administrador global del inquilino de Azure AD B2C.
2. Para cambiar al inquilino de Azure AD B2C, seleccione el directorio B2C en la esquina superior derecha del portal.
3. En **Directivas**, seleccione **Directivas del propietario del recurso**.
4. Proporcione un nombre para la directiva, como *ROPC_Auth*, y, a continuación, haga clic en **Notificaciones de la aplicación**.
5. Seleccione las notificaciones de la aplicación que necesite para su aplicación, como el *nombre para mostrar*, la *dirección de correo electrónico* y el *proveedor de identidades*.
6. Haga clic en **Aceptar** y luego en **Crear**.

A continuación, verá un punto de conexión como en este ejemplo:

`https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Registro de una aplicación

1. En la configuración de B2C, haga clic en **Aplicaciones** y luego en **Agregar**.
2. Proporcione un nombre para la aplicación, como *ROPC_Auth_app*.
3. Haga clic en **No** para **API web o aplicación web** y haga clic en **Sí** para **Cliente nativo**.
4. Deje los demás valores como están y haga clic en **Crear**.
5. Seleccione la nueva aplicación y tome nota del identificador de la aplicación.

## <a name="test-the-policy"></a>Prueba de la directiva

Use su aplicación favorita de desarrollo de API para generar una llamada de API y revise la respuesta para depurar la directiva. Cree una llamada similar a esta con la información que se muestra en la tabla siguiente como el cuerpo de la solicitud POST:
- Reemplace *yourtenant.onmicrosoft.com* con el nombre del inquilino B2C
- Reemplace *B2C_1A_ROPC_Auth* con el nombre completo de la directiva ROPC
- Reemplace *bef2222d56-552f-4a5b-b90a-1988a7d634c3* con el identificador de la aplicación desde el registro.

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Clave | Valor |
| --- | ----- |
| nombre de usuario | leadiocl@outlook.com |
| contraseña | Passxword1 |
| grant_type | contraseña |
| ámbito | openid bef2222d56-552f-4a5b-b90a-1988a7d634c3 offline_access |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| response_type | id_token del token |

*Client_id* es el valor que anotó anteriormente como identificador de la aplicación. *Offline_access* es opcional si quiere recibir un token de actualización. 

La solicitud POST real tiene el siguiente aspecto:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: te.cpim.windows.net
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Una respuesta correcta con acceso sin conexión es similar a la siguiente:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Canjear un token de actualización

Cree una llamada POST similar a esta con la información que se muestra en la tabla siguiente como el cuerpo de la solicitud POST:

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Clave | Valor |
| --- | ----- |
| grant_type | refresh_token |
| response_type | ID_token |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| resource | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* y *resource* son los valores que anotó anteriormente como identificadores de la aplicación. *Refresh_token* es el token que recibió en la llamada de autenticación que se ha mencionado anteriormente.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementación con su SDK nativo preferido o uso de la autenticación de la aplicación


La implementación de Azure AD B2C cumple los estándares de OAuth 2.0 o el ROPC de cliente público y debe ser compatible con la mayoría de SDK de cliente.  Hemos probado este flujo (en producción) exhaustivamente con AppAuth para iOS y AppAuth para Android.  Consulte https://appauth.io/ para obtener la información más reciente.

Puede descargar ejemplos funcionales, que se han configurado para su uso con Azure AD B2C desde GitHub en https://aka.ms/aadb2cappauthropc para Android y https://aka.ms/aadb2ciosappauthropc.




