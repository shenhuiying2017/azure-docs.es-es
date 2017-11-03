---
title: "¿Cuáles son las novedades de Azure Active Directory? | Microsoft Docs"
description: "Use estas notas de la versión para mantenerse al día de **versiones más recientes**, **problemas conocidos**, **correcciones de errores**, **funciones obsoletas** y **planes de cambios** en Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 0361d5b48af876af2ccc522351484d369554bfca
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="whats-new-in-azure-active-directory"></a>¿Cuáles son las novedades de Azure Active Directory?

Estamos mejorando Azure Active Directory de forma continuada para usted. Para ayudarle a mantenerse al día con los avances más recientes, este tema le proporciona información acerca de:

-   Versiones más recientes 
-   Problemas conocidos 
-   Corrección de errores 
-   Funciones obsoletas 
-   Planes de cambios 

Vuelva a visitar esta página con regularidad, ya que se actualiza mensualmente.


## <a name="october-2017"></a>Octubre de 2017

**Tipo:** plan de cambio  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  


**Desuso de las API de informes de Azure AD (versión beta) bajo el nodo `https://graph.windows.net/<tenant-name>/reports/`**

Azure Portal proporciona lo siguiente:

- Una nueva consola de administración de Azure Active Directory 
- Nuevas API de informes de actividades y de seguridad
 
Debido a estas nuevas funcionalidades, las API de informes bajo el punto de conexión **/reports** se retirará el 10 de diciembre de 2017. 

---

**Tipo:** fijo   
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** SSO  


