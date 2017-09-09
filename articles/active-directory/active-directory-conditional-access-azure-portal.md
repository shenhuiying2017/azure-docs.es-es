---
title: Acceso condicional de Azure Active Directory | Microsoft Docs
description: "Use el control de acceso condicional en Azure Active Directory para comprobar la existencia de condiciones específicas durante la autenticación para acceder a aplicaciones."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: c97f05caec4c302c847e2297d136c6614e82fd93
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---
# <a name="conditional-access-in-azure-active-directory"></a>Acceso condicional en Azure Active Directory

En un mundo Mobile First, Cloud First, Azure Active Directory permite el inicio de sesión único en dispositivos, aplicaciones y servicios desde cualquier parte. Con la proliferación de dispositivos (como BYOD), el trabajo fuera de las redes corporativas y las aplicaciones SaaS de terceros, los profesionales de TI se enfrentan con dos objetivos opuestos:

- Permitir que los usuarios finales sean productivos en cualquier lugar y en cualquier momento.
- Proteger los activos corporativos en todo momento.

Para mejorar la productividad, Azure Active Directory proporciona a los usuarios una amplia variedad de opciones de acceso a los recursos corporativos. Con la administración del acceso a las aplicaciones, Azure Active Directory le permite asegurarse de que solo *las personas adecuadas* puedan acceder a sus aplicaciones. ¿Y si quiere tener un mayor control sobre el modo en que las personas adecuadas acceden a sus recursos en determinadas condiciones? ¿Y si se dan situaciones en las que quiere bloquear el acceso a determinadas aplicaciones incluso a las *personas adecuadas*? Por ejemplo, es posible que quiera que las personas adecuadas tengan acceso a determinadas aplicaciones desde una red de confianza y que no tengan acceso a esas mismas aplicaciones desde una red en la que no confía. También puede abordar estas cuestiones mediante el acceso condicional.

El acceso condicional es una funcionalidad de Azure Active Directory que le permite aplicar controles en el acceso a las aplicaciones de su entorno según condiciones específicas. Mediante controles, puede enlazar requisitos adicionales con el acceso o puede bloquearlo. La implementación del acceso condicional se basa en directivas. Un enfoque basado en directivas simplifica su experiencia de configuración porque sigue su modo de pensar sobre los requisitos de acceso.  

Normalmente, los requisitos de acceso se definen mediante declaraciones basadas en el siguiente patrón:

![Control](./media/active-directory-conditional-access-azure-portal/10.png)

Al reemplazar las dos repeticiones de "*esto*" por información del mundo real, tiene un ejemplo de una declaración de directiva que probablemente le será familiar:

*Cuando los contratistas intentan acceder a nuestras aplicaciones de nube desde redes que no son de confianza, se bloquea el acceso.*

La declaración de directiva anterior destaca la eficacia del acceso condicional. Aunque puede permitir que los contratistas accedan de forma básica a sus aplicaciones de nube (**quién**), mediante acceso condicional, también puede definir condiciones bajo las cuales es posible el acceso (**cómo**).

En el contexto del acceso condicional de Azure Active Directory,

- "**Cuando esto sucede**" se denomina **declaración de condición**
- "**Entonces haga esto**" se denomina **controles**.

![Control](./media/active-directory-conditional-access-azure-portal/11.png)

La combinación de una declaración de condición con los controles representa una directiva de acceso condicional.

