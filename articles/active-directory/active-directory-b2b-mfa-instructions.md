---
title: "Acceso condicional para usuarios de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "La colaboración B2B de Azure Active Directory admite Multi-Factor Authentication (MFA) para poder acceder de manera selectiva a las aplicaciones corporativas."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: fac4657e9b78e900c052384e212aa9f3915f970d
ms.lasthandoff: 04/13/2017


---

# <a name="conditional-access-for-b2b-collaboration-users"></a>Acceso condicional para usuarios de colaboración B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Multi-Factor Authentication para usuarios B2B
Con la colaboración B2B de Azure AD, las organizaciones tienen la capacidad de aplicar directivas de autenticación multifactor para usuarios de B2B. Estas directivas se pueden aplicar en el nivel de inquilino, el nivel de aplicación o el nivel de usuario individual, del mismo modo que estas directivas pueden habilitarse para empleados a tiempo completo y miembros de la organización. Se aplican las directivas MFA en la organización de recursos.

Esto significa lo siguiente:
1. El administrador o el trabajador de la información de la empresa A invita a un usuario de la empresa B a una aplicación Foo de la empresa A.
2. La aplicación *Foo* de la empresa A se configura para requerir MFA en el acceso.
3. Cuando el usuario de la empresa B trata de acceder a la aplicación Foo desde el espacio de la empresa A, se le pide que complete un desafío de MFA según sea necesario mediante la directiva de MFA de la empresa A.
4. Para que el usuario pueda configurar su MFA con la empresa A, elige su opción de MFA.
5. Esto funcionará para cualquier identidad (Azure AD o MSA, por ejemplo, si los usuarios de la empresa B se autentican con un identificador social).
6. La empresa A deberá tener SKU premium adecuados de Azure AD que sean compatibles con MFA. El usuario de la empresa B consumirá esta licencia de la empresa A.

En resumen, el espacio invitador *siempre* es responsable de la MFA de los usuarios de colaboración B2B de la organización del asociado, no la propia organización del asociado (incluso si tiene funcionalidades de MFA).

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configuración de Multi-Factor Authentication para los usuarios de colaboración B2B
Para descubrir lo fácil que es configurar MFA para los usuarios de colaboración B2B, vea el siguiente vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Experiencia de MFA de los usuarios B2B para el canje de ofertas
En la siguiente animación se puede ver el canje, como se muestra en este vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Restablecimiento de la MFA para los usuarios de colaboración B2B
Actualmente, el administrador puede requerir que se vuelvan a probar los usuarios de colaboración B2B solo mediante los siguientes cmdlets de PowerShell. Por lo tanto, los siguientes cmdlets de PowerShell deben usarse si desea restablecer el método de prueba de un usuario de la colaboración B2B.

1. Conectarse a Azure

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Obtenga todos los usuarios con los métodos de prueba.

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Aquí tiene un ejemplo:

  ```
  PS C:\Users\tjwasserGet-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Restablezca el método MFA para un usuario específico. Después, puede usar ese nombre principal de usuario para ejecutar el comando de restablecimiento con el fin de requerir al usuario de la colaboración B2B volver a establecer métodos de prueba. Ejemplo:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>¿Por qué se realiza el MFA en el arrendamiento de recursos?

En la versión actual, MFA está siempre en el arrendamiento de recursos. El motivo es de esto es la previsibilidad.

Por ejemplo, supongamos que un usuario de Contoso (Sally) está invitado a Fabrikam y Fabrikam ha habilitado MFA para usuarios de B2B.

Ahora, si Contoso tiene una directiva MFA habilitada para App1 pero no para App2, entonces si miramos la notificación de MFA de Contoso en el token para determinar si Sally debe realizar la MFA en Fabrikam o no, podría ocurrir el siguiente problema:

* Día 1: Sally ha realizado la MFA en Contoso porque tiene acceso a App1, entonces no verá el aviso de MFA de Fabrikam.

* Día 2: Sally ha accedido a App2 en Contoso, por tanto ahora cuando accede a Fabrikam, tendrá que registrarse para MFA en Fabrikam.

Esto puede resultar muy confuso para Sally y es probable que dará lugar a caídas en las finalizaciones de inicio de sesión.

Además, aunque Contoso tenga la funcionalidad de MFA, no siempre Fabrikam confiará en la directiva de MFA de Contoso.

Por último, el MFA del inquilino de recursos también funciona para MSA e identificadores sociales, así como para organizaciones de asociados que no tengan una configuración de MFA.

Por lo tanto, la recomendación de MFA para usuarios de B2B es requerir siempre MFA de recursos. Esto podría provocar un MFA doble en algunos casos, pero cada vez que se accede a los inquilinos de recursos, la experiencia de los usuarios finales es predecible: Sally debe registrarse para MFA con el inquilino de recursos.

### <a name="device-location-and-risk-based-conditional-access-for-b2b-users"></a>Dispositivo, ubicación y acceso condicional basado en riesgos para usuarios de B2B

Cuando la organización de Contoso habilita las directivas de acceso condicional basado en dispositivo para sus datos corporativos, se impide el acceso desde dispositivos no administrados (es decir, dispositivos que no están administrados por la organización de Contoso y que no son conformes con las directivas de dispositivo de Contoso).

Si el dispositivo del usuario de B2B no está administrado por Contoso, esto significa que se bloqueará el acceso de usuarios de B2B de las organizaciones de asociados comerciales en el contexto en que se aplican estas directivas.

Es un buen nivel esperar que los usuarios de otra organización tengan su dispositivo administrado por la organización invitadora. Por lo tanto, en actualizaciones futuras, se va a habilitar a Contoso para que confíe en el estado de cumplimiento de dispositivo de asociados específicos. Esto permitiría a Contoso aplicar directivas por las que un usuario de Fabrikam pueda acceder a los recursos de Contoso si se usa también un dispositivo administrado de Fabrikam.

Mientras tanto, Contoso puede crear listas de exclusión que contengan usuarios asociados específicos a partir de la directiva de acceso condicional basado en dispositivo.

#### <a name="location-based-conditional-access-for-b2b"></a>Acceso condicional basado en ubicación para B2B

Las directivas de acceso condicional basado en ubicación se pueden aplicar a los usuarios de B2B si la organización invitadora (por ejemplo, Contoso) puede crear un perímetro de red de confianza (es decir, un intervalo de direcciones IP) que define sus organizaciones de asociados (por ejemplo, Fabrikam).

#### <a name="risk-based-conditional-access-for-b2b"></a>Acceso condicional basado en riesgos para B2B

Actualmente, no se pueden aplicar directivas basadas en riesgo a usuarios de B2B, ya que se realiza la evaluación de riesgos en la organización principal del usuario de B2B (en otras palabras, el arrendatario de identidades del usuario de B2B).

Para actualizaciones futuras, estamos considerando la posibilidad de federar la puntuación de riesgo de los asociados (con su consentimiento) para que Contoso pueda proteger sus datos y aplicaciones compartidas externamente no solo de los riesgos de los que son conscientes, sino también de los que no conocen, porque puede estar produciéndose en otro lugar.

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

