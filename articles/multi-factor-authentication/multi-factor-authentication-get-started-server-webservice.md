---
title: "Servicio web de aplicación móvil del Servidor Azure MFA | Microsoft Docs"
description: "La aplicación Microsoft Authenticator ofrece una opción de autenticación fuera de banda adicional.  Permite al Servidor MFA utilizar notificaciones push en los usuarios."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 83b04e48dd528881097bcf16bc03e1a18ea20c43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Habilitación de la autenticación de aplicación móvil con Servidor Azure Multi-Factor Authentication

La aplicación Microsoft Authenticator ofrece una opción de comprobación fuera de banda adicional. En lugar de realizar una llamada de teléfono automatizada o SMS al usuario durante el inicio de sesión, Azure Multi-Factor Authentication inserta una notificación en la aplicación Microsoft Authenticator en el smartphone o en una tableta del usuario. El usuario simplemente toca **Comprobar** (o escribe un PIN y toca "Autenticar") en la aplicación para completar el inicio de sesión.

Se recomienda usar una aplicación móvil para la verificación en dos pasos cuando la recepción telefónica es poco confiable. Si usa la aplicación como generador del token OATH, no se requiere ninguna conexión a Internet o una red.

Según su entorno, puede que desee implementar el servicio web de aplicación móvil en el mismo servidor que el Servidor Microsoft Azure Multi-Factor Authentication o en otro servidor con conexión a Internet.

## <a name="requirements"></a>Requisitos

Para usar la aplicación Microsoft Authenticator, se requiere lo siguiente para que pueda comunicarse correctamente con el servicio web de aplicación móvil:

