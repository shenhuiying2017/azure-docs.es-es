---
title: Condiciones del acceso condicional de Azure Active Directory | Microsoft Docs
description: Aprenda cómo se utilizan las asignaciones en el acceso condicional a Azure Active Directory para desencadenar una directiva.
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5f0ff092a7535448d48642e972d1d36652f1b83f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735148"
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Condiciones del acceso condicional de Azure Active Directory 

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede controlar el modo en que los usuarios autorizados acceden a las aplicaciones en la nube. En una directiva de acceso condicional, definirá la respuesta ("haga esto") al motivo para desencadenarla ("cuando esto suceda"). 

![Control](./media/active-directory-conditional-access-conditions/10.png)


En el contexto del acceso condicional:

- "**Cuando esto sucede**" se denomina **condición**. 
- "**Entonces haga esto**" se denomina **control de acceso**.

La combinación de las condiciones con los controles de acceso representa una directiva de acceso condicional.

![Control](./media/active-directory-conditional-access-conditions/61.png)


Las condiciones que no haya configurado en una directiva de acceso condicional no se aplicarán. Algunas condiciones son [obligatorias](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) para poder aplicar una directiva de acceso condicional a un entorno. 

En este artículo se ofrece información general de las condiciones y de cómo se utilizan en una directiva de acceso condicional. 

## <a name="users-and-groups"></a>Usuarios y grupos

La condición de usuarios y grupos es obligatoria en una directiva de acceso condicional. En la directiva, puede seleccionar **Todos los usuarios** o bien usuarios y grupos específicos.

![Control](./media/active-directory-conditional-access-conditions/111.png)

Cuando se selecciona:

- **Todos los usuarios**, la directiva se aplica a todos los usuarios del directorio. Esto incluye a los usuarios invitados.

- **Seleccionar usuarios y grupos**, puede establecer las siguientes opciones:

    - **Todos los usuarios invitados**: le permite tener como destino una directiva para usuarios invitados de B2B. Esta condición coincide con cualquier cuenta de usuario que tenga el atributo *userType* establecido en *Invitado*. Puede usar esta configuración en caso de que tenga que aplicar una directiva tan pronto como se cree una cuenta en el flujo de invitación de Azure AD.

    - **Roles de directorio**: le permiten segmentar una directiva según la asignación de roles del usuario. Esta condición admite roles de directorio como *Administrador global* o *Administrador de contraseñas*.

    - **Usuarios y grupos**: puede tener como destino conjuntos específicos de usuarios. Por ejemplo, puede seleccionar un grupo que contenga todos los miembros del departamento de recursos humanos cuando tenga una aplicación HR seleccionada como aplicación de nube.

Un grupo puede ser cualquier tipo de grupo en Azure AD, como grupos de distribución y de seguridad dinámicos o asignados.

También puede excluir determinados usuarios o grupos de una directiva. Un caso de uso común son las cuentas de servicio si su directiva aplica la autenticación multifactor (MFA). 

Dirigirse a grupos específicos de usuarios es útil para la implementación de una nueva directiva. En una nueva directiva, debería dirigir solo un conjunto inicial de usuarios para validar el comportamiento de la directiva. 



## <a name="cloud-apps"></a>Aplicaciones de nube 

