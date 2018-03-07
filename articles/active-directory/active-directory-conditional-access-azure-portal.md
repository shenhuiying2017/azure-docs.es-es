---
title: Acceso condicional de Azure Active Directory | Microsoft Docs
description: "Conozca cómo el acceso condicional en Azure Active Directory puede ayudarle a administrar el control de acceso desde una ubicación central."
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
ms.date: 02/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 7e4a65d81a7dade9ae63a24657d15a97a3a588b2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="conditional-access-in-azure-active-directory"></a>Acceso condicional en Azure Active Directory

La seguridad es una preocupación importante para las organizaciones que usan la nube. Un aspecto clave de la seguridad en la nube es la identidad y el acceso en lo referente a administrar los recursos de nube. En un mundo que da prioridad a los dispositivos móviles y la nube, los usuarios pueden acceder a los recursos de su organización mediante diversos dispositivos y aplicaciones desde cualquier parte. Como consecuencia de ello, ya no es suficiente con centrarse en quién puede acceder a un recurso. Para dominar el equilibrio entre seguridad y productividad, los profesionales de TI también necesitan incluir en la decisión sobre el control de acceso el modo en que se accede a los recursos. Con el acceso condicional de Azure AD, puede abordar este requisito. El acceso condicional es una funcionalidad de Azure Active Directory que le permite aplicar controles sobre el acceso a las aplicaciones de su entorno desde una ubicación central de acuerdo con condiciones específicas. 


![Control](./media/active-directory-conditional-access-azure-portal/81.png)

En este artículo se proporciona una introducción a los conceptos del acceso condicional en Azure AD.


## <a name="common-scenarios"></a>Escenarios comunes

En un mundo Mobile First, Cloud First, Azure Active Directory permite el inicio de sesión único en dispositivos, aplicaciones y servicios desde cualquier parte. Con la proliferación de dispositivos (como BYOD), el trabajo fuera de las redes corporativas y las aplicaciones SaaS de terceros, los profesionales de TI se enfrentan con dos objetivos opuestos:

- Capacitar a los usuarios para ser productivos donde sea y cuando sea.
- Proteger los activos corporativos en todo momento.

Mediante directivas de acceso condicional, puede aplicar los controles de acceso adecuados en las condiciones necesarias. El acceso condicional de Azure AD le proporciona seguridad adicional cuando es necesario y no se cruza en el camino de los usuarios cuando no lo es. 

A continuación se muestran algunos problemas de acceso comunes con los que puede ayudarle el acceso condicional:



- **[Riesgo de inicio de sesión](active-directory-conditional-access-conditions.md#sign-in-risk)**: Azure AD Identity Protection detecta riesgos de inicio de sesión. ¿Cómo se restringe el acceso si un riesgo de inicio de sesión detectado indica un actor incorrecto? ¿Y si quisiera tener una prueba sólida de que un inicio de sesión lo ha realizado realmente un usuario legítimo o duda lo bastante como para incluso bloquear el acceso a una aplicación a usuarios específicos?

- **[Ubicación de red](active-directory-conditional-access-locations.md)**: Azure AD es accesible desde cualquier lugar. ¿Y si se realiza un intento de acceso desde una ubicación de red que no se encuentra bajo el control de su departamento de TI? El uso de una combinación de nombre de usuario y contraseña podría ser bastante bueno como prueba de identidad para los intentos de acceso a los recursos desde la red corporativa. ¿Y si demanda una prueba de identidad más sólida para los intentos de acceso que se inician desde otros países o regiones inesperadas del mundo? ¿Y si quisiera bloquear incluso los intentos de acceso desde determinadas ubicaciones?  

- **[Administración de dispositivos](active-directory-conditional-access-conditions.md#device-platforms)**: en Azure AD, los usuarios pueden acceder a las aplicaciones en la nube desde una amplia variedad de dispositivos, como dispositivos móviles y personales. ¿Y si demanda que el intento de acceso solo debería realizarse mediante dispositivos que administra su departamento de TI? ¿Y si quisiera impedir incluso el acceso de determinados tipos de dispositivos en las aplicaciones en la nube de su entorno? 

- **[Aplicación cliente](active-directory-conditional-access-conditions.md#client-apps)**: en la actualidad, se puede acceder a muchas aplicaciones en la nube mediante diferentes tipos de aplicaciones, como aplicaciones web, aplicaciones móviles o aplicaciones de escritorio. ¿Y si un intento de acceso se realiza mediante un tipo de aplicación cliente que provoca problemas conocidos? ¿Y si un dispositivo que administra el departamento de TI lo necesita para determinados tipos de aplicaciones? 

Estas preguntas y sus respuestas relacionadas representan escenarios de acceso comunes en el acceso condicional de Azure AD. El acceso condicional es una funcionalidad de Azure Active Directory que le permite administrar escenarios de acceso mediante un enfoque basado en directivas.


## <a name="conditional-access-policies"></a>Directivas de acceso condicional

Una directiva de acceso condicional es la definición de un escenario de acceso usando el siguiente patrón:

![Control](./media/active-directory-conditional-access-azure-portal/10.png)

**Entonces haga esto** define la respuesta de la directiva. Es importante advertir que el objetivo de una directiva de acceso condicional no es conceder acceso a una aplicación en la nube. En Azure AD, conceder acceso a las aplicaciones en la nube es asunto de las asignaciones de usuario. Con una directiva de acceso condicional, se controla cómo los usuarios autorizados (los usuarios a los que se les ha concedido acceso a una aplicación en la nube) pueden acceder a las aplicaciones en la nube en condiciones específicas. La respuesta sería aplicar requisitos adicionales, como autenticación multifactor, dispositivos administrados, etc. En el contexto del acceso condicional de Azure AD, los requisitos que impone la directiva se conocen como controles de acceso. En su forma más restrictiva, la directiva puede bloquear el acceso. Para más información, consulte [Controles en el acceso condicional de Azure Active Directory](active-directory-conditional-access-controls.md).
     

**Cuando esto suceda** define la razón para la que se desencadena la directiva. Esta razón se caracteriza por un grupo de condiciones que se han cumplido. En el acceso condicional de Azure AD, las dos condiciones de asignación desempeñan un papel especial:

- **[Usuarios](active-directory-conditional-access-conditions.md#users-and-groups)**: los usuarios que realizan un intento de acceso (**Quién**). 

- **[Aplicaciones en la nube](active-directory-conditional-access-conditions.md#cloud-apps)**: los destinos de un intento de acceso (**Qué**).    

Ambas condiciones son obligatorias en una directiva de acceso condicional. Además de las dos condiciones obligatorias, también puede incluir condiciones adicionales que describan cómo se lleva a cabo el intento de acceso. Por ejemplo, usar dispositivos móviles o desde ubicaciones que se encuentran fuera de la red corporativa. Para más información, consulte [Conditions in Azure Active Directory conditional access](active-directory-conditional-access-conditions.md) (Condiciones en el acceso condicional de Azure Active Directory).   

La combinación de condiciones con controles de acceso representa una directiva de acceso condicional. 

![Control](./media/active-directory-conditional-access-azure-portal/51.png)

Con el acceso condicional de Azure AD, puede controlar el modo en que los usuarios autorizados acceden a las aplicaciones en la nube. El objetivo de una directiva de acceso condicional es aplicar controles de acceso adicionales sobre los intentos de acceso a las aplicaciones en la nube, basados en cómo se realizan estos intentos de acceso.

Una ventaja de usar un enfoque basado en directivas para proteger el acceso a las aplicaciones en la nube es que puede empezar a bosquejar los requisitos de directivas en su entorno mediante la estructura que se describe en este artículo sin preocuparse por la implementación técnica. 

## <a name="what-you-need-to-know"></a>Lo que necesita saber

### <a name="general-requirements-for-using-conditional-access"></a>Requisitos generales para usar el acceso condicional

Puede usar el acceso condicional de Azure AD para proteger las aplicaciones en la nube cuando un intento de autenticación procede de:

- Un explorador web

- Una aplicación cliente que use autenticación moderna

- Exchange ActiveSync

Para más información, consulte [Aplicaciones cliente](active-directory-conditional-access-conditions.md#client-apps).

Algunas [aplicaciones en la nube](active-directory-conditional-access-conditions.md#cloud-apps) también admiten protocolos de autenticación heredados. Esto se aplica, por ejemplo, a SharePoint Online y Exchange Online. Cuando una aplicación cliente puede utilizar un protocolo de autenticación heredado para tener acceso a una aplicación en la nube, Azure AD no puede aplicar una directiva de acceso condicional en este intento de acceso. Para evitar que una aplicación cliente omita el cumplimiento de directivas, debe comprobar si es posible habilitar solo la autenticación moderna en las aplicaciones en la nube afectadas.

Algunos ejemplos de aplicaciones cliente a las que el acceso condicional no se aplica son:

- Office 2010 y versiones anteriores

- Office 2013, si no está habilitada la autenticación moderna

Para más información, consulte [Configuración de SharePoint Online y Exchange Online para el acceso condicional de Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md).


### <a name="license-requirements-for-using-conditional-access"></a>Requisitos de licencia para usar el acceso condicional

Para usar el acceso condicional, se necesita una licencia de Azure AD Premium. Para obtener la licencia correcta para sus requisitos, consulte [Comparación de las características con disponibilidad general de las ediciones Gratis, Básico y Premium](https://www.microsoft.com/en-us/cloud-platform/azure-active-directory-features).


## <a name="next-steps"></a>pasos siguientes

- Si quiere saber más sobre:
    - Condiciones, consulte [Conditions in Azure Active Directory conditional access](active-directory-conditional-access-conditions.md) (Condiciones en el acceso condicional de Azure Active Directory).

    - Controles de acceso, consulte [Controles de acceso en el acceso condicional de Azure Active Directory](active-directory-conditional-access-controls.md).

- Si quiere obtener algo más de experiencia con la configuración de directivas de acceso condicional, consulte [Introducción al acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 
