---
title: "Seguridad de contraseñas por niveles de Azure AD | Microsoft Docs"
description: "Explica cómo Azure AD aplica contraseñas seguras y protege las contraseñas de los usuarios frente a los ciberdelincuentes."
services: active-directory
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.openlocfilehash: 683badcfb67dd9e98058d560a6b13d1a3474d3e9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Un enfoque de varios niveles para la seguridad de contraseñas de Azure AD

En este artículo se describen los procedimientos recomendados que puede seguir como usuario o administrador para proteger su cuenta de Azure Active Directory (Azure AD) o Microsoft.

 > [!NOTE]
 > **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md).
 >
 > Los administradores de Azure AD pueden restablecer las contraseñas de usuario mediante las instrucciones del artículo [Restablecimiento de la contraseña de un usuario en Azure Active Directory](active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Requisitos de contraseña

Azure AD incorpora los siguientes enfoques comunes para proteger las contraseñas:

* Requisitos de longitud de contraseña
* Requisitos de complejidad de contraseña
* Expiración de contraseña normal y periódica

Para más información sobre el restablecimiento de contraseña en Azure Active Directory, consulte el tema [Autoservicio de restablecimiento de contraseña de Azure AD para profesionales de TI](active-directory-passwords-update-your-own-password.md).

## <a name="azure-ad-password-protections"></a>Protección de contraseñas de Azure AD

Azure AD y el sistema de cuentas de Microsoft usan enfoques de demostrada eficacia en el sector para garantizar la protección segura de las contraseñas de usuario y administrador, entre los que se incluyen:

* Contraseñas prohibidas dinámicamente
* Smart Password Lockout

Para más información sobre la administración de contraseñas basada en la investigación actual, consulte el documento técnico [Password Guidance](http://aka.ms/passwordguidance) (Guía de contraseñas).

### <a name="dynamically-banned-passwords"></a>Contraseñas prohibidas dinámicamente

Azure AD y el sistema de cuentas de Microsoft salvaguardan la protección de contraseñas mediante la prohibición dinámica de las contraseñas de uso más común. El equipo de Azure AD Identity Protection analiza con regularidad las listas de contraseñas prohibidas, y así impide que los usuarios seleccionen contraseñas de uso más común. Este servicio está disponible en Azure AD y los clientes del servicio de la cuenta de Microsoft.

Al crear contraseñas, es importante que los administradores promuevan en los usuarios la selección de frases de contraseña que incluyan una combinación de letras, números, caracteres o palabras. Este enfoque ayuda a crear contraseñas de usuario que es prácticamente imposible poner en peligro pero que son más fáciles de recordar para los usuarios.

#### <a name="password-breaches"></a>Infracciones de contraseña

Microsoft se esfuerza siempre por situarse un paso por delante de los delincuentes cibernéticos.

El equipo de Azure AD Identity Protection analiza continuamente las contraseñas que se usan con frecuencia. Los delincuentes cibernéticos también usan estrategias parecidas para informar de sus ataques, como la creación de una [tabla arco iris](https://en.wikipedia.org/wiki/Rainbow_table) para descifrar hashes de contraseña.

Microsoft analiza continuamente las [infracciones en los datos](https://www.privacyrights.org/data-breaches) para mantener una lista de contraseñas prohibidas actualizada dinámicamente, que garantiza que las contraseñas vulnerables se prohíban antes de que se conviertan en una amenaza real para los clientes de Azure AD. Para más información sobre nuestros esfuerzos de seguridad actuales, consulte [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx) (Informe de inteligencia de seguridad de Microsoft).

### <a name="smart-password-lockout"></a>Smart Password Lockout

Cuando AD Azure detecta un posible delincuente cibernético que intenta realizar modificaciones en la contraseña de un usuario, bloqueamos la cuenta de usuario con Smart Password Lockout. Azure AD está diseñado para determinar el riesgo asociado con sesiones de inicio de sesión específicas. Con los datos de seguridad más actualizados, aplicamos semántica de bloqueo para detener las amenazas de Internet.

Si un usuario está bloqueado en Azure AD, su pantalla tiene un aspecto similar al siguiente:

  ![Bloqueado en Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

Para otras cuentas de Microsoft, su pantalla tiene un aspecto similar al siguiente:

  ![Bloqueado en una cuenta de Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Para más información sobre el restablecimiento de contraseña en Azure Active Directory, consulte el tema [Autoservicio de restablecimiento de contraseña de Azure AD para profesionales de TI](active-directory-passwords-update-your-own-password.md).

  >[!NOTE]
  >Los administradores de Azure AD pueden usar [Windows Hello](https://www.microsoft.com/windows/windows-hello) para evitar que los usuarios creen contraseñas tradicionales.
  >

## <a name="next-steps"></a>Pasos siguientes

* [Actualización de la propia contraseña](active-directory-passwords-update-your-own-password.md)
* [Aspectos básicos de la administración de identidades de Azure](fundamentals-identity.md)
* [Informe sobre la actividad de restablecimiento de contraseña](active-directory-passwords-reporting.md)
