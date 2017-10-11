---
title: "Características de seguridad de Azure que ayudan a la administración de identidades | Microsoft Docs"
description: " Este artículo proporciona una visión general de las principales características de seguridad de Azure que contribuyen a la administración de identidades. Las soluciones de administración de la identidad y el acceso de Microsoft permiten al departamento de TI proteger el acceso a las aplicaciones y los recursos en el centro de datos corporativo y en la nube. Además, esto posibilita que existan más niveles de validación, como la autenticación multifactor y las directivas de acceso condicional. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: terrylan
ms.openlocfilehash: 8d00882caf5411240c5f0a3533c78c3dbe361ef2
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-identity-management-security-overview"></a>Información general sobre seguridad de administración de identidades de Azure
Las soluciones de administración de la identidad y el acceso de Microsoft permiten al departamento de TI proteger el acceso a las aplicaciones y los recursos en el centro de datos corporativo y en la nube. Además, esto posibilita que existan más niveles de validación, como la autenticación multifactor y las directivas de acceso condicional. La supervisión de actividades sospechosas mediante auditorías, alertas e informes de seguridad avanzados contribuye a minimizar los posibles problemas de seguridad. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) proporciona un inicio de sesión único para miles de aplicaciones en la nube (SaaS) y acceso a aplicaciones web que se ejecutan de forma local.

Entre las ventajas en materia de seguridad de Azure Active Directory (AD) se incluye la capacidad de:

* Crear y administrar una identidad única para cada usuario en toda la empresa híbrida, lo que mantiene los usuarios, grupos y dispositivos sincronizados
* Proporcionar un acceso de inicio de sesión único a las aplicaciones, incluidas miles de aplicaciones SaaS preintegradas
* Habilitar la seguridad del acceso a las aplicaciones mediante la aplicación de la autenticación multifactor basada en reglas para las aplicaciones locales o en la nube
* Proporcionar un acceso remoto seguro a las aplicaciones web locales a través del proxy de la aplicación de Azure AD

El objetivo de este artículo es proporcionar una visión general de las principales características de seguridad de Azure que contribuyen a la administración de identidades. Además, se incluyen vínculos a artículos que ofrecen detalles de cada una de estas características para que pueda tener más información al respecto.  

El artículo se centra en las siguientes funcionalidades de administración de identidades de Azure principales:

* Inicio de sesión único
* Proxy inverso
* Multi-Factor Authentication
* Supervisión de seguridad, alertas e informes basados en aprendizaje automático
* Administración de identidades y acceso de consumidores
* Registro de dispositivos
* Privileged Identity Management
* Protección de identidad
* Administración de identidades híbridas

## <a name="single-sign-on"></a>Inicio de sesión único
Inicio de sesión único (SSO) significa poder tener acceso a todas las aplicaciones y los recursos que necesita para hacer negocios, iniciando sesión una sola vez con una única cuenta de usuario. Una vez que ha iniciado sesión, puede tener acceso a todas las aplicaciones que necesite sin tener que autenticarse (por ejemplo, escribiendo una contraseña) una segunda vez.

Muchas organizaciones confían en el modelo de software como servicio (SaaS), como Office 365, Box y Salesforce para la productividad del usuario final. Tradicionalmente, el personal de TI tenía que crear y actualizar individualmente cuentas de usuario en cada aplicación SaaS y los usuarios tenían que recordar una contraseña para cada aplicación SaaS.

Azure AD extiende los entornos de Active Directory locales a la nube, permitiendo a los usuarios usar su cuenta profesional principal no solo para iniciar sesión en sus dispositivos unidos a dominios y recursos de la empresa, sino también en todas las aplicaciones web y SaaS necesarias para su trabajo.

Los usuarios no solo no tienen que administrar varios conjuntos de nombres de usuario y contraseñas, sino que es posible aprovisionar o cancelar el aprovisionamiento del acceso a las aplicaciones automáticamente en función de los grupos de la organización y de su estado de empleado. Azure AD introduce controles de seguridad y acceso que permiten administrar de forma centralizada el acceso de los usuarios a través de aplicaciones SaaS.

