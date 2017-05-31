---
title: "Azure AD Connect: Solucionar problemas de autenticación de paso a través | Documentos de Microsoft"
description: "En este artículo se describe cómo solucionar problemas de autenticación de paso a través de Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Solucionar problemas de autenticación de paso a través de Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: aceba2cfeac03eae78e89ef46926b2a388663f09
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-pass-through-authentication"></a>Cómo solucionar problemas de autenticación de paso a través de Azure Active Directory

Este artículo le ayudará a encontrar información para solucionar problemas comunes durante la instalación, el registro o la desinstalación de conectores de autenticación de paso a través (ya sea a través de Azure AD Connect o un conector independiente). También durante la habilitación y el uso de la característica Autenticación de paso a través de Azure Active Directory (Azure AD) en el inquilino.

## <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Problemas durante la instalación de conectores (a través de Azure AD Connect o de un conector independiente)

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Ya existe un conector del proxy de aplicación de Azure AD

Un conector de autenticación de paso a través no se puede instalar en el mismo servidor que un conector de [proxy de aplicación de Azure AD](../../active-directory/active-directory-application-proxy-get-started.md). El conector de autenticación de paso a través se tendrá que instalar en un servidor independiente.

### <a name="an-unexpected-error-occured"></a>Se ha producido un error inesperado

[Recopile los registros del conector](#collecting-pass-through-authentication-connector-logs) en el servidor y póngase en contacto con el Soporte técnico de Microsoft para que le solucionen el problema.

## <a name="issues-during-registration-of-connectors"></a>Problemas durante el registro de conectores

### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>No se pudo realizar el registro del conector porque había puertos bloqueados

Asegúrese de que el servidor en el que se ha instalado el conector puede comunicarse tanto con nuestras direcciones URL de servicio como con los puertos que se enumeran [aquí](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>No se puede registrar el conector debido a errores de autorización de token o cuenta

Asegúrese de que usa una cuenta de administrador global solo en la nube para todas las operaciones de instalación y registro de conector independiente o Azure AD Connect. Hay un problema conocido con las cuentas de administrador global habilitadas para MFA; desactive temporalmente MFA (solo para completar las operaciones) para proporcionar una solución alternativa.

### <a name="an-unexpected-error-occurred"></a>Se ha producido un error inesperado

[Recopile los registros del conector](#collecting-pass-through-authentication-connector-logs) en el servidor y póngase en contacto con el Soporte técnico de Microsoft para que le solucionen el problema.

## <a name="issues-during-uninstallation-of-connectors"></a>Problemas durante la desinstalación de conectores

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Mensaje de advertencia al desinstalar Azure AD Connect

Si la característica Autenticación de paso a través está habilitada en su inquilino e intenta desinstalar Azure AD Connect, aparecerá el siguiente mensaje de advertencia: "Users will not be able to sign-in to Azure AD unless you have other Pass-through Authentication agents installed on other servers" (Los usuarios no podrán iniciar sesión en Azure AD, a menos que tenga otros agentes de autenticación de paso a través instalados en otros servidores).

Para no interrumpir el inicio de sesión del usuario, es preciso tener una instalación de [alta disponibilidad](active-directory-aadconnect-pass-through-authentication.md) en vigor para desinstalar Azure AD Connect.

## <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Problemas con la habilitación de la característica Autenticación de paso a través

### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>La característica no se pudo habilitar porque no había conectores disponibles

Necesita tener al menos un conector activo para habilitar la característica Autenticación de paso a través en el inquilino. Puede instalar un conector mediante la instalación de Azure AD Connect o de un conector independiente.

### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>La característica no se habilitó porque había puertos bloqueados

Asegúrese de que el servidor en el que se ha instalado Azure AD Connect puede comunicarse tanto con nuestras direcciones URL de servicio como con los puertos que se enumeran [aquí](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>No se puede realizar la habilitación de la característica debido a errores de autorización de cuenta o token.

Asegúrese de que usa una cuenta de administrador global solo en la nube cuando habilite la característica. Hay un problema conocido con las cuentas de administrador global habilitadas para Multi-Factor Authentication (MFA); desactive temporalmente MFA (solo para completar la operación) para proporcionar una solución alternativa.

## <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Problemas al usar la característica Autenticación de paso a través

### <a name="user-facing-sign-in-errors"></a>Errores de inicio de sesión a los que se enfrenta el usuario

La característica informa de los siguientes errores a los que se enfrenta el usuario en la pantalla de inicio de sesión de Azure AD. Dichos errores se detallan a continuación, junto con los pasos para resolverlos.

|Error|Description|Resolución
| --- | --- | ---
|AADSTS80001|No es posible conectarse a Active Directory.|Asegúrese de que los servidores del conector son miembros del mismo bosque de AD que los usuarios cuyas contraseñas hay que validar y que pueden conectarse a Active Directory.  
|AADSTS8002|Se ha agotado el tiempo de espera al conectarse a Active Directory.|Asegúrese de que Active Directory está disponible y responde a las solicitudes de los conectores.
|AADSTS80004|El nombre de usuario transferido al conector no era válido.|Asegúrese de que el usuario esté intentando iniciar sesión con el nombre de usuario correcto.
|AADSTS80005|La validación encontró una excepción WebException impredecible|Es probable que sea un error transitorio. Vuelva a intentarlo. Si el error no desaparece, póngase en contacto con el soporte técnico de Microsoft.
|AADSTS80007|Error al establecer comunicación con Active Directory.|Compruebe los registros del conector para más información y verifique que Active Directory está funcionando según lo previsto.

## <a name="collecting-pass-through-authentication-connector-logs"></a>Recopilación de registros del conector de autenticación de paso a través

En función del tipo de problema, es posible que tenga que buscar estos registros en distintos lugares.

### <a name="connector-event-logs"></a>Registros de eventos de conector

Para ver los errores relacionados con el conector, abra la aplicación Visor de eventos en el servidor y consulte **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Para ver registros detallados de análisis y depuración, puede habilitar el registro de "Sesión". No ejecute el conector con este registro habilitado durante las operaciones normales; úselo solo para solucionar problemas. Tenga en cuenta que el contenido del registro solo se ve cuando el registro se vuelve a deshabilitar.

### <a name="detailed-trace-logs"></a>Registros de seguimiento detallados

Para solucionar errores de inicio de sesión de los usuarios, busque los registros de seguimiento en **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Estos registros incluyen los motivos por los que un usuario concreto no pudo iniciar sesión mediante la característica Autenticación de paso a través. A continuación encontrará una entrada de registro de ejemplo:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Para obtener detalles descriptivos del error ('1328' en el ejemplo anterior), abra el símbolo del sistema y ejecute el siguiente comando. Nota: será preciso que reemplace '1328' por el número de error real que ve en los registros.

`Net helpmsg 1328`

El resultado debería ser similar al que se muestra a continuación:

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Registros de controlador de dominio

Si están habilitados los registros de auditoría, se puede encontrar información adicional en los registros de seguridad de los controladores de dominio. A continuación puede ver una manera sencilla de consultar las solicitudes de inicio de sesión enviadas por los conectores de autenticación de paso a través:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

