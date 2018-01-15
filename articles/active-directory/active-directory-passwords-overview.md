---
title: "Información general sobre el autoservicio de restablecimiento de contraseña | Microsoft Docs"
description: "¿Para qué puede servir el autoservicio de restablecimiento de contraseña de Azure AD en una organización?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 074a22d0d3d6f4218be431409dcb0e516d226335
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Autoservicio de restablecimiento de contraseña de Azure AD para profesionales de TI

Con el autoservicio de restablecimiento de contraseña de Azure Active Directory (Azure AD), los usuarios pueden restablecer sus propias contraseñas cuando y donde sea necesario. Al mismo tiempo, los administradores pueden controlar cómo se restablece la contraseña de un usuario. Los usuarios ya no necesitan llamar a un departamento de soporte técnico para restablecer las contraseñas. El autoservicio de restablecimiento de contraseña de Azure AD incluye:

* **Autoservicio de cambio de contraseña**: el usuario conoce la contraseña pero desea cambiarla por una nueva.
* **Autoservicio de restablecimiento de contraseña**: el usuario no puede iniciar sesión y desea restablecer la contraseña mediante uno o varios de los siguientes métodos de autenticación validados:
   * Enviar un mensaje de texto a un teléfono móvil validado.
   * Realizar una llamada telefónica a un teléfono móvil o teléfono de trabajo validado.
   * Enviar un mensaje de correo electrónico a una cuenta de correo electrónico secundaria validada.
   * Responder a sus preguntas de seguridad.
* **Autoservicio de desbloqueo de cuentas**: el usuario no puede iniciar sesión con su contraseña y se ha bloqueado. El usuario quiere desbloquear su cuenta sin intervención del administrador mediante el uso de los métodos de autenticación.

## <a name="why-choose-azure-ad-sspr"></a>¿Por qué elegir el autoservicio de restablecimiento de contraseña de Azure AD?

El autoservicio de restablecimiento de contraseña de Azure AD le ayuda a:

* **Reducir los costos**: el restablecimiento de contraseñas con asistencia de un departamento de soporte técnico supone normalmente el 20 % de las llamadas de asistencia en TI de una organización. 
* **Mejorar la experiencia del usuario final** y **reducir el volumen del departamento de soporte técnico**, ya que proporciona a los usuarios finales la capacidad de resolver sus propios problemas con las contraseñas. No es necesario llamar a un departamento de soporte técnico ni abrir una solicitud de soporte técnico.
* **Impulsar la movilidad**: los usuarios pueden restablecer sus contraseñas desde cualquier lugar en que se encuentren.
* **Mantener el control** de la directiva de seguridad. Los administradores pueden seguir aplicando las directivas que tienen hoy en día.

Si está listo, puede empezar a usar el autoservicio de restablecimiento de contraseña de Azure AD con nuestra [guía de inicio rápido](active-directory-passwords-getting-started.md). Podrá proporcionar rápidamente a sus usuarios la capacidad de restablecer sus propias contraseñas.

## <a name="azure-ad-sspr-availability"></a>Disponibilidad del autoservicio de restablecimiento de contraseña de Azure AD

El autoservicio de restablecimiento de contraseña de Azure AD está disponible en tres niveles, en función de la suscripción de que se trate:

* **Azure AD Free** : los administradores que están solo en la nube pueden restablecer sus propias contraseñas.
* **Azure AD Basic** o cualquier **suscripción de pago de Office 365**: los usuarios que están solo en la nube pueden restablecer sus propias contraseñas.
* **Azure AD Premium**: cualquier usuario o administrador, incluidos los que están solo en la nube, los federados o los usuarios sincronizados con contraseña, pueden restablecer sus propias contraseñas. Algunas contraseñas locales requieren que la escritura diferida de contraseñas esté habilitada.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Precios, SLA, actualizaciones y hoja de ruta de Azure AD

Encontrará más información sobre licencias, precios y futuros planes en los sitios siguientes:

* [Detalles de precios de Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Precios de Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/)
* [Acuerdo de Nivel de Servicio de Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Actualizaciones de Azure](https://azure.microsoft.com/updates/)
* [Azure Roadmap](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>pasos siguientes

* ¿Está listo para empezar a trabajar con SSPR? [Configure el autoservicio de restablecimiento de contraseña de Azure AD](active-directory-passwords-getting-started.md).
* Planee una implementación correcta del autoservicio de restablecimiento de contraseña para los usuarios mediante las directrices que aparecen en nuestra [guía de implementación](active-directory-passwords-best-practices.md).
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md).
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).
