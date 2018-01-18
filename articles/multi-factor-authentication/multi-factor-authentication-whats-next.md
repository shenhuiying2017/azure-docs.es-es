---
title: "Configuración de Azure Multi-Factor Authentication| Microsoft Docs"
description: "Este artículo describe cómo configurar Azure Multi-Factor Authentication para informes, alertas de fraude, omisiones por única vez, mensajes de voz personalizados, almacenamiento en caché, IP de confianza y contraseñas de aplicación."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4421b995e69e115fbb6c7379af79aaef537aed0d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configuración de Azure Multi-Factor Authentication

Este artículo le ayudará a administrar Azure Multi-Factor Authentication ahora que ya está preparado y con todo funcionando. Abarca varios temas que le permitirán sacar el máximo partido de Azure Multi-Factor Authentication. No todas estas características están disponibles en cada una de las [versiones de Azure Multi-Factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need).

| Característica | DESCRIPCIÓN | 
|:--- |:--- |
| [Bloqueo y desbloqueo de usuarios](#block-and-unblock-users) |Utilice la característica de bloquear y desbloquear usuarios para impedirles recibir solicitudes de autenticación. |
| [Alerta de fraude](#fraud-alert) |Configure la alerta de fraude para que los usuarios puedan informar sobre intentos fraudulentos de acceso a sus recursos. |
| [Omisión por única vez](#one-time-bypass) |Utilice la característica de omisión por única vez para permitir a los usuarios autenticarse una sola vez _omitiendo_ Multi-Factor Authentication. |
| [Mensajes de voz personalizados](#custom-voice-messages) |Utilice la característica de mensajes de voz personalizados para usar sus propias grabaciones o saludos con Multi-Factor Authentication. |
| [Almacenamiento en caché](#caching-in-azure-multi-factor-authentication) |Utilice la característica de almacenamiento en caché para establecer un período de tiempo específico para que los intentos de autenticación siguientes se realicen correctamente de forma automática. |
| [Direcciones IP de confianza](#trusted-ips) |Los administradores de un inquilino administrado o federado pueden usar la característica de IP de confianza para omitir la verificación en dos pasos de los usuarios que inician sesión desde la intranet local de la empresa. |
| [Contraseñas de aplicación](#app-passwords) |Utilice la característica de contraseñas de aplicación para permitir a una aplicación omitir Multi-Factor Authentication y seguir funcionando. |
| [Recordar Multi-Factor Authentication para exploradores y dispositivos de confianza](#remember-multi-factor-authentication-for-trusted-devices) |Utilice esta característica para recordar exploradores y dispositivos de confianza durante un número determinado de días después de que un usuario haya iniciado sesión correctamente mediante Multi-Factor Authentication. |
| [Métodos de verificación seleccionables](#selectable-verification-methods) |Utilice esta característica para seleccionar la lista de métodos de autenticación que los usuarios pueden utilizar. |

## <a name="block-and-unblock-users"></a>Bloqueo y desbloqueo de usuarios

Utilice la característica de _bloqueo y desbloqueo de usuarios_ para impedirles recibir solicitudes de autenticación. Todos los intentos de autenticación para los usuarios bloqueados se denegarán automáticamente. Los usuarios permanecen bloqueados durante 90 días a partir del momento en que se bloqueen.

### <a name="block-a-user"></a>Bloquear a un usuario

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Servidor MFA** > **Bloquear o desbloquear usuarios**.
3. Seleccione **Agregar** para bloquear a un usuario.
4. Seleccione el **Grupo de replicación**. Escriba el nombre de usuario del usuario bloqueado como **nombre de usuario<span></span>@domain.com**. Escriba un comentario en el campo **Motivo**.
5. Seleccione **Agregar** para acabar de bloquear al usuario.

### <a name="unblock-a-user"></a>Desbloquear a un usuario

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Servidor MFA** > **Bloquear o desbloquear usuarios**.
3. Seleccione **Desbloquear** en la columna **Acción** situada junto al usuario que quiere desbloquear.
4. Escriba un comentario en el campo **Motivo para desbloquear**.
5. Seleccione **Desbloquear** para acabar de desbloquear al usuario.

## <a name="fraud-alert"></a>Alerta de fraude

Configure la característica de _alerta de fraude_ para que los usuarios puedan informar sobre intentos fraudulentos de acceso a sus recursos. Los usuarios pueden informar de los intentos de fraude con la aplicación móvil o mediante su teléfono.

### <a name="turn-on-fraud-alerts"></a>Activación de alertas de fraude

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Servidor MFA** > **Alerta de fraude**.

   ![Activación de alertas de fraude](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Establezca la configuración **Permitir a los usuarios enviar alertas de fraude** en **Activado**.
4. Seleccione **Guardar**.

### <a name="configuration-options"></a>Opciones de configuración

- **Bloquear al usuario si se notifican fraudes**: si se informa de que un usuario ha cometido fraude, su cuenta se bloquea durante 90 días o hasta que un administrador la desbloquee. Un administrador puede revisar los inicios de sesión usando el informe de inicio de sesión y puede tomar las medidas adecuadas para prevenir el fraude en el futuro. El administrador puede, a continuación, [desbloquear](#unblock-a-user) la cuenta de usuario.
- **Código para notificar fraudes durante el saludo inicial**: cuando los usuarios reciben una llamada telefónica al realizar la verificación en dos pasos, normalmente presionan **#** para confirmar el inicio de sesión. Si desea notificar un fraude, el usuario introduce un código antes de presionar **#**. De manera predeterminada, dicho código es **0**, pero se puede personalizar.

  >[!NOTE]
  >El saludo predeterminado de Microsoft solicita a los usuarios que presionen **0#** para enviar una alerta de fraude. Si quiere usar un código distinto a **0**, grabe y cargue su propio saludo personalizado con las instrucciones adecuadas para sus usuarios.
  >

### <a name="view-fraud-reports"></a>Visualización de notificaciones de fraude

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Azure Active Directory** > **Inicios de sesión**. El informe de fraude ahora forma parte del informe de inicios de sesión de Azure AD estándar.

## <a name="one-time-bypass"></a>Omisión por única vez

La característica de _omisión por única vez_ permite a un usuario autenticarse una sola vez y, por consiguiente, no realizar la verificación en dos pasos. La omisión es temporal y expira una vez que ha pasado el número especificado de segundos. En las situaciones en las que la aplicación móvil o el teléfono no reciben una notificación o una llamada, puede habilitar una omisión por única vez para que el usuario pueda acceder al recurso deseado.

### <a name="create-a-one-time-bypass"></a>Creación de una omisión por única vez

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Servidor MFA** > **Omisión por única vez**.

   ![Creación de una omisión por única vez](./media/multi-factor-authentication-whats-next/onetimebypass.png)

3. Seleccione **Agregar**.
4. Si es necesario, seleccione el grupo de replicación para esta omisión.
5. Escriba el nombre de usuario como **nombre de usuario<span></span>@domain.com**. Escriba el número de segundos que debería durar la omisión. Escriba el motivo de la omisión. 
6. Seleccione **Agregar**. El límite de tiempo entra en vigor inmediatamente. El usuario tiene que iniciar sesión antes de que expire la omisión por única vez. 

### <a name="view-the-one-time-bypass-report"></a>Visualización del informe de omisión por única vez

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Vaya a **Active Directory** > **Servidor MFA** > **Omisión por única vez**.

## <a name="custom-voice-messages"></a>Mensajes de voz personalizados
Puede usar sus propias grabaciones o saludos para la verificación en dos pasos con la característica de los _mensajes de voz personalizados_. Además, pueden utilizarse estos mensajes o reemplazarse por grabaciones de Microsoft.

Antes de comenzar, tenga en cuenta las restricciones siguientes:

* Los formatos de archivo compatibles son .wav y. mp3.
* El límite de tamaño de archivo es 5 MB.
* Los mensajes de autenticación deben durar menos de 20 segundos. Los mensajes que duren más de 20 segundos pueden hacer que la verificación cause error. El usuario podría no responder antes de que finalice el mensaje y se agote el tiempo de espera de la verificación.

### <a name="set-up-a-custom-message"></a>Configuración de un mensaje personalizado

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Servidor MFA** > **Configuración de la llamada telefónica**.

   ![Grabar mensajes de teléfono personalizados](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Seleccione **Agregar saludo**.
4. Elija el tipo de saludo. Elija el idioma.
5. Seleccione un archivo de sonido. mp3 o .wav para cargar.
6. Seleccione **Agregar**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Almacenamiento en caché en Azure Multi-Factor Authentication

Puede establecer un período de tiempo para permitir intentos de autenticación cuando se autentica un usuario mediante el uso de la característica _almacenamiento en caché_. Los intentos de autenticación posteriores para el usuario dentro del período de tiempo especificado se realizan correctamente de forma automática. El almacenamiento en caché se utiliza principalmente cuando sistemas locales, como VPN, envían varias solicitudes de comprobación mientras la primera solicitud aún está en curso. Esta característica permite que las solicitudes posteriores se realicen correctamente de forma automática después de que el usuario lleve a cabo correctamente la primera comprobación en curso. 

>[!NOTE]
>La característica de almacenamiento en caché no está destinada a utilizarse para inicios de sesión en Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Configuración del almacenamiento en caché 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Servidor MFA** > **Reglas de almacenamiento en caché**.

   ![Configuración de las reglas del almacenamiento en caché](./media/multi-factor-authentication-whats-next/cachingrules.png)

3. Seleccione **Agregar**.
4. Seleccione un **tipo de caché** de la lista desplegable. Escriba el número máximo de **segundos de caché**. 
5. Si es necesario, seleccione un tipo de autenticación y especifique una aplicación. 
6. Seleccione **Agregar**.


## <a name="trusted-ips"></a>IP de confianza

Los administradores de un inquilino administrado o federado utilizan la característica de _IP de confianza_ de Azure Multi-Factor Authentication. La característica omite la verificación en dos pasos para los usuarios que inician sesión desde la intranet de la empresa. La característica está disponible con la versión completa de Azure Multi-Factor Authentication y no la versión que es gratis para administradores. Para más información sobre cómo obtener la versión completa de Azure Multi-Factor Authentication, consulte [Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Tipo de inquilino de Azure AD | Opciones de características de IP de confianza |
|:--- |:--- |
| Administrado |**Intervalos específicos de direcciones IP**: los administradores pueden especificar un intervalo de direcciones IP que puede omitir la verificación en dos pasos para los usuarios que inician sesión desde la intranet de la empresa.|
| Federado |**Todos los usuarios federados**: todos los usuarios federados que inician sesión desde dentro de la organización pueden omitir la verificación en dos pasos. Los usuarios omiten la verificación mediante el uso de una notificación que emiten los servicios de federación de Active Directory (AD FS).<br/>**Intervalos específicos de direcciones IP**: los administradores pueden especificar un intervalo de direcciones IP que puede omitir la verificación en dos pasos para los usuarios que inician sesión desde la intranet de la empresa. |

La omisión de las direcciones IP de confianza solo funciona desde dentro de la intranet de la empresa. Si selecciona la opción **Todos los usuarios federados** y un usuario inicia sesión desde fuera de la intranet de la empresa, el usuario tendrá que autenticarse mediante la verificación en dos pasos. El proceso es el mismo, incluso si el usuario presenta una notificación de AD FS. 

**Experiencia del usuario final dentro de la red corporativa**

Cuando se deshabilita la característica de la IP de confianza, la verificación en dos pasos es necesaria para los flujos del explorador. Se necesitan contraseñas de aplicación para anteriores aplicaciones de cliente enriquecidas. 

Cuando se habilita la característica de la IP de confianza, la verificación en dos pasos *no* es necesaria para los flujos del explorador. Las contraseñas de aplicación *no* son obligatorias para las anteriores aplicaciones de cliente enriquecidas, siempre que el usuario no haya creado una contraseña de aplicación. Después de que la contraseña de aplicación esté en uso, la contraseña sigue siendo necesaria. 

**Experiencia del usuario final fuera de la red corporativa**

Independientemente de si la característica de la IP de confianza está habilitada, la verificación en dos pasos es necesaria para los flujos del explorador. Se necesitan contraseñas de aplicación para anteriores aplicaciones de cliente enriquecidas. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Habilitación de las ubicaciones con nombre mediante el acceso condicional

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el lado izquierdo, seleccione **Azure Active Directory** > **Acceso condicional** > **Ubicaciones con nombre**.
3. Seleccione **Nueva ubicación**.
4. Escriba un nombre para la ubicación.
5. Seleccione **Marcar como ubicación de confianza**.
6. Escriba el intervalo de IP en la notación CIDR como **192.168.1.1/24**.
7. Seleccione **Crear**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Habilite la característica de direcciones IP de confianza mediante el acceso condicional

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el lado izquierdo, seleccione **Azure Active Directory** > **Acceso condicional** > **Ubicaciones con nombre**.
3. Seleccione **Configurar IP de confianza de MFA**.
4. En la página **Configuración del servicio**, en **IP de confianza**, seleccione una de las dos opciones siguientes:
   
   * **Para solicitudes de usuarios federados cuyo origen esté en mi intranet**: para elegir esta opción, active la casilla. Todos los usuarios federados que inicien sesión desde la red corporativa omitirán la verificación en dos pasos mediante una notificación emitida por AD FS. Asegúrese de que AD FS tiene una regla para agregar la notificación de intranet al tráfico adecuado. Si la regla no existe, cree la siguiente regla en AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Para solicitudes de un intervalo específico de IP públicas**: para elegir esta opción, escriba las direcciones IP en el cuadro de texto mediante la notación CIDR.
   
     * Para las direcciones IP que se encuentran en el intervalo xxx.xxx.xxx.1 mediante xxx.xxx.xxx.254, use una notación como **xxx.xxx.xxx.0/24**.
     * Para una única dirección IP, use una notación como **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Especifique un máximo de 50 intervalos de direcciones IP. Los usuarios que inician sesión desde estas direcciones IP omiten la comprobación en dos pasos.

5. Seleccione **Guardar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Habilite la característica de direcciones IP de confianza mediante la configuración del servicio

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la izquierda, seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**.
4. En Multi-Factor Authentication, seleccione **Configuración del servicio**.
5. En la página **Configuración del servicio**, en **IP de confianza**, seleccione una de las dos opciones siguientes:
   
   * **Para solicitudes de usuarios federados en mi intranet**: para elegir esta opción, active la casilla. Todos los usuarios federados que inicien sesión desde la red corporativa omitirán la verificación en dos pasos mediante una notificación emitida por AD FS. Asegúrese de que AD FS tiene una regla para agregar la notificación de intranet al tráfico adecuado. Si la regla no existe, cree la siguiente regla en AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **Para solicitudes de un intervalo específico de subredes de direcciones IP**: para elegir esta opción, escriba las direcciones IP en el cuadro de texto mediante la notación CIDR. 
     
     * Para las direcciones IP que se encuentran en el intervalo xxx.xxx.xxx.1 mediante xxx.xxx.xxx.254, use una notación como **xxx.xxx.xxx.0/24**.
     * Para una única dirección IP, use una notación como **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Especifique un máximo de 50 intervalos de direcciones IP. Los usuarios que inician sesión desde estas direcciones IP omiten la comprobación en dos pasos.

6. Seleccione **Guardar**.

![Habilite las direcciones IP de confianza mediante la configuración del servicio](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Contraseñas de aplicación

Algunas aplicaciones, como Office 2010 o las versiones anteriores y Apple Mail, no admiten la verificación en dos pasos. Las aplicaciones no están configuradas para aceptar una segunda comprobación. Para usar estas aplicaciones, aprovéchese de la característica _contraseñas de aplicación_. Puede usar una contraseña de aplicación en lugar de su contraseña habitual para permitir que una aplicación omita la verificación en dos pasos y siga funcionando.

>[!NOTE]
>Autenticación moderna para los clientes de Microsoft Office 2013 y versiones posteriores
> 
>Los clientes de Office 2013 y versiones posteriores (incluido Outlook) admiten protocolos de autenticación modernos y se pueden habilitar para que funcionen con la verificación en dos pasos. Después de que se habilite el cliente, las contraseñas de aplicación no le serán necesarias. Para obtener más información, consulte [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)(Anuncio de la versión preliminar pública de la autenticación moderna de Office 2013).
>

### <a name="considerations-about-app-passwords"></a>Consideraciones acerca de las contraseñas de aplicación

Al utilizar las contraseñas de aplicación, considere los siguientes puntos importantes:

* Las contraseñas de aplicación solo se escriben una vez por cada aplicación. No es necesario que los usuarios realicen un seguimiento de las contraseñas y las escriban en cada ocasión.
* La contraseña real se genera automáticamente y no la proporciona el usuario. Las contraseñas generadas automáticamente son más difíciles de adivinar y, por tanto, más seguras.
* Hay un límite de 40 contraseñas por usuario. 
* Las aplicaciones que almacenan en caché las contraseñas y las usan en escenarios locales pueden comenzar a dar error porque no se conoce la contraseña de aplicación fuera de la cuenta profesional o educativa. Un ejemplo de este escenario es el de los mensajes de correo electrónico de Exchange que son locales pero se archivan en la nube. En este escenario, no funciona la misma contraseña.
* Una vez habilitada Multi-Factor Authentication en una cuenta de usuario, se pueden usar las contraseñas de aplicación con la mayoría de los clientes sin explorador como Outlook y Microsoft Skype Empresarial. No se pueden realizar acciones administrativas con contraseñas de aplicación mediante aplicaciones sin explorador como Windows PowerShell. No se pueden realizar las acciones, incluso cuando el usuario tiene una cuenta administrativa. Para ejecutar scripts de PowerShell, cree una cuenta de servicio con una contraseña segura y no la habilite para la verificación en dos pasos.

>[!WARNING]
>Las contraseñas de aplicación no funcionan en entornos híbridos donde los clientes se comunican tanto en el entorno local como en los puntos de conexión de detección automática en la nube. Las contraseñas de dominio deben autenticarse en local. Las contraseñas de aplicación deben autenticarse con la nube.
>

### <a name="guidance-for-app-password-names"></a>Guía de nombres para las contraseñas de aplicación

Los nombres de las contraseñas de aplicación deberían reflejar el dispositivo en el que se usan. Si tiene un portátil con aplicaciones sin explorador como Outlook, Word y Excel, cree una contraseña de aplicación denominada **Portátil** para estas aplicaciones. Cree otra contraseña de aplicación denominada **Escritorio** para las mismas aplicaciones que se ejecutan en el equipo de escritorio. 

>[!NOTE]
>Se recomienda crear una contraseña de aplicación por dispositivo, en lugar de por aplicación.

### <a name="federated-or-single-sign-on-app-passwords"></a>Contraseñas de aplicaciones de inicio de sesión único o federado

Azure AD admite la federación, o el inicio de sesión único (SSO), con Active Directory Domain Services (AD DS) local de Windows Server. Si su organización está federada con Azure AD y está usando Azure Multi-Factor Authentication, considere los siguientes puntos acerca de las contraseñas de aplicación.

>[!NOTE]
>Los siguientes puntos solo se aplican a los clientes federados (SSO).

* Azure AD comprueba las contraseñas de aplicación y, por tanto, se omite la federación. La federación solo se usa activamente al configurar la contraseñas de aplicación.
* En el caso de los usuarios federados (SSO), nunca se contacta con el proveedor de identidades (IdP), a diferencia del flujo pasivo. Las contraseñas de aplicación se almacenan en la cuenta profesional o educativa. Si un usuario deja la empresa, su información fluye hacia la cuenta profesional o educativa utilizando **DirSync** en tiempo real. La deshabilitación o eliminación de la cuenta puede tardar hasta tres horas en sincronizarse, lo que puede retrasar la deshabilitación o eliminación de la contraseña de aplicación en Azure AD.
* La configuración de Access Control de cliente local no se cumple con la característica de las contraseñas de aplicación.
* No hay ningún registro o funcionalidad de auditoría en la autenticación local que esté disponible para usarse con la característica de las contraseñas de aplicación.
* Algunas arquitecturas avanzadas requieren una combinación de credenciales para la verificación de dos pasos con los clientes. Estas credenciales pueden incluir una contraseña o nombre de usuario de una cuenta profesional o educativa y las contraseñas de aplicación. Los requisitos dependen de cómo se realiza la autenticación. Para los clientes que se autentican en una infraestructura local, se necesitará una contraseña y un nombre de usuario para la cuenta profesional o educativa. Para los clientes que se autentican en Azure AD, se necesitará una contraseña de aplicación.

  Por ejemplo, supongamos que tiene la siguiente arquitectura:

  * Su instancia local de Active Directory está federada con Azure AD.
  * Está usando Exchange Online.
  * Está usando Skype Empresarial local.
  * Está usando Azure Multi-Factor Authentication.

  ![Uso de contraseñas de aplicación en una organización federada](./media/multi-factor-authentication-whats-next/federated.png)

  En este escenario, utilice las credenciales siguientes:

  * Para iniciar sesión en Skype Empresarial, utilice el nombre de usuario y contraseña de la cuenta profesional o educativa.
  * Para acceder a la libreta de direcciones a través de un cliente de Outlook que se conecta a Exchange Online, utilice una contraseña de aplicación.

### <a name="allow-users-to-create-app-passwords"></a>Permita que los usuarios creen contraseñas de aplicación

De forma predeterminada, los usuarios no pueden crear contraseñas de aplicación. Debe habilitarse la característica de las contraseñas de aplicación. Para dar a los usuarios la posibilidad de crear contraseñas de aplicación, use el procedimiento siguiente:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la izquierda, seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**.
4. En Multi-Factor Authentication, seleccione **Configuración del servicio**.
5. En la página **Configuración del servicio**, seleccione la opción **Permitir a los usuarios crear contraseñas de aplicación para iniciar sesión en aplicaciones sin explorador**.

   ![Permita que los usuarios creen contraseñas de aplicación](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Creación de contraseñas de aplicación

Los usuarios pueden crear contraseñas de aplicación durante el registro inicial. El usuario tiene la opción de crear contraseñas de aplicación al final del proceso de registro.

Los usuarios también pueden crear contraseñas de aplicación después del registro. Las contraseñas de aplicación se pueden cambiar en la configuración de Azure Portal o del Portal de Office 365. Para más información e instrucciones detalladas para los usuarios, consulte [¿Qué son las contraseñas de aplicación de Azure Multi-Factor Authentication?](./end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Recuerde el servicio Multi-Factor Authentication para los dispositivos de confianza
La característica _Recordar Multi-Factor Authentication_ para dispositivos y exploradores que el usuario considera de confianza es gratis para todos los usuarios de Multi-Factor Authentication. Los usuarios pueden omitir las verificaciones posteriores durante un número especificado de días, una vez hayan iniciado sesión correctamente en un dispositivo mediante el uso de Multi-Factor Authentication. Esta característica permite mejorar la facilidad de uso, ya que minimiza el número de veces que un usuario puede realizar la verificación en dos pasos en el mismo dispositivo.

>[!IMPORTANT]
>Si una cuenta o un dispositivo corren peligro, el hecho de recordar Multi-Factor Authentication para los dispositivos de confianza puede afectar a la seguridad. Si una cuenta corporativa se pone en peligro o un dispositivo de confianza es objeto de pérdida o robo, debe [restaurar Multi-Factor Authentication en todos los dispositivos](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>La acción de restauración revoca el estado de confianza de todos los dispositivos y el usuario debe volver a realizar la verificación en dos pasos. También puede pedir a los usuarios que restauren Multi-Factor Authentication en sus propios dispositivos con las instrucciones que se indican en [Administración de la configuración de la verificación en dos pasos](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Cómo funciona la característica

La característica Recordar Multi-Factor Authentication establece una cookie persistente en el explorador cuando un usuario selecciona la opción **No preguntar de nuevo durante X días** en el momento del inicio de sesión. Al usuario no se le volverá a solicitar Multi-Factor Authentication desde ese mismo explorador hasta que expire la cookie. Si el usuario abre un explorador diferente en el mismo dispositivo o borra sus cookies, se le pedirá de nuevo la verificación. 

La opción **No preguntar de nuevo durante X días** no está disponible en las aplicaciones sin explorador, independientemente de si la aplicación admite la autenticación moderna. Estas aplicaciones usan _tokens de actualización_ que proporcionan nuevos tokens de acceso cada hora. Cuando se valida un token de actualización, Azure AD comprueba que la última verificación en dos pasos estaba dentro del número de días especificado. 

La característica reduce el número de autenticaciones en las aplicaciones web, que normalmente se solicitan siempre. La característica aumenta el número de autenticaciones para los clientes de autenticación moderna, que normalmente se solicita cada 90 días.

>[!IMPORTANT]
>La característica **Recordar Multi-Factor Authentication** no es compatible con la característica **Mantener la sesión iniciada** de AD FS cuando los usuarios realizan la verificación en dos pasos para AD FS mediante el Servidor Azure Multi-Factor Authentication o una solución de terceros para la autenticación multifactor.
>
>Si los usuarios seleccionan **Mantener la sesión iniciada** en AD FS y también marcan su dispositivo como de confianza para Multi-Factor Authentication, no se comprobará el usuario automáticamente después de que expire el número de días de la característica **Recordar Multi-Factor Authentication**. Azure AD solicita una nueva verificación en dos pasos, pero AD FS devuelve un token con la fecha y la notificación originales de Multi-Factor Authentication en lugar de volver a realizar la verificación en dos pasos. Esta reacción crea un bucle de comprobación entre Azure AD y AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Habilitación de Recordar Multi-Factor Authentication

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la izquierda, seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**.
4. En Multi-Factor Authentication, seleccione **Configuración del servicio**.
5. En la página **Configuración del servicio**, en la **Administración de Recordar Multi-Factor Authentication**, seleccione la opción **Permitir que los usuarios recuerden la autenticación multifactor en dispositivos de confianza**.

   ![Recuerde el servicio Multi-Factor Authentication para los dispositivos de confianza](./media/multi-factor-authentication-whats-next/remember.png)

6. Establezca el número de días para permitir que los dispositivos de confianza omitan la verificación en dos pasos. El valor predeterminado es 14 días.
7. Seleccione **Guardar**.

### <a name="mark-a-device-as-trusted"></a>Marca de un dispositivo como de confianza

Después de habilitar la característica Recordar Multi-Factor Authentication, al iniciar sesión los usuarios pueden marcar un dispositivo como de confianza si seleccionan **No volver a preguntar**.

![Seleccione "No volver a preguntar" para los dispositivos de confianza](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Métodos de verificación seleccionables

Puede elegir los métodos de verificación que están disponibles para los usuarios mediante la característica _métodos de verificación seleccionables_. En la tabla siguiente se proporciona una breve información general de los métodos.

Cuando los usuarios inscriben sus cuentas en Azure Multi-Factor Authentication, deciden su método de verificación preferido de las opciones que ha habilitado. Las instrucciones para el proceso de inscripción de los usuarios se proporcionan en [Configuración de mi cuenta para la verificación en dos pasos](multi-factor-authentication-end-user-first-time.md).

| Método | DESCRIPCIÓN |
|:--- |:--- |
| Llamada al teléfono |Hace una llamada de voz automática. El usuario responde a la llamada y pulsa la # del teclado del teléfono para autenticarse. El número de teléfono no se sincroniza con Active Directory local. |
| Mensaje de texto al teléfono |Envía un mensaje de texto que contiene un código de verificación. Se pide al usuario que escriba el código de verificación en la interfaz de inicio de sesión. Este proceso se llama "SMS unidireccional". SMS bidireccional significa que el usuario debe devolver un mensaje de texto con un código determinado. SMS bidireccional se encuentra en desuso y no se admitirá después del 14 de noviembre de 2018. Los usuarios configurados para usar SMS bidireccional pasarán automáticamente a la verificación de _Llamada telefónica_ en ese momento.|
| Notificación a través de aplicación móvil |Envía una notificación push a su teléfono o dispositivo registrado. El usuario ve la notificación y selecciona **Comprobar** para completar la comprobación. La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificación desde aplicación móvil |La aplicación Microsoft Authenticator genera un nuevo código de verificación de OATH cada 30 segundos. El usuario escribe el código de verificación en la interfaz de inicio de sesión. La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Habilitar y deshabilitar los métodos de verificación

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la izquierda, seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**.
4. En Multi-Factor Authentication, seleccione **Configuración del servicio**.
5. En la página **Configuración del servicio**, en **Opciones de verificación**, active o desactive los métodos para proporcionar a los usuarios.

   ![Seleccione los métodos de verificación](./media/multi-factor-authentication-whats-next/authmethods.png)

6. Haga clic en **Save**(Guardar).
