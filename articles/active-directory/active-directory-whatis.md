---
title: "¿Qué es Azure Active Directory?"
description: Usar Azure Active Directory para ampliar sus identidades locales existentes en la nube o desarrollar aplicaciones integradas de Azure AD.
services: active-directory
documentationcenter: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.openlocfilehash: e5eafd4d25d2638ab5d9f904a7e0c00b36501d68
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="what-is-azure-active-directory"></a>¿Qué es Azure Active Directory?
Azure Active Directory (Azure AD) es el directorio basado en la nube de varios inquilinos y el servicio de administración de identidades de Microsoft. Azure AD combina servicios de directorio fundamentales, gobierno avanzado de la identidad y administración del acceso a las aplicaciones. Azure AD ofrece también una plataforma muy completa basada en estándares que permite a los desarrolladores proporcionar control de acceso a sus aplicaciones, en función de una directiva y reglas centralizadas. 

Para los administradores de TI, Azure AD proporciona una solución asequible y fácil de usar para dar a los empleados y asociados comerciales acceso de inicio de sesión único (SSO) a [miles de aplicaciones SaaS en la nube](active-directory-saas-tutorial-list.md) como Office365, Salesforce.com, DropBox y Concur.

Para los desarrolladores de aplicaciones, Azure AD permite al usuario centrarse en la creación de su aplicación facilitando y acelerando la integración de la misma con una solución de administración de identidades de clase mundial usada por millones de organizaciones de todo el mundo.

Azure AD también incluye una serie completa de capacidades de administración de identidades, incluida Multi-Factor Authentication, el registro de dispositivos, la administración de contraseñas de autoservicio, la administración de grupos de autoservicio, la administración de cuentas con privilegios, el control de acceso basado en roles, la supervisión del uso de aplicaciones, la auditoría enriquecida y la supervisión y alertas de seguridad. Estas funcionalidades pueden ayudar a proteger aplicaciones basadas en la nube, optimizar los procesos de TI, reducir costos y asegurar que se cumplen los objetivos de cumplimiento normativo corporativos.

Además, con solo [cuatro clics](./connect/active-directory-aadconnect-get-started-express.md), Azure AD puede integrarse con un Windows Server Active Directory existente, lo que da a las organizaciones la capacidad de aprovechar sus inversiones en identidad locales existentes para administrar el acceso a aplicaciones SaaS basadas en la nube.

Si es un cliente de Office 365, Azure o Dynamics CRM Online, es posible que no se dé cuenta de que ya está usando Azure AD. Cada inquilino de Office 365, Azure y Dynamics CRM ya es en realidad un inquilino de Azure AD. ¡Siempre que lo desee, puede empezar a usar ese inquilino para administrar el acceso a miles de otras aplicaciones en la con las que se integra Azure AD!

