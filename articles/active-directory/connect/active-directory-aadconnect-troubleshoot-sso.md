---
title: "Azure AD Connect: solución de problemas de inicio de sesión único de conexión directa | Microsoft Docs"
description: "En este tema se describe cómo solucionar los problemas del inicio de sesión único de conexión directa de Azure Active Directory (SSO de conexión directa de Azure AD)."
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
ms.date: 08/26/2017
ms.author: billmath
ms.openlocfilehash: 7eea3621a52bf13dc44e89c342c503905ff24a0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Solución de problemas de inicio de sesión único de conexión directa de Azure Active Directory

Este artículo sirve de ayuda para encontrar información acerca de cómo solucionar los problemas comunes relativos al inicio de sesión único de conexión directa de Azure AD.

## <a name="known-issues"></a>Problemas conocidos

- En algunos casos, el proceso para habilitar el inicio de sesión único de conexión directa puede tardar hasta 30 minutos.
- El soporte técnico para el explorador Edge no está disponible.
- La activación de licencias en clientes de Office, especialmente en escenarios de equipos compartidos, provoca solicitudes de inicio de sesión adicionales para los usuarios.
- SSO de conexión directa no funciona en modo de exploración privada en Firefox. y 
- El inicio de sesión único de conexión directa no funciona en Internet Explorer cuando está activado el modo de protección mejorada.
- El inicio de sesión único de conexión directa no funciona en exploradores móviles en iOS y Android.
- Si va a sincronizar treinta bosques de AD o más, no se puede habilitar SSO de conexión directa mediante Azure AD Connect. Como alternativa, también puede [habilitar manualmente](#manual-reset-of-azure-ad-seamless-sso) la característica en su inquilino.
- Al agregar direcciones URL de servicio de Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) a la zona "Sitios de confianza" en lugar de a la zona "Intranet local", **se impide que los usuarios inicie sesión**.

## <a name="check-status-of-the-feature"></a>Comprobación del estado de la característica

Asegúrese de que la característica de SSO de conexión directa esté aún **habilitada** en el inquilino. Puede comprobar el estado; para ello, vaya a la hoja **Azure AD Connect** en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administración de Azure Active Directory: hoja de Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Motivos del error de inicio de sesión en el centro de administración de Azure Active Directory (necesita una licencia Premium)

Si el inquilino tiene una licencia de Azure AD Premium asociada, también puede buscar el [informe actividad de inicio de sesión](../active-directory-reporting-activity-sign-ins.md) en el [centro de administración de Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administración de Azure Active Directory: informe de inicios de sesión](./media/active-directory-aadconnect-sso/sso9.png)

