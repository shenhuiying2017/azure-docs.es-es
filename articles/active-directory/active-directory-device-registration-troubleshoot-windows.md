---
title: "Solución de problemas de registro automático de equipos unidos a un dominio Azure AD para Windows 10 y Windows Server 2016 | Microsoft Docs"
description: "Solución de problemas de registro automático de equipos unidos a un dominio Azure AD para Windows 10 y Windows Server 2016."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 2faf328e6622b9a1e3b529d62b61061659041fbd
ms.contentlocale: es-es
ms.lasthandoff: 03/10/2017

---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>Solución de problemas de registro automático de equipos unidos a un dominio en Azure AD: Windows 10 y Windows Server 2016

Este tema es aplicable a los siguientes clientes:

-   Windows 10
-   Windows Server 2016

Para otros clientes Windows, vea [Solución de problemas de registro automático de equipos unidos a un dominio en Azure AD para clientes de nivel inferior de Windows](active-directory-device-registration-troubleshoot-windows-legacy.md).

En este tema se supone que ha configurado el registro automático de dispositivos unidos a un dominio como se describe en [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-device-registration-get-started.md) para admitir los escenarios siguientes:

- [Acceso condicional basado en dispositivos](active-directory-conditional-access-automatic-device-registration-setup.md)

- [Perfiles móviles de empresa](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello para empresas](active-directory-azureadjoin-passport-deployment.md)


En este documento se proporcionan instrucciones sobre cómo resolver problemas potenciales. 

El registro se admite en la actualización de noviembre de 2015 de Windows 10 y en versiones posteriores.  
Se recomienda usar la Actualización de aniversario para habilitar los escenarios anteriores.

## <a name="step-1-retrieve-the-registration-status"></a>Paso 1: Recuperar el estado del registro 

**Para recuperar el estado del registro:**

1. Abra el símbolo del sistema como administrador.

2. Escriba **dsregcmd /status**



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>Paso 2: Evaluar el estado del registro 

Revise los siguientes campos y asegúrese de que tengan los valores esperados:

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

En este campo se muestra si el dispositivo está registrado en Azure AD. Si el valor se muestra como "NO", el registro no ha terminado. 

**Causas posibles:**

- Error de autenticación del equipo para el registro.

- Hay un servidor proxy HTTP en la organización que el equipo no puede detectar.

- El equipo no puede llegar a Azure AD para la autenticación o a DRS de Azure para el registro.

- Es posible que el equipo no esté en la red interna de la organización o en una VPN con conexión directa a una implementación local del controlador de dominio de AD.

- Si el equipo tiene un TPM, su estado puede ser incorrecto.

- Puede haber un error de configuración en los servicios indicados en el documento anterior que necesita volver a verificar. Los ejemplos comunes son:

    - El servidor de federación no tiene habilitados los puntos de conexión de WS-Trust.

    - Es posible que el servidor de federación no pueda permitir la autenticación de entrada de equipos de la red mediante la autenticación integrada de Windows.

    - No hay ningún objeto de punto de conexión de servicio que haga referencia a su nombre de dominio comprobado en Azure AD en el bosque de AD al que pertenece el equipo.

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

Este campo muestra si el dispositivo está unido o no a una implementación local de Active Directory. Si el valor se muestra como **NO**, el dispositivo no se puede registrar automáticamente en Azure AD. Primero compruebe que el dispositivo está unido a una implementación local de Active Directory antes de registrarlo en Azure AD. Si desea unir el equipo a Azure AD directamente, vaya a la información sobre las funcionalidades de la unión a Azure Active Directory.

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

En este campo se muestra si el dispositivo está registrado en Azure AD, pero como un dispositivo personal (marcado como “Unido al área de trabajo”). Si este valor debería aparecer como "NO" para un equipo unido a un dominio registrado en Azure AD, pero se muestra como YES, significa que se ha agregado una cuenta profesional o educativa antes de que el equipo termine el registro. En este caso, la cuenta se ignora si se usa la versión Actualización de aniversario de Windows 10 (1607 cuando se ejecuta el comando WinVer en la ventaja “Ejecutar” o una ventana del símbolo del sistema).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES y AzureADPrt : YES
  
En estos campos se muestra que el usuario se ha autenticado correctamente en Azure AD al iniciar sesión en el dispositivo. Si aparece “NO”, las causas posibles son:

- Clave de almacenamiento incorrecta (STK) en TPM asociada con el dispositivo tras el registro (comprobar KeySignTest mientras se ejecuta con privilegios elevados).

- Id. de inicio de sesión alternativo

- No se ha encontrado el proxy HTTP

## <a name="next-steps"></a>Pasos siguientes

Para más información, vea [Preguntas más frecuentes sobre el registro automático de dispositivos](active-directory-device-registration-faq.md) 
