---
title: Comparación de la colaboración B2B y B2C de Azure Active Directory | Microsoft Docs
description: ¿Cuál es la diferencia entre la colaboración B2B de Azure Active Directory y Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 19e8e9d22938fa8a18299d67aa77824aaae3f6da
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267195"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparación de la colaboración B2B y B2C de Azure Active Directory

La colaboración B2B de Azure Active Directory (Azure AD) y Azure AD B2C permiten trabajar con usuarios externos en Azure AD. Pero, ¿cómo se comparan?


Funcionalidades de la colaboración B2B |     Oferta independiente de Azure AD B2C
-------- | --------
Destinado a: organizaciones que desean poder autenticar a los usuarios desde una organización asociada, independientemente del proveedor de identidades. | Destinado a: invitar a los clientes de su móvil y aplicaciones web, ya sean personas, instituciones u organizaciones, en Azure AD.
Identidades compatibles: empleados con cuentas profesionales o educativas, asociados con cuentas profesionales o educativas o cualquier dirección de correo electrónico. Pronto se admitirá la federación directa.  | Identidades compatibles: usuarios consumidores con cuentas de aplicación local (cualquier dirección de correo electrónico o nombre de usuario) o cualquier identidad social compatible con federación directa.
En qué directorio se encuentran los usuarios asociados: los usuarios asociados de organizaciones externas se administran en el mismo directorio que los empleados, pero con una anotación especial. Pueden administrarse del mismo modo que los empleados, pueden agregarse a los mismos grupos, etc.  | En qué directorio se encuentran las entidades de usuarios consumidores: en el directorio de aplicaciones. Se administra de manera independiente del directorio de asociados y de empleados de la organización (si existe).
Se admite el inicio de sesión único (SSO) en todas las aplicaciones conectadas a Azure AD. Por ejemplo, puede proporcionar acceso a Office 365, o bien a aplicaciones locales y a otras aplicaciones SaaS como Salesforce o Workday.  |  Se admite el inicio de sesión único para aplicaciones propiedad de los clientes dentro de los inquilinos de Azure AD B2C. No se admite el inicio de sesión único en Office 365 ni en otras aplicaciones SaaS de Microsoft y de terceros.
Ciclo de vida de socio comercial: administrado por la organización anfitriona o invitadora.  | Ciclo de vida de cliente: mediante autoservicio o administrado por la aplicación.
Directiva de seguridad y cumplimiento: administradas por la organización anfitriona o invitadora.  | Directiva de seguridad y cumplimiento: administradas por la aplicación.
Personalización de marca: se utiliza la marca de la organización anfitriona o invitadora.  |    Personalización de marca: administrada por la aplicación. Normalmente suele tratarse de productos con marca, y la organización pasa a un segundo plano.
Más información: [entrada de blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentación](what-is-b2b.md)  | Más información: [página de producto](https://azure.microsoft.com/services/active-directory-b2c/), [documentación](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la colaboración de Azure AD B2B?](what-is-b2b.md)
- [Propiedades de usuario de la colaboración B2B](user-properties.md)