![Control](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Controles

En una directiva de acceso condicional, los controles definen qué es lo que debe suceder cuando se ha satisfecho una declaración de condición.  
Con controles, puede bloquear el acceso o permitir el acceso con requisitos adicionales.
Cuando configura una directiva que permite el acceso, debe seleccionar al menos un requisito.   

### <a name="grant-controls"></a>Controles de concesión
La implementación actual de Azure Active Directory le permite configurar los siguientes requisitos de control de concesión:

![Control](./media/active-directory-conditional-access-azure-portal/05.png)

- **Multi-factor Authentication**: pude exigir autenticación fuerte mediante la autenticación multifactor. Como proveedor, puede usar la autenticación multifactor de Azure o un proveedor de autenticación multifactor de terceros, en combinación con Active Directory Federation Services (AD FS). La autenticación multifactor ayuda a proteger los recursos ante el acceso por parte de un usuario no autorizado que haya conseguido el nombre de usuario y la contraseña de un usuario válido.

- **Dispositivo compatible**: puede configurar directivas de acceso condicional basadas en dispositivo. El objetivo de una directiva de acceso condicional basado en el dispositivo es conceder acceso a los recursos configurados solo desde dispositivos de confianza. Requerir un dispositivo compatible es una opción para definir qué es un dispositivo de confianza. Para información más detallada consulte [Configuración de directivas de acceso condicional basadas en dispositivos de Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

- **Dispositivo unido a un dominio**: requerir un dispositivo unido a un dominio es otra opción para configurar directivas de acceso condicional basadas en el dispositivo. Este requisito hace referencia a equipos de escritorio, equipos portátiles y tabletas de empresa con Windows que están unidos a una instancia local de Active Directory. Para información más detallada consulte [Configuración de directivas de acceso condicional basadas en dispositivos de Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

Si tiene varios controles seleccionados, también puede configurar si todos ellos son necesarios al procesarse su directiva.

![Control](./media/active-directory-conditional-access-azure-portal/06.png)

### <a name="session-controls"></a>Controles de sesión
Los controles de sesión permiten limitar la experiencia desde una aplicación en la nube. Los controles de sesión son aplicados por aplicaciones en la nube y se basan en información adicional sobre la sesión proporcionada por Azure AD a la aplicación.

![Control](./media/active-directory-conditional-access-azure-portal/31.png)

#### <a name="use-app-enforced-restrictions"></a>Usar restricciones que exige la aplicación
Puede usar este control para requerir que Azure AD transmita la información del dispositivo a la aplicación en la nube. Esto ayuda a la aplicación en la nube a saber si el usuario procede de un dispositivo compatible o un dispositivo unido al dominio. Actualmente este control solo se admite con SharePoint como aplicación en la nube. SharePoint usa la información del dispositivo para proporcionar a los usuarios una experiencia completa o limitada según el estado del dispositivo.
Para más información sobre cómo requerir el acceso limitado con SharePoint consulte [Controlar el acceso desde dispositivos no administrados](https://aka.ms/spolimitedaccessdocs).

## <a name="condition-statement"></a>Declaración de condición

En la sección anterior se han presentado las opciones admitidas para bloquear o restringir el acceso a los recursos en forma de controles. En una directiva de acceso condicional, se definen los criterios que es necesario satisfacer para que los controles se apliquen en forma de una declaración de condición.  

Puede incluir las siguientes asignaciones en la declaración de condición:

![Control](./media/active-directory-conditional-access-azure-portal/07.png)


### <a name="who"></a>¿Quién?

Cuando se configura una directiva de acceso condicional, es necesario seleccionar los usuarios o grupos a los que se aplica la directiva. En muchos casos, deberá hacer que sus controles se apliquen a un conjunto específico de usuarios. Puede definir este conjunto en una declaración de condición, seleccionando los usuarios y grupos a los que se aplica la directiva. Si es necesario, también puede eximir un conjunto de usuarios de su directiva si los excluye explícitamente.  

![Control](./media/active-directory-conditional-access-azure-portal/08.png)



### <a name="what"></a>¿Qué?

Cuando se configura una directiva de acceso condicional, es necesario seleccionar las aplicaciones de nube a las que se aplica la directiva.
Normalmente, hay determinadas aplicaciones en el entorno que requieren más atención que otras, desde la perspectiva de la protección. Esto afecta, por ejemplo, a las aplicaciones que tienen acceso a datos confidenciales.
Al seleccionar aplicaciones de nube, se define el ámbito de las aplicaciones de nube a las que se aplica la directiva. Si es necesario, también puede excluir explícitamente un conjunto de aplicaciones de su directiva.

![Control](./media/active-directory-conditional-access-azure-portal/09.png)

Para obtener una lista completa de las aplicaciones de nube que puede utilizar en la directiva de acceso condicional, consulte la [Referencia técnica del acceso condicional de Azure Active Directory](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).

### <a name="how"></a>¿Cómo?

Siempre y cuando el acceso se realice bajo condiciones que pueda controlar, puede que no haya necesidad de imponer controles adicionales sobre cómo los usuarios accederán a las aplicaciones de nube. Sin embargo, las cosas podrían cambiar si el acceso a sus aplicaciones de nube se realiza, por ejemplo, desde redes o dispositivos que no son de confianza. En una declaración de condición, puede definir determinadas condiciones de acceso que tienen requisitos adicionales respecto al modo en que se realiza el acceso a las aplicaciones.

![Condiciones](./media/active-directory-conditional-access-azure-portal/21.png)


## <a name="conditions"></a>Condiciones

En la implementación actual de Azure Active Directory, puede definir condiciones para las siguientes áreas:

- Riesgo de inicio de sesión
- Plataformas de dispositivo
- Ubicaciones
- Aplicaciones cliente

![Condiciones](./media/active-directory-conditional-access-azure-portal/21.png)

### <a name="sign-in-risk"></a>Riesgo de inicio de sesión

El riesgo del inicio de sesión es un objeto que utiliza Azure Active Directory para rastrear la probabilidad de que no sea el legítimo propietario de una cuenta de usuario quien haya realizado un intento de inicio de sesión. En este objeto, la probabilidad (alta, media o baja) se almacena en un formato de atributo denominado [nivel de riesgo de inicio de sesión](active-directory-reporting-risk-events.md#risk-level). El objeto se genera durante un inicio de sesión de un usuario si Azure Active Directory ha detectado riesgos de inicio de sesión. Para más información, consulte la sección sobre los [inicios de sesión peligrosos](active-directory-identityprotection.md#risky-sign-ins).  
Puede utilizar el nivel calculado de riesgo de inicio de sesión como condición en una directiva de acceso condicional. 

![Condiciones](./media/active-directory-conditional-access-azure-portal/22.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

La plataforma de dispositivo se caracteriza por el sistema operativo que se ejecuta en el dispositivo:

- Android
- iOS
- Windows Phone
- Windows
- macOS (versión preliminar). 

![Condiciones](./media/active-directory-conditional-access-azure-portal/02.png)

Puede definir las plataformas de dispositivos que se incluyen, así como las plataformas de dispositivos que se excluyen de una directiva.  
Para usar plataformas de dispositivos en la directiva, primero cambie los conmutadores de configuración a **Sí** y, luego, seleccione las plataformas de dispositivos a las que se aplica la directiva (puede ser todas o algunas de ellas). Si selecciona algunas plataformas de dispositivos, la directiva solo afecta a estas plataformas. En este caso, la directiva no afecta a los inicios de sesión en otras plataformas compatibles.


### <a name="locations"></a>Ubicaciones

La ubicación se identifica mediante la dirección IP del cliente que ha usado para conectarse con Azure Active Directory. Para esta condición es necesario estar familiarizado **Ubicaciones con nombre** y **IP de confianza de MFA**.  

**Ubicaciones con nombre** es una característica de Azure Active Directory que permite etiquetar intervalos de direcciones IP de confianza en las organizaciones. En su entorno, puede usar las ubicaciones con nombre en el contexto de la detección de [eventos de riesgo](active-directory-reporting-risk-events.md), así como del acceso condicional. Para más información sobre cómo configurar ubicaciones con nombre en Azure Active Directory, vea [Ubicaciones con nombre en Azure Active Directory](active-directory-named-locations.md).

El número de ubicaciones que se pueden configurar está restringido por el tamaño del objeto relacionado en Azure AD. Puede configurar:
 
 - Una ubicación con nombre de hasta 500 intervalos IP
 - Un máximo de 60 ubicaciones con nombre (versión preliminar) con un intervalo IP asignado a cada una 


**IP de confianza de MFA** es una característica de Multi-Factor Authentication que permite definir intervalos IP de confianza que representan la intranet local de su organización. Cuando se configura una condición de ubicación, IP de confianza le permite distinguir entre conexiones realizadas desde la red de su organización y todas las demás ubicaciones. Para más información, vea [IP de confianza](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  



Puede incluir todas las ubicaciones o todas las direcciones IP de confianza y puede excluir todas las direcciones IP de confianza.

![Condiciones](./media/active-directory-conditional-access-azure-portal/03.png)


### <a name="client-apps"></a>Aplicaciones cliente

La aplicación cliente puede estar en un nivel genérico, es decir, la aplicación (navegador web, aplicación móvil, cliente de escritorio) que ha usado para conectarse a Azure Active Directory, o puede seleccionar específicamente Exchange Active Sync.  
La autenticación heredada hace referencia a clientes que usan autenticación básica, como clientes de Office antiguos que no usan autenticación moderna. El acceso condicional no se admite actualmente con la autenticación heredada.

![Condiciones](./media/active-directory-conditional-access-azure-portal/04.png)


Para obtener una lista completa de las aplicaciones de cliente que puede utilizar en la directiva de acceso condicional, consulte la [Referencia técnica del acceso condicional de Azure Active Directory](active-directory-conditional-access-technical-reference.md#client-apps-conditions).



## <a name="common-scenarios"></a>Escenarios comunes

### <a name="requiring-multi-factor-authentication-for-apps"></a>Exigir autenticación multifactor para las aplicaciones

Muchos entornos tienen aplicaciones que requieren un mayor nivel de protección que otras.
Este es, por ejemplo, el caso de aplicaciones que tienen acceso a datos confidenciales.
Si quiere agregar otra capa de protección a estas aplicaciones, puede configurar una directiva de acceso condicional que exija autenticación multifactor cuando los usuarios accedan a estas aplicaciones.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Exigir autenticación multifactor para el acceso desde redes que no son de confianza

Este escenario es similar al escenario anterior porque agrega un requisito para la autenticación multifactor.
Sin embargo, la diferencia principal es la condición de este requisito.  
Mientras que el escenario anterior se centra en aplicaciones con acceso a datos confidenciales, este escenario se centra en ubicaciones de confianza.  
En otras palabras, podría tener un requisito de autenticación multifactor si un usuario accede a una aplicación desde una red que no es de confianza.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Solo los dispositivos de confianza pueden acceder a servicios de Office 365

Si usa Intune en su entorno, puede comenzar a usar inmediatamente la directiva de acceso condicional en la consola de Azure.

Muchos clientes de Intune usan el acceso condicional para asegurarse de que solo los dispositivos de confianza puedan acceder a servicios de Office 365. Esto significa que los dispositivos móviles se inscriben con Intune y satisfacen los requisitos de la directiva de cumplimiento y que los equipos con Windows están unidos a un dominio local. Una importante mejora es que no es necesario establecer la misma directiva para cada uno de los servicios de Office 365.  Cuando cree una nueva directiva, configure las aplicaciones de nube para que incluyan cada una de las aplicaciones de Office 365 que quiere proteger con acceso condicional.

## <a name="next-steps"></a>Pasos siguientes

- Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 
