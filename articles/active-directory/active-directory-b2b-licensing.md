---
title: "Guía de concesión de licencias de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "La colaboración B2B de Azure Active Directory no necesita licencias de Azure AD de pago, pero también puede obtener características de pago para los usuarios invitados de B2B"
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
ms.date: 06/29/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 972b37e0009b3096691784d785901b0a5585eb4c
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Guía de concesión de licencias de colaboración B2B de Azure Active Directory

Use la colaboración B2B para invitar a usuarios invitados a su inquilino de Azure AD para proporcionarles acceso a los servicios de Azure AD y a los recursos que estén disponibles.  

No se aplica ningún cargo por invitar a los usuarios de B2B ni por asignarlos a una aplicación en Azure AD. Hasta 10 aplicaciones por usuario invitado y 3 informes básicos también son gratuitos para los usuarios de colaboración B2B. Si su usuario invitado tiene cualquier licencia relevante asignada al inquilino de Azure AD del partner, también se obtendrá la licencia en el suyo.

Si quiere proporcionar acceso a las características de Azure AD de pago, a esos usuarios invitados de B2B se les deben proporcionar las licencias de Azure AD adecuadas. Un inquilino que invita con una licencia de pago de Azure AD puede asignar derechos de usuario de colaboración de B2B a cinco usuarios invitados adicionales al inquilino. 

## <a name="licensing-examples"></a>Ejemplos de concesión de licencias
- Un cliente quiere invitar a 100 usuarios de colaboración B2B a su inquilino de Azure AD. El cliente asigna aprovisionamiento y administración de acceso a todos los usuarios, pero 50 usuarios también necesitan acceso condicional y MFA. Este cliente debe adquirir 10 licencias de Azure AD Basic y 10 licencias de Azure AD Premium P1 para dar cobertura a estos usuarios de B2B correctamente. Si el cliente planea usar las características de Identity Protection con usuarios de B2B, deben tener licencias de Azure AD Premium P2 para dar cobertura a los usuarios invitados en la misma relación 5:1.
- Un cliente tiene 10 empleados y todos ellos tienen licencias actualmente en el nivel Premium P1 de Azure AD. Ahora quieren invitar a 60 usuarios de B2B y todos necesitan Multi-Factor Authentication (MFA). En la regla de concesión de licencias basada en la relación 5:1, el cliente debe disponer al menos de 12 licencias en el nivel Premium P1 de Azure AD para dar cobertura a los 60 usuarios de colaboración B2B. Como ya tienen 10 licencias Premium P1 para sus 10 empleados, tienen derechos para invitar a 50 usuarios de B2B con características de Premium P1 como MFA. Por tanto, en este ejemplo, deben adquirir 2 licencias adicionales de Premium P1 para dar cobertura a los 10 usuarios de colaboración de B2B restantes.

> [!NOTE]
> Todavía no es necesario asignar licencias directamente a los usuarios de B2B para habilitar estos derechos de usuario de colaboración de B2B.

El cliente que tiene el inquilino que invita debe ser el único en determinar cuántos usuarios de colaboración de B2B necesitan pagar las funciones de Azure AD. Dependiendo de qué características de pago de Azure AD quiera para sus usuarios invitados, debe tener suficientes licencias de pago de Azure AD para dar cobertura a los usuarios de colaboración de B2B en la relación 5:1. 

## <a name="additional-licensing-details"></a>Detalles de licencias adicionales
- No es necesario realmente asignar licencias a cuentas de usuario de B2B. En función de la relación 5:1, las licencias se calculan y notifican automáticamente.
- Todos los usuarios invitados obtienen los derechos que ofrece la edición gratuita de Azure AD si no existe ninguna licencia de pago de Azure AD en el inquilino.
- Si un usuario de colaboración de B2B ya tiene una licencia de pago de Azure AD de su organización, no consume una de las licencias de colaboración de B2B del inquilino que invita.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Discusión avanzada: ¿Cuáles son las consideraciones relativas a las licencias cuando se agregan usuarios de una organización conglomerada como "miembros" mediante las API?
Un usuario invitado de B2B es aquel que se ha invitado desde una organización del asociado para que trabaje con la organización host. Por lo general, cualquier otro caso no se considerará apto para B2B aunque use características de B2B. Echemos un vistazo a estos dos casos en particular:

1. Si un host invita a un empleado que usa una dirección de consumidor
  1. Este escenario no es compatible con nuestras directivas de licencias y no se recomienda.

2. Si una organización host agrega un usuario de otra organización conglomerado
  1. En este caso, se invita al usuario mediante las API de B2B, pero este caso no es tradicionalmente B2B. Idealmente, debemos hacer que estas organizaciones inviten a otros usuarios de organizaciones como miembros (nuestra API lo permite). En este caso, deben asignarse las licencias a estos miembros para que accedan a los recursos de la organización invitadora.

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
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory (P+F)](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

