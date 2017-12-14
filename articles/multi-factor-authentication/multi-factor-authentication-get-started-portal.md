---
title: Portal de usuarios para el Servidor Azure MFA | Microsoft Docs
description: "En esta página de Azure Multi-Factor Authentication, se describe cómo empezar a trabajar con Azure MFA y el Portal de usuarios."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 15f4ef7b70027a820dc50a9399891bbab77d7e12
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Portal de usuarios para el Servidor Azure Multi-Factor Authentication

El Portal de usuarios es un sitio web de IIS que permite a los usuarios inscribirse en Azure Multi-Factor Authentication (MFA) y mantener sus cuentas. Un usuario puede cambiar el número de teléfono, modificar el PIN o evitar la verificación en dos pasos durante su próximo inicio de sesión.

Los usuarios inician sesión en el portal de usuarios con el nombre de usuario y la contraseña normales y, después, completan una llamada de verificación en dos pasos o responden a preguntas de seguridad para completar la autenticación. Si se permite la inscripción de usuarios, los usuarios configurarán su número de teléfono y su PIN la primera vez que inicien sesión en el Portal de usuarios.

Es posible que se configuren administradores para el Portal de usuarios y que se les conceda permiso para agregar usuarios nuevos y actualizar los existentes.

Según su entorno, puede que desee implementar el Portal de usuarios en el mismo servidor que el Servidor Microsoft Azure Multi-Factor Authentication o en otro servidor con conexión a Internet.

![Portal de usuarios de MFA](./media/multi-factor-authentication-get-started-portal/portal.png)

> [!NOTE]
> El portal de usuarios solo está disponible con el Servidor Multi-Factor Authentication. Si utiliza Multi-Factor Authentication en la nube, remita a sus usuarios a [Configuración de mi cuenta para la verificación en dos pasos](./end-user/multi-factor-authentication-end-user-first-time.md) o [Administración de la configuración de la verificación en dos pasos](./end-user/multi-factor-authentication-end-user-manage-settings.md).

## <a name="install-the-web-service-sdk"></a>Instalación del SDK del servicio web

En ambos escenarios, si el SDK del servicio web de Azure Multi-Factor Authentication aún **no** está instalado en el Servidor Azure Multi-Factor Authentication (MFA), siga los pasos que se indican a continuación.

1. Abra la consola del Servidor Multi-Factor Authentication.
2. Vaya a **Web Service SDK** (SDK del servicio web) y seleccione **Install Web Service SDK** (Instalar SDK del servicio web).
3. Complete la instalación mediante los valores predeterminados a menos que necesite cambiarlos por algún motivo.
4. Enlace un certificado SSL al sitio en IIS.

Si tiene alguna pregunta acerca de cómo configurar un certificado SSL en un servidor IIS, consulte el artículo [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Configuración de un certificado SSL en IIS)///.

El SDK del servicio web debe estar protegido con un certificado SSL. Un certificado autofirmado es adecuado para este propósito. Importe el certificado al almacén "Entidades de certificación de raíz de confianza" de la cuenta Equipo local en el servidor web del Portal de usuarios para que confíe en ese certificado cuando inicie la conexión SSL.

