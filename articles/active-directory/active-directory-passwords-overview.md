---

title: "Información general sobre el autoservicio de restablecimiento de contraseña | Microsoft Docs"
description: "¿Para qué puede servir el autoservicio de restablecimiento de contraseña de Azure AD en una organización?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: 9f72a4900f20282827ff939aa41bc4f306e547a3
ms.contentlocale: es-es
ms.lasthandoff: 05/20/2017


---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Autoservicio de restablecimiento de contraseña de Azure AD para profesionales de TI

La palabra "autoservicio" está de moda entre muchos departamentos de TI de todo el mundo, que se utiliza con diferentes significados. El mercado está atestado de productos que le permiten administrar los grupos locales, las contraseñas o los perfiles de usuario en la nube o a nivel local.

El autoservicio de restablecimiento de contraseña (SSPR) de Azure Active Directory (Azure AD) se diferencia de otros servicios por la facilidad de uso e implementación. El autoservicio de restablecimiento de contraseña de Azure AD combina un conjunto de funcionalidades que:

* Permiten a los usuarios administrar su propia contraseña
  * Desde cualquier dispositivo
  * En cualquier ubicación
  * En cualquier momento
* Mantienen el cumplimiento con directivas que defina como administrador

Si está listo, puede comenzar a utilizar SSPR de Azure AD con la [guía de inicio rápido](active-directory-passwords-getting-started.md) y permitir que los usuarios restablezcan las contraseñas con rapidez.

## <a name="what-is-possible"></a>Lo que es posible

* El **cambio de la contraseña de autoservicio** permite a los usuarios finales o administradores cambiar sus contraseñas sin ayuda del administrador.
* El **desbloqueo de cuentas de autoservicio** permite a los usuarios finales desbloquear su propia cuenta sin ayuda del administrador.
* El **Autoservicio de restablecimiento de contraseña** permite a los usuarios finales o administradores restablecer sus contraseñas sin ayuda del administrador. El Autoservicio de restablecimiento de contraseña requiere Azure AD Premium o Básico; véase [Ediciones de Azure Active Directory](active-directory-editions.md).
* El **restablecimiento de contraseña iniciado por el administrador** permite a un administrador restablecer la contraseña de un usuario final o de otro administrador desde [Azure Portal](https://docs.microsoft.com/azure/azure-portal-overview).
* Los **informes de actividad de administración de contraseñas** proporcionan a los administradores perspectivas sobre una actividad de registro y restablecimiento de contraseña en su organización; véase [informes de administración](active-directory-passwords-reporting.md).
* La **escritura diferida de contraseñas** permite la administración de contraseñas locales desde la nube, por lo que todos los escenarios anteriores pueden realizarse por los usuarios sincronizados con contraseña o federados, o en nombre de ellos. La escritura diferida de contraseñas requiere [Azure AD Premium](active-directory-get-started-premium.md).

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Por qué elegir el autoservicio de restablecimiento de contraseñas de Azure AD

* **Reducir los costos**: el restablecimiento de contraseñas con asistencia de un servicio y departamento de soporte técnico normalmente supone el 20 % del gasto en TI de una organización.
* **Mejorar experiencias de usuarios finales** y **reducir el volumen del departamento de soporte técnico**: los usuarios finales pueden resolver sus propios problemas con las contraseñas de inmediato sin necesidad de llamar a un departamento de soporte técnico ni de abrir una solicitud de soporte técnico.
* **Impulsar la movilidad**: los usuarios pueden restablecer sus contraseñas desde cualquier lugar en que se encuentren.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilidad de autoservicio de restablecimiento de contraseña de Azure AD

El autoservicio de restablecimiento de contraseña de Azure AD está disponible en tres niveles, en función de la suscripción de que se trate.

* **Azure AD Gratis**: los administradores que están solo en la nube pueden restablecer sus propias contraseñas.
* **Azure AD Básico** o cualquier **suscripción de pago de Office 365**: los usuarios y administradores que están solo en la nube pueden restablecer sus propias contraseñas.
* **Azure AD Premium**: cualquier usuario o administrador, incluidos los que están solo en la nube, los federados o los usuarios sincronizados con contraseña, pueden restablecer sus propias contraseñas. Algunas contraseñas locales requieren que la escritura diferida de contraseñas esté habilitada.

## <a name="azure-ad-self-service-password-reset-a-sum-of-the-parts"></a>El autoservicio de restablecimiento de contraseña de Azure AD, una suma de las partes

El autoservicio de restablecimiento de contraseña de Azure AD consta de los siguientes componentes:

* **Portal de configuración de administración de contraseñas**: aquí puede controlar opciones para la administración de contraseñas en el inquilino a través de Azure Portal.
* **Portal de registro de restablecimiento de contraseñas**: aquí los usuarios pueden registrarse automáticamente para el restablecimiento de contraseñas.
* **Portal de restablecimiento de contraseñas**: aquí los usuarios pueden restablecer su contraseña mediante los desafíos definidos por el administrador y las respuestas que los usuarios han proporcionado.
* **Portal de cambio de contraseñas de usuario**: aquí los usuarios pueden cambiar sus propias contraseñas si escriben su contraseña anterior y proporcionan una contraseña nueva.
* **Informes de administración de contraseñas**: aquí los administradores pueden ver y analizar los informes de actividad de contraseñas de los inquilinos en Azure Portal.
* **Escritura diferida de contraseñas en entornos locales mediante Azure AD Connect**: permite habilitar la administración de usuarios sincronizados locales, federados o con contraseñas desde la nube.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Precios, SLA, actualizaciones y hoja de ruta de Azure AD

Se puede consultar más información sobre estos temas en las páginas siguientes.

* [**Detalles de precios de Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Precios de Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Acuerdos de Nivel de Servicio**](https://azure.microsoft.com/support/legal/sla/)
* [**Acuerdo de Nivel de Servicio de Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Actualizaciones de Azure**](https://azure.microsoft.com/updates/)
* [**Hoja de ruta de Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planee e implemente SSPR en sus usuarios mediante las instrucciones que se encuentran aquí.
* [**Personalización**](active-directory-passwords-customize.md): personalización de la experiencia de SSPR para la empresa
* [**Informes**](active-directory-passwords-reporting.md): detectan si los usuarios acceden a la funcionalidad de SSPR, cuándo lo hacen y dónde.
* [**Profundización técnica**](active-directory-passwords-how-it-works.md): conozca lo que hay detrás para comprender cómo funciona.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR


