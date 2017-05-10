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
ms.date: 04/24/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: d3c3f6ba0da73a8297f437a56f190f90274957ab
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Configuración del inicio de sesión del usuario con la autenticación de paso a través de Azure Active Directory

## <a name="what-is-azure-active-directory-azure-ad-pass-through-authentication"></a>¿Qué es la autenticación de paso a través de Azure Active Directory (Azure AD)?

Permitir a los usuarios utilizar las mismas credenciales (contraseñas) para iniciar sesión tanto en los recursos locales como en los servicios en la nube es beneficioso no solo para los usuarios sino también para la organización. Los usuarios tienen una contraseña menos que recordar. Esto mejora la experiencia del usuario y reduce las posibilidades de que los usuarios olviden cómo iniciar sesión. Esto, a su vez, reduce los costos del departamento de soporte técnico, ya que los problemas relacionados con las contraseñas normalmente consumen la mayor parte de los recursos de soporte técnico.

Muchas organizaciones usan [la sincronización de contraseñas de Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), una característica de [Azure AD Connect](active-directory-aadconnect.md) que sincroniza las contraseñas de los usuarios de una instancia de Active Directory local con Azure AD, como una forma de proporcionar a los usuarios las mismas credenciales en los recursos locales y en los servicios en la nube. Sin embargo, otras organizaciones requieren que las contraseñas, incluso en un formulario con hash, no salgan de los límites internos de su organización.

La autenticación de paso a través de Azure AD proporciona una solución sencilla para estas organizaciones. Cuando los usuarios inician sesión en Azure AD, garantiza que las contraseñas de los usuarios se validan directamente con su instancia de Active Directory local. Esta característica también proporciona las siguientes ventajas:

- Fácil de usar
  - La validación de la contraseña se realiza sin la necesidad de realizar complejas implementaciones locales o la configuración de la red.
  - Solo utiliza un conector local ligero que escucha las solicitudes de validación de contraseña y las responde.
  - El conector local puede actualizarse automáticamente para poder recibir mejoras de características y correcciones de errores automáticamente.
  - Se puede configurar junto con [Azure AD Connect](active-directory-aadconnect.md). El conector local ligero se instala en el mismo servidor que Azure AD Connect.
- Protección
  - Las contraseñas locales nunca se almacenan en la nube.
  - El conector local ligero solo realiza conexiones salientes desde dentro de la red. Por consiguiente, no hay ningún requisito para instalar el conector en una red perimetral.
  - La autenticación de paso a través funciona perfectamente con Azure Multi-factor Authentication.
- Confiable y escalable
  - Se pueden instalar conectores locales ligeros adicionales en varios servidores para lograr una alta disponibilidad de las solicitudes de inicio de sesión.