Más información:

* [Información general de inicio de sesión único](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
* [Integración del inicio de sesión único de Azure Active Directory con aplicaciones SaaS](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Proxy inverso
El proxy de aplicación de Azure AD permite publicar aplicaciones locales (como sitios de [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US), [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx) y aplicaciones basadas en [IIS](http://www.iis.net/)) en la red privada y proporciona un acceso seguro a los usuarios externos a la red. El proxy de aplicación proporciona acceso remoto e inicio de sesión único (SSO) para muchos tipos de aplicaciones web locales, junto con las miles de aplicaciones SaaS que Azure AD admite. Los empleados pueden iniciar sesión en sus aplicaciones desde casa, en sus propios dispositivos, y autenticarse a través de este proxy basado en la nube.

Más información:

* [Habilitación del proxy de la aplicación de Azure AD](../active-directory/active-directory-application-proxy-enable.md)
* [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Inicio de sesión único con el Proxy de aplicación](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
* [Uso de acceso condicional](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-factor Authentication (MFA) es un método de autenticación que requiere el uso de más de un método de verificación y agrega un segundo nivel de seguridad crítico a las transacciones e inicios de sesión del usuario. MFA ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Proporciona autenticación sólida mediante una gran variedad de opciones de verificación, como llamadas telefónicas, mensajes de texto, notificaciones de aplicaciones móviles, códigos de verificación y tokens OAuth de terceros.

Más información:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [¿Qué es Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
* [Cómo funciona Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Supervisión de seguridad, alertas e informes basados en aprendizaje automático
La supervisión y las alertas de seguridad, así como los informes basados en el aprendizaje automático que identifican patrones de acceso incoherentes, puede ayudarle a proteger su negocio. Puede usar los informes de acceso y uso de Active Directory de Azure para proporcionar visibilidad sobre la integridad y la seguridad del directorio de su organización. Con esta información, un administrador de directorios puede determinar mejor dónde puede haber posibles riesgos de seguridad de modo que pueda planear adecuadamente la mitigación de estos riesgos.

En el Portal de Azure clásico, los informes se clasifican de la manera siguiente:

* Informes de anomalías: contienen eventos de inicio de sesión que se consideran anómalos. Nuestro objetivo es que sea consciente de dicha actividad y que pueda tomar una decisión sobre si un evento es sospechoso.
* Informes de aplicaciones integradas: proporcionan información sobre cómo se usan en su organización aplicaciones en la nube. Azure Active Directory ofrece integración con miles de aplicaciones en la nube.
* Informes de errores: indican errores que se pueden producir al aprovisionar cuentas a aplicaciones externas.
* Informes específicos del usuario: muestran los datos de actividad de dispositivo o de inicio de sesión de un usuario concreto.
* Registros de actividad: contienen un registro de todos los eventos auditados en las últimas 24 horas, los últimos 7 días o los últimos 30 días, así como los cambios en la actividad del grupo y la actividad de registro y de restablecimiento de contraseña.

Más información:

* [Visualización de los informes de acceso y uso](../active-directory/active-directory-view-access-usage-reports.md)
* [Introducción a los informes de Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
* [Guía de informes de Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Administración de identidades y acceso de consumidores
Azure Active Directory B2C es un servicio de administración de identidades global y de alta disponibilidad para aplicaciones orientadas al consumidor que se puede utilizar con cientos de millones de identidades. Se puede integrar en plataformas móviles y web. Los consumidores pueden iniciar sesión en todas sus aplicaciones con una experiencia totalmente personalizable, usando sus cuentas de las redes sociales o mediante credenciales nuevas.

En el pasado, los desarrolladores de aplicaciones que deseaban registrar e iniciar la sesión de los consumidores en sus aplicaciones tenían que escribir su propio código. Y usaban bases de datos o sistemas locales para almacenar nombres de usuario y contraseñas. Azure Active Directory B2C ofrece a su organización una manera mejor de integrar la administración de identidades de consumidor en las aplicaciones gracias a la ayuda de una plataforma segura basada en estándares y un amplio conjunto de directivas extensibles.

El uso de Azure Active Directory B2C permite a los consumidores registrarse en las aplicaciones con sus cuentas de redes sociales existentes (Facebook, Google, Amazon, LinkedIn) o creando nuevas credenciales (dirección de correo electrónico y contraseña o nombre de usuario y contraseña).

Más información:

* [¿Qué es Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Versión preliminar de Azure Active Directory B2C: registro e inicio de sesión de consumidores en las aplicaciones](../active-directory-b2c/active-directory-b2c-overview.md)
* [Versión preliminar de Azure Active Directory B2C: tipos de aplicaciones](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registro de dispositivos
El Registro de dispositivos de Azure AD es la base de los escenarios de [acceso condicional](../active-directory/active-directory-conditional-access-device-registration-overview.md) basado en dispositivos. Cuando se registra un dispositivo, el Registro de dispositivos de Azure Active Directory le proporciona una identidad que se utiliza para autenticar el dispositivo cuando el usuario inicia sesión. El dispositivo autenticado y los atributos del dispositivo pueden utilizarse para aplicar directivas de acceso condicional para las aplicaciones que se hospedan en la nube y locales.

Cuando se combina con una solución de administración de dispositivos móviles (MDM) como Intune, los atributos del dispositivo en Azure Active Directory se actualizan con información adicional sobre este. Esto le permite crear reglas de acceso condicional que obligan a que el acceso desde dispositivos cumpla con las normas de seguridad y cumplimiento.

Más información:

* [Introducción al Registro de dispositivos de Azure Active Directory](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Privileged Identity Management de Azure Active Directory (AD) le permite administrar, controlar y supervisar las identidades con privilegios y el acceso a los recursos en Azure AD y en otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.

A veces, los usuarios necesitan llevar a cabo operaciones con privilegios en recursos de Azure u Office 365 o en otras aplicaciones SaaS. Esto significa a menudo que las organizaciones tienen que concederles acceso con privilegios permanentes en Azure AD. Esto representa un riesgo de seguridad creciente para los recursos hospedados en la nube porque las organizaciones no pueden supervisar suficientemente qué hacen esos usuarios con sus privilegios administrativos. Además, si se pone en peligro una cuenta de usuario que tiene acceso con privilegios, esta situación podría afectar a su seguridad en la nube global. Administración de identidades con privilegios de Azure AD le ayuda a resolver este riesgo.

Azure AD Privileged Identity Management le permite:

* Ver qué usuarios son administradores de Azure AD
* Habilitar el acceso administrativo a petición y "justo a tiempo" en servicios de Microsoft Online Services, como Office 365 e Intune
* Obtener informes sobre el historial de acceso de administrador y sobre los cambios en las asignaciones de administrador
* Obtener alertas sobre el acceso a un rol con privilegios

Más información:

* [Administración de identidades con privilegios de Azure AD](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Roles en Privileged Identity Management de Azure AD](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Privileged Identity Management de Azure AD: Incorporación o eliminación de un rol de usuario](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Protección de identidad
Azure AD Identity Protection es un servicio de seguridad que proporciona una vista consolidada de eventos de riesgo y puntos vulnerables que afectan a las identidades de su organización. Identity Protection aprovecha las funciones de detección de anomalías existentes de Azure Active Directory (disponibles mediante informes de actividad anómala de Azure AD) e introduce nuevos tipos de eventos de riesgo que pueden detectar anomalías en tiempo real.

Más información:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Presentación de Azure AD e Identity: versión preliminar de Identity Protection)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Administración de identidades híbridas
Microsoft trata las identidades tanto a nivel local como en la nube, de forma que se crea una sola identidad de usuario para la autenticación y la autorización en todos los recursos, sin importar su ubicación.

Más información:

* [Notas del producto sobre identidad híbrida](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog del equipo de Active Directory](https://blogs.technet.microsoft.com/ad/)
