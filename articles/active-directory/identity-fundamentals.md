---
title: "Aspectos básicos de la administración de identidades de Azure"
description: Las identidades basadas en la nube constituyen ahora la mejor manera de mantener el control y la visibilidad sobre el modo y el momento en que los usuarios acceden a las aplicaciones y los datos corporativos.
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: a7eb669aac109ae8cde6d75129004d322c270651
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="fundamentals-of-azure-identity-management"></a>Aspectos básicos de la administración de identidades de Azure

Cada vez más recursos digitales corporativos residen fuera de la red corporativa, en la nube y en dispositivos, y disponer de una solución de administración de acceso y de identidades basada en la nube se está convirtiendo en una necesidad. Las identidades basadas en la nube constituyen ahora la mejor manera de mantener el control y la visibilidad sobre el modo y el momento en que los usuarios acceden a las aplicaciones y los datos corporativos.

Microsoft lleva más de una década protegiendo identidades basadas en la nube y ahora, con [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), los usuarios pueden acceder a los mismos sistemas de protección. Con Azure AD, los administradores de las empresas pueden garantizar fácilmente la responsabilidad de los usuarios y administradores con mayor seguridad y gobernanza que nunca.

Azure AD Premium está una solución de administración de acceso y de identidades basada en la nube con funcionalidades de protección avanzadas que habilite una identidad segura para todas las aplicaciones, protección de identidad (mejoradas por el [gráfico de inteligencia de seguridad de Microsoft](https://www.microsoft.com/en-us/security/intelligence)) y Privileged Identity Management. Azure AD Premium no es solo otra herramienta de supervisión o generación de informes, sino que también puede proteger las identidades del usuario en tiempo real y permite crear directivas de acceso que se adaptan en función del riesgo, adaptable para proteger los datos de su organización.

En este breve vídeo verá una introducción rápida a la protección y administración de identidades de Azure AD:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft no solo proporciona una identidad que le lleva a todas partes, sino también un conjunto de herramientas que automatizan, ayudan a proteger y administran la TI en su organización. Aunque haya llegado la informática en la nube, sigue siendo preciso administrar y controlar las tareas de TI, como las llamadas al departamento de soporte técnico para restablecer contraseñas de usuario, la administración de grupos de usuario y las solicitudes de aplicaciones. Y para complicar aún más el panorama, en la actualidad los empleados llevan sus dispositivos personales al trabajo y usan las aplicaciones SaaS disponibles. Todo ello hace que no sea nada fácil mantener el control sobre sus aplicaciones en los centros de datos corporativos y en las plataformas en la nube pública.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Conexión de una instancia local de Active Directory a Azure AD y Office 365
Las organizaciones que han hecho grandes inversiones en instancias locales de Active Directory pueden extender dichas inversiones a la nube mediante la integración de sus directorios locales con Azure AD en una [administración de identidades híbridas](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Dicho cambio aumenta la productividad de los usuarios, ya que proporciona una identidad común para acceder a los recursos, independientemente de su ubicación. Los usuarios y organizaciones pueden utilizar el inicio de sesión único (SSO) para acceder tanto a los recursos locales como a los servicios en la nube, como Office 365.

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) es la única herramienta necesaria para realizar la integración. Azure AD Connect proporciona funcionalidades para satisfacer sus necesidades de sincronización de identidades y reemplaza las versiones anteriores de las herramientas de integración de identidades como DirSync y Azure AD Sync. Con Azure AD Connect, la administración y sincronización de identidades entre un entorno local y Azure AD se habilitan a través de:

- Sincronización: este componente es responsable de la creación de usuarios, grupos y otros objetos. También es responsable de asegurarse de que la información de identidad de los usuarios y los grupos de su entorno local coincide con la de la nube. También se puede habilitar la escritura diferida de contraseñas para no perder la sincronización de los directorios locales cuando un usuario actualiza su contraseña en Azure AD.
- AD FS: la federación es una funcionalidad opcional que proporciona Azure AD Connect y puede utilizarse para configurar un entorno híbrido mediante una infraestructura local de AD FS. Se puede usar para abordar implementaciones complejas como un inicio de sesión único, la aplicación de la directiva de inicio de sesión de AD y MFA mediante tarjeta inteligente o de terceros.
- Seguimiento de estado: [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) puede proporcionar una sólida supervisión y una ubicación central en Azure Portal donde se puede ver esta actividad.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Aumento de la productividad y reducción de los costos del departamento de soporte técnico gracias al autoservicio y al inicio de sesión único

Los empleados son más productivos cuando tienen que recordar un único nombre de usuario y contraseña, y cuando pueden trabajar indistintamente con cualquier dispositivo. También ahorran tiempo cuando pueden realizar ellos mismos tareas como [restablecer una contraseña olvidada](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) o solicitar acceso a una aplicación sin tener que depender de la ayuda del departamento de soporte técnico.

Azure AD [extiende el entorno local de Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) a la nube, lo que permite que los usuarios empleen su cuenta organizativa principal tanto para los dispositivos personales unidos a un dominio y los recursos de la empresa como para todas las aplicaciones web y SaaS que necesitan usar para realizar su trabajo. Además de no tener que recordar varios nombres de usuario y contraseñas, el acceso a las aplicaciones por parte de los usuarios también se puede aprovisionar (o desaprovisionar) automáticamente en función de su pertenencia a los grupos de la organización y de su estado como empleado. Además, puede controlar el acceso de las aplicaciones de la galería o de sus propias aplicaciones locales que ha desarrollado y publicado mediante el [proxy de la aplicación de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Administración y control del acceso a los recursos corporativos
Las soluciones de administración de identidades y acceso de Microsoft ayudan al departamento de TI a proteger el acceso a las aplicaciones y los recursos en el centro de datos corporativo y en la nube. para lo que se habilitan más niveles de validación, como la [autenticación multifactor](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) y las [directivas de acceso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). La supervisión de actividades sospechosas mediante auditorías, alertas e informes de seguridad avanzados contribuye a minimizar los posibles problemas de seguridad.

Las directivas de acceso condicional de Azure AD Premium proporcionan al administrador de la organización la capacidad de crear reglas de acceso basada en directivas para todas las aplicaciones conectadas a AD Azure (aplicaciones SaaS, aplicaciones personalizadas que se ejecutan en la nube o aplicaciones web locales). Azure AD evalúa estas directivas en tiempo real y las aplica cada vez que un usuario intenta acceder a una aplicación. Las directivas de protección de identidad de Azure le permiten emprender una acción automáticamente si se detecta actividad sospechosa. Estas acciones pueden incluir bloquear el acceso a los usuarios de alto riesgo, exigir la autenticación multifactor y restablecer las contraseñas de usuario, si parece que se han comprometido las credenciales.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), que se incluye en la oferta de P2 de Azure Active Directory Premium, permite detectar, restringir y administrar cuentas administrativas y su acceso a los recursos de Azure Active Directory y a otros servicios en línea de Microsoft. También facilita la administración del acceso administrativo a petición durante el período exacto necesario.

Privileged Identity Management puede aplicar los derechos de administrador a petición para que los administradores puedan solicitar la elevación temporal y con autenticación multifactor de sus privilegios durante períodos preconfigurados antes de que sus cuentas vuelvan a un estado de usuario normal.

## <a name="benefits-of-azure-identity"></a>Ventajas de la identidad de Azure

Con la administración de identidades de Azure se puede:

-   Crear y administrar una identidad única para cada usuario en toda la empresa, lo que mantiene a los usuarios, grupos y dispositivos sincronizados con [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Proporcionar acceso de inicio de sesión único a las aplicaciones, incluidas miles de aplicaciones SaaS integradas previamente, o proporcionar acceso remoto seguro a aplicaciones SaaS locales mediante el [proxy de la aplicación de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Habilitar la seguridad del acceso de las aplicaciones mediante la aplicación de [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) basado en reglas para las aplicaciones locales y en la nube.

-   Mejorar la productividad del usuario con el [autoservicio de restablecimiento de contraseña](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) y las solicitudes de acceso de grupos y aplicaciones mediante el [portal MyApps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Sacar provecho de la [alta disponibilidad y confiabilidad](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) de una solución a nivel mundial de administración de identidades y acceso basada en la nube de categoría empresarial.

## <a name="next-steps"></a>pasos siguientes
[Obtener más información sobre soluciones de identidad de Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)