Vaya a **Azure Active Directory** -> **Inicios de sesión** en el [centro de administración de Azure Active Directory](https://aad.portal.azure.com/) y haga clic en la actividad de inicio de sesión de un usuario específico. Busque el campo **CÓDIGO DE ERROR DE INICIO DE SESIÓN**. Busque la correspondencia entre el valor de ese campo y un motivo de error y la resolución en la siguiente tabla:

|Código de error de inicio de sesión|Motivo del error de inicio de sesión|Resolución
| --- | --- | ---
| 81001 | El vale de Kerberos del usuario es demasiado grande. | Reduzca la pertenencia a grupos del usuario e inténtelo de nuevo.
| 81002 | No se ha podido validar el vale de Kerberos del usuario. | Vela la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81003 | No se ha podido validar el vale de Kerberos del usuario. | Vela la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81004 | Error al intentar la autenticación Kerberos. | Vela la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81008 | No se ha podido validar el vale de Kerberos del usuario. | Vela la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81009 | No se ha podido validar el vale de Kerberos del usuario. | Vela la [Lista de comprobación de solución de problemas](#troubleshooting-checklist).
| 81010 | Error de SSO de conexión directa porque el vale de Kerberos del usuario ha expirado o no es válido. | El usuario debe iniciar sesión desde un dispositivo unido al dominio dentro de la red corporativa.
| 81011 | No se encuentra el objeto de usuario con la información del vale de Kerberos del usuario. | Use Azure AD Connect para sincronizar la información del usuario en Azure AD.
| 81012 | El usuario que intenta iniciar sesión en Azure AD es distinto del que inició sesión en el dispositivo. | Inicie sesión desde un dispositivo diferente.
| 81013 | No se encuentra el objeto de usuario con la información del vale de Kerberos del usuario. |Use Azure AD Connect para sincronizar la información del usuario en Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de comprobación de solución de problemas

Use la siguiente lista de comprobación para solucionar problemas de SSO de conexión directa:

- Compruebe si la característica SSO de conexión directa está habilitada en Azure AD Connect. Si no puede habilitarla (por ejemplo, debido a que hay un puerto bloqueado), asegúrese de que cumple todos los [requisitos previos](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites).
- Compruebe que las dos direcciones URL de Azure AD (https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net) forman parte de la configuración de la zona de intranet del usuario.
- Asegúrese de que el dispositivo corporativo se ha unido al dominio de AD.
- Asegúrese de que el usuario ha iniciado sesión en el dispositivo con una cuenta de dominio de AD.
- Asegúrese de que la cuenta del usuario provenga de un bosque de AD donde esté configurado SSO de conexión directa.
- Asegúrese de que el dispositivo esté conectado a la red corporativa.
- Asegúrese de que la hora del dispositivo esté sincronizada con la de Active Directory y la de los controladores de dominio, y de que difiere un máximo de 5 minutos de cada una de ellas.
- Enumere los vales de Kerberos existentes en el dispositivo usando el comando **klist** desde un símbolo del sistema. Compruebe si los vales emitidos para la cuenta de equipo `AZUREADSSOACCT` están presentes. Los vales de Kerberos de los usuarios suelen ser válidos durante 12 horas. Puede tener una configuración diferente en Active Directory.
- Purgue los vales de Kerberos existentes desde el dispositivo mediante el comando **klist purge** e inténtelo de nuevo.
- Para determinar si hay problemas relacionados con JavaScript, revise los registros de la consola del explorador (en "Herramientas de desarrollo") .
- Revise también los [registros de controlador de dominio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Registros de controlador de dominio

Si se habilita la auditoría de eventos correctos en el controlador de dominio, cada vez que un usuario inicie sesión mediante SSO de conexión directa, se grabará una entrada de seguridad en el registro de eventos. Puede encontrar estos eventos de seguridad con la siguiente consulta (busque el evento **4769** asociado a la cuenta de equipo **AzureADSSOAcc$**):

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Restablecimiento manual de la característica

Si el procedimiento de solución de problemas no sirve de ayuda, restablezca manualmente la característica en su inquilino. Siga estos pasos en el servidor local donde se ejecuta Azure AD Connect:

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Paso 1: Importación del módulo de PowerShell de SSO de conexión directa

1. En primer lugar, descargue e instale el [Ayudante para el inicio de sesión de Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. A continuación, descargue e instale el [módulo de Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Vaya a la carpeta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe el módulo de PowerShell de SSO de conexión directa mediante este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>Paso 2: Obtención de la lista de bosques de AD en la que se ha habilitado el SSO de conexión directa

1. Ejecute PowerShell como administrador. En PowerShell, llame a `New-AzureADSSOAuthenticationContext`. Cuando se le solicite, escriba las credenciales de administrador global de su inquilino.
2. Llame a `Get-AzureADSSOStatus`. Aparecerá la lista de bosques de AD (examine la lista "Dominios") en la que se ha habilitado esta característica.

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>Paso 3: Deshabilitación del SSO de conexión directa para cada bosque de AD en el que esté configurado

1. Llame a `$creds = Get-Credential`. Cuando se le pida, escriba las credenciales del administrador de dominio para el bosque de AD deseado.
2. Llame a `Disable-AzureADSSOForest -OnPremCredentials $creds`. Este comando quita la cuenta de equipo `AZUREADSSOACCT` del controlador de dominio local para este bosque de AD específico.
3. Repita los pasos anteriores para cada bosque de AD en el que haya configurado la característica.

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>Paso 4: Habilitación de SSO de conexión directa para cada bosque de AD

1. Llame a `Enable-AzureADSSOForest`. Cuando se le pida, escriba las credenciales del administrador de dominio para el bosque de AD deseado.
2. Repita los pasos anteriores para cada bosque de AD en el que desee configurar la característica.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Paso 5. Habilite la característica en su inquilino

Llame a `Enable-AzureADSSO` y escriba "true" en el mensaje `Enable: ` para activar la característica en su inquilino.
