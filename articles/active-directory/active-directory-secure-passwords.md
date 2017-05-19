---
title: "Protección de contraseñas en Azure AD y restablecimiento de contraseñas mediante Smart Password Lockout | Microsoft Docs"
description: "Explica qué es un inquilino de Azure AD y cómo administrar Azure mediante Azure Active Directory"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 910ca62c331df0825e1403c19fe2f53672a7359d
ms.contentlocale: es-es
ms.lasthandoff: 05/08/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Protección de contraseñas en Azure AD y restablecimiento de contraseñas mediante Smart Password Lockout
En este artículo se describen los procedimientos recomendados que puede seguir como usuario o administrador para proteger su instancia de Azure Active Directory (Azure AD) y sus cuentas de servicio de la cuenta de Microsoft. 

 >[!NOTE]
 >Los administradores de Azure AD pueden restablecer contraseñas de usuario haciendo clic en el nombre del directorio. En el [Portal de administración de Azure](https://manage.windowsazure.com), elija la página Usuarios, haga clic en el nombre del usuario y en Restablecer contraseña. 
 >

Azure AD incorpora los siguientes enfoques comunes para proteger las contraseñas:
 *    Requisitos de longitud de contraseña
 *    Requisitos de "complejidad" de contraseña
 *    Expiración de contraseña normal y periódica 

Para más información sobre las funcionalidades de administración de contraseñas, consulte [Administración de contraseñas en Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords). 

## <a name="azure-ad-password-protection"></a>Protección de contraseñas de Azure AD
Azure AD y el sistema de cuentas de Microsoft usan enfoques de demostrada eficacia en el sector para garantizar la protección segura de las contraseñas de usuario y administrador. 

En esta sección se explica cómo Azure AD protege las contraseñas mediante los métodos siguientes:
 *    Contraseñas prohibidas dinámicamente
 *    Smart Password Lockout

Para más información sobre la administración de contraseñas basada en la investigación actual, consulte el documento técnico [Password Guidance](http://aka.ms/passwordguidance) (Guía de contraseñas). 

### <a name="dynamically-banned-passwords"></a>Contraseñas prohibidas dinámicamente
Azure AD y el sistema de la cuenta de Microsoft salvaguardan la protección de contraseñas mediante la prohibición dinámica de todas las contraseñas de uso más común. El equipo de Azure AD Identity Protection analiza de manera rutinaria las listas de contraseñas prohibidas, y así impide que los usuarios seleccionen contraseñas de uso más común. Este servicio está disponible en Azure AD y los clientes del servicio de la cuenta de Microsoft. 

Al crear contraseñas, es importante que los administradores promuevan en los usuarios la selección de frases de contraseña poco comunes que incluyan una combinación de letras, números y caracteres. De esta manera las contraseñas resultan casi imposibles de adivinar. 

**Listas de infracciones**

Azure AD se esfuerza siempre por situarse un paso por delante de los delincuentes cibernéticos. Una manera de hacer esto es evitar que los usuarios creen contraseñas que se encuentren en la lista actual de ataques.

El equipo de Azure AD Identity Protection analiza continuamente las contraseñas que se usan con frecuencia. Los delincuentes cibernéticos también usan estrategias parecidas para informar de sus ataques, como la creación de una [tabla arco iris](https://en.wikipedia.org/wiki/Rainbow_table) para descifrar hashes de contraseña. 

Microsoft analiza continuamente las [infracciones en los datos](https://www.privacyrights.org/data-breaches) para mantener una lista de contraseñas prohibidas actualizada dinámicamente, que garantiza que las contraseñas vulnerables se prohíban antes de que se conviertan en una amenaza real para los clientes de Azure AD. Para más información sobre nuestros esfuerzos de seguridad actuales, consulte [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx) (Informe de inteligencia de seguridad de Microsoft). 

### <a name="smart-password-lockout"></a>Smart Password Lockout

Cuando AD Azure detecta un posible delincuente cibernético que intenta realizar modificaciones en la contraseña de un usuario, bloqueamos la cuenta de usuario con Smart Password Lockout. Azure AD está diseñado para determinar el riesgo asociado con sesiones de inicio de sesión específicas. 

Con los datos de seguridad más actualizados, aplicamos semántica de bloqueo a las amenazas de Internet. De esta forma, no se bloquea a los usuarios, en caso de que un delincuente cibernético haya realizado modificaciones en las contraseñas de usuario de su red.

Si un usuario está bloqueado en Azure AD, su pantalla tiene un aspecto similar al siguiente:

  ![Bloqueado en Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
Y para otras cuentas de Microsoft, su pantalla tiene un aspecto similar al siguiente:

  ![Bloqueado en una cuenta de Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Para más información sobre la administración de contraseñas en Azure Active Directory, consulte [Funcionamiento de la administración de contraseñas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works).

  >[!NOTE]
  >Los administradores de Azure AD pueden usar [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) para evitar que los usuarios creen contraseñas tradicionales.
  >

## <a name="next-steps"></a>Pasos siguientes
[Actualización de la propia contraseña](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Aspectos básicos de la administración de identidades de Azure](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[Visión operativa con los informes de administración de contraseñas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



