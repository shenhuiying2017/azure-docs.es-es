---
title: "Azure Active Directory Connect: solución de problemas de inicio de sesión único de conexión directa | Microsoft Docs"
description: "En este tema se describe cómo solucionar los problemas del inicio de sesión único de conexión directa de Azure Active Directory."
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
ms.date: 01/05/2018
ms.author: billmath
ms.openlocfilehash: aa28431c5926656ae97ded3f23b83f2a91c60487
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Solución de problemas de inicio de sesión único de conexión directa de Azure Active Directory

Este artículo sirve de ayuda para encontrar información sobre cómo solucionar los problemas comunes relativos al inicio de sesión único de conexión directa (SSO de conexión directa) de Azure Active Directory (Azure AD).

## <a name="known-problems"></a>Problemas conocidos

- En algunos casos, el proceso para habilitar el inicio de sesión único de conexión directa puede tardar hasta 30 minutos.
- Si deshabilita y vuelve a habilitar Inicio de sesión único de conexión directa en el inquilino, los usuarios no podrán tener la experiencia de inicio de sesión único hasta que sus vales de Kerberos en caché, que normalmente son válidos durante diez horas, hayan expirado.
- El soporte técnico para el explorador Edge no está disponible.
- Iniciar clientes de Office, especialmente en escenarios de equipos compartidos, provoca solicitudes de inicio de sesión adicionales para los usuarios. Los usuarios deben escribir sus nombres de usuario con frecuencia, pero no sus contraseñas.
- Si el inicio de sesión único de conexión directa se realiza correctamente, el usuario no tiene la oportunidad de seleccionar **Mantener la sesión iniciada**. Debido a este comportamiento, los escenarios de asignación de SharePoint y OneDrive no funcionan.
- SSO de conexión directa no funciona en modo de exploración privada en Firefox.
- El inicio de sesión único de conexión directa no funciona en Internet Explorer cuando está activado el modo de protección mejorada.
- El inicio de sesión único de conexión directa no funciona en exploradores móviles en iOS y Android.
- Si va a sincronizar treinta bosques de Active Directory o más, no se puede habilitar el inicio de sesión único de conexión directa mediante Azure AD Connect. Como alternativa, también puede [habilitar manualmente](#manual-reset-of-azure-ad-seamless-sso) la característica en su inquilino.
- Al agregar direcciones URL de servicio de Azure AD (https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net) a la zona de sitios de confianza en lugar de a la zona de la intranet local, *se impide que los usuarios inicien sesión*.

## <a name="check-the-status-of-the-feature"></a>Comprobación del estado de la característica

Asegúrese de que la característica de SSO de conexión directa esté aún **habilitada** en el inquilino. Puede comprobar el estado; para ello, vaya al panel **Azure AD Connect** en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administración de Azure Active Directory: panel de Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Motivos del error de inicio de sesión en el centro de administración de Azure Active Directory (se necesita una licencia Premium)

Si el inquilino tiene una licencia de Azure AD Premium asociada, también puede buscar el [informe actividad de inicio de sesión](../active-directory-reporting-activity-sign-ins.md) en el [centro de administración de Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administración de Azure Active Directory: informe de inicios de sesión](./media/active-directory-aadconnect-sso/sso9.png)

Examine **Azure Active Directory** > **Inicios de sesión** en el [centro de administración de Azure Active Directory](https://aad.portal.azure.com/) y después seleccione la actividad de inicio de sesión de un usuario específico. Busque el campo **CÓDIGO DE ERROR DE INICIO DE SESIÓN**. Busque la correspondencia entre el valor de ese campo y un motivo de error y la resolución en la siguiente tabla:

|Código de error de inicio de sesión|Motivo del error de inicio de sesión|Resolución
| --- | --- | ---
| 81001 | El vale de Kerberos del usuario es demasiado grande. | Reduzca la pertenencia a grupos del usuario e inténtelo de nuevo.
| 81002 | No se puede validar el vale Kerberos del usuario. | Vea la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81003 | No se puede validar el vale Kerberos del usuario. | Vea la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81004 | Error al intentar la autenticación Kerberos. | Vea la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81008 | No se puede validar el vale Kerberos del usuario. | Vea la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81009 | No se puede validar el vale Kerberos del usuario. | Vea la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81010 | Error de SSO de conexión directa porque el vale de Kerberos del usuario ha expirado o no es válido. | El usuario debe iniciar sesión desde un dispositivo unido al dominio dentro de la red corporativa.
| 81011 | No se puede encontrar el objeto de usuario con la información del vale de Kerberos del usuario. | Use Azure AD Connect para sincronizar la información del usuario en Azure AD.
| 81012 | El usuario que intenta iniciar sesión en Azure AD es distinto del que inició sesión en el dispositivo. | El usuario debe iniciar sesión desde un dispositivo diferente.
| 81013 | No se puede encontrar el objeto de usuario con la información del vale de Kerberos del usuario. |Use Azure AD Connect para sincronizar la información del usuario en Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de comprobación de solución de problemas

Use la siguiente lista de comprobación para solucionar problemas de SSO de conexión directa:

- Asegúrese de que la característica SSO de conexión directa está habilitada en Azure AD Connect. Si no puede habilitarla (por ejemplo, debido a que hay un puerto bloqueado), asegúrese de que cumple todos los [requisitos previos](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites).
- Si ha habilitado tanto [Azure AD Join](../active-directory-azureadjoin-overview.md) como SSO de conexión directa en el inquilino, asegúrese de que el problema no está relacionado con Azure AD Join. SSO de Azure AD Join tiene prioridad sobre SSO de conexión directa, si el dispositivo está tanto registrado con Azure AD como unido a un dominio. Con SSO de Azure AD Join, el usuario ve un icono de inicio de sesión que dice "Conectado a Windows".
- Asegúrese de que las dos direcciones URL de Azure AD (https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net) forman parte de la configuración de la zona de la intranet del usuario.
- Asegúrese de que el dispositivo corporativo se ha unido al dominio de Active Directory.
- Asegúrese de que el usuario ha iniciado sesión en el dispositivo a través de una cuenta de dominio de Active Directory.
- Asegúrese de que la cuenta del usuario provenga de un bosque de Active Directory donde esté configurado SSO de conexión directa.
- Asegúrese de que el dispositivo está conectado a la red corporativa.
- Asegúrese de que la hora del dispositivo esté sincronizada con la de Active Directory y la de los controladores de dominio, y de que difieren entre sí un máximo de cinco minutos.
- Enumere los vales de Kerberos existentes en el dispositivo mediante el comando `klist` desde un símbolo del sistema. Asegúrese de que los vales emitidos para la cuenta de equipo `AZUREADSSOACCT` están presentes. Los vales de Kerberos de los usuarios suelen ser válidos durante diez horas. Puede que haya configuraciones distintas en Active Directory.
- Si deshabilita y vuelve a habilitar Inicio de sesión único de conexión directa en el inquilino, los usuarios no podrán tener la experiencia de inicio de sesión único hasta que sus vales de Kerberos en caché hayan expirado.
- Purgue los vales de Kerberos existentes desde el dispositivo mediante el comando `klist purge` e inténtelo de nuevo.
- Para determinar si hay problemas relacionados con JavaScript, revise los registros de la consola del explorador (en **Herramientas de desarrollo**).
- Revise los [registros de controlador de dominio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Registros del controlador de dominio

Si habilita la auditoría de eventos correctos en el controlador de dominio, cada vez que un usuario inicie sesión mediante SSO de conexión directa, se graba una entrada de seguridad en el registro de eventos. Para encontrar estos eventos de seguridad, utilice la consulta siguiente. (Busque el evento **4769** asociado a la cuenta de equipo **AzureADSSOAcc$**).

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Restablecimiento manual de la característica

Si el procedimiento de solución de problemas no sirve de ayuda, restablezca manualmente la característica en su inquilino. Siga estos pasos en el servidor local donde se ejecuta Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Paso 1: Importación del módulo de PowerShell de SSO de conexión directa

1. Descargue e instale [Microsoft Online Services - Ayudante para el inicio de sesión](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Descargue e instale el [módulo de Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Examine la carpeta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe el módulo de PowerShell de SSO de conexión directa mediante este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Paso 2: Obtención de la lista de bosques de Active Directory en la que se habilitó el SSO de conexión directa

1. Ejecute PowerShell como administrador. En PowerShell, llame a `New-AzureADSSOAuthenticationContext`. Cuando se le solicite, escriba las credenciales de administrador global de su inquilino.
2. Llame a `Get-AzureADSSOStatus`. Este comando proporciona la lista de bosques de Active Directory (examine la lista "Dominios") en la que se ha habilitado esta característica.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Paso 3: Deshabilitación de SSO de conexión directa para cada bosque de Active Directory en el que se configuró la característica

1. Llame a `$creds = Get-Credential`. Cuando se le pida, escriba las credenciales del administrador de dominio para el bosque de Active Directory deseado.
2. Llame a `Disable-AzureADSSOForest -OnPremCredentials $creds`. Este comando quita la cuenta de equipo `AZUREADSSOACCT` del controlador de dominio local para este bosque de Active Directory específico.
3. Repita los pasos anteriores para cada bosque de Active Directory en el que se configuró la característica.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Paso 4: Habilitación de SSO de conexión directa para cada bosque de Active Directory

1. Llame a `Enable-AzureADSSOForest`. Cuando se le pida, escriba las credenciales del administrador de dominio para el bosque de Active Directory deseado.
2. Repita los pasos anteriores para cada bosque de Active Directory en el que desea configurar la característica.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Paso 5. Habilite la característica en su inquilino

Para activar la característica en el inquilino, llame a `Enable-AzureADSSO` y escriba **true** en el mensaje `Enable:`.
