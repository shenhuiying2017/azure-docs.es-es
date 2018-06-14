---
title: Experiencia de canje de invitación de colaboración B2B de Azure Active Directory | Microsoft Docs
description: Describe la experiencia de canje de invitación de colaboración B2B de Azure AD para los usuarios finales, incluido el acuerdo con los términos de privacidad.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2e354bc4ae06e86afd5d14e87ef796fce942521b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074788"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Experiencia de invitación de colaboración B2B de Azure Active Directory

Para colaborar con los usuarios de organizaciones de partners a través de la colaboración B2B de Azure Active Directory (Azure AD), puede invitar a usuarios a acceder a aplicaciones compartidas. Después de agregar a un usuario invitado al directorio a través de la interfaz de usuario, o de que el usuario reciba una invitación a través de PowerShell, la primera vez los usuarios invitados deben pasar por un proceso de consentimiento para aceptar los [términos de privacidad](#privacy-policy-agreement). Este proceso se produce de cualquiera de las maneras siguientes:

- El invitador envía un vínculo directo a una aplicación compartida. El invitado hace clic en el vínculo para iniciar sesión, acepta los términos de privacidad y accede sin problemas al recurso compartido. (El usuario invitado aún recibe un correo electrónico de invitación con una dirección URL de canje, pero, salvo algunos casos especiales, ya no es necesario usar el correo electrónico de invitación).  
- El usuario invitado recibe un correo electrónico de invitación y hace clic en la dirección URL de canje. Como parte del primer inicio de sesión, se le pedirá que acepte los términos de privacidad.

## <a name="redemption-through-a-direct-link"></a>Canje a través de un vínculo directo

Un invitador puede invitar a un usuario invitado mediante el envío de un vínculo directo a una aplicación compartida. Para el usuario invitado, la experiencia de canje es tan sencilla como iniciar sesión en la aplicación que se compartió con él. Puede hacer clic en un vínculo a la aplicación, revisar y aceptar los términos de privacidad y, a continuación, acceder a la aplicación sin problemas. En la mayoría de los casos, los usuarios invitados ya no necesitan hacer clic en una dirección URL de canje en un correo electrónico de invitación.

Si ha invitado a usuarios a través de la interfaz de usuario, o si elige enviar el correo electrónico de invitación como parte de la experiencia de invitación de PowerShell, el usuario invitado recibirá igualmente un correo electrónico de invitación. Este correo electrónico es útil para los casos especiales siguientes:

- El usuario no tiene una cuenta de Azure AD ni una cuenta de Microsoft (MSA). En este caso, el usuario debe crear una MSA antes de hacer clic en el vínculo, o puede utilizar la dirección URL de canje en el correo electrónico de invitación. El proceso de canje, automáticamente, le pedirá al usuario crear una MSA.
- A veces, el objeto de usuario invitado puede no tener una dirección de correo electrónico debido a un conflicto con un objeto de contacto (por ejemplo, un objeto de contacto de Outlook). En este caso, el usuario debe hacer clic en la dirección URL de canje en el correo electrónico de invitación.
- El usuario puede iniciar sesión con un alias de la dirección de correo electrónico a la que se invitó. (Un alias es una dirección de correo electrónico adicional asociada con una cuenta de correo electrónico). En este caso, el usuario debe hacer clic en la dirección URL de canje en el correo electrónico de invitación.

Si estos casos especiales son importantes para su organización, se recomienda que invite a los usuarios mediante métodos que aún envíen el correo electrónico de invitación. Además, si un usuario no se encuentra en uno de estos casos especiales, aún puede hacer clic en la dirección URL en un correo electrónico de invitación para acceder.

## <a name="redemption-through-the-invitation-email"></a>Canje a través del correo electrónico de invitación

Si los usuarios son invitados a través de un método que envía un correo electrónico de invitación, también pueden canjear una invitación a través del correo electrónico de invitación. Un usuario invitado puede hacer clic en la dirección URL de canje en el correo electrónico y, a continuación, revisar y aceptar los términos de privacidad. El proceso se describe con más detalle a continuación:

1.  Para ser invitado, el usuario recibe una invitación por correo electrónico que se envía desde **Invitaciones de Microsoft**.
2.  El invitado selecciona **Introducción** en el correo electrónico.
3.  Si el invitado no tiene una cuenta de Azure AD o una MSA, se le pedirá que cree una MSA.
4.  Al invitado se le redirige a la pantalla **Review permissions** (Revisar permisos), donde puede revisar la declaración de privacidad de la organización que hace la invitación y aceptar los términos.

## <a name="privacy-policy-agreement"></a>Acuerdo de la directiva de privacidad

Después de que un usuario invitado inicie sesión por primera vez para acceder a recursos en una organización asociada, verá la pantalla **Review permissions** (Revisar permisos). Aquí puede revisar la declaración de privacidad de la organización que hace la invitación. Para poder continuar, el usuario debe aceptar el uso de su información de acuerdo con las directivas de privacidad de la organización que hace la invitación.

![Captura de pantalla que muestra la configuración de usuario en el panel de acceso](media/active-directory-b2b-redemption-experience/ConsentScreen.png) 

Para obtener información sobre cómo puede, como administrador de inquilinos, vincular con la declaración de privacidad de su organización, consulte [Procedimiento: Incorporación de información de privacidad de su organización en Azure AD](https://aka.ms/adprivacystatement).

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Incorporación de usuarios de colaboración B2B de Azure Active Directory en Azure Portal](active-directory-b2b-admin-add-users.md)
- [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B a Azure Active Directory?](active-directory-b2b-iw-add-users.md)
- [Incorporación de usuarios de colaboración B2B de Azure Active Directory con PowerShell](active-directory-b2b-api.md#powershell)
- [Salir de una organización como usuario invitado](active-directory-b2b-leave-the-organization.md)