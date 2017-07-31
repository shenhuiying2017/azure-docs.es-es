---
title: "Autenticación de paso a través de Azure AD: inicio rápido | Microsoft Docs"
description: "En este artículo se describe cómo empezar a usar la autenticación de paso a través de Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c1bc7cc5fe53d04019f68a520fb03c9187a6148b
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticación de paso a través de Azure Active Directory: inicio rápido

La autenticación de paso a través de Azure Active Directory (Azure AD) permite a los usuarios iniciar sesión en aplicaciones basadas en la nube y locales con las mismas contraseñas. Los usuarios inician sesión mediante la validación de sus contraseñas directamente en la instancia de Active Directory local.

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Implementación de la autenticación de paso a través de Azure AD

Para implementar la autenticación de paso a través, debe seguir estos pasos:
1. *Comprobar los requisitos previos*: configure el entorno local y de inquilinos correctamente antes de habilitar la característica.
2. *Habilitar la característica*: active la autenticación de paso a través en el inquilino e instale un agente local ligero para controlar las solicitudes de validación de contraseñas.
3. *Probar la característica*: pruebe el inicio de sesión de los usuarios con la autenticación de paso a través.
4. *Garantizar la alta disponibilidad*: instale un segundo agente independiente que proporcione alta disponibilidad para las solicitudes de inicio de sesión.

## <a name="step-1-check-prerequisites"></a>Paso 1: Comprobación de los requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos:

### <a name="on-the-azure-portal"></a>En el Portal de Azure

1. Cree una cuenta de administrador global solo en la nube en el inquilino de Azure AD. De esta manera, puede administrar la configuración del inquilino en caso de que los servicios locales fallen o no estén disponibles. Información acerca de la [incorporación de una cuenta de administrador global que está solo en la nube](../active-directory-users-create-azure-portal.md). Este paso es esencial para garantizar que no queda bloqueado fuera de su inquilino.
2. Agregue uno o varios [nombres de dominio personalizados](../active-directory-add-domain.md) al inquilino de Azure AD. Los usuarios inician sesión con uno de estos nombres de dominio.

### <a name="in-your-on-premises-environment"></a>En el entorno local

1. Identifique un servidor con Windows Server 2012 R2 o posterior en el que ejecutar Azure AD Connect. Agregue el servidor al mismo bosque de AD que los usuarios cuyas contraseñas se deben validar.
2. Instale la [versión más reciente de Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) en el servidor identificado en el paso 2. Si ya tiene Azure AD Connect en ejecución, asegúrese de que la versión es 1.1.486.0 o posterior.
3. Identifique un servidor adicional que ejecute Windows Server 2012 R2 o posterior en el que se ejecute un agente de autenticación independiente. La versión del agente de autenticación debe ser 1.5.58.0 o posterior. Este servidor es necesario para garantizar la alta disponibilidad de las solicitudes de inicio de sesión. Agregue el servidor al mismo bosque de AD que los usuarios cuyas contraseñas se deben validar.
4. Si hay un firewall entre los servidores y Azure AD, debe configurar los elementos siguientes:
   - Abrir los puertos: asegúrese de que los agentes de autenticación en los servidores pueden realizar solicitudes salientes a Azure AD a través de los puertos 80 y 443. Si el firewall aplica las reglas en función de los usuarios de origen, abra estos puertos para el tráfico procedente de los servicios de Windows que se ejecutan como un servicio de red.
   - Permitir puntos de conexión de Azure AD: si se habilita el filtrado de direcciones URL, asegúrese de que los agentes de autenticación pueden comunicarse con **\*.msappproxy.net** y **\*.servicebus.windows.net**.
   - Comprobar las conexiones de Direct IP: asegúrese de que los agentes de autenticación en los servidores pueden efectuar conexiones de Direct IP con los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

## <a name="step-2-enable-the-feature"></a>Paso 2: Habilitación de la característica