Azure Active Directory admite la detección automática de campos de inicio de sesión para las aplicaciones que presentan un campo de nombre de usuario y contraseña HTML.  Estos pasos se documentan en [Captura automática de campos de inicio de sesión para una aplicación](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Puede encontrar esta funcionalidad mediante la adición de una aplicación *situada fuera de la galería* en la página **Aplicaciones empresariales** en [Azure Portal](http://aad.portal.azure.com). Además, en esta nueva aplicación puede configurar el modo **Inicio de sesión único** en **Inicio de sesión único basado en contraseña**. Para ello, especifique una URL web y, a continuación, guarde la página.
 
Debido a un problema del servicio, esta funcionalidad se deshabilitó temporalmente. El problema está resuelto y la detección automática del campo de inicio de sesión vuelve a estar disponible.

---

**Tipo:** nueva característica  
**Categoría del servicio:** MFA  
**Funcionalidad del producto:** seguridad y protección de identidades  


En el mundo en que vivimos, la autenticación multifactor (MFA) es una parte esencial de la protección de su organización. El equipo de identidad de Microsoft está desarrollando la autenticación multifactor para que las credenciales se adapten mejor y la experiencia sea más fluida. Hoy me complace anunciar dos pasos importantes en este proceso: 

- Integración de los resultados del desafío multifactor directamente en el informe de inicio de sesión de Azure AD, incluido el acceso mediante programación a los resultados de MFA

- Integración más profunda de la configuración de MFA en la experiencia de configuración principal de Azure AD en Azure Portal

Con esta versión preliminar pública, los informes y la administración de MFA forman parte de la experiencia de configuración principal de Azure AD, lo que le permite administrar la funcionalidad del portal de administración de MFA dentro de la experiencia de Azure AD.


---
**Tipo:** nueva característica  
**Categoría de servicio:** Términos de uso  
**Funcionalidad del producto:** gobierno  


**Términos de uso de Azure AD** proporciona un método sencillo para presentar la información a los usuarios finales. Esto garantiza que los usuarios ven las declinaciones de responsabilidades pertinentes para los requisitos legales o de cumplimiento.

Los términos de uso de Azure AD puede utilizarse en los escenarios siguientes:

- Términos de uso generales para todos los usuarios de su organización. 

- Términos de uso específicos basados en los atributos de un usuario (por ejemplo médicos por un lado y enfermeras por otro, empleados nacionales por un lado e internacionales por otro, en función de grupos dinámicos). 

- Términos de uso específicos para el acceso a las aplicaciones de alto impacto empresarial, como Salesforce.

Para obtener más información, consulte [Términos de uso de Azure Active Directory](active-directory-tou.md).


---
**Tipo:** nueva característica  
**Categoría del servicio:** PIM  
**Funcionalidad del producto:** Privileged Identity Management  


Con Privileged Identity Management (PIM) (versión preliminar) de Azure Active Directory, puede administrar, controlar y supervisar el acceso a los recursos de Azure dentro de su organización. Esto incluye las suscripciones, los grupos de recursos e incluso las máquinas virtuales. Todos los recursos de Azure Portal que aprovechen la funcionalidad de control de acceso basado en rol (RBAC) de Azure puede aprovechar las excelentes funcionalidades de seguridad y administración del ciclo de vida que ofrece PIM de Azure AD, además de algunas características nuevas muy útiles que tenemos previsto incorporar pronto a los roles de Azure AD.

Para obtener más información, consulte [PIM para recursos de Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Tipo:** nueva característica  
**Categoría de servicio:** Revisiones de acceso  
**Funcionalidad del producto:** gobierno  


Novedades de Azure AD

Las revisiones de acceso (versión preliminar) permiten a las organizaciones administrar de forma eficiente las pertenencias a grupos y el acceso a las aplicaciones empresariales: 

- Puede volver a certificar el acceso de usuario invitado mediante las revisiones de acceso sobre su acceso a aplicaciones y su pertenencia a grupos. La información que proporcionan las revisiones de acceso permite a los revisores decidir de manera eficiente si los invitados deben seguir teniendo acceso.

- Puede volver a certificar el acceso de los empleados a las aplicaciones y las pertenencias a grupos con las revisiones de acceso.

Puede recopilar los controles de revisiones de acceso en programas importantes para que su organización realice un seguimiento de las revisiones de aplicaciones vulnerables o de cumplimiento normativo.

Para obtener más información, consulte [Revisiones de acceso de Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Tipo:** nueva característica  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** SSO  


**Capacidad de ocultar las aplicaciones de terceros de Mis aplicaciones y del iniciador de Office 365**

Ahora puede administrar mejor las aplicaciones que se muestran en los portales de usuario a través de una nueva propiedad **hide app**. Esto resulta útil en los casos en que los iconos de las aplicaciones se muestran para servicios de back-end o iconos duplicados y acaban bloqueando los iniciadores de aplicaciones del usuario. El botón de alternancia se encuentra en la sección de propiedades de la aplicación de terceros y presenta la etiqueta **Visible to user?** (¿Visible para el usuario?). También puede ocultar una aplicación mediante programación a través de PowerShell. 

Para obtener más información, consulte [Hide a third-party application from user's experience in Azure Active Directory](active-directory-coreapps-hide-third-party-app.md) (Ocultar una aplicación de terceros de la experiencia del usuario en Azure Active Directory). 


**¿Qué está disponible?**

 Como parte de la transición a la nueva consola de administración, hemos creado dos nuevas API para recuperar los registros de actividad de Azure AD disponibles. El nuevo conjunto de API proporciona más funciones de filtro y ordenación, así como actividades de auditoría e inicio de sesión. Los datos que estaban disponibles anteriormente a través de los informes de seguridad ahora son accesibles a través de la API de eventos de riesgo de Identity Protection en Microsoft Graph.


## <a name="september-2017"></a>Septiembre de 2017

**Tipo:** característica modificada  
**Categoría del servicio:** Microsoft Identity Manager  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  


Un paquete acumulativo de revisiones (compilación 4.4.1642.0) está disponible desde el 25 de septiembre de 2017 para Microsoft Identity Manager (MIM) 2016 Service Pack 1 (SP1). Este paquete acumulativo de revisiones:

- Resuelve problemas y agrega mejoras.
- Es una actualización acumulativa que reemplaza todas las actualizaciones de MIM 2016 SP1 hasta la compilación 4.4.1459.0 de Microsoft Identity Manager 2016. 
- Requiere que disponga de **Microsoft Identity Manager 2016, compilación 4.4.1302.0.** 

Para obtener más información, consulte [Paquete acumulativo de revisiones (compilación 4.4.1642.0) está disponible para Microsoft Identity Manager SP1 de 2016](https://support.microsoft.com/en-us/help/4021562). 

---