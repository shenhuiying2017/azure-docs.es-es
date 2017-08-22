---
title: "Azure AD Connect: Autenticación de paso a través (actualización de la versión preliminar de los agentes de autenticación) | Microsoft Docs"
description: "En este artículo se describe cómo actualizar la configuración de la Autenticación de paso a través de Azure Active Directory (Azure AD)."
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
ms.openlocfilehash: 940cb4466ef5d730c42d04d0107f6901f55eb155
ms.contentlocale: es-es
ms.lasthandoff: 08/07/2017

---

# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Autenticación de paso a través de Azure Active Directory: actualización de la versión preliminar de los agentes de autenticación

## <a name="overview"></a>Información general

Este artículo está dirigido a los clientes que usan la Autenticación de paso a través de Azure AD en versión preliminar. Se acaba de actualizar el software del agente de autenticación y, en consecuencia, también se ha cambiado el nombre. Debe actualizar _manualmente_ la versión preliminar de los agentes de autenticación instalados en los servidores locales. Esta actualización manual requiere una única acción. Todas las actualizaciones futuras de los agentes de autenticación son automáticas. Los motivos para actualizar son los siguientes:

- Las versiones preliminares de los agentes de autenticación no recibirán más correcciones de seguridad o de errores.
-   Las versiones preliminares de los agentes de autenticación no se pueden instalar en otros servidores para lograr alta disponibilidad.

## <a name="check-versions-of-your-authentication-agents"></a>Comprobación de las versiones de los agentes de autenticación

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Paso 1: Comprobar si los agentes de autenticación están instalados

Siga estos pasos para comprobar si los agentes de autenticación están instalados:

1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con las credenciales de administrador global del inquilino.
2. Seleccione **Azure Active Directory** en la barra de navegación de la izquierda.
3. Seleccione **Azure AD Connect**. 
4. Seleccione **Autenticación de paso a través**. En esta hoja se enumeran los servidores donde están instalados los agentes de autenticación.

![Centro de administración de Azure Active Directory: hoja de Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Paso 2: Comprobar las versiones de los agentes de autenticación

Para comprobar las versiones de los agentes de autenticación, en cada servidor identificado en el paso anterior, siga estas instrucciones:

1. Vaya a **Panel de control -> Programas -> Programas y características** en el servidor local.
2. Si hay una entrada de "**Microsoft Azure AD Connect Authentication Agent**" (Agente de autenticación de Microsoft Azure AD Connect), no es necesario realizar ninguna acción en este servidor.
3. Si hay una entrada de "**Microsoft Azure AD Application Proxy Connector**" (Conector proxy de aplicación de Microsoft Azure AD), en las versiones 1.5.132.0 o anteriores, debe actualizar manualmente en este servidor.

![Versión preliminar del agente de autenticación](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Procedimientos recomendados antes de iniciar la actualización

Antes de actualizar, asegúrese de que existen los siguientes elementos:

1. **Cree cuenta de administrador global solo en la nube**: no realice la actualización sin tener una cuenta de administrador global solo en la nube para utilizarla en situaciones de emergencia en que los agentes de autenticación de paso a través no funcionen correctamente. Información acerca de la [incorporación de una cuenta de administrador global que está solo en la nube](../active-directory-users-create-azure-portal.md). Este paso es esencial y se asegura de no quedar bloqueado fuera de su inquilino.
2.  **Asegure una alta disponibilidad**: si no lo ha hecho anteriormente, instale un segundo agente de autenticación independiente para ofrecer alta disponibilidad en las solicitudes de inicio de sesión; para ello, siga estas [instrucciones](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Actualización del agente de autenticación en el servidor de Azure AD Connect

Necesita actualizar Azure AD Connect antes de actualizar el agente de autenticación en el mismo servidor. Siga estos pasos tanto en el servidor principal como en el servidor provisional de Azure AD Connect:

1. **Actualice Azure AD Connect**: siga este [artículo](./active-directory-aadconnect-upgrade-previous-version.md) y actualice a la última versión de Azure AD Connect.
2. **Desinstale la versión preliminar del agente de autenticación**: descargue [este script de PowerShell](https://aka.ms/rmpreviewagent) y ejecútelo como administrador en el servidor.
3. **Descargue la última versión del agente de autenticación (versiones 1.5.193.0 o posteriores)**: inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con las credenciales de administrador global del inquilino. Seleccione **Azure Active Directory -> Azure AD Connect -> Autenticación de paso a través -> Descargar agente**. Acepte las condiciones del servicio y descargue la última versión del agente de autenticación.
4. **Instale la última versión del agente de autenticación**: ejecute el archivo ejecutable descargado en el paso 3. Proporcione las credenciales de administrador global de su inquilino cuando se le solicite.
5. **Verifique que se ha instalado la última versión**: como se indicó anteriormente, vaya a **Panel de control -> Programas -> Programas y características** y verifique si hay una entrada de "**Microsoft Azure AD Connect Authentication Agent**" (Agente de autenticación de Microsoft Azure AD Connect).

>[!NOTE]
>Si activa la hoja Autenticación de paso a través en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) después de completar los pasos anteriores, verá dos entradas del agente de autenticación por servidor; una entrada en la que aparece el agente de autenticación como **activo** y otra, como **inactivo**. Se _espera_ que esto sea así. La entrada **Inactivo** se quita automáticamente después de unos días.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Actualización del agente de autenticación en otros servidores

Siga estos pasos para actualizar los agentes de autenticación en otros servidores (donde no se instala Azure AD Connect):

1. **Desinstale la versión preliminar del agente de autenticación**: descargue [este script de PowerShell](https://aka.ms/rmpreviewagent) y ejecútelo como administrador en el servidor.
2. **Descargue la última versión del agente de autenticación (versiones 1.5.193.0 o posteriores)**: inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con las credenciales de administrador global del inquilino. Seleccione **Azure Active Directory -> Azure AD Connect -> Autenticación de paso a través -> Descargar agente**. Acepte las condiciones del servicio y descargue la última versión.
3. **Instale la última versión del agente de autenticación**: ejecute el archivo ejecutable descargado en el paso 2. Proporcione las credenciales de administrador global de su inquilino cuando se le solicite.
4. **Verifique que se ha instalado la última versión**: como se indicó anteriormente, vaya a **Panel de control -> Programas -> Programas y características** y verifique si hay una entrada denominada **Microsoft Azure AD Connect Authentication Agent** (Agente de autenticación de Microsoft Azure AD Connect).

>[!NOTE]
>Si activa la hoja Autenticación de paso a través en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) después de completar los pasos anteriores, verá dos entradas del agente de autenticación por servidor; una entrada en la que aparece el agente de autenticación como **activo** y otra, como **inactivo**. Se _espera_ que esto sea así. La entrada **Inactivo** se quita automáticamente después de unos días.

## <a name="next-steps"></a>Pasos siguientes
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): información para resolver problemas habituales de esta característica.