* Servidor Azure Multi-Factor Authentication v6.0 o superior.
* Debe instalar el servicio web de aplicación móvil en un servidor web con conexión a Internet con Microsoft® [Internet Information Services (IIS) 7.x o superior](http://www.iis.net/).
* ASP.NET v4.0.30319 debe estar instalado, registrado y establecido en Permitido.
* Entre los servicios de rol necesarios, se incluyen ASP.NET y Compatibilidad con la metabase de IIS 6.
* Se debe poder tener acceso al servicio web de aplicación móvil mediante una dirección URL pública.
* El servicio web de aplicación móvil debe estar protegido con un certificado SSL.
* Debe instalar el SDK del servicio web de Azure Multi-Factor Authentication en IIS 7.x o superior en el **mismo servidor donde esté instalado el Servidor Azure Multi-Factor Authentication**.
* El SDK del servicio web de Azure Multi-Factor Authentication debe estar protegido con un certificado SSL.
* El servicio web de aplicación móvil debe ser capaz de conectarse al SDK del servicio web de Azure Multi-Factor Authentication a través de SSL.
* El servicio web de aplicación móvil debe ser capaz de autenticarse con el SDK del servicio web de Azure MFA mediante las credenciales de una cuenta de servicio que pertenezca a un grupo de seguridad llamado "PhoneFactor Admins". La cuenta de servicio y el grupo existen en Active Directory si Servidor Azure Multi-Factor Authentication se encuentra en un servidor unido al dominio. La cuenta de servicio y el grupo existen localmente en Servidor Azure Multi-Factor Authentication si no está unido al dominio.

## <a name="install-the-mobile-app-web-service"></a>Instalación del servicio web de aplicación móvil

Antes de instalar el servicio web de aplicación móvil, tenga en cuenta los detalles siguientes:

* Necesita una cuenta de servicio que forme parte del grupo "PhoneFactor Admins". Esta cuenta puede ser la misma que la utilizada para la instalación del Portal de usuarios.
* Resulta útil abrir un explorador web en un servidor web con conexión a Internet e ir a la dirección URL del SDK del servicio web que se especificó en el archivo web.config. Si el explorador puede obtener acceso al servicio web correctamente, debería solicitarle credenciales. Escriba el nombre de usuario y la contraseña que se especificaron en el archivo web.config tal y como aparecen en el archivo. Asegúrese de que no aparezca ningún error ni advertencia de certificado.
* Si existe un proxy inverso o un firewall en el servidor web del servicio web de aplicación móvil que está realizando la descarga de SSL, puede editar el archivo web.config del servicio web de aplicación móvil para que este pueda usar http en lugar de https. SSL sigue siendo necesario en la aplicación móvil para el firewall o proxy inverso. Agregue la siguiente clave a la sección \<appSettings\>:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Instalación del SDK del servicio web

En ambos escenarios, si el SDK del servicio web de Azure Multi-Factor Authentication aún **no** está instalado en el Servidor Azure Multi-Factor Authentication (MFA), siga los pasos que se indican a continuación.

1. Abra la consola del Servidor Multi-Factor Authentication.
2. Vaya a **Web Service SDK** (SDK del servicio web) y seleccione **Install Web Service SDK** (Instalar SDK del servicio web).
3. Complete la instalación mediante los valores predeterminados a menos que necesite cambiarlos por algún motivo.
4. Enlace un certificado SSL al sitio en IIS.

Si tiene alguna pregunta acerca de cómo configurar un certificado SSL en un servidor IIS, consulte el artículo [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Configuración de un certificado SSL en IIS)///.

El SDK del servicio web debe estar protegido con un certificado SSL. Un certificado autofirmado es adecuado para este propósito. Importe el certificado al almacén "Entidades de certificación de raíz de confianza" de la cuenta Equipo local en el servidor web del Portal de usuarios para que confíe en ese certificado cuando inicie la conexión SSL.

![SDK del servicio web de configuración del Servidor MFA](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>Instalación del servicio

1. **En el servidor MFA**, vaya a la ruta de instalación.
2. Vaya a la carpeta donde está instalado el servidor Azure MFA, el valor predeterminado es **C:\Archivos de programa\Azure Multi-factor Authentication**.
3. Busque el archivo de instalación **MultiFactorAuthenticationMobileAppWebServiceSetup64**. Si el servidor **no** dispone de conexión a Internet, copie el archivo de instalación en el servidor que sí dispone de ella.
4. Si el servidor MFA **no** dispone de conexión a Internet cambie al **servidor con conexión a Internet**.
5. Ejecute el archivo de instalación **MultiFactorAuthenticationMobileAppWebServiceSetup64** como administrador, cambie el sitio si lo desea y modifique el directorio Virtual a un nombre corto, si lo prefiere.
6. Después de finalizar la instalación, vaya a **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (o el directorio correspondiente según el nombre del directorio virtual) y edite el archivo Web.Config.

   * Busque la clave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** y cambie **value=""** a **value="DOMAIN\User"** donde "DOMAIN\User" es una cuenta de servicio que forma parte del grupo "PhoneFactor Admins".
   * Busque la clave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** y cambie **value=""** a **value="Password"** donde Password es la contraseña de la cuenta de servicio escrita en la línea anterior.
   * Busque la opción **pfMobile App Web Service_pfwssdk_PfWsSdk** y cambie el valor de **http://localhost:4898/PfWsSdk.asmx** a la URL del SDK del servicio web (por ejemplo: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).
   * Guarde el archivo Web.Config y cierre el Bloc de notas.

   > [!NOTE]
   > Dado que se usa SSL para esta conexión, consulte el SDK del servicio web por **nombre de dominio completo** y **no por la dirección IP**. El certificado SSL se debe haber emitido para el nombre de dominio completo y la dirección URL usada debe coincidir con el nombre del certificado.

7. Si el sitio web donde se instaló el servicio web de la aplicación móvil aún no está enlazado con un certificado firmado públicamente, instale el certificado en el servidor, abra el Administrador de IIS y enlace el certificado al sitio web.
8. Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el servicio web de aplicación móvil (por ejemplo, https://mfa.contoso.com/MultiFactorAuthMobileAppWebService). Asegúrese de que no aparezca ningún error ni advertencia de certificado.
9. Para más información sobre los métodos disponibles en el SDK de servicios web, vea el archivo de Ayuda del servidor MFA.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Establecimiento de la configuración del servicio web de aplicación móvil en el Servidor Azure Multi-Factor Authentication

Ahora que el servicio web de la aplicación móvil está instalado, debe configurar Servidor Azure Multi-Factor Authentication para que funcione con el portal.

1. En la consola del Servidor Multi-Factor Authentication, haga clic en el icono Portal de usuarios. Si los usuarios pueden controlar sus métodos de autenticación, active **Aplicación móvil** en la pestaña Configuración, en **Permitir a los usuarios seleccionar el método**. Sin esta característica habilitada, los usuarios finales deberán ponerse en contacto con el departamento de soporte técnico para completar la activación de la aplicación móvil.
2. Active la casilla **Permitir a los usuarios activar la aplicación móvil**.
3. Active la casilla **Permitir inscripción de usuario**.
4. Haga clic en el icono de la **aplicación móvil**.
5. Escriba la dirección URL que se va a usar con el directorio virtual que creó al instalar MultiFactorAuthenticationMobileAppWebServiceSetup64 (por ejemplo: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) en el campo **URL de servicio web de aplicación móvil:**.
6. Rellene el campo **Nombre de cuenta** con el nombre de la empresa u organización que aparecerá en la aplicación móvil para esta cuenta.
   ![Valores de la aplicación móvil para la configuración del servidor MFA](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>Pasos siguientes

- [Escenarios avanzados con Azure Multi-Factor Authentication y VPN de terceros](multi-factor-authentication-advanced-vpn-configurations.md).
