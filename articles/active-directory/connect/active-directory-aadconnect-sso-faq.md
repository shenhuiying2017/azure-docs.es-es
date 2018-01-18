---
title: "Azure AD Connect: preguntas más frecuentes sobre el inicio de sesión único de conexión directa | Microsoft Docs"
description: "Respuestas a las preguntas más frecuentes sobre el inicio de sesión único de conexión directa de Azure Active Directory."
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
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: bbaed9ee5db895810fac476ea5bf560800b0bdec
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Preguntas más frecuentes sobre el inicio de sesión único de conexión directa de Azure Active Directory

En este artículo se ofrece respuesta a las preguntas más frecuentes sobre el inicio de sesión único de conexión directa (SSO de conexión directa) de Azure Active Directory. Siga comprobando si hay contenido nuevo.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>¿Con qué métodos de inicio de sesión funciona el SSO de conexión directa?

SSO de conexión directa se puede combinar con los métodos de inicio de sesión mediante [sincronización de hash de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) o [autenticación de paso a través](active-directory-aadconnect-pass-through-authentication.md). Pero esta característica no se puede usar con los Servicios de federación de Active Directory (AD FS).

## <a name="is-seamless-sso-a-free-feature"></a>¿SSO de conexión directa es una característica gratuita?

SSO de conexión directa es una característica gratuita y no es necesario usar ninguna versión de pago de Azure AD para usarla.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>¿Está disponible el inicio de sesión único de conexión directa en la [nube Microsoft Azure Alemania](http://www.microsoft.de/cloud-deutschland) y en la [nube de Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Nº El inicio de sesión único de conexión directa solo está disponible en la instancia mundial de Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>¿Qué aplicaciones aprovechan la funcionalidad de parámetro `domain_hint` o `login_hint` de SSO de conexión directa?

Estamos en el proceso de compilar las listas de aplicaciones que envían estos parámetros y de los que no lo hacen. Si tiene aplicaciones que le interesen, indíquelo en la sección de comentarios.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>¿SSO de conexión directa admite `Alternate ID` como nombre de usuario en lugar de `userPrincipalName`?

Sí. SSO de conexión directa admite `Alternate ID` como el nombre de usuario cuando se configura en Azure AD Connect, tal como se muestra [aquí](active-directory-aadconnect-get-started-custom.md). No todas las aplicaciones de Office 365 admiten `Alternate ID`. Consulte la documentación de la aplicación específica para conocer la declaración de compatibilidad.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>¿Cuál es la diferencia entre la experiencia de inicio de sesión único que proporciona [Azure AD Join](../active-directory-azureadjoin-overview.md) y SSO de conexión directa?

[Azure AD Join](../active-directory-azureadjoin-overview.md) permite el inicio de sesión único a los usuarios si sus dispositivos se han registrado con Azure AD. Estos dispositivos no deben estar unidos al dominio necesariamente. SSO se proporciona mediante *tokens de actualización principal* o *PRT*, y no con Kerberos. La experiencia del usuario es más adecuada en dispositivos Windows 10. SSO se realiza automáticamente en el explorador Edge. También funciona en Chrome mediante una extensión de explorador.

Puede usar tanto Azure AD Join como SSO de conexión directa en el inquilino. Estas dos características son complementarias. Si ambas están activadas, SSO de Azure AD Join tiene prioridad sobre SSO de conexión directa.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Quiero registrar dispositivos no de Windows 10 con Azure AD, sin usar AD FS. ¿Puedo usar SSO de conexión directa en su lugar?

Sí, en este escenario se necesita la versión 2.1 o posterior del [cliente para unirse al área de trabajo](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>¿Cómo puedo implementar la clave de descifrado de Kerberos de la cuenta de equipo `AZUREADSSOACC`?

Es importante implementar con frecuencia la clave de descifrado de Kerberos de la cuenta de equipo `AZUREADSSOACC` (que representa a Azure AD) creada en el bosque local de AD.

>[!IMPORTANT]
>Se recomienda implementar la clave de descifrado de Kerberos al menos cada 30 días.

Siga estos pasos en el servidor local donde se ejecuta Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Paso 1. Obtención de la lista de bosques de AD en los que se habilitó SSO de conexión directa

1. En primer lugar, descargue e instale [Microsoft Online Services - Ayudante para el inicio de sesión](http://go.microsoft.com/fwlink/?LinkID=286152).
2. A continuación, descargue e instale el [módulo de Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Vaya a la carpeta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe el módulo de PowerShell de SSO de conexión directa mediante este comando: `Import-Module .\AzureADSSO.psd1`.
5. Ejecute PowerShell como administrador. En PowerShell, llame a `New-AzureADSSOAuthenticationContext`. Este comando debería mostrar un cuadro emergente para escribir las credenciales de administrador global del inquilino.
6. Llame a `Get-AzureADSSOStatus`. Aparecerá la lista de bosques de AD (examine la lista "Dominios") en la que se ha habilitado esta característica.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Paso 2: Actualización de la clave de descifrado de Kerberos en cada bosque de AD en el que se haya configurado

1. Llame a `$creds = Get-Credential`. Cuando se le pida, escriba las credenciales del administrador de dominio para el bosque de AD deseado.
2. Llame a `Update-AzureADSSOForest -OnPremCredentials $creds`. Este comando actualiza la clave de descifrado de Kerberos de la cuenta de equipo `AZUREADSSOACC` en este bosque de AD concreto y la actualiza en Azure AD.
3. Repita los pasos anteriores para cada bosque de AD en el que haya configurado la característica.

>[!IMPORTANT]
>Asegúrese de _no_ ejecutar el comando `Update-AzureADSSOForest` más de una vez. De lo contrario, la característica deja de funcionar hasta que expira la hora de los vales Kerberos de los usuarios y se vuelven a emitir por parte de la instancia local de Active Directory.

## <a name="how-can-i-disable-seamless-sso"></a>¿Cómo se deshabilita SSO de conexión directa?

SSO de conexión directa se puede deshabilitar con Azure AD Connect.

Ejecute Azure AD Connect, elija "Change user sign-in page" (Cambiar página de inicio de sesión de usuario) y haga clic en "Siguiente". A continuación, desactive la opción "Habilitar el inicio de sesión único". Continúe con el asistente. Cuando haya finalizado con el asistente, el SSO de conexión directa estará deshabilitado en su inquilino.

Sin embargo, verá un mensaje en pantalla en el que se anuncia lo siguiente:

"El inicio de sesión único ya está deshabilitado, pero es necesario completar otros pasos manualmente para finalizar el proceso. Más información".

Para completar el proceso, siga estos pasos manuales en el servidor local donde se ejecuta Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Paso 1. Obtención de la lista de bosques de AD en los que se habilitó SSO de conexión directa

1. En primer lugar, descargue e instale [Microsoft Online Services - Ayudante para el inicio de sesión](http://go.microsoft.com/fwlink/?LinkID=286152).
2. A continuación, descargue e instale el [módulo de Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Vaya a la carpeta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe el módulo de PowerShell de SSO de conexión directa mediante este comando: `Import-Module .\AzureADSSO.psd1`.
5. Ejecute PowerShell como administrador. En PowerShell, llame a `New-AzureADSSOAuthenticationContext`. Este comando debería mostrar un cuadro emergente para escribir las credenciales de administrador global del inquilino.
6. Llame a `Get-AzureADSSOStatus`. Aparecerá la lista de bosques de AD (examine la lista "Dominios") en la que se ha habilitado esta característica.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Paso 2: Elimine manualmente la cuenta de equipo `AZUREADSSOACCT` de cada bosque de AD que encuentre en la lista.

## <a name="next-steps"></a>pasos siguientes

- [**Inicio rápido**](active-directory-aadconnect-sso-quick-start.md): desarrollo y ejecución de SSO de conexión directa de Azure AD.
- [**Profundización técnica**](active-directory-aadconnect-sso-how-it-works.md): descripción del funcionamiento de esta característica.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-sso.md): información para resolver problemas habituales de esta característica.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.
