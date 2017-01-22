---
title: Portal de usuarios para el Servidor Azure MFA | Microsoft Docs
description: "En esta página de Azure Multi-Factor Authentication, se describe cómo empezar a trabajar con Azure MFA y el Portal de usuarios."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1222223f8c45249402bfdd04c8754074f877e132
ms.openlocfilehash: 1236489212b2a9c421972599a12511d5bc42efdf


---
# <a name="deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Implementación del Portal de usuarios para el Servidor Azure Multi-Factor Authentication
El Portal de usuarios permite al administrador instalar y configurar el Portal de usuarios de Azure Multi-Factor Authentication. El Portal de usuarios es un sitio web de IIS que permite a los usuarios inscribirse en Azure Multi-Factor Authentication y mantener sus cuentas. Un usuario puede cambiar el número de teléfono, modificar el PIN o evitar la verificación en dos pasos durante su próximo inicio de sesión.

Los usuarios iniciarán sesión en el Portal de usuarios mediante el nombre de usuario y la contraseña normales y, después, completarán una llamada de la verificación en dos pasos o responderán a preguntas de seguridad para completar la autenticación. Si se permite la inscripción de usuarios, los usuarios configurarán su número de teléfono y su PIN la primera vez que inicien sesión en el Portal de usuarios.

Es posible que se configuren administradores para el Portal de usuarios y que se les conceda permiso para agregar usuarios nuevos y actualizar los existentes.

