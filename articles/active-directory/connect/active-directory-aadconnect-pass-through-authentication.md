---
title: "Azure AD Connect: Autenticación de paso a través | Microsoft Docs"
description: "En este artículo se describe la autenticación de paso a través de Azure Active Directory (Azure AD) y cómo permite inicios de sesión de Azure AD mediante la validación de las contraseñas de los usuarios en una instancia local de Active Directory."
services: active-directory
keywords: "qué es la autenticación de paso a través de Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4fc3c822e657ce1a66a59e15c1a7d636a9f699e6
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Configuración del inicio de sesión del usuario con la autenticación de paso a través de Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>¿Qué es la autenticación de paso a través de Azure Active Directory?

Permitir a los usuarios utilizar las mismas credenciales (contraseñas) para iniciar sesión tanto en los recursos locales como en los servicios en la nube es beneficioso tanto para los usuarios como para la organización. Una contraseña menos que recordar significa una mejor experiencia para el usuario. También reduce las posibilidades de que los usuarios olviden cómo iniciar sesión. Por lo tanto, los costos del departamento de soporte técnico se reducen, ya que los problemas relacionados con las contraseñas normalmente consumen la mayor parte de sus recursos.

Muchas organizaciones usan la [sincronización de contraseñas de Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), una característica de [Azure AD Connect](active-directory-aadconnect.md) que sincroniza las contraseñas de los usuarios de una instancia de Active Directory local con Azure Active Directory (Azure AD) como una forma de proporcionar a los usuarios las mismas credenciales para los recursos locales y los servicios en la nube. Sin embargo, otras organizaciones requieren que las contraseñas, incluso en un formulario con hash, no salgan de los límites internos de su organización.

La característica de autenticación de paso a través de Azure AD ofrece una solución sencilla para estas organizaciones. Cuando los usuarios inician sesión en Azure AD, esta característica garantiza que sus contraseñas se validan directamente con la instancia de Active Directory local. Esta característica también proporciona las siguientes ventajas:

- Fácil de usar
  - La validación de la contraseña se realiza sin la necesidad de realizar complejas implementaciones locales o la configuración de la red.
  - El conector local ligero escucha las solicitudes de validación de contraseña y las responde.
  - El conector local recibe automáticamente mejoras de las características y corrige los errores.
  - Se puede configurar junto con [Azure AD Connect](active-directory-aadconnect.md). El conector local ligero se instala en el mismo servidor que Azure AD Connect.
- Protección
  - Las contraseñas locales nunca se almacenan en la nube.
  - El conector local ligero solo realiza conexiones salientes desde la red. Por lo tanto, no es necesario instalar el conector en una red perimetral, también conocida como DMZ.
  - La autenticación de paso a través funciona perfectamente con Azure Multi-factor Authentication.
- Confiable y escalable
  - Se pueden instalar conectores locales ligeros adicionales en varios servidores para lograr una alta disponibilidad de las solicitudes de inicio de sesión.

