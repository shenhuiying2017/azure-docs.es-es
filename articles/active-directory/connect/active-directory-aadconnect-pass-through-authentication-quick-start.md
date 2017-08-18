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
ms.date: 08/04/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: e5fb323090d8c3a533199b396047a1c63b5ae9f4
ms.contentlocale: es-es
ms.lasthandoff: 08/07/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticación de paso a través de Azure Active Directory: inicio rápido

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Implementación de la autenticación de paso a través de Azure AD

La autenticación de paso a través de Azure Active Directory (Azure AD) permite a los usuarios iniciar sesión en aplicaciones basadas en la nube y locales con las mismas contraseñas. Los usuarios inician sesión mediante la validación de sus contraseñas directamente en la instancia de Active Directory local.

>[!IMPORTANT]
>La autenticación de paso a través de Azure AD se encuentra actualmente en versión preliminar. Si ha usado esta característica durante la versión preliminar, debe asegurarse de actualizar las versiones preliminares de los agentes de autenticación con las instrucciones proporcionadas [aquí](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Debe seguir estas instrucciones para implementar la autenticación de paso a través:

## <a name="step-1-check-prerequisites"></a>Paso 1: Comprobación de los requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos:

### <a name="on-the-azure-active-directory-admin-center"></a>En el Centro de administración de Azure Active Directory

1. Cree una cuenta de administrador global solo en la nube en el inquilino de Azure AD. De esta manera, puede administrar la configuración del inquilino en caso de que los servicios locales fallen o no estén disponibles. Información acerca de la [incorporación de una cuenta de administrador global que está solo en la nube](../active-directory-users-create-azure-portal.md). Este paso es esencial para garantizar que no queda bloqueado fuera de su inquilino.
2. Agregue uno o varios [nombres de dominio personalizados](../active-directory-add-domain.md) al inquilino de Azure AD. Los usuarios inician sesión con uno de estos nombres de dominio.

### <a name="in-your-on-premises-environment"></a>En el entorno local

1. Identifique un servidor con Windows Server 2012 R2 o posterior en el que ejecutar Azure AD Connect. Agregue el servidor al mismo bosque de AD que los usuarios cuyas contraseñas se deben validar.
2. Instale la [última versión de Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) en el servidor identificado en el paso anterior. Si ya tiene Azure AD Connect en ejecución, asegúrese de que la versión es 1.1.557.0 o posterior.
3. Identifique un servidor adicional que ejecute Windows Server 2012 R2 o posterior en el que se ejecute un agente de autenticación independiente. La versión del agente de autenticación debe ser 1.5.193.0 o posterior. Este servidor es necesario para garantizar la alta disponibilidad de las solicitudes de inicio de sesión. Agregue el servidor al mismo bosque de AD que los usuarios cuyas contraseñas se deben validar.
4. Si hay un firewall entre los servidores y Azure AD, debe configurar los elementos siguientes:
   - Asegúrese de que los agentes de autenticación pueden realizar solicitudes **salientes** a Azure AD a través de los puertos siguientes:
   
   | Número de puerto | Cómo se usa |
   | --- | --- |
   | **80** | Descarga de listas de revocación de certificados (CRL) al validar el certificado SSL |
   | **443** | Toda la comunicación saliente con nuestro servicio |
   
   Si el firewall fuerza las reglas según los usuarios que las originan, abra estos puertos para el tráfico de Servicios de Windows que se ejecutan como un servicio de red.
   - Si el firewall o el proxy permiten la creación de listas blancas con DNS, cree una lista blanca para las conexiones a **\*.msappproxy.net** y **\*.servicebus.windows.net**. En caso contrario, permita el acceso a los [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653), que se actualizan cada semana.
   - Los agentes de autenticación necesitan acceder a **login.windows.net** y **login.microsoftonline.com** para el registro inicial, por lo que se debe abrir el firewall también para dichas direcciones URL.
   - Para la validación de certificados, desbloquee las siguientes direcciones URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** y **www.microsoft.com:80**. Estas direcciones URL se utilizan para la validación de certificados con otros productos de Microsoft, por lo que es posible que estas direcciones URL estén bloqueadas.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Paso 2: Habilitación de la compatibilidad con Exchange ActiveSync (opcional)

Siga estas instrucciones para habilitar la compatibilidad con Exchange ActiveSync:

1. Use [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) y ejecute el comando siguiente:
```
Get-OrganizationConfig | fl per*
```

2. Compruebe el valor de `PerTenantSwitchToESTSEnabled`. Si el valor es **true**, significa que el inquilino está configurado correctamente; este suele ser el caso de la mayoría de los clientes. Si el valor es **false**, ejecute el siguiente comando:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Verifique que el valor de `PerTenantSwitchToESTSEnabled` ahora está establecido en **true**. Espere una hora antes de continuar con el paso siguiente.

Si encuentra algún problema durante este paso, consulte la [guía de solución de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues) para más información.

## <a name="step-3-enable-the-feature"></a>Paso 3: Habilitación de la característica

La autenticación de paso a través se puede habilitar por medio de [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>La autenticación de paso a través se puede habilitar en el servidor principal o provisional de Azure AD Connect. Se recomienda muy especialmente habilitarlo desde el servidor principal.

Si va a instalar Azure AD Connect por primera vez, elija la [ruta de acceso de instalación personalizada](active-directory-aadconnect-get-started-custom.md). En la página **Inicio de sesión de usuario**, elija **Autenticación de paso a través** como método de inicio de sesión. Si se completa correctamente, se instala un agente de autenticación de paso a través en el mismo servidor que Azure AD Connect. Además, se habilita la característica de autenticación de paso a través en el inquilino.

![Azure AD Connect: inicio de sesión de usuario](./media/active-directory-aadconnect-sso/sso3.png)

Si ya tiene instalado Azure AD Connect (mediante la ruta de [instalación rápida](active-directory-aadconnect-get-started-express.md) o de [instalación personalizada](active-directory-aadconnect-get-started-custom.md)), elija **Cambiar inicio de sesión de usuario** en Azure AD Connect y haga clic en **Siguiente**. Seleccione **Autenticación de paso a través** como métodos de inicio de sesión. Si se completa correctamente, se instala un agente de autenticación de paso a través en el mismo servidor que Azure AD Connect y se habilita la característica en el inquilino.

![Azure AD Connect: Change user sign-in page (Cambiar página de inicio de sesión del usuario)](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>La autenticación de paso a través es una característica de nivel de inquilino. Su activación afecta al inicio de sesión de los usuarios en _todos_ los dominios administrados del inquilino. Si cambia de AD FS a Autenticación de paso a través, se recomienda esperar al menos doce horas antes de apagar la infraestructura de AD FS; este tiempo de espera sirve para garantizar que los usuarios pueden mantener la sesión iniciada en Exchange ActiveSync durante la transición.

## <a name="step-4-test-the-feature"></a>Paso 4: Prueba de la característica

Siga estas instrucciones para verificar que ha habilitado la autenticación de paso a través correctamente:

1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con las credenciales de administrador global del inquilino.
2. Seleccione **Azure Active Directory** en la barra de navegación de la izquierda.
3. Seleccione **Azure AD Connect**.
4. Verifique que la característica **Autenticación de paso a través** aparece como **Habilitado**.
5. Seleccione **Autenticación de paso a través**. En esta hoja se enumeran los servidores donde están instalados los agentes de autenticación.

![Centro de administración de Azure Active Directory: hoja de Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centro de administración de Azure Active Directory: hoja de Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

En esta fase, los usuarios de todos los dominios administrados del inquilino pueden iniciar sesión con la autenticación de paso a través. Pero, los usuarios de dominios federados siguen iniciando sesión mediante Servicios de federación de Active Directory (AD FS) o cualquier otro proveedor de federación que se haya configurado previamente. Si convierte un dominio de federado a administrado, todos los usuarios del mismo empiezan automáticamente a iniciar sesión mediante autenticación de paso a través. A los usuarios que están solo en la nube no les afecta la autenticación de paso a través.

## <a name="step-5-ensure-high-availability"></a>Paso 5: Garantía de alta disponibilidad

Si tiene previsto implementar la autenticación de paso a través en un entorno de producción, debe instalar un agente de autenticación independiente. Instale este segundo agente de autenticación en un servidor _distinto_ al que ejecuta Azure AD Connect y el primer agente de autenticación. Esta configuración proporciona alta disponibilidad de las solicitudes de inicio de sesión. Siga estas instrucciones para implementar un agente de autenticación independiente:

1. **Descargue la última versión del agente de autenticación (versiones 1.5.193.0 o posteriores)**: inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con las credenciales de administrador global del inquilino.
2. Seleccione **Azure Active Directory** en la barra de navegación de la izquierda.
3. Seleccione **Azure AD Connect** y después **Autenticación de paso a través**. Seleccione **Descargar agente**.
4. Haga clic en el botón **Accept terms & download** (Aceptar términos y descargar).
5. **Instale la última versión del agente de autenticación**: ejecute el archivo ejecutable descargado en el paso anterior. Proporcione las credenciales de administrador global de su inquilino cuando se le solicite.

![Centro de administración de Azure Active Directory: botón Download Authentication Agent (Descargar agente de autenticación)](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centro de administración de Azure Active Directory: hoja Descargar agente](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

## <a name="next-steps"></a>Pasos siguientes
- [**Limitaciones actuales**](active-directory-aadconnect-pass-through-authentication-current-limitations.md): esta funcionalidad actualmente está en su versión preliminar. Obtenga información sobre los escenarios que se admiten y los que no.
- [**Profundización técnica** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md): descripción del funcionamiento de esta característica.
- [**Preguntas más frecuentes**](active-directory-aadconnect-pass-through-authentication-faq.md): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): aprenda a resolver problemas comunes de esta característica.
- [**SSO de conexión directa de Azure AD**](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para rellenar solicitudes de características nuevas.