<center>![Configuración](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implementación del Portal de usuarios en el mismo servidor que el Servidor Azure Multi-Factor Authentication
Los siguientes requisitos previos son obligatorios para instalar el Portal de usuarios en el mismo servidor que el Servidor Azure Multi-Factor Authentication:

* IIS, incluido ASP.NET y compatibilidad con la metabase de IIS 6 (para IIS 7 o posterior).
* El usuario que haya iniciado sesión debe tener derechos de administrador para el equipo y el dominio, si corresponde.  La razón es que la cuenta necesita permisos para crear grupos de seguridad de Active Directory.

### <a name="to-deploy-the-user-portal"></a>Para implementar el Portal de usuarios
1. En el Servidor Azure Multi-Factor Authentication: haga clic en el icono **User Portal** (Portal de usuarios) en el menú de la izquierda y después en el botón **.Install User Portal** (Instalar portal de usuarios).
2. Haga clic en **Next**.
3. Haga clic en **Next**.
4. Si el equipo está unido a un dominio y Active Directory no está configurado para proteger la comunicación entre el Portal de usuarios y el servicio Azure Multi-Factor Authentication, se muestra el paso de Active Directory. Haga clic en el botón **Next** (Siguiente) para completar la configuración de forma automática.
5. Haga clic en **Next**.
6. Haga clic en **Siguiente**.
7. Haga clic en **Cerrar**.
8. Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuarios (por ejemplo, https://www.publicwebsite.com/MultiFactorAuth). Asegúrese de que no aparezca ningún error ni advertencia de certificado.

<center>![Configuración](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploy-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Implementación del Portal de usuarios del Servidor Azure Multi-Factor Authentication en otro servidor
Para permitir que la aplicación Microsoft Authenticator se comunique con el Portal de usuarios, complete los siguientes requisitos: 

* Debe usar la versión 6.0 o posterior del Servidor Azure Multi-Factor Authentication.
* El Portal de usuarios debe estar instalado en un servidor web con conexión a Internet con Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x o posterior.
* Si utiliza IIS 6.x, asegúrese de que ASP.NET v2.0.50727 esté instalado, registrado y establecido en **Permitido**.
* Entre los servicios de rol necesarios cuando se usan IIS 7.x o versiones posteriores, se incluyen ASP.NET y Compatibilidad con la metabase de IIS 6.
* El Portal de usuarios debe estar protegido con un certificado SSL.
* El SDK del servicio web de Azure Multi-Factor Authentication en IIS 6.x, IIS 7.x o posterior debe estar instalado en el servidor donde esté instalado el Servidor Azure Multi-Factor Authentication.
* El SDK del servicio web de Azure Multi-Factor Authentication debe estar protegido con un certificado SSL.
* El Portal de usuarios debe ser capaz de conectarse al SDK del servicio web de Azure Multi-Factor Authentication a través de SSL.
* El Portal de usuarios debe poder autenticarse en el SDK del servicio web de Azure Multi-Factor Authentication mediante las credenciales de una cuenta de servicio del grupo de seguridad "PhoneFactor Admins". La cuenta de servicio y el grupo existen en Active Directory si Servidor Azure Multi-Factor Authentication se ejecuta en un servidor unido al dominio. La cuenta de servicio y el grupo existen localmente en Servidor Azure Multi-Factor Authentication si no está unido al dominio.

Para instalar el Portal de usuarios en un servidor diferente a Servidor Azure Multi-Factor Authentication, se deben seguir estos tres pasos:

1. Instalación del SDK del servicio web
2. Instalación del Portal de usuarios
3. Establecimiento de la configuración del Portal de usuarios en Servidor Azure Multi-Factor Authentication

### <a name="step-1-install-the-web-service-sdk"></a>Paso 1: Instalación del SDK del servicio web
Si el SDK del servicio web de Azure Multi-Factor Authentication aún no está instalado en Servidor Azure Multi-Factor Authentication, vaya a dicho servidor y abra el servidor Azure Multi-Factor Authentication. Haga clic en el icono **Web Service SDK** (SDK del servicio web) y en **Install Web Service SDK** (Instalar SDK del servicio web), y siga las instrucciones que aparecen. 

El SDK del servicio web debe estar protegido con un certificado SSL. Un certificado autofirmado es adecuado para este propósito, pero debe importarse al almacén "Entidades de certificación de raíz de confianza" de la cuenta Equipo local en el servidor. Ahora el SDK del servicio web puede confiar en ese certificado al iniciar la conexión SSL.

<center>![Configuración](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="step-2-install-the-user-portal"></a>Paso 2: Instalación del Portal de usuarios
Antes de instalar el Portal de usuarios en un servidor independiente, tenga en cuenta los siguientes procedimientos recomendados:

* Resulta útil abrir un explorador web en un servidor web con conexión a Internet e ir a la dirección URL del SDK del servicio web que se especificó en el archivo web.config. Si el explorador puede obtener acceso al servicio web correctamente, debería solicitarle credenciales. Escriba el nombre de usuario y la contraseña que se especificaron en el archivo web.config tal y como aparecen en el archivo. Asegúrese de que no aparezca ningún error ni advertencia de certificado.
* Si existe un proxy inverso o un firewall en el servidor web del Portal de usuarios que está realizando la descarga de SSL, puede editar el archivo web.config del Portal de usuarios y agregar la siguiente clave en la sección `<appSettings>` para que el Portal de usuarios pueda usar http en lugar de https:

    `<add key="SSL_REQUIRED" value="false"/>`

#### <a name="to-install-the-user-portal"></a>Para instalar el Portal de usuarios
1. Abra el Explorador de Windows en el servidor Azure Multi-Factor Authentication y vaya a la carpeta donde esté instalado Servidor Azure Multi-Factor Authentication (por ejemplo, C:\Archivos de programa\Multi-Factor Authentication Server). Elija la versión de 32 o 64 bits del archivo de instalación MultiFactorAuthenticationUserPortalSetup según corresponda al servidor donde se instalará el Portal de usuarios. Copie el archivo de instalación en el servidor con conexión a Internet.
2. En el servidor web con conexión a Internet, se debe ejecutar el archivo de instalación con derechos de administrador. La manera más fácil de hacerlo es abrir un símbolo del sistema como administrador e ir a la ubicación donde se copió el archivo de instalación.
3. Ejecute el archivo de instalación MultiFactorAuthenticationUserPortalSetup64 y cambie el nombre de directorio virtual y sitio si lo desea.
4. Después de finalizar la instalación del Portal de usuarios, vaya a C:\inetpub\wwwroot\MultiFactorAuth (o el directorio correspondiente según el nombre del directorio virtual) y edite el archivo web.config.
5. Busque la clave USE_WEB_SERVICE_SDK y cambie el valor de false a true. Busque las claves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME y WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD y establezca los valores en el nombre de usuario y la contraseña de la cuenta de servicio del grupo de seguridad PhoneFactor Admins (consulte la sección de requisitos). Asegúrese de especificar el nombre de usuario y la contraseña entre comillas al final de la línea (value=””/>). Debe usar un nombre de usuario completo (por ejemplo, dominio\nombreDeUsuario o máquina\nombreDeUsuario).
6. Busque la configuración pfup_pfwssdk_PfWsSdk y cambie el valor de "http://localhost:4898/PfWsSdk.asmx" a la dirección URL del SDK del servicio web que se ejecuta en Servidor Azure Multi-Factor Authentication (por ejemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Dado que se utiliza SSL para esta conexión, consulte el SDK del servicio web por nombre de servidor, no por la dirección IP, ya que el certificado SSL se emitió para el nombre del servidor. Si el nombre del servidor no se resuelve en una dirección IP del servidor con conexión a Internet, agregue una entrada al archivo hosts en ese servidor para asignar el nombre de Servidor Azure Multi-Factor Authentication a su dirección IP. Una vez realizados los cambios, guarde el archivo web.config.

    Vaya al artículo sobre la [protección de los recursos mediante el Servidor Azure Multi-factor Authentication con AD FS](multi-factor-authentication-get-started-adfs-w2k12.md#edit-the-multifactorauthenticationadfsadapterconfig-file) para más información acerca de cómo editar el archivo de configuración.

7. Si el sitio web donde se instaló el Portal de usuarios (por ejemplo, Sitio web predeterminado) aún no está enlazado con un certificado firmado públicamente, instale el certificado en el servidor, abra el Administrador de IIS y enlace el certificado al sitio web.
8. Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuarios (por ejemplo, https://www.publicwebsite.com/MultiFactorAuth). Asegúrese de que no aparezca ningún error ni advertencia de certificado.

### <a name="step-3-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Paso 3: Establecimiento de la configuración del Portal de usuarios en el Servidor Azure Multi-Factor Authentication
Ahora que el Portal del usuario está instalado, debe configurar el Servidor Azure Multi-Factor Authentication para que funcione con el portal.

Servidor Azure Multi-Factor Authentication ofrece varias opciones para el portal de usuarios.  En la tabla siguiente se proporciona una lista de estas opciones y se obtiene una explicación de para qué se usan.

| Configuración del portal de usuarios | Description |
|:--- |:--- |
| URL del portal de usuarios | Especifique la dirección URL en la que se hospedará el portal. |
| Autenticación principal | Especifique el tipo de autenticación que se usará al iniciar sesión en el portal.  Autenticación de Windows, Radius o LDAP. |
| Permitir que los usuarios inicien sesión | Permite a los usuarios especificar un nombre de usuario y una contraseña en la página de inicio de sesión del Portal de usuarios.  Si no está seleccionada, las casillas se atenuarán. |
| Permitir inscripción de usuario | Permite al usuario inscribirse en Multi-Factor Authentication al llevarlo a una pantalla de configuración en la que se solicita información adicional, como el número de teléfono. Solicitar teléfono de reserva permite a los usuarios especificar un número de teléfono secundario. Solicita un token OATH de terceros permite a los usuarios especificar un token OATH de terceros. |
| Permitir a los usuarios iniciar Omisión por única vez | Permite a los usuarios iniciar Omisión por única vez.  Si un usuario lo configura, entrará en vigor la próxima vez que el usuario inicie sesión. Solicitar segundos de omisión proporciona al usuario un cuadro para que puedan cambiar el valor predeterminado de 300 segundos. De lo contrario, la omisión por única vez solo funciona durante 300 segundos. |
|  Permitir a los usuarios seleccionar el método  | Permite a los usuarios especificar su método de contacto principal.  Puede tratarse de llamada de teléfono, un mensaje de texto, una aplicación móvil o una token OATH. |
| Permitir a los usuarios seleccionar el idioma | Permite a los usuarios cambiar el idioma que se usa para la llamada de teléfono, el mensaje de texto, la aplicación móvil o el token OATH. |
| Permitir a los usuarios activar la aplicación móvil | Permite a los usuarios generar un código de activación para completar el proceso de activación de la aplicación móvil que se usa con el servidor.  También puede establecer el número de dispositivos en los que se puede activar la aplicación, entre 1 y 10. |
| Usar preguntas de seguridad para la reserva | Permite preguntas de seguridad en caso de que la verificación en dos pasos genere un error.  Puede especificar el número de preguntas de seguridad que se deben responder correctamente. |
| Permitir a los usuarios asociar el token OATH de terceros | Permite a los usuarios especificar un token OATH de terceros. |
| Usar token OATH para la reserva | Permite el uso de un token OATH en caso de que la verificación en dos pasos no sea correcta. También puede especificar el tiempo de espera de la sesión en minutos. |
| Habilitación del registro | Habilita el registro en el Portal de usuarios. Los archivos de registro se encuentra en C:\Archivos de programa\Multi-Factor Authentication Server\Logs. |

La mayoría de estas configuraciones son visibles para el usuario una vez que están habilitados y hayan iniciado sesión en el Portal de usuarios.

![Configuración del portal de usuarios](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Para establecer la configuración del Portal de usuarios en Servidor Azure Multi-Factor Authentication
1. En el Servidor Azure Multi-Factor Authentication, haga clic en el icono **User Portal** (Portal de usuarios). En la pestaña Settings (Configuración), escriba la dirección URL del Portal de usuarios en el cuadro de texto **User Portal URL** (URL del Portal de usuarios). Esta dirección URL se insertará en los mensajes de correo electrónico que se envían a los usuarios cuando se importan al Servidor Azure Multi-Factor Authentication si se habilitó la funcionalidad de correo electrónico.
2. Elija la configuración que desea usar en el Portal de usuarios. Por ejemplo, si se permite que los usuarios controlen sus métodos de autenticación, asegúrese de que **Allow users to select method** (Permitir a los usuarios seleccionar el método) está activado, junto con los métodos entre los que pueden elegir.
3. Haga clic en el vínculo **Help** (Ayuda) en la esquina superior derecha para obtener ayuda sobre cualquiera de las opciones que aparecen.

<center>![Configuración](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Pestaña Administradores
Esta pestaña simplemente permite agregar los usuarios que tendrán privilegios de administrador.  Al agregar un administrador, puede ajustar de forma precisa los permisos que recibe. Haga clic en el botón **Add** (Agregar), seleccione un usuario y sus permisos y haga clic en **Add** (Agregar).

![Administradores del portal de usuarios](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>Preguntas de seguridad
Esta pestaña permite especificar las preguntas de seguridad que los usuarios necesitarán responder si se selecciona la opción **Use security questions for fallback** (Usar preguntas de seguridad para la reserva).  El Servidor Azure Multi-Factor Authentication incluye preguntas predeterminadas que puede usar. Puede cambiar el orden o agregar sus propias preguntas.  Al agregar sus propias preguntas, también puede especificar el idioma en el que desea que aparezcan esas preguntas.

![Preguntas de seguridad del portal de usuarios](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="saml"></a>SAML
Utilice esta pestaña para configurar el Portal de usuarios para que acepte notificaciones de un proveedor de identidades con SAML.  Puede especificar el tiempo de espera de la sesión, el certificado de verificación y la dirección URL de redireccionamiento al cerrar la sesión.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>IP de confianza
Esta pestaña permite especificar direcciones IP individuales o intervalos de direcciones IP que pueden agregarse para que si un usuario inicia sesión desde una de estas direcciones no necesite completar la verificación en dos pasos.

![IP de confianza del portal de usuarios](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Autoservicio de inscripción de usuarios
Si desea que los usuarios inicien sesión y se inscriban, debe seleccionar las opciones **Allow users to log in** (Permitir que los usuarios inicien sesión) y **Allow user enrollment** (Permitir inscripción de usuario) en la pestaña Settings (Configuración). Recuerde que la configuración que seleccione afectará a la experiencia de inicio de sesión del usuario.

Por ejemplo, cuando un usuario inicia sesión en el Portal de usuarios por primera vez, pasa a la página Azure Multi-Factor Authentication User Setup (Configuración de usuario de Azure Multi-Factor Authentication).  Dependiendo de cómo haya configurado Azure Multi-Factor Authentication, el usuario puede seleccionar el método de autenticación.  

Si seleccionan el método de verificación Voice Call (Llamada de voz) o está preconfigurado para usar ese método, la página solicitará al usuario que escriba su número de teléfono principal y la extensión, si procede.  También puede especificar un número de teléfono de reserva.  

![IP de confianza del portal de usuarios](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Si el usuario debe usar un PIN al realizar la autenticación, la página también le pedirá que cree un PIN.  Después de escribir sus números de teléfono y PIN (si procede), el usuario hace clic en el botón **Call Me Now to Authenticate** (Llamarme ahora para autenticar).  Azure Multi-Factor Authentication llevará a cabo una verificación mediante llamada de teléfono al número de teléfono principal del usuario.  El usuario debe responder a la llamada de teléfono y escribir su PIN (si procede) y presione # para avanzar al siguiente paso del proceso de autoinscripción.   

Si el usuario selecciona el método de verificación Text Message (Mensaje de texto) o se ha configurado previamente para usar este método, la página solicitará al usuario su número de teléfono móvil.  Si el usuario debe usar un PIN al realizar la autenticación, la página también le pedirá que escriba un PIN.  Después de escribir su número de teléfono y PIN (si procede), el usuario hace clic en el botón **Text Me Now to Authenticate** (Enviarme un SMS ahora para autenticar).  Azure Multi-Factor Authentication llevará a cabo una verificación mediante un SMS al teléfono móvil del usuario.  El usuario recibe el mensaje de texto con un código de acceso de un solo uso, responde al mensaje con ese código más su PIN (si procede).

![SMS del portal de usuarios](./media/multi-factor-authentication-get-started-portal/text.png)   

Si el usuario selecciona el método de verificación Mobile App (Aplicación móvil), la página solicitará al usuario que instale la aplicación Microsoft Authenticator en su dispositivo y genere un código de activación.  Después de instalar la aplicación, el usuario hace clic en el botón Generate Activation Code (Generar código de activación).    

> [!NOTE]
> Para usar la aplicación Microsoft Authenticator, el usuario debe habilitar las notificaciones de inserción para su dispositivo.

A continuación, la página muestra un código de activación y una dirección URL, junto con la imagen de un código de barras.  Si el usuario debe usar un PIN al realizar la autenticación, la página también le pedirá que escriba un PIN.  El usuario escribe el código de activación y la dirección URL en la aplicación Microsoft Authenticator o usa el escáner para digitalizar la imagen del código de barras y hace clic en el botón Activate (Activar).    

Una vez completada la activación, el usuario hace clic en el botón **Authenticate Me Now** (Autenticarme ahora).  Azure Multi-Factor Authentication llevará a cabo una verificación en la aplicación móvil del usuario.  El usuario debe escribir su PIN (si procede) y presionar el botón Autenticar en su aplicación móvil para avanzar al siguiente paso del proceso de autoinscripción.  

Si los administradores han configurado Servidor Azure Multi-Factor Authentication para recopilar preguntas y respuestas acerca de la seguridad, el usuario pasa a la página de preguntas de seguridad.  El usuario debe seleccionar cuatro preguntas de seguridad y proporcionar respuestas a las preguntas seleccionadas.    

![Preguntas de seguridad del portal de usuarios](./media/multi-factor-authentication-get-started-portal/secq.png)  

La autoinscripción del usuario ahora está completa y el usuario inicia sesión en el Portal de usuarios.  Los usuarios pueden iniciar sesión en el Portal de usuarios en cualquier momento para cambiar sus números de teléfono, PIN, métodos de autenticación y preguntas de seguridad si sus administradores lo permiten.




<!--HONumber=Jan17_HO1-->


