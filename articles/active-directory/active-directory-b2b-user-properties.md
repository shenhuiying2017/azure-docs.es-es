---
title: "Propiedades de un usuario de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Las propiedades de un usuario de colaboración B2B de Azure Active Directory son configurables."
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
ms.date: 05/25/2017
ms.author: sasubram
ms.openlocfilehash: 6e49cb202ed03bf50fb9ca34d34924cda434829c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propiedades de un usuario de colaboración B2B de Azure Active Directory

Un usuario de colaboración de negocio a negocio (B2B) de Azure Active Directory (Azure AD) es un usuario con UserType = Guest. Dicho usuario suele ser de una organización asociada y tiene, de forma predeterminada, privilegios limitados en el directorio de la invitación.

En función de las necesidades de la organización invitadora, un usuario de colaboración de B2B de Azure AD puede tener cualquiera de los siguientes estados de cuenta:

- Estado 1: alojado en una instancia externa de Azure AD y representado como un usuario invitado en la organización que invita. En este caso, el usuario de B2B inicia sesión con una cuenta de Azure AD que pertenece al inquilino invitado. Aunque la organización asociada no use Azure AD, se crea el usuario invitado en Azure AD. Los requisitos son que el usuario canjea su invitación y Azure AD comprueba su dirección de correo electrónico. Esta solución también se denomina inquilino Just-In-Time (JIT) o inquilino "viral".

- Estado 2: alojado en una cuenta Microsoft y representado como usuario invitado en la organización host. En este caso, el usuario invitado inicia sesión con una cuenta Microsoft. La identidad social (google.com o similar), que no es una cuenta Microsoft, se crea como una cuenta Microsoft durante el canje de la oferta.

- Estado 3: alojado en la instancia de Active Directory local de la organización host y sincronizado con la instancia de Azure AD de la organización host. Durante esta versión se debe usar PowerShell para cambiar manualmente el valor de UserType de dichos usuarios en la nube.

- Estado 4: alojado en la instancia de Azure AD de la organización host con UserType = Guest y credenciales que administra dicha organización.

  ![Se muestran las iniciales del invitador](media/active-directory-b2b-user-properties/redemption-diagram.png)


Ahora, veamos cómo es un usuario de colaboración de B2B de Azure AD en estado 1 en Azure AD.

### <a name="before-invitation-redemption"></a>Antes del canje de la invitación

![Antes del canje de la oferta](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Después del canje de la invitación

![Después del canje de la oferta](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Propiedades clave del usuario de colaboración de B2B de Azure AD
### <a name="usertype"></a>UserType
Esta propiedad indica la relación del usuario con el espacio host. Esta propiedad puede tener dos valores:
- Member: este valor indica un empleado de la organización host y un usuario en la plantilla de dicha organización. Por ejemplo, este usuario espera tener acceso solo a sitios internos. Este usuario no se consideraría un colaborador externo.

- Invitado: este valor indica un usuario que no se considera interno de la empresa, como un colaborador externo, un asociado, un cliente o un usuario similar. No se espera que un usuario así reciba una comunicación interna del CEO o que reciba beneficios de la empresa, por ejemplo.

  > [!NOTE]
  > UserType no tiene relación alguna con la forma en que el usuario inicia sesión, el rol de directorio del usuario, etc. Esta propiedad simplemente indica la relación del usuario con la organización host y permite a la organización exigir directivas que dependan de esta propiedad.

### <a name="source"></a>Source
Esta propiedad indica la forma en que el usuario inicia sesión.

- Invited User: este usuario ha recibido la invitación, pero aún no la ha canjeado.

- External Active Directory: este usuario está alojado en una organización externa y se autentica mediante una cuenta de Azure AD que pertenece a la otra organización. Este tipo de inicio de sesión corresponde al estado 1.

- Microsoft account: el usuario está alojado en una cuenta Microsoft y se autentica mediante una cuenta Microsoft. Este tipo de inicio de sesión corresponde al estado 2.

- Windows Server Active Directory: este usuario inicia sesión desde una instancia de Active Directory local que pertenece a esta organización. Este tipo de inicio de sesión corresponde al estado 3.

- Azure Active Directory: este usuario se autentica mediante una cuenta de Azure AD que pertenece a esta organización. Este tipo de inicio de sesión corresponde al estado 4.
  > [!NOTE]
  > Source y UserType son propiedades independientes. Un valor de Source no implica un valor concreto de UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>¿Se pueden agregar usuarios de B2B de Azure AD como miembros, en lugar de como invitados?
Normalmente, un usuario invitado y uno de B2B de Azure AD son sinónimos. Por tanto, de manera predeterminada los usuarios de colaboración de B2B de Azure AD se agregan como usuario con UserType = Guest. Sin embargo, en algunos casos, la organización asociada forma parte de una organización mayor a la que también pertenece la organización host. En ese caso, la organización host puede tratar a los usuarios de la organización asociada como miembros, en lugar de como invitados. Use las API del Administrador de invitaciones de B2B de Azure AD para agregar un usuario de la organización asociada a la organización host como miembro, o para invitarlo.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtro de usuarios invitados en el directorio

![Filtrar usuarios invitados](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Conversión de UserType
Actualmente, los usuarios pueden convertir el valor Member de UserType en Guest, y viceversa, mediante PowerShell. Sin embargo, se supone que la propiedad UserType representa la relación del usuario con la organización. Por tanto, el valor de esta propiedad solo se debe cambiar si cambia la relación del usuario con la organización. Si cambia la relación del usuario, ¿deben atenderse problemas como si se debe cambiar el nombre principal de usuario (UPN)? ¿Debe el usuario seguir teniendo acceso a los mismos recursos? ¿Debe asignarse un buzón de correo? Por lo tanto, no se recomienda cambiar el valor de UserType mediante el uso de PowerShell como una actividad atómica. Además, en caso de que esta propiedad se vuelva inmutable mediante PowerShell, no se recomienda depender de este valor.

## <a name="remove-guest-user-limitations"></a>Eliminación de limitaciones de usuarios invitados
Puede haber casos en los que desee ofrecer a los usuarios invitados privilegios más altos. Puede agregar un usuario invitado a cualquier rol e, incluso, eliminar las restricciones de usuario invitado predeterminadas en el directorio para concederle los mismos privilegios que a los miembros.

Se pueden desactivar las limitaciones de usuarios invitados predeterminadas para que a los usuarios invitados del directorio de la empresa se les concedan los mismos permisos que a los usuarios que son miembros.

![Eliminación de limitaciones de usuarios invitados](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Auditoría y generación de informes de usuarios de colaboración B2B](active-directory-b2b-auditing-and-reporting.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)