![Autenticación de paso a través de Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Puede combinar la autenticación de paso a través con la característica de [inicio de sesión único de conexión directa](active-directory-aadconnect-sso.md) (SSO). De esta manera, cuando los usuarios se encuentren en sus equipos corporativos dentro de la red corporativa, no tendrán siquiera que escribir la contraseña para iniciar sesión en Azure AD (una experiencia realmente integrada).

## <a name="whats-available-during-preview"></a>¿Que está disponible en la versión preliminar?

>[!NOTE]
>La autenticación de paso a través de Azure AD está actualmente en versión preliminar. Es una característica gratuita y no es necesario usar ninguna versión de pago de Azure AD para usarla.

Los siguientes escenarios son totalmente compatibles durante la versión preliminar:

- Todas las aplicaciones web basadas en explorador
- Las aplicaciones cliente de Office 365 que admitan la [autenticación moderna](https://aka.ms/modernauthga)

En la versión preliminar, los siguientes escenarios _no_ se admiten:

- Las aplicaciones cliente de Office heredadas y Exchange ActiveSync (es decir, las aplicaciones de correo electrónico nativas en dispositivos móviles). <br>Se recomienda a las organizaciones que cambien a la autenticación moderna, en caso de que sea posible. Esto permite la compatibilidad de la autenticación de paso a través, pero también ayuda a proteger sus identidades mediante características de [acceso condicional](../active-directory-conditional-access.md) como la autenticación multifactor.
- Azure AD Join para dispositivos con Windows 10.

>[!IMPORTANT]
>Como solución alternativa para los escenarios donde la función de autenticación de paso a través no está permitida en la actualidad (aplicaciones cliente de Office heredadas, Exchange ActiveSync y Azure AD Join para dispositivos con Windows 10), cuando se habilita la autenticación de paso a través, la sincronización de contraseñas también se habilita de manera predeterminada. La sincronización de contraseñas actúa como reserva únicamente en estos escenarios concretos. Si no se necesita, la sincronización de contraseñas se puede desactivar en la página [Características opcionales](active-directory-aadconnect-get-started-custom.md#optional-features) del asistente de Azure AD Connect.

### <a name="prerequisites"></a>Requisitos previos

Para poder habilitar la autenticación de paso a través de Azure AD, necesita tener antes lo siguiente:

- Un inquilino de Azure AD del que sea administrador global.

  >[!NOTE]
  >Se recomienda encarecidamente que la cuenta de administrador global esté solo en la nube. De esta manera, puede administrar la configuración del inquilino en caso de que los servicios locales fallen o no estén disponibles. Información acerca de la [incorporación de una cuenta de administrador global que está solo en la nube](../active-directory-users-create-azure-portal.md).

- Azure AD Connect, versión 1.1.486.0 o posterior. Se recomienda usar la [versión más reciente de Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- Un servidor con Windows Server 2012 R2 o posterior en el que ejecutar Azure AD Connect.
  - Este servidor debe ser miembro del mismo bosque de AD que los usuarios cuyas contraseñas se deben validar.
  - Tenga en cuenta que un conector de autenticación de paso a través se instala en el mismo servidor como Azure AD Connect. Compruebe que la versión del conector es la 1.5.58.0 o una posterior.

    >[!NOTE]
    >Se admiten entornos de varios bosques si hay relaciones de confianza de bosque entre los bosques de AD y si el enrutamiento de sufijos de nombre está configurado correctamente.

- Si desea alta disponibilidad, necesitará servidores adicionales que ejecuten Windows Server 2012 R2 o posterior para instalar conectores independientes. (La versión del conector debe ser 1.5.58.0 o posterior).
- Si hay un firewall entre cualquiera de estos conectores y Azure AD:
    - Si está habilitado el filtrado para direcciones URL, asegúrese de que el conector puede comunicarse con las siguientes direcciones URL:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - Asegúrese de que los conectores también crean conexiones IP directas con los [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Asegúrese de que el firewall no realiza la inspección de SSL, ya que los conectores usan certificados de cliente para comunicarse con Azure AD.
    - Asegúrese de que el conector puede realizar solicitudes salientes a Azure AD a través de los puertos 80 y 443.
      - Si el firewall aplica las reglas en función de los usuarios de origen, abra estos puertos para el tráfico procedente de los servicios de Windows que se ejecutan como servicio de red.
      - Los conectores realizan solicitudes HTTP a través del puerto 80 para la descarga de listas de revocación de certificados SSL. Esto también es necesario para que la funcionalidad de actualización automática se lleve a cabo correctamente.
      - Los conectores realizan solicitudes HTTPS a través del puerto 443 para todas las demás operaciones, como habilitar y deshabilitar la característica, registrar conectores, descargar actualizaciones de conector y controlar todas las solicitudes de inicio de sesión de usuario.

     >[!NOTE]
     >Recientemente hemos realizado mejoras para reducir el número de puertos que necesitan los conectores para comunicarse con nuestro servicio. Si se ejecutan versiones anteriores de Azure AD Connect o de los conectores independientes, los puertos adicionales (5671, 8080, 9090, 9091, 9350, 9352, 10100-10120) deben mantenerse abiertos.

### <a name="enable-azure-ad-pass-through-authentication"></a>Habilitación de la autenticación de paso a través de Azure AD

La autenticación de paso a través de Azure AD se puede habilitar por medio de Azure AD Connect.

Si va a realizar una instalación nueva de Azure AD Connect, elija la [ruta de acceso de instalación personalizada](active-directory-aadconnect-get-started-custom.md). En la página **Inicio de sesión de usuario**, seleccione **Autenticación de paso a través**. Si finaliza satisfactoriamente, se instala un conector de autenticación de paso a través en el mismo servidor que Azure AD Connect. Además, habilita la característica de autenticación de paso a través en su inquilino.

![Azure AD Connect: inicio de sesión de usuario](./media/active-directory-aadconnect-sso/sso3.png)

Si ya tiene instalado Azure AD Connect, configure la instancia con la ruta de [instalación rápida](active-directory-aadconnect-get-started-express.md) o de [instalación personalizada](active-directory-aadconnect-get-started-custom.md), elija **Cambiar inicio de sesión de usuario** en Azure AD Connect y haga clic en **Siguiente**. Después, seleccione **Autenticación de paso a través** para instalar un conector de autenticación de paso a través en el mismo servidor que Azure AD Connect y habilitar la característica en el inquilino.

![Azure AD Connect: Change user sign-in page (Cambiar página de inicio de sesión del usuario)](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>La autenticación de paso a través de Azure AD es una característica de nivel de inquilino. Afecta al inicio de sesión de usuario en todos los dominios administrados de su inquilino. Sin embargo, los usuarios de dominios federados seguirán iniciando sesión mediante Servicios de federación de Active Directory (ADFS) o cualquier otro proveedor de federación que se haya configurado previamente. Si convierte un dominio federado en administrado, todos los usuarios del mismo empiezan automáticamente a iniciar sesión mediante autenticación de paso a través. A los usuarios que están solo en la nube no les afecta la autenticación de paso a través.

Si planea utilizar la autenticación de paso a través en una implementación de producción, se recomienda encarecidamente instalar un segundo conector en un servidor independiente (que no sea donde se ejecutan Azure AD Connect y el primer conector). Esto garantiza alta disponibilidad de las solicitudes de inicio de sesión. Puede instalar tantos conectores adicionales como sea necesario. La cantidad que se vaya a instalar depende el número máximo y medio de solicitudes de inicio de sesión que controle el inquilino.

Para implementar un conector independiente, siga las instrucciones a continuación.

#### <a name="step-1-download-and-install-the-connector"></a>Paso 1: Descargar e instalar el conector

En este paso se descarga el software del conector y se instala en el servidor.

1.    [Descargue](https://go.microsoft.com/fwlink/?linkid=837580) el conector más reciente. Compruebe que la versión del conector es la 1.5.58.0 o una posterior.
2.    Abra el símbolo del sistema como administrador.
3.    Ejecute el siguiente comando. La opción **/q** significa "instalación desatendida", que no le pide que acepte el contrato de licencia para el usuario final: `
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>Solo se puede instalar un conector por servidor.

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Paso 2: Registrar el conector en Azure AD para la autenticación de paso a través

En este paso, se registra en nuestro servicio el conector instalado en su servidor y se hace que esté disponible para recibir solicitudes de inicio de sesión.

1.    Abra una ventana de PowerShell como administrador.
2.    Navegue hasta **C:\Archivos de programa\Microsoft AAD App Proxy Connector** y ejecute el script como se muestra a continuación: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Cuando se le solicite, escriba las credenciales de la cuenta de administrador global en el inquilino de Azure AD.

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>¿Cómo funciona la autenticación de paso a través de Azure AD?

Cuando un usuario intenta iniciar sesión en Azure AD (y si la autenticación de paso a través está habilitada en el inquilino), ocurre lo siguiente:

1. El usuario escribe su nombre de usuario y contraseña en la página de inicio de sesión de Azure AD. Nuestro servicio coloca el nombre de usuario y la contraseña (cifrada mediante clave pública) en cola para la validación.
2. Uno de los conectores locales disponibles realiza una llamada saliente a la cola y recupera el nombre de usuario y la contraseña.
3. Después, el conector valida el nombre de usuario y la contraseña en Active Directory mediante las API de Windows estándares (un mecanismo similar al que se usa en AD FS). Tenga en cuenta que el nombre de usuario puede ser que el nombre de usuario predeterminado local (normalmente, `userPrincipalName`) o de otro atributo (conocido como `Alternate ID`) configurado en Azure AD Connect.
4. Después, el controlador de dominio local evalúa la solicitud y devuelve una respuesta (satisfactoria o de error) al conector,
5. que, a su vez, la devuelve a Azure AD.
6. Luego, Azure AD evalúa la respuesta y responde al usuario según corresponda. Por ejemplo, genera un token para la aplicación o solicita Multi-Factor Authentication.

En el siguiente diagrama se ilustran los distintos pasos. Todas las solicitudes y respuestas se realizan a través del canal HTTPS.

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="password-writeback"></a>Escritura diferida de contraseñas

En caso de que haya configurado la [escritura diferida de contraseñas](../active-directory-passwords-update-your-own-password.md) en el inquilino y para un usuario concreto, si el usuario inicia sesión mediante la autenticación de paso a través, puede cambiar o restablecer la contraseña como antes. La contraseña se volverá a escribir en la instancia local de Active Directory, tal como cabría esperar.

Sin embargo, si no está configurada la escritura diferida de contraseñas en el inquilino o el usuario no tiene una licencia válida de Azure AD asignada, el usuario no podrá actualizar la contraseña en la nube, aunque haya expirado. En su lugar, verá el siguiente mensaje: "La organización no le permite actualizar la contraseña en este sitio. Actualícela de acuerdo con el método que recomienda la organización o, si necesita ayuda, póngase en contacto con el administrador".

## <a name="next-steps"></a>Pasos siguientes
- Consulte cómo habilitar la característica de [Inicio de sesión único de conexión directa con Azure Active Directory](active-directory-aadconnect-sso.md) en el inquilino.
- Leer nuestra [guía de solución de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) para aprender a resolver problemas comunes de la autenticación de paso a través de Azure AD.

## <a name="feedback"></a>Comentarios
Sus comentarios son importantes. Si tiene alguna pregunta, utilice la sección de comentarios. Utilice nuestro [foro de UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) para las solicitudes de características nuevas.