![Autenticación de paso a través de Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Si se combina con la característica [Inicio de sesión único de conexión directa](active-directory-aadconnect-sso.md), los usuarios no necesitarán escribir sus contraseñas para iniciar sesión en Azure AD, cuando se encuentran en sus equipos corporativos dentro de su red corporativa (una experiencia realmente integrada).

## <a name="whats-available-during-preview"></a>¿Que está disponible en la versión preliminar?

>[!NOTE]
>La autenticación de paso a través de Azure AD está actualmente en versión preliminar. Es una característica gratuita y no es necesario usar ninguna edición de pago de Azure AD para usarla. 

Los siguientes escenarios son totalmente compatibles durante la versión preliminar:

- Todas las aplicaciones basadas en explorador.
- Aplicaciones de cliente de Office 365 que admiten la [autenticación moderna](https://aka.ms/modernauthga).

Los siguientes escenarios NO se admiten durante la versión preliminar:

- Las aplicaciones cliente de Office heredadas y Exchange ActiveSync (es decir, aplicaciones de correo electrónico nativas en dispositivos móviles).
  - Se recomienda a las organizaciones que cambien a la autenticación moderna, en caso de que sea posible. Esto permite la compatibilidad de la autenticación de paso a través, pero también ayuda a proteger sus identidades mediante características de [acceso condicional](../active-directory-conditional-access.md) como la autenticación multifactor.
- Azure AD Join para dispositivos con Windows 10.

>[!IMPORTANT]
>Como solución alternativa para los escenarios que la función de autenticación de paso a través no admite en la actualidad (aplicaciones cliente de Office heredadas, Exchange ActiveSync y Azure AD Join para dispositivos con Windows 10), la sincronización de contraseñas también se habilita de manera predeterminada cuando se habilita la autenticación de paso a través. La sincronización de contraseñas actúa como reserva solo en estos escenarios concretos. Si no se necesita, la sincronización de contraseñas se puede desactivar en la página [Características opcionales](active-directory-aadconnect-get-started-custom.md#optional-features) en el asistente de Azure AD Connect.

## <a name="how-to-enable-azure-ad-pass-through-authentication"></a>¿Cómo se habilita la autenticación de paso a través de Azure AD?

### <a name="pre-requisites"></a>Requisitos previos

Para poder habilitar la autenticación de paso a través de Azure AD, es preciso cumplir los siguientes requisitos previos:

- Un inquilino de Azure AD del que sea administrador global.

>[!NOTE]
>Es muy recomendable que la cuenta del administrador global esté solo en la nube para que pueda administrar la configuración del inquilino, en caso de que sus servicios locales generen errores o dejen de estar disponibles. Puede agregar una cuenta de administrador global solo en la nube como se muestra [aquí](../active-directory-users-create-azure-portal.md).

- Azure AD Connect, versión 1.1.486.0 o superior. Se recomienda usar la [versión más reciente de Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- Un servidor con Windows Server 2012 R2 o posterior en el que ejecutar Azure AD Connect.
  - Este servidor debe ser miembro del mismo bosque de AD que los usuarios cuyas contraseñas se deben validar.
  - Tenga en cuenta que un conector de autenticación de paso a través se instala en el mismo servidor como Azure AD Connect. Compruebe que la versión del conector es la 1.5.58.0 o una superior.

>[!NOTE]
>Los entornos de varios bosques se admiten si hay relaciones de confianza de bosque entre los bosques de AD y el enrutamiento de sufijos de nombre está configurado correctamente.

- Si desea alta disponibilidad, necesitará servidores adicionales que ejecuten Windows Server 2012 R2 o posterior para instalar conectores independientes (la versión tiene que ser la 1.5.58.0 o una superior).
- Si hay un firewall entre cualquiera de estos conectores y Azure AD, asegúrese de lo siguiente:
    - Si está habilitado el filtrado para direcciones URL, asegúrese de que el conector puede comunicarse con las siguientes direcciones URL:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - Los conectores también crean conexiones IP directas con los [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Asegúrese de que el firewall no realiza la inspección de SSL mientras los conectores usan certificados de cliente para comunicarse con Azure AD.
    - Asegúrese de que el conector puede realizar solicitudes salientes a Azure AD a través de los puertos 80 y 443.
      - Si el firewall aplica las reglas en función de los usuarios de origen, abra estos puertos para el tráfico procedente de los servicios de Windows que se ejecutan como un servicio de red.
      - Los conectores realizan solicitudes HTTP a través del puerto 80 para la descarga de listas de revocación de certificados SSL. Esto también es necesario para que la funcionalidad de actualización automática se lleve a cabo correctamente.
      - Los conectores realizan solicitudes HTTPS a través del puerto 443 para todas las demás operaciones, como habilitar y deshabilitar la característica, registrar conectores, descargar actualizaciones de conector y controlar todas las solicitudes de inicio de sesión de usuario.

>[!NOTE]
>Recientemente hemos realizado mejoras para reducir el número de puertos que necesitan los conectores para comunicarse con nuestro servicio. Si se ejecutan versiones anteriores de Azure AD Connect o de los conectores independientes, los puertos adicionales (5671, 8080, 9090, 9091, 9350, 9352, 10100 10120) deben mantenerse abiertos.

### <a name="enabling-azure-ad-pass-through-authentication"></a>Habilitación de la autenticación de paso a través de Azure AD

La autenticación de paso a través de Azure AD se puede habilitar por medio de Azure AD Connect.

Si va a realizar una instalación nueva de Azure AD Connect, elija la [ruta de acceso de instalación personalizada](active-directory-aadconnect-get-started-custom.md). En la página "Inicio de sesión de usuario", seleccione "Autenticación de paso a través". Si finaliza satisfactoriamente, se instala un conector de autenticación de paso a través en el mismo servidor que Azure AD Connect. Además, habilita la característica de autenticación de paso a través en su inquilino.

![Azure AD Connect: Inicio de sesión de usuario](./media/active-directory-aadconnect-sso/sso3.png)

Si ya tiene una instalación de Azure AD Connect, utilice la ruta de acceso de la [instalación rápida](active-directory-aadconnect-get-started-express.md) o de la [instalación personalizada](active-directory-aadconnect-get-started-custom.md), elija "Change user sign-in page" (Cambiar página de inicio de sesión del usuario) en Azure AD Connect y haga clic en "Siguiente". Después, seleccione "Autenticación de paso a través" para instalar un conector de autenticación de paso a través en el mismo servidor que Azure AD Connect y habilitar la característica en su inquilino.

![Azure AD Connect: Change user sign-in page (Cambiar página de inicio de sesión del usuario)](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>La autenticación de paso a través de Azure AD es una característica de nivel de inquilino. Afecta al inicio de sesión de usuario en todos los dominios administrados de su inquilino. Sin embargo, los usuarios de dominios federados seguirán iniciando sesión mediante Servicios de federación de Active Directory (ADFS) o cualquier otro proveedor de federación que se haya configurado previamente. Si convierte un dominio de federado a administrado, todos los usuarios del mismo empiezan automáticamente a iniciar sesión mediante autenticación de paso a través. A los usuarios que están solo en la nube no les afecta la autenticación de paso a través.

### <a name="ensuring-high-availability"></a>Garantía de alta disponibilidad

Si planea utilizar la autenticación de paso a través en una implementación de producción, se recomienda encarecidamente que instale un segundo conector en un servidor independiente (que no sea en el que se ejecutan Azure AD Connect y el primer conector) para asegurarse de que dispone de alta disponibilidad de las solicitudes de inicio de sesión. Puede instalar tantos conectores adicionales como necesite; la cantidad que se instale se basa en el número máximo y medio de solicitudes de inicio de sesión que controla el inquilino.

Para implementar un conector independiente, siga estas instrucciones:

#### <a name="step-1-download-and-install-the-connector"></a>Paso 1: Descargar e instalar el conector

En este paso se descarga el software del conector y se instala en el servidor.

1.    [Descargue](https://go.microsoft.com/fwlink/?linkid=837580) el conector más reciente. Compruebe que la versión del conector es la 1.5.58.0 o una superior.
2.    Abra un símbolo del sistema como administrador.
3.    Ejecute el siguiente comando (/q significa instalación desatendida, a instalación no le pide que acepte el contrato de licencia para el usuario final):

`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`
>[!NOTE]
>Solo se puede instalar un conector por servidor.

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Paso 2: Registrar el conector en Azure AD para la autenticación de paso a través

En este paso, se registra en nuestro servicio el conector instalado en su servidor y se hace que esté disponible para recibir solicitudes de inicio de sesión.

1.    Abra una ventana de PowerShell como administrador.
2.    Navegue hasta **C:\Archivos de programa\Microsoft AAD App Proxy Connector** y ejecute el script como se muestra a continuación: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Cuando se le solicite, escriba las credenciales de una cuenta de administrador global en el inquilino de Azure AD.

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>¿Cómo funciona la autenticación de paso a través de Azure AD?

Cuando un usuario intenta iniciar sesión en Azure AD (y si la autenticación de paso a través está habilitada en el inquilino), ocurre lo siguiente:

1. El usuario escribe su nombre de usuario y contraseña en la página de inicio de sesión de Azure AD. Nuestro servicio coloca el nombre de usuario y la contraseña (cifrada mediante una clave pública) en una cola para su validación.
2. Uno de los conectores locales disponibles realiza una llamada saliente a la cola y recupera el nombre de usuario y la contraseña.
3. Después, el conector valida el nombre de usuario y la contraseña en Active Directory mediante las API de Windows estándar (un mecanismo similar al que usa AD FS). Tenga en cuenta que el nombre de usuario puede ser que el nombre de usuario predeterminado local (normalmente, "userPrincipalName") o de otro atributo (conocido como "Id. alternativo") configurado en Azure AD Connect.
4. Después, el controlador de dominio local evalúa la solicitud y devuelve una respuesta (satisfactoria o error) al conector,
5. que, a su vez, la devuelve a Azure AD.
6. Luego, Azure AD evalúa la respuesta y responde al usuario según corresponda. Por ejemplo, genera un token para la aplicación o solicita la autenticación multifactor.

En el diagrama siguiente también se muestran los distintos pasos. Tenga en cuenta que todas las solicitudes y respuestas que se realizan a través del canal HTTPS.

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="note-about-password-writeback"></a>Nota acerca de la escritura diferida de contraseñas

En caso de que haya configurado la [escritura diferida de contraseñas](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) en su inquilino y para un usuario concreto, si el usuario inicia sesión mediante la autenticación de paso a través, puede cambiar o restablecer su contraseña como antes. La contraseña se volverá a escribir en la instancia local de Active Directory, tal como cabría esperar.

Sin embargo, si cualquiera de estas condiciones no se cumple (no está configurada la escritura diferida de contraseñas o el usuario no tiene una licencia válida de Azure AD asignada), el usuario no podrá actualizar sus contraseñas en la nube, ni siquiera cuando su contraseña haya caducado. En su lugar verá el siguiente mensaje: "La organización no permite actualizar la contraseña en este sitio. Actualícela de acuerdo con el método que recomienda la organización o, si necesita ayuda, póngase en contacto con el administrador. ".

## <a name="troubleshooting-pass-through-authentication"></a>Solución de problemas de la autenticación de paso a través

Esta sección le ayudará a encontrar información para solucionar algunos de los problemas comunes no solo durante la instalación, el registro o la desinstalación de conectores de autenticación de paso a través (ya sea a través de Azure AD Connect o un conector independiente), sino también durante la habilitación y manejo de la característica en su inquilino.

### <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Problemas durante la instalación de conectores (a través de Azure AD Connect o de un conector independiente)

#### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Ya existe un conector del proxy de aplicación de Azure AD

No se puede instalar un conector de autenticación de paso a través en el mismo servidor que un conector del [proxy de aplicación de Azure AD](../../active-directory/active-directory-application-proxy-get-started.md). El conector de autenticación de paso a través se tendrá que instalar en un servidor independiente.

#### <a name="an-unexpected-error-occured"></a>Se ha producido un error inesperado

[Recopile los registros del conector](#collecting-pass-through-authentication-connector-logs) en el servidor y póngase en contacto con el Soporte técnico de Microsoft para que le solucionen el problema.

### <a name="issues-during-registration-of-connectors"></a>Problemas durante el registro de conectores

#### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>No se pudo realizar el registro del conector porque había puertos bloqueados

Asegúrese de que el servidor en el que se ha instalado el conector puede comunicarse tanto con nuestras direcciones URL de servicio como con los puertos que se enumeran [aquí](#pre-requisites).

#### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>No se puede registrar el conector debido a errores de autorización de token o cuenta

Asegúrese de que usa una cuenta de administrador global solo en la nube para todas las operaciones de instalación y registro de conector independiente o Azure AD Connect. Hay un problema conocido con las cuentas de administrador global habilitadas para MFA; desactive temporalmente MFA (solo para completar las operaciones) para proporcionar una solución alternativa.

#### <a name="an-unexpected-error-occurred"></a>Se ha producido un error inesperado

[Recopile los registros del conector](#collecting-pass-through-authentication-connector-logs) en el servidor y póngase en contacto con el Soporte técnico de Microsoft para que le solucionen el problema.

### <a name="issues-during-un-installation-of-connectors"></a>Problemas durante la desinstalación de conectores

#### <a name="warning-message-when-un-installing-azure-ad-connect"></a>Mensaje de advertencia al desinstalar Azure AD Connect

Si la autenticación de paso a través está habilitada en su inquilino e intenta desinstalar Azure AD Connect, aparecerá el siguiente mensaje de advertencia: "Users will not be able to sign-in to Azure AD unless you have other pass-through authentication agents installed on other servers." (Los usuarios no podrán iniciar sesión en Azure AD, a menos que tenga otros agentes de autenticación de paso a través instalados en otros servidores).

Para no interrumpir el inicio de sesión del usuario, es preciso tener una instalación de [alta disponibilidad](#ensuring-high-availability) en vigor para desinstalar Azure AD Connect.

### <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Problemas con la habilitación de la característica de autenticación de paso a través

#### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>La característica no se pudo habilitar porque no había conectores disponibles

Necesita tener al menos conector activo para habilitar la autenticación de paso a través en el inquilino. Puede instalar un conector mediante la instalación de Azure AD Connect o de un conector independiente.

#### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>La característica no se habilitó porque había puertos bloqueados

Asegúrese de que el servidor en el que se ha instalado Azure AD Connect puede comunicarse tanto con nuestras direcciones URL de servicio como con los puertos que se enumeran [aquí](#pre-requisites).

#### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>No se puede realizar la habilitación de la característica debido a errores de autorización de cuenta o token.

Asegúrese de que usa una cuenta de administrador global solo en la nube cuando habilite la característica. Hay un problema conocido con las cuentas de administrador global habilitadas para Multi-Factor Authentication (MFA); desactive temporalmente MFA (solo para completar la operación) para proporcionar una solución alternativa.

### <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Problemas al usar la característica de autenticación de paso a través

#### <a name="user-facing-sign-in-errors"></a>Errores de inicio de sesión a los que se enfrenta el usuario

La característica informa de los siguientes errores a los que se enfrenta el usuario en la pantalla de inicio de sesión de Azure AD. Dichos errores se detallan a continuación, junto con los pasos para resolverlos.

|Error|Description|Resolución
| --- | --- | ---
|AADSTS80001|No es posible conectarse a Active Directory.|Asegúrese de que los servidores del conector son miembros del mismo bosque de AD que los usuarios cuyas contraseñas hay que validar y que pueden conectarse a Active Directory.  
|AADSTS8002|Se ha agotado el tiempo de espera al conectarse a Active Directory.|Asegúrese de que Active Directory está disponible y responde a las solicitudes de los conectores.
|AADSTS80004|El nombre de usuario transferido al conector no era válido.|Asegúrese de que el usuario esté intentando iniciar sesión con el nombre de usuario correcto.
|AADSTS80005|La validación encontró una excepción WebException impredecible|Es probable que sea un error transitorio. Vuelva a intentarlo. Si el error no desaparece, póngase en contacto con el soporte técnico de Microsoft.
|AADSTS80007|Error al establecer comunicación con Active Directory.|Compruebe los registros del conector para más información y verifique que Active Directory está funcionando según lo previsto.

### <a name="collecting-pass-through-authentication-connector-logs"></a>Recopilación de registros del conector de autenticación de paso a través

En función del tipo de problema, es posible que dichos registros tenga que buscarlos en distintos lugares.

#### <a name="connector-event-logs"></a>Registros de eventos de conector

Para ver los errores relacionados con el conector, abra la aplicación Visor de eventos en el servidor y consulte **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Para ver registros detallados de análisis y depuración, puede habilitar el registro de "Sesión". No ejecute el conector con este registro habilitado durante las operaciones normales; úselo solo para solucionar problemas. Tenga en cuenta que el contenido del registro solo se ve cuando el registro se vuelve a deshabilitar.

#### <a name="detailed-trace-logs"></a>Registros de seguimiento detallados

Para solucionar errores de inicio de sesión de los usuarios, busque los registros de seguimiento en **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Dichos registros incluyen los motivos por los que un usuario concreto no pudo iniciar sesión mediante la característica de autenticación de paso a través. A continuación encontrará una entrada de registro de ejemplo:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Para obtener detalles descriptivos del error ('1328' en el ejemplo anterior), abra el símbolo del sistema y ejecute el siguiente comando. Nota: será preciso que reemplace '1328' por el número de error real que ve en los registros.

`Net helpmsg 1328`

El resultado debería ser similar al que se muestra a continuación:

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

#### <a name="domain-controller-logs"></a>Registros de controlador de dominio

Si están habilitados los registros de auditoría, se puede encontrar información adicional en los registros de seguridad de los controladores de dominio. A continuación puede ver una manera sencilla de consultar las solicitudes de inicio de sesión que han enviado los conectores de autenticación de paso a través:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="feedback"></a>Comentarios

Sus comentarios son importantes. Puede enviarlos a la siguiente dirección de correo electrónico: [aadopauthfeedback@microsoft.com](mailto:aadopauthfeedback@microsoft.com). Si tiene las solicitudes de características nuevas, use nuestro [foro de UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect).