La autenticación de paso a través se puede habilitar por medio de [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>La autenticación de paso a través se puede habilitar en el servidor principal o provisional de Azure AD Connect. Se recomienda muy especialmente habilitarlo desde el servidor principal.

Si va a instalar Azure AD Connect por primera vez, elija la [ruta de acceso de instalación personalizada](active-directory-aadconnect-get-started-custom.md). En la página **Inicio de sesión de usuario**, elija **Autenticación de paso a través** como método de inicio de sesión. Si se completa correctamente, se instala un agente de autenticación de paso a través en el mismo servidor que Azure AD Connect. Además, se habilita la característica de autenticación de paso a través en el inquilino.

![Azure AD Connect: inicio de sesión de usuario](./media/active-directory-aadconnect-sso/sso3.png)

Si ya tiene instalado Azure AD Connect (mediante la ruta de [instalación rápida](active-directory-aadconnect-get-started-express.md) o de [instalación personalizada](active-directory-aadconnect-get-started-custom.md)), elija **Cambiar inicio de sesión de usuario** en Azure AD Connect y haga clic en **Siguiente**. Seleccione **Autenticación de paso a través** como métodos de inicio de sesión. Si se completa correctamente, se instala un agente de autenticación de paso a través en el mismo servidor que Azure AD Connect y se habilita la característica en el inquilino.

![Azure AD Connect: Change user sign-in page (Cambiar página de inicio de sesión del usuario)](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>La autenticación de paso a través es una característica de nivel de inquilino. Su activación afecta al inicio de sesión de los usuarios en _todos_ los dominios administrados del inquilino.

## <a name="step-3-test-the-feature"></a>Paso 3: Prueba de la característica

Después del paso 2, los usuarios de todos los dominios administrados del inquilino iniciarán sesión con la autenticación de paso a través. Pero, los usuarios de dominios federados siguen iniciando sesión mediante Servicios de federación de Active Directory (AD FS) o cualquier otro proveedor de federación que se haya configurado previamente. Si convierte un dominio de federado a administrado, todos los usuarios del mismo empiezan automáticamente a iniciar sesión mediante autenticación de paso a través. A los usuarios que están solo en la nube no les afecta la autenticación de paso a través.

## <a name="step-4-ensure-high-availability"></a>Paso 4: Garantía de alta disponibilidad

Si tiene previsto implementar la autenticación de paso a través en un entorno de producción, debe instalar un agente de autenticación independiente. Instale este segundo agente de autenticación en un servidor _distinto_ al que ejecuta Azure AD Connect y el primer agente de autenticación. Esta configuración proporciona alta disponibilidad de las solicitudes de inicio de sesión. Siga estas instrucciones para implementar un agente de autenticación independiente:

### <a name="download-and-install-the-authentication-agent-software-on-your-server"></a>Descarga del software del agente de autenticación e instalación en el servidor

1.  [Descargue](https://go.microsoft.com/fwlink/?linkid=837580) el software más reciente del agente de autenticación. Compruebe que la versión es la 1.5.58.0 o una posterior.
2.  Abra el símbolo del sistema como administrador.
3.  Ejecute el siguiente comando (**/q** significa "instalación desatendida", la instalación no le pide que acepte los términos de licencia): `
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>Únicamente puede instalar un solo agente de autenticación por servidor.

### <a name="register-the-authentication-agent-with-azure-ad"></a>Registro del agente de autenticación con Azure AD

1.  Abra una ventana de PowerShell como administrador.
2.  Navegue hasta **C:\Archivos de programa\Microsoft AAD App Proxy Connector** y ejecute el script como se muestra a continuación: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.  Cuando se le solicite, escriba las credenciales de la cuenta de administrador global en el inquilino de Azure AD.

## <a name="next-steps"></a>Pasos siguientes
- [**Limitaciones actuales**](active-directory-aadconnect-pass-through-authentication-current-limitations.md): esta característica está actualmente en versión preliminar. Obtenga información sobre los escenarios que se admiten y los que no.
- [**Profundización técnica** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md): descripción del funcionamiento de esta característica.
- [**Preguntas más frecuentes**](active-directory-aadconnect-pass-through-authentication-faq.md): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): aprenda a resolver problemas comunes de esta característica.
- [**SSO de conexión directa de Azure AD**](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.