![Pila de Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>¿Cuál es el grado de confiabilidad Azure AD?
El diseño multiempresa, distribuido geográficamente y de alta disponibilidad de Azure AD significa que puede confiar en él para sus necesidades empresariales más críticas. Ejecutándose en 28 centros de datos en todo el mundo con conmutación por error automatizada, tendrá la tranquilidad de saber que Azure AD es muy confiable y que incluso si un centro de datos deja de funcionar, hay copias de su directorio de datos activas en al menos dos centros de datos más dispersos por regiones y disponibles para acceso instantáneo.

Para más información, consulta los [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Elección de una edición
Todos los servicios de negocios de Microsoft Online dependen de Azure Active Directory (Azure AD) para el inicio de sesión y otras necesidades de identidad. Si se suscribe a alguno de los servicios de negocios de Microsoft Online (por ejemplo, Office 365, Microsoft Azure, etc.), recibirá Azure AD con acceso a todas las características gratuitas. Con la edición gratuita de Azure Active Directory puede administrar usuarios y grupos, sincronizar directorios locales, obtener inicio de sesión único entre Azure, Office 365 y miles de aplicaciones SaaS conocidas como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc. 

Para mejorar su instancia de Azure Active Directory, puede agregar funcionalidades de pago con las ediciones Azure Active Directory Basic, Premium P1 y Premium P2. Las ediciones de pago de Azure Active Directory se crean encima del directorio gratuito existente, y proporcionan funcionalidades de tipo empresarial que abarcan autoservicio, supervisión mejorada, informes de seguridad, Multi-Factor Authentication (MFA) y un acceso seguro para sus trabajadores móviles.

> [!NOTE]
> Para ver las opciones de precios de estas ediciones, consulte [Precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, Premium P2 y Azure Active Directory Basic no se admiten actualmente en China. Póngase en contacto con nosotros en el Foro de Azure Active Directory para más información.
>

* **Edición básica de Azure Active Directory** : esta edición, concebida para los trabajadores de tareas con primeras necesidades en la nube, proporciona soluciones de administración de identidades de autoservicio y de acceso a las aplicaciones basado en la nube. Con la edición básica de Azure Active Directory, obtiene características de mejora de la productividad y reducción de costos, como administración de acceso basado en grupo, autoservicio de restablecimiento de contraseña para aplicaciones en la nube y proxy de aplicaciones de Azure Active Directory (para publicar aplicaciones web locales con Azure Active Directory), y todas ellas respaldadas por un contrato de nivel de servicio empresarial con un tiempo de actividad del 99,9 por ciento.
* **Azure Active Directory Premium P1** : esta edición, dirigida a las organizaciones con necesidades más acuciantes de administración de identidades y acceso, agrega completas funcionalidades de administración de identidades de tipo empresarial y permite a los usuarios híbridos acceder sin problemas a funcionalidades locales y en la nube. Incluye todo lo que necesitan los trabajadores de información y los administradores de identidades en los entornos híbridos para el acceso entre aplicaciones, la administración de identidades y acceso de autoservicio (IAM), la protección de identidades y la seguridad en la nube. Admite recursos avanzados de administración y delegación, como grupos dinámicos y administración de grupos de autoservicio. Incluye Microsoft Identity Manager (un conjunto de aplicaciones de administración de identidades y acceso locales) y proporciona funcionalidades de escritura diferida en la nube que permiten soluciones como el autoservicio de restablecimiento de contraseña para los usuarios locales.
* **Azure Active Directory Premium P2** : esta nueva oferta, diseñada con protección avanzada para todos los usuarios y administradores, incluye todas las funcionalidades de Azure AD Premium P1, así como nuestras nuevas características Identity Protection y Privileged Identity Management. Azure Active Directory Identity Protection aprovecha miles de millones de señales para proporcionar acceso condicional basado en el riesgo a las aplicaciones y los datos críticos de una empresa. También le ayudamos a administrar y proteger cuentas privilegiadas con Azure Active Directory Privileged Identity Management para que pueda descubrir, restringir y supervisar a los administradores y su acceso a los recursos y proporcionar acceso a tiempo cuando sea necesario.  

> [!NOTE]
> Hay varias funcionalidades de Azure Active Directory disponibles mediante las ediciones de "pago por uso":
>
> * Active Directory B2C es la solución de administración de identidades y acceso para las aplicaciones orientados al consumidor. Para obtener más información, consulte [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Azure Multi-Factor Authentication se puede usar a través de proveedores por usuario o por autenticación. Para obtener más información, consulte [Qué es Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>¿Cómo puedo comenzar?

**Si es administrador de TI:**

* [¡Pruébelo!](https://azure.microsoft.com/trial/get-started-active-directory/) Puede suscribirse a una evaluación gratuita de 30 días hoy e implementar su primera solución en la nube en menos de 5 minutos con este vínculo.

* Lea [Introducción a Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) para obtener sugerencias y trucos sobre cómo preparar un inquilino de Azure AD y tenerlo en funcionamiento rápidamente.

**Si es desarrollador:**
 
* Consulta las [Instrucciones para desarrolladores](active-directory-developers-guide.md) de Azure Active Directory

* [Inicie una evaluación](https://azure.microsoft.com/trial/get-started-active-directory/): suscríbase hoy mismo a una prueba gratuita de 30 días y comience a integrar sus aplicaciones con Azure AD.

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre los conceptos básicos de la administración de identidades y accesos de Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
