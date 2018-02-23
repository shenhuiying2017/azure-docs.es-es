---
title: Condiciones del acceso condicional de Azure Active Directory | Microsoft Docs
description: "Aprenda cómo se utilizan las asignaciones en el acceso condicional a Azure Active Directory para desencadenar una directiva."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2415a2c2c0143b4abeb8ec1ecab379a204456874
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Condiciones del acceso condicional de Azure Active Directory 

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede controlar el modo en que los usuarios autorizados acceden a las aplicaciones en la nube. En una directiva de acceso condicional, se define la respuesta ("hacer esto") a una motivo para desencadenar la directiva ("cuando esto sucede"). 

![Control](./media/active-directory-conditional-access-conditions/10.png)


En el contexto del acceso condicional:

- "**Cuando esto sucede**" se denomina **condición**. 
- "**Entonces haga esto**" se denomina **control de acceso**.

La combinación de las condiciones con los controles de acceso representa una directiva de acceso condicional.

![Control](./media/active-directory-conditional-access-conditions/61.png)

En este artículo se ofrece información general de las condiciones y de cómo se utilizan en una directiva de acceso condicional. 


## <a name="users-and-groups"></a>Usuarios y grupos

La condición de usuarios y grupos es obligatoria en una directiva de acceso condicional. En la directiva, puede seleccionar **Todos los usuarios** o bien usuarios y grupos específicos.

![Control](./media/active-directory-conditional-access-conditions/02.png)

Cuando se selecciona:

- **Todos los usuarios**, la directiva se aplica a todos los usuarios del directorio. Esto incluye a los usuarios invitados.

- **Seleccionar usuarios y grupos**, puede tener como destino conjuntos específicos de usuarios. Por ejemplo, puede seleccionar un grupo que contenga todos los miembros del departamento de recursos humanos cuando tenga una aplicación HR seleccionada como aplicación de nube. 

- Un grupo, puede ser cualquier tipo de grupo en Azure AD, como grupos de distribución y de seguridad dinámicos o asignados.

También puede excluir determinados usuarios o grupos de una directiva. Un caso de uso común son las cuentas de servicio si su directiva aplica la autenticación multifactor. 

Dirigirse a grupos específicos de usuarios es útil para la implementación de una nueva directiva. En una nueva directiva, debería dirigir solo un conjunto inicial de usuarios para validar el comportamiento de la directiva. 



## <a name="cloud-apps"></a>Aplicaciones de nube 

