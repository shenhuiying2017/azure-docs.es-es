---
title: "Servicio web de aplicación móvil del Servidor Azure MFA | Microsoft Docs"
description: "La aplicación Microsoft Authenticator ofrece una opción de autenticación fuera de banda adicional.  Permite al Servidor MFA utilizar notificaciones push en los usuarios."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/25/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Habilitación de la autenticación de aplicación móvil con Servidor Azure Multi-Factor Authentication

La aplicación Microsoft Authenticator ofrece una opción de comprobación fuera de banda adicional. En lugar de realizar una llamada de teléfono automatizada o SMS al usuario durante el inicio de sesión, Azure Multi-Factor Authentication inserta una notificación en la aplicación Microsoft Authenticator en el smartphone o en una tableta del usuario. El usuario simplemente toca **Comprobar** (o escribe un PIN y toca "Autenticar") en la aplicación para completar el inicio de sesión. 

Se recomienda usar una aplicación móvil para la verificación en dos pasos cuando la recepción telefónica es poco confiable. Si usa la aplicación como generador del token OATH, no se requiere ninguna conexión a Internet o una red. 

Para instalar el portal de usuarios en un servidor diferente a Servidor Azure Multi-Factor Authentication, se deben seguir estos pasos:

1. Instalación del SDK del servicio web
2. Instalación del servicio web de aplicación móvil
3. Establecimiento de la configuración del servicio web de aplicación móvil en el Servidor Azure Multi-Factor Authentication
4. Activación de la aplicación Microsoft Authenticator para usuarios finales

## <a name="requirements"></a>Requisitos

Para usar la aplicación Microsoft Authenticator, se requiere lo siguiente para que pueda comunicarse correctamente con el servicio web de aplicación móvil:

