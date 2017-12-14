---
title: "Azure AD Connect: funcionamiento del inicio de sesión único de conexión directa | Microsoft Docs"
description: "En este artículo se describe el funcionamiento de la característica Inicio de sesión único de conexión directa de Azure Active Directory."
services: active-directory
keywords: "qué es Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0a28cd9016588d266670aa5a7fcbdd854d7ebce0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Inicio de sesión único de conexión directa de Azure Active Directory: información técnica detallada

En este artículo se proporcionan los detalles técnicos sobre cómo funciona la característica Inicio de sesión único de conexión directa (SSO de conexión directa) de Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>¿Cómo funciona SSO de conexión directa?

Esta sección tiene dos partes:
1. La configuración de la característica SSO de conexión directa.
2. Funcionamiento de una transacción de inicio de sesión de usuario único con SSO de conexión directa.

### <a name="how-does-set-up-work"></a>¿Cómo funciona la configuración?

SSO de conexión directa se habilita a través de Azure AD Connect tal como se muestra [aquí](active-directory-aadconnect-sso-quick-start.md). Cuando se habilita la característica, se producen los pasos siguientes:
- Una cuenta de equipo denominada `AZUREADSSOACC` (que representa a Azure AD) se crea en la instancia local de Active Directory (AD).
- La clave de descifrado de Kerberos de la cuenta de equipo se comparte de manera segura con Azure AD.
- Además, se crean dos nombres de entidad de seguridad de servicio (SPN) de Kerberos que representan las dos direcciones URL que se usan en el inicio de sesión de Azure AD.

>[!NOTE]
> La cuenta de equipo y los SPN de Kerberos se crean en cada bosque de AD que sincronice con Azure AD (a través de Azure AD Connect) y para cuyos usuarios desee SSO de conexión directa. Migre la cuenta de equipo `AZUREADSSOACC` a una unidad organizativa (UO) donde se almacenen otras cuentas de equipo para garantizar que se administre de la misma manera y que no se elimine.

>[!IMPORTANT]
>Se recomienda encarecidamente [sustituir la clave de descifrado de Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) de la cuenta de equipo de `AZUREADSSOACC` al menos cada 30 días.

### <a name="how-does-sign-in-with-seamless-sso-work"></a>¿Cómo funciona el inicio de sesión con SSO de conexión directa?

Una vez que se completa la instalación, SSO de conexión directa funcionan del mismo modo que cualquier otro inicio de sesión que usa la autenticación integrada de Windows (IWA). El flujo es el siguiente:

1. El usuario intenta acceder a una aplicación (por ejemplo, Outlook Web App en https://outlook.office365.com/owa/) desde un dispositivo corporativo unido a un dominio dentro de la red corporativa.
2. Si el usuario todavía no inicia sesión, se le redirige a la página de inicio de sesión de Azure AD.

  >[!NOTE]
  >Si la solicitud de inicio de sesión de Azure AD incluye un parámetro `domain_hint` (que identifica al inquilino, por ejemplo, contoso.onmicrosoft.com) o `login_hint` (que identifica al usuario, por ejemplo, user@contoso.onmicrosoft.com o user@contoso.com) se omite el paso 2.

3. El usuario escribe su nombre de usuario en la página de inicio de sesión de Azure AD.
4. Con JavaScript en segundo plano, Azure AD desafía al explorador, a través de una respuesta 401 No autorizado, a que proporcione un vale de Kerberos.
5. A su vez, el explorador solicita un vale desde Active Directory para la cuenta de equipo `AZUREADSSOACC` (que representa a Azure AD).
6. Active Directory busca la cuenta de equipo y devuelve al explorador un vale Kerberos cifrado con el secreto de la cuenta de equipo.
7. El explorador reenvía el vale de Kerberos que adquirió desde Active Directory a Azure AD (en una de las [direcciones URL de Azure AD que se agregó previamente a la configuración de la zona de intranet del explorador](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature)).
8. Azure AD descifra el vale de Kerberos, que incluye la identidad del usuario que inició sesión en el dispositivo corporativo, mediante la clave compartida previamente.
9. Después de la evaluación, Azure AD devuelve un token a la aplicación o le pide al usuario que realice pruebas adicionales, como la autenticación multifactor.
10. Si el inicio de sesión del usuario se realiza correctamente, el usuario puede acceder a la aplicación.

En el diagrama siguiente se ilustran todos los componentes y los pasos implicados.

![Inicio de sesión único de conexión directa](./media/active-directory-aadconnect-sso/sso2.png)

SSO de conexión directa es una característica oportunista, lo que significa que, si se genera un error, la experiencia de inicio de sesión se revierte a su comportamiento habitual, es decir, el usuario deberá escribir su contraseña para iniciar sesión.

## <a name="next-steps"></a>Pasos siguientes

- [**Inicio rápido**](active-directory-aadconnect-sso-quick-start.md): desarrollo y ejecución de SSO de conexión directa de Azure AD.
- [**Preguntas más frecuentes**](active-directory-aadconnect-sso-faq.md): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-sso.md): aprenda a resolver problemas comunes de esta característica.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.
