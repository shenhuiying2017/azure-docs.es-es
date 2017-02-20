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
ms.date: 02/03/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: ae154e09e3b9ef2182e74b1dc5a0d8da3922b0df


---

# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propiedades de un usuario de colaboración B2B de Azure Active Directory

## <a name="defining-a-b2b-collaboration-user"></a>Definición de un usuario de colaboración B2B
Un usuario de colaboración B2B es un usuario con UserType = Invitado, o un usuario invitado. Este usuario normalmente representa un usuario de una organización asociada y tiene, de forma predeterminada, privilegios limitados en el directorio invitador. Según las necesidades de la organización invitadora, un usuario de colaboración B2B puede tener uno de los siguientes estados de cuenta:
1. Alojado en una instancia externa de Azure Active Directory (Azure AD) y representado como un usuario invitado en la organización anfitriona. En este caso, el usuario B2B inicia sesión con una cuenta de Azure AD que pertenece a su espacio principal. Si la organización externa a la que pertenece el usuario no usa Azure AD en el momento de la invitación, se crea el usuario invitado en Azure AD "justo a tiempo" cuando el usuario canjea su invitación, después de comprobar su dirección de correo electrónico. También se denomina "espacio justo a tiempo" (JIT) o, a veces, "espacio viral".
2. Alojado en una cuenta Microsoft y representado como un usuario invitado de la organización anfitriona. En este caso, el usuario invitado inicia sesión con una cuenta Microsoft. En la colaboración B2B de la actualización de la versión preliminar publica de Azure AD, la identidad social distinta de SMA (google.com o similar) del usuario invitado se crea como una cuenta Microsoft justo a tiempo durante el canje de la oferta.
3. Alojado en la instancia de Active Directory local de la organización anfitriona y sincronizado con la instancia de Azure AD de la organización anfitriona. Durante esta versión, se debe cambiar manualmente el valor de la propiedad UserType de dichos usuarios en la nube a Invitado mediante PowerShell. En futuras versiones este proceso se realiza automáticamente como parte de Azure AD Connect.
4. Alojado en la instancia de Azure AD de la organización anfitriona con UserType = Invitado y con credenciales que administra dicha organización.

  ![mostrar las iniciales del invitador](media/active-directory-b2b-user-properties/redemption-diagram.png)


Ahora, veremos a qué se parece un usuario de colaboración B2B con estado 1 en Azure AD.

### <a name="before-invitation-redemption"></a>Canje antes de la invitación

![Canje antes de la invitación](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Canje después de la invitación

![Canje después de la invitación](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-b2b-collaboration-user"></a>Propiedades clave del usuario de colaboración B2B.

### <a name="usertype"></a>UserType
Este atributo indica la relación del usuario con el espacio anfitrión. Puede tener dos valores:
- Miembro: un empleado de la organización anfitriona, un usuario en la plantilla de esa organización. Por ejemplo, se trata de un usuario que espera poder acceder a sitios solo para uso interno. Este usuario no se consideraría un colaborador externo.
- Invitado: esto indica que un usuario que no se consideraría interno de la empresa. Podría ser un colaborador externo, un asociado, un cliente o un usuario similar que no espera recibir una nota interna del CEA, por ejemplo, o ventajas empresariales.

  > ![NOTE] La propiedad UserType no tiene ninguna relación con la forma en que el usuario inicia sesión, o al rol de directorio al que pertenece el usuario, etc. Este atributo simplemente indica la relación del usuario con la organización anfitriona y permite a la organización exigir las directivas que dependen de este atributo.

### <a name="source"></a>Origen
Cómo el usuario inicia sesión.

- Usuario invitado: este usuario ha recibido la invitación, pero aún no ha canjeado su invitación.

- Instancia de Active Directory externa: este usuario está alojado en una organización externa y se autentica con una cuenta de Azure AD que pertenece a la otra organización. Esto se corresponde con el estado 1 anterior.

- Cuenta Microsoft: el usuario está alojado en MSA y se autentica con una cuenta Microsoft. Esto se corresponde con el estado 2 anterior.

- Instancia de Windows Server AD: este usuario se ha sincronizado desde la instancia de Active Directory local que pertenece a esta organización. Esto se corresponde con el estado 3 anterior.

- Azure Active Directory: este usuario se autentica con una cuenta de Azure AD que pertenece a esta organización. Esto se corresponde con el estado 4 anterior.

  > ![NOTE] Source y UserType son atributos independientes. Un valor de origen no implica un atributo UserType determinado.

## <a name="can-b2b-users-be-added-as-members-instead-of-guests"></a>¿Se pueden agregar usuarios B2B como miembros en lugar de invitados?
Normalmente, un usuario invitado y uno B2B son sinónimos. Por lo tanto, el usuario de colaboración B2B se agrega como un usuario con UserType = Invitado de forma predeterminada. Sin embargo, en algunos casos, la organización del asociado es realmente más que un miembro de una organización paraguas más grande a la que también pertenece la organización anfitriona. En este caso, la organización anfitriona quiere tratar a los usuarios de la organización del asociado como miembros y no como invitados. Por tanto, use las API del administrador de la invitación de B2B para agregar un usuario de la organización asociada a la anfitriona como miembro, así como para invitarlo.

## <a name="filtering-for-guest-users-in-the-directory"></a>Filtrado de usuarios invitados en el directorio

![Filtrado de usuarios invitados](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="converting-usertype"></a>Conversión de UserType
Actualmente, en PowerShell, los usuarios pueden convertir el valor de UserType de Miembro a Invitado, y viceversa. Sin embargo, la propiedad UserType debería representar la relación del usuario con la organización. Por lo tanto, esta propiedad solo cambia si ha modificado la relación del usuario con la organización. Si cambia la relación del usuario, deben responderse otras preguntas como: ¿Debe cambiar el UPN? ¿Debe el usuario seguir teniendo acceso a los recursos a los que ya tenían acceso antes? ¿Debe asignarse un buzón de correo? Por lo tanto, no se recomienda cambiar el valor de UserType en PowerShell como una actividad atómica. Además, en el futuro, haremos que esta propiedad sea inmutable a través de PowerShell, por lo que no se recomienda tomar una dependencia de este valor.

## <a name="removing-guest-user-limitations"></a>Eliminación de las limitaciones de usuarios invitado
Puede haber casos en los que desee ofrecer a los usuarios invitados privilegios más altos. Para ello, puede agregar un usuario invitado a cualquier rol e, incluso, eliminar las restricciones de usuario invitado predeterminadas en el directorio para concederles los mismos privilegios que los miembros. Siga leyendo para obtener más información.

Se puede desactivar las limitaciones de usuario invitado predeterminadas para que los usuarios invitados del directorio de la empresa tengan los mismos permisos de directorio que un usuario normal (miembro).

![Eliminación de las limitaciones de usuario invitado](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