* Servidor Azure Multi-Factor Authentication v6.0 o superior.
* Debe instalar el servicio web de aplicación móvil en un servidor web con conexión a Internet con Microsoft® [Internet Information Services (IIS) 7.x o superior](http://www.iis.net/).
* ASP.NET v4.0.30319 debe estar instalado, registrado y establecido en Permitido.
* Entre los servicios de rol necesarios, se incluyen ASP.NET y Compatibilidad con la metabase de IIS 6.
* Se debe poder tener acceso al servicio web de aplicación móvil mediante una dirección URL pública.
* El servicio web de aplicación móvil debe estar protegido con un certificado SSL.
* Debe instalar el SDK del servicio web de Azure Multi-Factor Authentication en IIS 7.x o superior en el mismo servidor donde esté instalado el Servidor Azure Multi-Factor Authentication.
* El SDK del servicio web de Azure Multi-Factor Authentication debe estar protegido con un certificado SSL.
* El servicio web de aplicación móvil debe ser capaz de conectarse al SDK del servicio web de Azure Multi-Factor Authentication a través de SSL.
* El servicio web de aplicación móvil debe ser capaz de autenticarse con el SDK del servicio web de Azure MFA mediante las credenciales de una cuenta de servicio que pertenezca a un grupo de seguridad llamado "PhoneFactor Admins". La cuenta de servicio y el grupo existen en Active Directory si Servidor Azure Multi-Factor Authentication se encuentra en un servidor unido al dominio. La cuenta de servicio y el grupo existen localmente en Servidor Azure Multi-Factor Authentication si no está unido al dominio.


## <a name="install-the-web-service-sdk"></a>Instalación del SDK del servicio web
Si el SDK del servicio web de Azure Multi-Factor Authentication aún no está instalado en Servidor Azure Multi-Factor Authentication (MFA), vaya a dicho servidor y abra el servidor Azure MFA. 

1. Haga clic en el icono SDK del servicio web.
2. Haga clic en **Instalar SDK del servicio web** y siga las instrucciones que se proporcionen. 

El SDK del servicio web debe estar protegido con un certificado SSL. Un certificado autofirmado es adecuado para este propósito. Importe el certificado al almacén "Entidades de certificación de raíz de confianza" de la cuenta Equipo local en el servidor web del Portal de usuarios para que confíe en ese certificado cuando inicie la conexión SSL.

![Configuración](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>Instalación del servicio web de aplicación móvil
Antes de instalar el servicio web de aplicación móvil, tenga en cuenta los detalles siguientes:

* Si el Portal de usuarios de Azure MFA ya está instalado en el servidor con conexión a Internet, el nombre de usuario, la contraseña y la dirección URL del SDK del servicio web pueden copiarse desde el archivo web.config del Portal de usuarios.
* Resulta útil abrir un explorador web en un servidor web con conexión a Internet e ir a la dirección URL del SDK del servicio web que se especificó en el archivo web.config. Si el explorador puede obtener acceso al servicio web correctamente, debería solicitarle credenciales. Escriba el nombre de usuario y la contraseña que se especificaron en el archivo web.config tal y como aparecen en el archivo. Asegúrese de que no aparezca ningún error ni advertencia de certificado.
* Si existe un proxy inverso o un firewall en el servidor web del servicio web de aplicación móvil que está realizando la descarga de SSL, puede editar el archivo web.config del servicio web de aplicación móvil para que este pueda usar http en lugar de https. SSL sigue siendo necesario en la aplicación móvil para el firewall o proxy inverso. Agregue la siguiente clave a la sección \<appSettings\>: 

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>Instalación del servicio

1. Abra el Explorador de Windows en el Servidor Azure Multi-Factor Authentication y vaya a la carpeta donde esté instalado el Servidor Azure MFA (habitualmente, C:\Archivos de programa\Azure Multi-Factor Authentication). Elija la versión de 32 bits o 64 bits del archivo de instalación Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup. Copie el archivo de instalación en el servidor con conexión a Internet.

2. En el servidor web con conexión a Internet, ejecute el archivo de instalación con derechos de administrador. Abra un símbolo del sistema como administrador y vaya a la ubicación donde se copió el archivo de instalación.

3. Ejecute el archivo de instalación Multi-Factor AuthenticationMobileAppWebServiceSetup, cambie el sitio si lo prefiere y cambie el directorio virtual a un nombre corto como "PA".

  Como los usuarios deben escribir la URL del servicio web de la aplicación móvil en el dispositivo móvil durante la activación, se recomienda un nombre de directorio virtual corto.

4. Después de finalizar la instalación de Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, vaya a C:C:\inetpub\wwwroot\PA (o el directorio correspondiente según el nombre del directorio virtual) y edite el archivo web.config. 

5. Ubique las claves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME y WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD. Establezca los valores en el nombre de usuario y la contraseña de la cuenta de servicio miembro del grupo de seguridad PhoneFactor Admins. Puede ser la misma cuenta que se utiliza como identidad del Portal de usuarios de Azure Multi-Factor Authentication si se ha instalado anteriormente. Asegúrese de especificar el nombre de usuario y la contraseña entre comillas al final de la línea (value=””/>). Use un nombre de usuario completo, como dominio\nombreDeUsuario o máquina\nombreDeUsuario.  

6. Busque la configuración pfMobile App Web Service_pfwssdk_PfWsSdk. Cambie el valor de *http://localhost:4898/PfWsSdk.asmx* a la dirección URL del SDK del servicio web que se ejecuta en Servidor Azure Multi-Factor Authentication (como https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). 

  Dado que se usa SSL para esta conexión, consulte el SDK del servicio web por nombre de servido, no por la dirección IP. El certificado SSL se debe haber emitido para el nombre del servidor y la dirección URL usada debe coincidir con el nombre del certificado. Es posible que el nombre del servidor no se resuelva en una dirección IP del servidor con conexión a Internet. Si este es el caso, agregue una entrada al archivo hosts en ese servidor para asignar el nombre de Servidor Azure Multi-Factor Authentication a su dirección IP. Una vez realizados los cambios, guarde el archivo web.config.

7. Si el sitio web donde se instaló el servicio web de la aplicación móvil aún no está enlazado con un certificado firmado públicamente, instale el certificado en el servidor, abra el Administrador de IIS y enlace el certificado al sitio web.

8. Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el servicio web de la aplicación móvil (como https://www.publicwebsite.com/PA). Asegúrese de que no aparezca ningún error ni advertencia de certificado.

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Establecimiento de la configuración del servicio web de aplicación móvil en el Servidor Azure Multi-Factor Authentication
Ahora que el servicio web de la aplicación móvil está instalado, debe configurar Servidor Azure Multi-Factor Authentication para que funcione con el portal.

1. En Servidor Azure MFA, haga clic en el icono Portal de usuarios. Si los usuarios pueden controlar sus métodos de autenticación, active **Aplicación móvil** en la pestaña Configuración, en **Permitir a los usuarios seleccionar el método**. Sin esta característica habilitada, los usuarios finales deberán ponerse en contacto con el departamento de soporte técnico para completar la activación de la aplicación móvil.
2. Active la casilla **Permitir a los usuarios activar la aplicación móvil**.
3. Active la casilla **Permitir inscripción de usuario**.
4. Haga clic en el icono de la aplicación móvil.
5. Escriba la dirección URL usada con el directorio virtual que creó al instalar Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Se puede escribir un nombre de cuenta en el espacio proporcionado. Este nombre de compañía se mostrará en la aplicación móvil. Si se deja en blanco, se mostrará el nombre del proveedor de Multi-Factor Authentication creado en el Portal de Azure clásico.

<center>![Configuración](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

