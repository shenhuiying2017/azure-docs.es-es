---
title: ¿Qué es Azure Active Directory (Azure AD)? | Microsoft Docs
description: Usar Azure Active Directory para ampliar sus identidades locales existentes en la nube o desarrollar aplicaciones integradas de Azure AD.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 003ce2edda3e2069eb7e05f58ecc2e208c818946
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="what-is-azure-active-directory"></a>¿Qué es Azure Active Directory?
Azure Active Directory (Azure AD) es un servicio multiinquilino de administración de identidades y de directorios basado en la nube que combina los servicios de directorio principales, la administración del acceso de las aplicaciones y la protección de identidades en una única solución. Azure AD ofrece también una plataforma muy completa basada en estándares que permite a los desarrolladores proporcionar control de acceso a sus aplicaciones, en función de una directiva y reglas centralizadas.

![Pila de Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Para los administradores de TI.** Azure AD proporciona una solución más segura para su organización mediante el uso de una administración de identidades y un acceso de inicio de sesión único (SSO) más seguros a miles de [aplicaciones de SaaS en la nube](active-directory-saas-tutorial-list.md) y de aplicaciones locales. A través de estas aplicaciones también obtendrá seguridad de las aplicaciones en la nube, acceso ininterrumpido, colaboración mejorada y automatización del ciclo de vida de la identidad de los empleados, lo que ayuda a mejorar tanto la seguridad como el cumplimiento de normas.

    Además, con solo [cuatro clics](./connect/active-directory-aadconnect-get-started-express.md), Azure AD se puede integrar en una instancia existente de Windows Server Active Directory, lo que permite a su organización usar las inversiones en identidad locales existentes para administrar el acceso a aplicaciones SaaS basadas en la nube.

- **Para desarrolladores de aplicaciones.** Azure AD le permite que centrarse en la creación de aplicaciones, ya que le permite integrarse en una solución de administración de identidades que usan millones de organizaciones de todo el mundo.

- **Para clientes de Office 365, Azure o Dynamics CRM Online.** Ya usa Azure AD. Todos los inquilinos de Office 365, Azure y Dynamics CRM Online son inquilinos de Azure AD, lo que le permite empezar a administrar de inmediato el acceso de los empleados a las aplicaciones en la nube integradas.

## <a name="how-reliable-is-azure-ad"></a>¿Cuál es el grado de confiabilidad Azure AD?
El diseño multiempresa, distribuido geográficamente y de alta disponibilidad de Azure AD significa que puede confiar en él para sus necesidades empresariales más críticas. Ejecutándose en 28 centros de datos en todo el mundo con conmutación por error automatizada, tendrá la tranquilidad de saber que Azure AD es muy confiable y que incluso si un centro de datos deja de funcionar, hay copias de su directorio de datos activas en al menos dos centros de datos más dispersos por regiones y disponibles para acceso instantáneo.

Para más información acerca de los contratos de nivel de servicio, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Elección de una edición
Todos los servicios de negocios de Microsoft Online dependen de Azure Active Directory (Azure AD) para el inicio de sesión y otras necesidades de identidad. Si se suscribe a alguno de los servicios de negocios de Microsoft Online (por ejemplo, Office 365, Microsoft Azure, etc.), recibirá Azure AD con acceso a todas las características gratuitas. Con la edición gratuita de Azure Active Directory puede administrar usuarios y grupos, sincronizar directorios locales, obtener inicio de sesión único entre Azure, Office 365 y miles de aplicaciones SaaS conocidas como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc. 

Para mejorar su instancia de Azure Active Directory, puede agregar funcionalidades de pago con las ediciones Azure Active Directory Basic, Premium P1 y Premium P2. Las ediciones de pago de Azure Active Directory se crean encima del directorio gratuito existente, y proporcionan funcionalidades de tipo empresarial que abarcan autoservicio, supervisión mejorada, informes de seguridad, Multi-Factor Authentication (MFA) y un acceso seguro para sus trabajadores móviles.

> [!NOTE]
> Para ver las opciones de precios de estas ediciones, consulte [Precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, Premium P2 y Azure Active Directory Basic no se admiten actualmente en China. Para más información acerca de los precios de Azure AD, puede ponerse en contacto con el foro de Azure Active Directory.
>

* **Azure Active Directory Basic**: esta edición se ha diseñado para los trabajadores de tareas con primeras necesidades en la nube y proporciona soluciones de administración de identidades de autoservicio y de acceso a las aplicaciones centradas en la nube. Con la edición básica de Azure Active Directory, obtiene características de mejora de la productividad y reducción de costos, como administración de acceso basado en grupo, autoservicio de restablecimiento de contraseña para aplicaciones en la nube y proxy de aplicaciones de Azure Active Directory (para publicar aplicaciones web locales con Azure Active Directory), y todas ellas respaldadas por un contrato de nivel de servicio empresarial con un tiempo de actividad del 99,9 por ciento.
* **Azure Active Directory Premium P1** : esta edición, dirigida a las organizaciones con necesidades más acuciantes de administración de identidades y acceso, agrega completas funcionalidades de administración de identidades de tipo empresarial y permite a los usuarios híbridos acceder sin problemas a funcionalidades locales y en la nube. Incluye todo lo que necesita para el trabajador de información y los administradores de identidades en entornos híbridos con relación al acceso a aplicaciones, identidad de autoservicio y administración de acceso (IAM), la protección de identidades y la seguridad en la nube. Admite recursos avanzados de administración y delegación, como grupos dinámicos y administración de grupos de autoservicio. Incluye Microsoft Identity Manager (un conjunto de aplicaciones de administración de identidades y acceso locales) y proporciona funcionalidades de escritura diferida en la nube que permiten soluciones como el autoservicio de restablecimiento de contraseña para los usuarios locales.
* **Azure Active Directory Premium P2**: esta nueva oferta se ha diseñado con protección avanzada para todos los usuarios y administradores, e incluye todas las funcionalidades de Azure AD Premium P1, además de Identity Protection y Privileged Identity Management. Azure Active Directory Identity Protection aprovecha miles de millones de señales para proporcionar acceso condicional basado en el riesgo a las aplicaciones y los datos críticos de una empresa. También le ayudamos a administrar y proteger cuentas con privilegios con Azure Active Directory Privileged Identity Management, con el fin de que pueda detectar, restringir y supervisar no solo a los administradores sino también su acceso a los recursos, así como proporcionar acceso Just-In-Time cuando sea necesario.  

> [!NOTE]
> Hay varias funcionalidades de Azure Active Directory disponibles mediante las ediciones de "pago por uso":
>
> * Active Directory B2C es la solución de administración de identidades y acceso para las aplicaciones orientados al consumidor. Para más información, consulte [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).
> * Azure Multi-Factor Authentication se puede usar a través de proveedores por usuario o por autenticación. Para más información, consulte [¿Qué es Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>¿Cómo puedo comenzar?

**Si es administrador de TI:**

* [¡Pruébelo!](https://azure.microsoft.com/trial/get-started-active-directory/) : con este vínculo puede registrarse hoy mismo para disfrutar de una versión de evaluación gratuita durante 30 días e implementar su primera solución para la nube en menos de cinco minutos.

* Lea [Introducción a Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) para obtener sugerencias y trucos sobre cómo preparar un inquilino de Azure AD y tenerlo en funcionamiento rápidamente.

**Si es desarrollador:**
 
* Consulte la [guía para desarrolladores](active-directory-developers-guide.md) de Azure Active Directory

* [Inicie una versión de prueba](https://azure.microsoft.com/trial/get-started-active-directory/): regístrese hoy mismo para disfrutar de una versión de evaluación gratuita durante 30 días y empiece a integrar sus aplicaciones en Azure AD

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre los conceptos básicos de la administración de identidades y accesos de Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