Una aplicación de nube es un sitio web o servicio. Esto incluye sitios web protegidos por el proxy de la aplicación de Azure. Para obtener una descripción detallada de las aplicaciones de nube admitidas, consulte la sección [Asignaciones de aplicaciones de nube](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

La condición de aplicaciones de nube es obligatoria en una directiva de acceso condicional. En la directiva, puede seleccionar **Todas las aplicaciones en la nube** o bien usuarios y grupos específicos.

![Control](./media/active-directory-conditional-access-conditions/03.png)

Puede seleccionar:

- **Todas las aplicaciones en la nube** para establecer como referencia las directivas de línea de base que se aplicarán a toda la organización. Un caso de uso común para esta selección es una directiva que requiere autenticación multifactor cuando se detecta un riesgo de inicio de sesión en cualquier aplicación de nube. Una directiva que se aplica a **todas las aplicaciones en la nube** se aplica al acceso a todos los sitios web y servicios. Este valor no se limita solo a las aplicaciones en la nube que aparecen en la lista **Seleccionar aplicaciones en la nube**.

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


Un caso de uso común para esta condición es una directiva que restringe el acceso a las aplicaciones en la nube a [dispositivos administrados](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Para conocer más escenarios, como la condición de la plataforma de dispositivo, consulte [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Estado del dispositivo

La condición de estado del dispositivo permite que los dispositivos híbridos unidos a Azure AD y aquellos que estén marcados como compatibles se excluyan de una política de acceso condicional. Esto es útil cuando la directiva solo debe aplicarse a dispositivos no administrados para proporcionar seguridad adicional a la sesión. Por ejemplo, aplique el control de sesión de Microsoft Cloud App Security únicamente cuando un dispositivo no esté administrado. 


![Condiciones](./media/active-directory-conditional-access-conditions/112.png)

Si desea bloquear el acceso de dispositivos no administrados, debe implementar el [acceso condicional basado en dispositivos](active-directory-conditional-access-policy-connected-applications.md).


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



Una aplicación se clasifica como:

- Un sitio web o servicio si utiliza los protocolos web SSO, SAML, WS-Fed u OpenID Connect para un cliente confidencial.

- Una aplicación móvil o aplicación de escritorio si utiliza la aplicación móvil de OpenID Connect para un cliente nativo.

Para obtener una lista completa de las aplicaciones cliente que puede utilizar en la directiva de acceso condicional, consulte [Condición de aplicaciones cliente](active-directory-conditional-access-technical-reference.md#client-apps-condition) en Referencia de configuración del acceso condicional de Azure Active Directory.

Los casos de uso comunes para esta condición son directivas que:

- Requieren un [dispositivo compatible](active-directory-conditional-access-policy-connected-applications.md) para aplicaciones móviles y de escritorio que descarguen grandes cantidades de datos al dispositivo, mientras permiten el acceso a través del explorador desde cualquier dispositivo.

- Bloquean el acceso de aplicaciones web, pero permiten el acceso desde aplicaciones de escritorio y móviles.

Además de utilizar protocolos de autenticación modernos y SSO web, puede aplicar esta condición a las aplicaciones de correo que utilizan Exchange ActiveSync, como las aplicaciones de correo electrónico nativas de la mayoría de los smartphones. Actualmente, aplicaciones de cliente con protocolos heredados deben protegerse mediante AD FS.

Esta condición solo se puede seleccionar si **Office 365 Exchange Online** es la única aplicación en la nube que ha seleccionado.

![Aplicaciones de nube](./media/active-directory-conditional-access-conditions/32.png)

La selección de **Exchange ActiveSync** como condición de aplicaciones cliente solo se admite si no hay otras condiciones en una directiva configurada. Sin embargo, puede restringir el ámbito de esta condición para que solo se aplique a las plataformas compatibles.

 
![Plataformas compatibles](./media/active-directory-conditional-access-conditions/33.png)

Aplicar esta condición solo a las plataformas compatibles equivale a todas las plataformas de dispositivo en una [condición de plataforma de dispositivo](active-directory-conditional-access-conditions.md#device-platforms).

![Plataformas compatibles](./media/active-directory-conditional-access-conditions/34.png)


 Para más información, consulte:

- [Configuración de SharePoint Online y Exchange Online para el acceso condicional de Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Autenticación heredada  

Ahora se aplica el acceso condicional a los clientes de Office antiguos que no admiten la autenticación moderna, así como a los clientes que utilizan protocolos de correo como POP, IMAP, SMTP, etc. Esto permite configurar directivas como el **bloqueo del acceso desde otros clientes**.


![Autenticación heredada](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Problemas conocidos

- La configuración de una directiva para **otros clientes** bloquea toda la organización ante determinados clientes como SPConnect. Esto se debe a que estos clientes se autentican de maneras inesperadas. Este problema no aplica a las aplicaciones principales de Office como los clientes de Office anteriores. 

- La directiva puede tardar hasta 24 horas en surtir efecto. 


#### <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Esto bloqueará los servicios Web Exchange (EWS)?**

Depende del protocolo de autenticación que use EWS. Si la aplicación EWS utiliza la autenticación moderna, lo cubrirá la aplicación cliente "Aplicaciones móviles y clientes de escritorio". Si la aplicación EWS utiliza la autenticación básica, lo cubrirá la aplicación cliente "Otros clientes".


**¿Qué controles se pueden usar en Otros clientes?**

Cualquier control puede configurarse para "Otros clientes". Sin embargo, la experiencia del usuario final será bloquear el acceso en todos los casos. "Otros clientes" no admiten controles como MFA, dispositivo compatible, unión a un dominio, etc. 
 
**¿Qué condiciones uso para Otros clientes?**

Para "Otros clientes" se pueden configurar cualesquiera condiciones.

**¿Admite Exchange ActiveSync todas las condiciones y controles?**

Nº Este es el resumen de la compatibilidad de Exchange ActiveSync (EAS):

- EAS solo admite que los destinos sean usuario y grupo. No admite invitado ni rol. Si se configura la condición de invitado o de rol, todos los usuarios se bloquearán, ya que no podemos determinar si la directiva debe aplicarse al usuario, o no.

- EAS solo funciona con Exchange como aplicación en la nube. 

- EAS no admite ninguna condición, salvo la propia aplicación cliente.

- EAS se pueden configurar con cualquier control (todos menos el cumplimiento del dispositivo provocarán un bloqueo).

**¿Se aplicarán las directivas a todas las aplicaciones de manera predeterminada a partir de ahora?**

Nº No hay ningún cambio en el comportamiento predeterminado de las directivas. Las directivas de continuarán aplicándose al explorador y a las aplicaciones móviles y clientes de escritorio de forma predeterminada.



## <a name="next-steps"></a>Pasos siguientes

- Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 