![SDK del servicio web de configuración del Servidor MFA](./media/multi-factor-authentication-get-started-portal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implementación del Portal de usuarios en el mismo servidor que el Servidor Azure Multi-Factor Authentication

Los siguientes requisitos previos son obligatorios para instalar el Portal de usuarios en el **mismo servidor** que el Servidor Azure Multi-Factor Authentication:

* IIS, incluido ASP.NET y compatibilidad con la metabase de IIS 6 (para IIS 7 o posterior).
* Una cuenta con derechos de administrador para el equipo y el dominio, si corresponde. La cuenta necesita permisos para crear grupos de seguridad de Active Directory.
* Proteja el portal de usuarios con un certificado SSL.
* Proteja el SDK del servicio web Azure Multi-Factor Authentication con un certificado SSL.

Para implementar el portal de usuarios, siga estos pasos:

1. Abra la consola del Servidor Azure Multi-Factor Authentication, haga clic en el icono **Portal de usuarios** en el menú de la izquierda y en **Instalar portal de usuarios**.
2. Complete la instalación mediante los valores predeterminados a menos que necesite cambiarlos por algún motivo.
3. Enlace un certificado SSL al sitio en IIS.

   > [!NOTE]
   > Este certificado SSL suele ser un certificado SSL firmado públicamente.

4. Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuarios (por ejemplo, https://mfa.contoso.com/MultiFactorAuth). Asegúrese de que no aparezca ningún error ni advertencia de certificado.

![Instalación del Portal de usuarios del servidor MFA](./media/multi-factor-authentication-get-started-portal/install.png)

Si tiene alguna pregunta acerca de cómo configurar un certificado SSL en un servidor IIS, consulte el artículo [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Configuración de un certificado SSL en IIS)///.

## <a name="deploy-the-user-portal-on-a-separate-server"></a>Implementación del portal de usuarios en un servidor independiente

Si el servidor donde se ejecuta el Servidor Azure Multi-Factor Authentication no es accesible desde Internet, debería instalar el Portal de usuarios en un **servidor independiente que sí lo sea**.

Si su organización usa la aplicación Microsoft Authenticator como uno de los métodos de verificación y desea implementar el portal de usuarios en su propio servidor, complete los siguientes requisitos:

* Use la versión 6.0 o posterior del Servidor Azure Multi-Factor Authentication.
* Instale el portal de usuarios en un servidor web que sea accesible desde Internet con Microsoft Internet Information Services (IIS) 6.x o posterior.
* Si utiliza IIS 6.x, asegúrese de que ASP.NET v2.0.50727 esté instalado, registrado y establecido en **Permitido**.
* Si usa IIS 7.x o superior, IIS, incluida la autenticación básica, ASP.NET y la compatibilidad con la metabase de IIS 6.
* Proteja el portal de usuarios con un certificado SSL.
* Proteja el SDK del servicio web Azure Multi-Factor Authentication con un certificado SSL.
* Asegúrese de que el portal de usuarios pueda conectarse al SDK del servicio web Azure Multi-Factor Authentication a través de SSL.
* Asegúrese de que el portal de usuarios pueda autenticarse en el SDK del servicio web Azure Multi-Factor Authentication mediante las credenciales de una cuenta de servicio del grupo de seguridad "PhoneFactor Admins". La cuenta de servicio y el grupo existen en Active Directory si el Servidor Azure Multi-Factor Authentication se ejecuta en un servidor unido al dominio. La cuenta de servicio y el grupo existen localmente en Servidor Azure Multi-Factor Authentication si no está unido al dominio.

Para instalar el portal de usuarios en un servidor diferente a Servidor Azure Multi-Factor Authentication, se deben seguir estos pasos:

1. **En el servidor MFA**, vaya a la ruta de instalación (por ejemplo: C:\Archivos de programa\Multi-Factor Authentication Server) y copie el archivo **MultiFactorAuthenticationUserPortalSetup64** en una ubicación accesible para el servidor con conexión a Internet en el que lo instalará.
2. **En el servidor web con conexión a Internet**, ejecute el archivo de instalación MultiFactorAuthenticationUserPortalSetup64 como administrador, cambie el sitio si lo desea y modifique el directorio Virtual a un nombre corto, si lo prefiere.
3. Enlace un certificado SSL al sitio en IIS.

   > [!NOTE]
   > Este certificado SSL suele ser un certificado SSL firmado públicamente.

4. Vaya a **C:\inetpub\wwwroot\MultiFactorAuth**
5. Edite el archivo Web.Config en el Bloc de notas

    * Busque la clave **"USE_WEB_SERVICE_SDK"** y cambie **value="false"** a **value="true"**
    * Busque la clave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** y cambie **value=""** a **value="DOMAIN\User"** donde "DOMAIN\User" es una cuenta de servicio que forma parte del grupo "PhoneFactor Admins".
    * Busque la clave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** y cambie **value=""** a **value="Password"** donde Password es la contraseña de la cuenta de servicio escrita en la línea anterior.
    * Busque el valor **https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx** y cambie esta dirección URL del marcador de posición a la dirección URL del SDK del servicio web que se instaló en el paso 2.
    * Guarde el archivo Web.Config y cierre el Bloc de notas.

6. Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuarios (por ejemplo, https://mfa.contoso.com/MultiFactorAuth). Asegúrese de que no aparezca ningún error ni advertencia de certificado.

Si tiene alguna pregunta acerca de cómo configurar un certificado SSL en un servidor IIS, consulte el artículo [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Configuración de un certificado SSL en IIS)///.

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Establecimiento de la configuración del Portal de usuarios en el Servidor Azure Multi-Factor Authentication

Ahora que el Portal del usuario está instalado, debe configurar el Servidor Azure Multi-Factor Authentication para que funcione con el portal.

1. En la consola del Servidor Azure Multi-Factor Authentication, haga clic en el icono **Portal de usuarios**. En la pestaña Settings (Configuración), escriba la dirección URL del Portal de usuarios en el cuadro de texto **User Portal URL** (URL del Portal de usuarios). Si se ha habilitado la funcionalidad de correo electrónico, esta dirección URL se incluye en los mensajes de correo electrónico que se envían a los usuarios cuando se importan al Servidor Azure Multi-Factor Authentication.
2. Elija la configuración que desea usar en el Portal de usuarios. Por ejemplo, si se permite que los usuarios elijan sus métodos de autenticación, asegúrese de que **Permitir a los usuarios seleccionar el método** esté activado, junto con los métodos entre los que pueden elegir.
3. Defina quiénes deben ser los administradores en la pestaña **Administradores**. Puede crear permisos administrativos granulares mediante las casillas y listas desplegables en los cuadros Agregar y Editar.

Configuración opcional:
- **Preguntas de seguridad**: defina las preguntas de seguridad aprobadas para el entorno y el idioma en el que aparecen.
- **Sesiones superadas**: configure la integración del portal de usuario con un sitio web basado en formularios con MFA.
- **IP de confianza**: permita a los usuarios omitir MFA al autenticar desde una lista de direcciones IP o intervalos de confianza.

![Configurar el Portal de usuarios del servidor MFA](./media/multi-factor-authentication-get-started-portal/config.png)

Servidor Azure Multi-Factor Authentication ofrece varias opciones para el portal de usuarios. En la tabla siguiente se proporciona una lista de estas opciones y se obtiene una explicación de para qué se usan.

| Configuración del portal de usuarios | Description |
|:--- |:--- |
| URL del portal de usuarios | Especifique la dirección URL en la que se hospedará el portal. |
| Autenticación principal | Especifique el tipo de autenticación que se usará al iniciar sesión en el portal. Autenticación de Windows, Radius o LDAP. |
| Permitir que los usuarios inicien sesión | Permite a los usuarios especificar un nombre de usuario y una contraseña en la página de inicio de sesión del Portal de usuarios. Si no está seleccionada esta opción, las casillas se atenuarán. |
| Permitir inscripción de usuario | Permite al usuario inscribirse en Multi-Factor Authentication al llevarlo a una pantalla de configuración en la que se solicita información adicional, como el número de teléfono. Solicitar teléfono de reserva permite a los usuarios especificar un número de teléfono secundario. Solicita un token OATH de terceros permite a los usuarios especificar un token OATH de terceros. |
| Permitir a los usuarios iniciar Omisión por única vez | Permite a los usuarios iniciar Omisión por única vez. Si un usuario configura esta opción, entrará en vigor la próxima vez que el usuario inicie sesión. Solicitar segundos de omisión proporciona al usuario un cuadro para que puedan cambiar el valor predeterminado de 300 segundos. De lo contrario, la omisión por única vez solo funciona durante 300 segundos. |
| Permitir a los usuarios seleccionar el método  | Permite a los usuarios especificar su método de contacto principal. Este método puede consistir en una llamada de teléfono, un mensaje de texto, una aplicación móvil o un token OATH. |
| Permitir a los usuarios seleccionar el idioma | Permite a los usuarios cambiar el idioma que se usa para la llamada de teléfono, el mensaje de texto, la aplicación móvil o el token OATH. |
| Permitir a los usuarios activar la aplicación móvil | Permite a los usuarios generar un código de activación para completar el proceso de activación de la aplicación móvil que se usa con el servidor.  También puede establecer el número de dispositivos en los que se puede activar la aplicación, entre 1 y 10. |
| Usar preguntas de seguridad para la reserva | Permite preguntas de seguridad en caso de que la verificación en dos pasos genere un error. Puede especificar el número de preguntas de seguridad que se deben responder correctamente. |
| Permitir a los usuarios asociar el token OATH de terceros | Permite a los usuarios especificar un token OATH de terceros. |
| Usar token OATH para la reserva | Permite el uso de un token OATH en caso de que la verificación en dos pasos no sea correcta. También puede especificar el tiempo de espera de la sesión en minutos. |
| Habilitación del registro | Habilita el registro en el Portal de usuarios. Los archivos de registro se encuentra en C:\Archivos de programa\Multi-Factor Authentication Server\Logs. |

La mayoría de estas configuraciones son visibles para el usuario del portal una vez que están habilitados y hayan iniciado sesión en el Portal de usuarios.

![Configuración del portal de usuarios](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Autoservicio de inscripción de usuarios

Si desea que los usuarios inicien sesión y se inscriban, debe seleccionar las opciones **Allow users to log in** (Permitir que los usuarios inicien sesión) y **Allow user enrollment** (Permitir inscripción de usuario) en la pestaña Settings (Configuración). Recuerde que la configuración que seleccione afectará a la experiencia de inicio de sesión del usuario.

Por ejemplo, cuando un usuario inicia sesión en el Portal de usuarios por primera vez, pasa a la página Azure Multi-Factor Authentication User Setup (Configuración de usuario de Azure Multi-Factor Authentication). Dependiendo de cómo haya configurado Azure Multi-Factor Authentication, el usuario puede seleccionar el método de autenticación.

Si seleccionan el método de verificación Voice Call (Llamada de voz) o está preconfigurado para usar ese método, la página solicitará al usuario que escriba su número de teléfono principal y la extensión, si procede. También puede especificar un número de teléfono de reserva.

![Registrar número de teléfono principal y de reserva](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Si el usuario debe usar un PIN al realizar la autenticación, la página le pedirá que lo cree. Después de escribir sus números de teléfono y PIN (si procede), el usuario hace clic en el botón **Call Me Now to Authenticate** (Llamarme ahora para autenticar). Azure Multi-Factor Authentication llevará a cabo una verificación mediante llamada de teléfono al número de teléfono principal del usuario. El usuario debe responder a la llamada de teléfono y escribir su PIN (si procede) y presione # para avanzar al siguiente paso del proceso de autoinscripción.

Si el usuario selecciona el método de verificación Text Message (Mensaje de texto) o se ha configurado previamente para usar este método, la página solicitará al usuario su número de teléfono móvil. Si el usuario debe usar un PIN al realizar la autenticación, la página también le pedirá que escriba un PIN.  Después de escribir su número de teléfono y PIN (si procede), el usuario hace clic en el botón **Text Me Now to Authenticate** (Enviarme un SMS ahora para autenticar). Azure Multi-Factor Authentication llevará a cabo una verificación mediante un SMS al teléfono móvil del usuario. El usuario recibe el mensaje de texto con un código de acceso de un solo uso, responde al mensaje con ese código más su PIN (si procede).

![SMS del portal de usuarios](./media/multi-factor-authentication-get-started-portal/text.png)

Si el usuario selecciona el método de verificación Mobile App (Aplicación móvil), la página solicitará al usuario que instale la aplicación Microsoft Authenticator en su dispositivo y genere un código de activación. Después de instalar la aplicación, el usuario hace clic en el botón Generate Activation Code (Generar código de activación).

> [!NOTE]
> Para usar la aplicación Microsoft Authenticator, el usuario debe habilitar las notificaciones de inserción para su dispositivo.

A continuación, la página muestra un código de activación y una dirección URL, junto con la imagen de un código de barras. Si el usuario debe usar un PIN al realizar la autenticación, la página también le pedirá que escriba un PIN. El usuario escribe el código de activación y la dirección URL en la aplicación Microsoft Authenticator o usa el escáner para digitalizar la imagen del código de barras y hace clic en el botón Activate (Activar).

Una vez completada la activación, el usuario hace clic en el botón **Authenticate Me Now** (Autenticarme ahora). Azure Multi-Factor Authentication llevará a cabo una verificación en la aplicación móvil del usuario. El usuario debe escribir su PIN (si procede) y presionar el botón Autenticar en su aplicación móvil para avanzar al siguiente paso del proceso de autoinscripción.

Si los administradores han configurado Servidor Azure Multi-Factor Authentication para recopilar preguntas y respuestas acerca de la seguridad, el usuario pasa a la página de preguntas de seguridad. El usuario debe seleccionar cuatro preguntas de seguridad y proporcionar respuestas a las preguntas seleccionadas.

![Preguntas de seguridad del portal de usuarios](./media/multi-factor-authentication-get-started-portal/secq.png)

La autoinscripción del usuario ahora está completa y el usuario inicia sesión en el Portal de usuarios. Los usuarios pueden iniciar sesión en el Portal de usuarios en cualquier momento para cambiar sus números de teléfono, PIN, métodos de autenticación y preguntas de seguridad si sus administradores permiten la modificación de sus métodos.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación del servicio web de aplicaciones móviles del Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md)