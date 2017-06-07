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
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: a543be452abbbe3057a5e275612968cd52c8b7aa
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Cómo solucionar problemas de inicio de sesión único de conexión directa de Azure Active Directory

## <a name="known-issues"></a>Problemas conocidos

- Si va a sincronizar más de 30 bosques de AD mediante Azure AD Connect, el asistente usado para configurar SSO de conexión directa no funcionará correctamente. Como alternativa, también puede [habilitar manualmente](#manual-reset-of-azure-ad-seamless-sso) la característica de SSO de conexión directa en su inquilino.
- Se agregan direcciones URL de servicio de Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) a la zona "Sitios de confianza" en lugar de a la zona "Intranet local".

## <a name="troubleshooting-checklist"></a>Lista de comprobación de solución de problemas

Use la siguiente lista de comprobación para la solución de problemas del SSO de conexión directa:

1. Compruebe si la característica de SSO de conexión directa SSO de conexión directa en el inquilino en la herramienta Azure AD Connect. Si no puede habilitar la característica (por ejemplo, debido a que hay un puerto bloqueado), asegúrese de que cumple todos los [requisitos previos](active-directory-aadconnect-sso.md#pre-requisites). Si aún surgen problemas al habilitar la característica, póngase en contacto con el soporte técnico de Microsoft.
2. Se ha definido que las dos direcciones URL de servicio (https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net) forman parte de la configuración de la zona de intranet.
3. Asegúrese de que el escritorio corporativo se ha unido al dominio de AD.
4. Asegúrese de que el usuario ha iniciado sesión en el escritorio con una cuenta de dominio de AD.
5. Asegúrese de que la cuenta del usuario provenga de un bosque de AD donde esté configurado SSO de conexión directa.
6. Asegúrese de que el escritorio está conectado a la red corporativa.
7. Asegúrese de que la hora del escritorio está sincronizada con la de Active Directory y de los controladores de dominio y de que difiere un máximo de 5 minutos de cada una de ellas.
8. Purgue los vales Kerberos existentes de su escritorio. Para hacerlo, ejecute el comando **klist purge** desde un símbolo del sistema. Los vales de Kerberos de los usuarios son normalmente válidos durante 12 horas; tenga en cuenta que es posible que los haya configurado de forma diferente en Active Directory.
9. Revise los registros de la consola del explorador (en "Herramientas de desarrollo") para ayudar a determinar posibles problemas.
10. Revise también los [registros de controlador de dominio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Registros de controlador de dominio

Si se habilita una auditoría correcta en un controlador de dominio, cada vez que un usuario inicia sesión mediante SSO de conexión directa se graba una entrada de seguridad (evento 4769 asociado con la cuenta de equipo **AzureADSSOAcc$**) en el registro de eventos. Para encontrar estos eventos de seguridad, utilice la consulta siguiente:

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>Restablecimiento manual del SSO de conexión directa de Azure AD

Si la solución de problemas no le ayuda, use los siguientes pasos para restablecer o habilitar manualmente la característica en su inquilino:

### <a name="1-import-the-seamless-sso-powershell-module"></a>1. Importación del módulo de PowerShell de SSO de conexión directa

- En primer lugar, descargue e instale el [Ayudante para el inicio de sesión de Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
- A continuación, descargue e instale el [módulo de Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
- Vaya a la carpeta `%programfiles%\Microsoft Azure Active Directory Connect`.
- Importe el módulo de PowerShell de SSO de conexión directa mediante este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2. Obtención de la lista de bosques de AD en la que se ha habilitado el SSO de conexión directa

- En PowerShell, llame a `New-AzureADSSOAuthenticationContext`. Se mostrará un cuadro emergente para escribir las credenciales de administrador de su inquilino de Azure AD.
- Llame a `Get-AzureADSSOStatus`. Aparecerá la lista de bosques de AD (examine la lista "Dominios") en la que se ha habilitado esta característica.

### <a name="3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3. Deshabilitación del SSO de conexión directa para cada bosque de AD en el que esté configurado

- En PowerShell, llame a `New-AzureADSSOAuthenticationContext`. Se mostrará un cuadro emergente para escribir las credenciales de administrador de su inquilino de Azure AD.
- Llame a `$creds = Get-Credential`. Se mostrará un cuadro emergente para escribir las credenciales del administrador de dominio para el bosque de AD deseado.
- Llame a `Disable-AzureADSSOForest -OnPremCredentials $creds`. Se quitará la cuenta de equipo AZUREADSSOACCT del controlador de dominio local y se deshabilitará esta característica para el bosque de AD específico.
- Repita los pasos anteriores para cada bosque de AD en el que haya configurado la característica.

### <a name="4-enable-seamless-sso-for-each-ad-forest"></a>4. Habilitación del SSO de conexión directa para cada bosque de AD

- Llame a `New-AzureADSSOAuthenticationContext`. Se mostrará un cuadro emergente para escribir las credenciales de administrador de su inquilino de Azure AD.
- Llame a `Enable-AzureADSSOForest`. Se mostrará un cuadro emergente para escribir las credenciales del administrador de dominio para el bosque de AD deseado.
- Repita los pasos anteriores para cada bosque de AD en el que quiera configurar la característica.

### <a name="5-enable-seamless-sso-on-your-tenant"></a>5. Habilitación del SSO de conexión directa en su inquilino

- Llame a `New-AzureADSSOAuthenticationContext`. Se mostrará un cuadro emergente para escribir las credenciales de administrador de su inquilino de Azure AD.
- Llame a `Enable-AzureADSSO` y escriba "true" en el mensaje `Enable: ` para activar la característica en su inquilino.

