---
title: "Azure AD Connect: preguntas más frecuentes sobre el inicio de sesión único de conexión directa | Microsoft Docs"
description: "Respuestas a las preguntas más frecuentes sobre el inicio de sesión único de conexión directa de Azure Active Directory."
services: active-directory
keywords: "qué es Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
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
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e363ade43ab9e2b2432c9efdc3ce1b661e278b2a
ms.contentlocale: es-es
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Preguntas más frecuentes sobre el inicio de sesión único de conexión directa de Azure Active Directory

En este artículo, abordamos las preguntas más frecuentes sobre el SSO de conexión directa de Azure AD. Siga comprobando si hay contenido nuevo.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>¿Con qué métodos de inicio de sesión funciona el SSO de conexión directa?

SSO de conexión directa se puede combinar con los métodos de inicio de sesión de [sincronización de hash de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) o [autenticación de paso a través](active-directory-aadconnect-pass-through-authentication.md), pero no con Servicios de federación de Active Directory (AD FS).

## <a name="is-seamless-sso-a-free-feature"></a>¿SSO de conexión directa es una característica gratuita?

SSO de conexión directa es una característica gratuita y no es necesario usar ninguna versión de pago de Azure AD para usarla. Seguirá siendo gratuita cuando la característica alcance la disponibilidad general.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>¿Qué aplicaciones aprovechan la funcionalidad de parámetro `domain_hint` o `login_hint` de SSO de conexión directa?

Estamos en el proceso de compilar las listas de aplicaciones que envían estos parámetros y de los que no lo hacen. Si tiene aplicaciones que le interesen, indíquelo en la sección de comentarios.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>¿SSO de conexión directa admite `Alternate ID` como nombre de usuario en lugar de `userPrincipalName`?

Sí. SSO de conexión directa admite `Alternate ID` como el nombre de usuario cuando se configura en Azure AD Connect, tal como se muestra [aquí](active-directory-aadconnect-get-started-custom.md). No todas las aplicaciones de Office 365 admiten `Alternate ID`. Consulte la documentación de la aplicación específica para conocer la declaración de compatibilidad.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Quiero registrar dispositivos no de Windows 10 con Azure AD, sin usar AD FS. ¿Puedo usar SSO de conexión directa en su lugar?

Sí, en este escenario se necesita la versión 2.1 o posterior del [cliente para unirse al área de trabajo](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-disable-seamless-sso"></a>¿Cómo se deshabilita SSO de conexión directa?

SSO de conexión directa se puede deshabilitar con Azure AD Connect.

Ejecute Azure AD Connect, elija "Change user sign-in page" (Cambiar página de inicio de sesión de usuario) y haga clic en "Siguiente". A continuación, desactive la opción "Habilitar el inicio de sesión único". Continúe con el asistente. Cuando haya finalizado con el asistente, el SSO de conexión directa estará deshabilitado en su inquilino.

Sin embargo, verá un mensaje en pantalla en el que se anuncia lo siguiente:

"El inicio de sesión único ya está deshabilitado, pero es necesario completar otros pasos manualmente para finalizar el proceso. Más información".

Los pasos manuales que necesita son los siguientes:

1. Obtención de la lista de bosques de AD en los que se habilitó SSO de conexión directa
- En primer lugar, descargue e instale [Microsoft Online Services - Ayudante para el inicio de sesión](http://go.microsoft.com/fwlink/?LinkID=286152).
- A continuación, descargue e instale el [módulo de Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
- Vaya a la carpeta `%programfiles%\Microsoft Azure Active Directory Connect`.
- Importe el módulo de PowerShell de SSO de conexión directa mediante este comando: `Import-Module .\AzureADSSO.psd1`.
  - En PowerShell, llame a `New-AzureADSSOAuthenticationContext`. El comando mostrará un cuadro emergente para escribir las credenciales de administrador de su inquilino de Azure AD.
  - Llame a `Get-AzureADSSOStatus`. Aparecerá la lista de bosques de AD (examine la lista "Dominios") en la que se ha habilitado esta característica.
2. Elimine manualmente la cuenta de equipo `AZUREADSSOACCT` de cada bosque de AD que encuentre en la lista.

## <a name="next-steps"></a>Pasos siguientes

- [**Inicio rápido** ](active-directory-aadconnect-sso-quick-start.md): desarrollo y ejecución de SSO de conexión directa de Azure AD.
- [**Profundización técnica** ](active-directory-aadconnect-sso-how-it-works.md): descripción del funcionamiento de esta característica.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-sso.md): aprenda a resolver problemas comunes de esta característica.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.

