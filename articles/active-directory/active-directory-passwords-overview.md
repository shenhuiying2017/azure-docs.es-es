---
title: "Información general sobre el autoservicio de restablecimiento de contraseña | Microsoft Docs"
description: "¿Para qué puede servir el autoservicio de restablecimiento de contraseña de Azure AD en una organización?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Autoservicio de restablecimiento de contraseña de Azure AD para profesionales de TI

El autoservicio de restablecimiento de contraseña de Azure Active Directory (Azure AD) (SSPR) permite a los usuarios restablecer las contraseñas por sí mismos cuando y donde lo necesiten, a la vez que permite a los administradores controlar cómo se restablecen las contraseñas. Los usuarios ya no necesitan llamar al departamento de soporte técnico para restablecer las contraseñas.

* **Cambio de contraseña de autoservicio**: el usuario conoce la contraseña pero desea cambiarla por una nueva.
* **Restablecimiento de contraseña de autoservicio**: el usuario no puede iniciar sesión y desea restablecer la contraseña mediante uno o varios de los siguientes métodos de autenticación validados.
   * Mensaje de texto a un teléfono móvil validado
   * Llamada telefónica a un teléfono móvil o teléfono de trabajo validado
   * Mensaje de correo electrónico a una cuenta de correo electrónico secundaria validada
   * Respuestas a sus preguntas de seguridad
* **Desbloqueo de cuenta de autoservicio**: el usuario no puede iniciar sesión con la contraseña, que se ha bloqueado, y le gustaría desbloquear la cuenta sin intervención del administrador mediante los métodos de autenticación.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Por qué elegir el autoservicio de restablecimiento de contraseñas de Azure AD

* **Reducir los costos**: el restablecimiento de contraseñas con asistencia de un departamento de soporte técnico supone normalmente el 20 % de las llamadas de asistencia en TI de una organización. 
* **Mejorar experiencias de usuarios finales** y **reducir el volumen del departamento de soporte técnico**: los usuarios finales pueden resolver sus propios problemas con las contraseñas de inmediato sin necesidad de llamar a un departamento de soporte técnico ni de abrir una solicitud de soporte técnico.
* **Impulsar la movilidad**: los usuarios pueden restablecer sus contraseñas desde cualquier lugar en que se encuentren.
* **Mantener el control** de directiva de seguridad. Los administradores pueden seguir aplicando las directivas que tienen hoy en día.

Si está listo, puede comenzar a utilizar SSPR de Azure AD con la [guía de inicio rápido](active-directory-passwords-getting-started.md) y permitir que los usuarios restablezcan las contraseñas con rapidez.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilidad de autoservicio de restablecimiento de contraseña de Azure AD

El autoservicio de restablecimiento de contraseña de Azure AD está disponible en tres niveles, en función de la suscripción de que se trate.

* **Azure AD Free**: los administradores que están solo en la nube pueden restablecer sus propias contraseñas.
* **Azure AD Basic** o cualquier **suscripción de pago de Office 365**: los usuarios que están solo en la nube pueden restablecer sus propias contraseñas.
* **Azure AD Premium**: cualquier usuario o administrador, incluidos los que están solo en la nube, los federados o los usuarios sincronizados con contraseña, pueden restablecer sus propias contraseñas. Algunas contraseñas locales requieren que la escritura diferida de contraseñas esté habilitada.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Precios, SLA, actualizaciones y hoja de ruta de Azure AD

Encontrará más información sobre licencias, precios y futuros planes en los sitios siguientes.

* [**Detalles de precios de Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Precios de Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Acuerdos de Nivel de Servicio**](https://azure.microsoft.com/support/legal/sla/)
* [**Acuerdo de Nivel de Servicio de Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Actualizaciones de Azure**](https://azure.microsoft.com/updates/)
* [**Hoja de ruta de Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Pasos siguientes

* ¿Está listo para empezar a trabajar con SSPR? [Configuración del autoservicio de restablecimiento de contraseña de Azure AD](active-directory-passwords-getting-started.md).
* Planee una implementación correcta de SSPR para los usuarios mediante las directrices que aparecen en nuestra [**guía de implementación**](active-directory-passwords-best-practices.md).
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md).
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).