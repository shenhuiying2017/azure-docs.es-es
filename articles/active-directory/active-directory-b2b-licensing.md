---
title: "Guía de concesión de licencias de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "La colaboración B2B de Azure Active Directory requiere licencias de pago de Azure AD en función de las características que desee para los usuarios de colaboración B2B."
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
ms.date: 05/23/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 4e620f3d76caa25ac0e5afb134f37ffe263935f0
ms.contentlocale: es-es
ms.lasthandoff: 04/13/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Guía de concesión de licencias de colaboración B2B de Azure Active Directory

La colaboración B2B de Azure Active Directory (Azure AD) extiende un conjunto seleccionado de características existentes de Azure AD a usuarios invitados al inquilino de Azure AD. Por tanto, los usuarios invitados de colaboración B2B de Azure AD obtendrán una licencia de Azure AD, que se alineará con los niveles de licencia existentes Gratis, Básico y Premium P1/P2, como se especifica aquí: https://azure.microsoft.com/pricing/details/active-directory/.

No se aplica ningún cargo por invitar a los usuarios de B2B ni por asignarlos a una aplicación en Azure AD. Además, hasta 10 aplicaciones por usuario invitado y 3 informes básicos también son gratis para usuarios de B2B, ya que forman parte del nivel “Gratis” de Azure AD.
Para todas las características de pago de Azure AD, que se extienden a los usuarios de B2B mediante la funcionalidad de colaboración B2B, es necesario conceder licencias con las licencias de pago de Azure AD (Básico, Premium P1 o Premium P2, en función de las características que se vayan a usar). El espacio empresarial invitador obtendrá 5 derechos de usuario de B2B con cada licencia de pago de Azure AD. Es decir, cada licencia de pago de Azure AD que proporciona los derechos para las características de pago de Azure AD a un usuario empleado de un inquilino, también proporcionará ahora los derechos para esas mismas características de pago de Azure AD a 5 usuarios B2B adicionales invitados al inquilino.

## <a name="licensing-examples"></a>Ejemplos de concesión de licencias
- Un cliente desea invitar a 100 usuarios de B2B a su inquilino de Azure AD y usará el aprovisionamiento y la administración de acceso basada en grupos para todos los usuarios, pero 50 de ellos también necesitarán MFA y el acceso condicional. El cliente debe adquirir 10 licencias en el nivel Básico de Azure AD y 10 licencias en el nivel Premium P1 de Azure AD para dar cobertura correctamente a todos los usuarios de B2B. De forma similar, si un espacio empresarial invitador pretende usar las características de Identity Protection con usuarios de B2B, debe disponer de licencias suficientes en el nivel Premium P2 de Azure AD para dar cobertura a todos estos usuarios de B2B con la relación 5:1.
- Un cliente tiene 10 empleados y todos ellos tienen licencias actualmente en el nivel Premium P1 de Azure AD. Ahora quieren invitar a 60 usuarios de B2B, y todos ellos necesitarán Multi-Factor Authentication (MFA). Según la regla de concesión de licencias basada en la relación 5:1, el cliente debe disponer al menos de 12 licencias en el nivel Premium P1 de Azure AD para dar cobertura a los 60 usuarios de colaboración B2B. Habida cuenta de que ya tienen 10 licencias en el nivel Premium P1 para sus 10 empleados, ya tienen derechos para invitar a 50 usuarios de B2B con características de Premium P1 como MFA. Por tanto, en este ejemplo, necesitan adquirir 2 licencias adicionales en el nivel Premium P1 para dar cobertura a los 10 usuarios de colaboración B2B restantes.

> [!NOTE]
> No hay necesidad de asignar licencias a los usuarios de B2B para habilitar estos derechos de usuario de colaboración B2B.

El cliente que posee el espacio empresarial invitador debe ser el que determine cuántos usuarios de colaboración B2B necesitan funcionalidades de pago de Azure AD y, en función de si se trata de características de los niveles Básico, Premium P1 o Premium P2, los clientes deben tener el número suficiente de licencias apropiadas de pago de Azure AD para dar cobertura a los usuarios de colaboración B2B con la relación 5:1. Si una empresa necesita derechos de usuario de colaboración B2B adicionales, debe adquirir las licencias de pago de Azure AD necesarias.

## <a name="additional-licensing-details"></a>Detalles de licencias adicionales
- La colaboración B2B puede proporcionar funcionalidades diferentes a los usuarios de colaboración B2B distintos, según el modelo existente de ediciones de Azure AD.
- Cada licencia de pago de Azure AD incluirá derechos para 5 usuarios de colaboración B2B (modelo 5:1).
- No es necesario realmente asignar licencias a cuentas de usuario de B2B. Los cálculos y los informes se realizarán de forma automática.
- Todos los usuarios invitados obtienen los derechos que ofrece la edición Gratis de Azure AD si no existe ninguna licencia de pago de Azure AD en el inquilino.
- Si un usuario de colaboración B2B tiene una licencia de pago de Azure AD como un empleado de su organización, no consumirá una de las licencias de colaboración B2B del espacio empresarial invitador.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Discusión avanzada: ¿Cuáles son las consideraciones relativas a las licencias cuando se agregan usuarios de una organización conglomerada como "miembros" mediante las API?
Un usuario invitado de B2B es aquel que se ha invitado desde una organización del asociado para que trabaje con la organización host. Por lo general, cualquier otro caso no se considerará apto para B2B aunque use características de B2B. Echemos un vistazo a estos dos casos en particular:

1. Si un host invita a un empleado que usa una dirección de consumidor
  1. Esto no es compatible con nuestras directivas de licencias y no se recomienda.

2. Si una organización host agrega un usuario de otra organización conglomerado
  1. Es el caso del usuario que recibe una invitación mediante las API de B2B, pero esto no es tradicionalmente B2B. Idealmente, debemos hacer que estas organizaciones inviten a otros usuarios de organizaciones como miembros (nuestra API lo permite). En este caso, deben asignarse las licencias a estos miembros para que accedan a los recursos de la organización invitadora.

  2. Algunas organizaciones podrían agregar usuarios de otra organización como "Invitados" como una directiva. Estos son dos casos:
      * La organización conglomerada ya usa Azure AD y autoriza el uso de licencias a los usuarios invitados de la otra organización: en este caso, no se espera que los usuarios invitados sigan la fórmula de 1:5 dispuesta anteriormente en este documento. 

      * La organización conglomerada no usa Azure AD o no tiene licencias adecuadas: en este caso, siga la fórmula de 1:5 dispuesta anteriormente en este documento.


## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