Una aplicación de nube es un sitio web o servicio. Esto incluye sitios web protegidos por el proxy de la aplicación de Azure. Para obtener una descripción detallada de las aplicaciones de nube admitidas, consulte la sección [Asignaciones de aplicaciones de nube](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

La condición de aplicaciones de nube es obligatoria en una directiva de acceso condicional. En la directiva, puede seleccionar **Todas las aplicaciones en la nube** o bien usuarios y grupos específicos.

![Control](./media/active-directory-conditional-access-conditions/03.png)

Puede seleccionar:

- **Todas las aplicaciones en la nube** para establecer como referencia las directivas de línea de base que se aplicarán a toda la organización. Un caso de uso común para esta selección es una directiva que requiere autenticación multifactor cuando se detecta un riesgo de inicio de sesión en cualquier aplicación de nube.

- Aplicaciones de nube individuales para dirigir servicios específicos por directiva. Por ejemplo, puede requerir que los usuarios tengan un [dispositivo compatible](active-directory-conditional-access-policy-connected-applications.md) para acceder a SharePoint Online. Esta directiva también se aplica a otros servicios cuando se accede al contenido de SharePoint, por ejemplo, Microsoft Teams. 

También excluyen aplicaciones específicas de una directiva; sin embargo, estas aplicaciones siguen estando sujetas a las directivas aplicadas a los servicios a los que acceden. 



## <a name="sign-in-risk"></a>Riesgo de inicio de sesión

El riesgo del inicio de sesión es un indicador de la probabilidad (alta, media o baja) que hay de que el legítimo propietario de una cuenta de usuario no haya realizado un intento de inicio de sesión. Azure AD calcula el nivel de riesgo de inicio de sesión durante el inicio de sesión de un usuario. Puede utilizar el nivel calculado de riesgo de inicio de sesión como condición en una directiva de acceso condicional. 

![Condiciones](./media/active-directory-conditional-access-conditions/22.png)

Para usar esta condición, debe tener [Azure Active Directory Identity Protection](active-directory-identityprotection.md) habilitado.
 
Los casos de uso comunes para esta condición son directivas que:

- Bloquean usuarios con un alto riesgo de inicio de sesión para evitar que usuarios potencialmente no legítimos accedan a sus aplicaciones de nube. 
- Requieren la autenticación multifactor para aquellos usuarios con un riesgo medio de inicio de sesión. Al aplicar la autenticación multifactor, puede proporcionar más confianza de que el inicio de sesión lo realiza el propietario legítimo de una cuenta.

Para más información, consulte [Inicios de sesión no seguros](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Plataformas de dispositivo

La plataforma de dispositivo se caracteriza por el sistema operativo que se ejecuta en el dispositivo. Azure AD identifica la plataforma mediante el uso de la información proporcionada por el dispositivo, como el agente de usuario. Debido a que esta información no está comprobada, se recomienda que todas las plataformas tengan una directiva aplicada, ya sea mediante el bloqueo del acceso, la exigencia del cumplimiento de las directivas de Intune o por que se requiera que el dispositivo esté unido a un dominio. El valor predeterminado es aplicar la directiva a todas las plataformas de dispositivo. 


![Condiciones](./media/active-directory-conditional-access-conditions/24.png)

Para obtener una lista completa de las plataformas de dispositivos compatibles, vea [Condición de plataforma de dispositivos](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Un caso de uso común para esta condición es una directiva que restringe el acceso a las aplicaciones de nube a [dispositivos de confianza](active-directory-conditional-access-policy-connected-applications.md#trusted-devices). Para conocer más escenarios, como la condición de la plataforma de dispositivo, consulte [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md).


## <a name="locations"></a>Ubicaciones

Con ubicaciones, tiene la opción de definir las condiciones que se basan en desde dónde se inicia un intento de conexión. 
     
![Condiciones](./media/active-directory-conditional-access-conditions/25.png)

Los casos de uso comunes para esta condición son directivas que:

- Requieren la autenticación multifactor a los usuarios que accedan a un servicio desde fuera de la red corporativa.  

- Bloquean el acceso a los usuarios que accedan a un servicio desde determinados países o regiones. 

Para más información, consulte [Condiciones de ubicación del acceso condicional de Azure Active Directory](active-directory-conditional-access-locations.md).


## <a name="client-apps"></a>Aplicaciones cliente

La condición de aplicaciones de cliente le permite aplicar una directiva a distintos tipos de aplicaciones, como:

- Sitios web y servicios
- Aplicaciones de escritorio y aplicaciones móviles 

![Condiciones](./media/active-directory-conditional-access-conditions/04.png)

Una aplicación se clasifica como:

- Un sitio web o servicio si utiliza los protocolos web SSO, SAML, WS-Fed u OpenID Connect para un cliente confidencial.

- Una aplicación móvil o aplicación de escritorio si utiliza la aplicación móvil de OpenID Connect para un cliente nativo.

Para obtener una lista completa de las aplicaciones de cliente que puede utilizar en la directiva de acceso condicional, consulte la [Referencia técnica del acceso condicional de Azure Active Directory](active-directory-conditional-access-technical-reference.md#client-apps-condition).

Los casos de uso comunes para esta condición son directivas que:

- Requieren un [dispositivo compatible](active-directory-conditional-access-policy-connected-applications.md) para aplicaciones móviles y de escritorio que descarguen grandes cantidades de datos al dispositivo, mientras permiten el acceso a través del explorador desde cualquier dispositivo.

- Bloquean el acceso de aplicaciones web, pero permiten el acceso desde aplicaciones de escritorio y móviles.

Además de utilizar protocolos de autenticación modernos y SSO web, puede aplicar esta condición a las aplicaciones de correo que utilizan Exchange ActiveSync, como las aplicaciones de correo electrónico nativas de la mayoría de los smartphones. Actualmente, aplicaciones de cliente con protocolos heredados deben protegerse mediante AD FS.

 Para más información, consulte:

- [Configuración de SharePoint Online y Exchange Online para el acceso condicional de Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>pasos siguientes

- Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 

