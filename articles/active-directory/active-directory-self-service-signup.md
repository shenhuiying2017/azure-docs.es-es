---
title: "Registro de autoservicio o de evaluación en Azure Active Directory | Microsoft Docs"
description: Uso del registro de autoservicio en un inquilino de Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 9f2b541d5028596f9beabc7fd82001b4c9dacad4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>¿Qué es el registro de autoservicio de Azure Active Directory?
En este artículo se explica el registro de autoservicio y como admitirlo en Azure Active Directory (Azure AD). Si desea asumir un nombre de dominio de un inquilino de Azure AD no administrado, consulte [Take over an unmanaged directory as administrator](domains-admin-takeover.md) (Toma de control de un directorio no administrado como administrador).

## <a name="why-use-self-service-signup"></a>Razones para usar la suscripción de autoservicio
* Permita que los usuarios tengan acceso a los servicios que desean más rápido.
* Cree ofertas basadas en correo electrónico para un servicio.
* Cree flujos de suscripción basados en correo electrónico que permitan a los usuarios crear identidades rápidamente con sus alias de correo electrónico del trabajo fáciles de recordar.
* Un directorio de Azure AD creado por autoservicio puede convertirse en un directorio administrado que se puede utilizar para otros servicios.

## <a name="terms-and-definitions"></a>Términos y definiciones
* **Registro de autoservicio**: método por el que un usuario se registra en un servicio en la nube y por el que se le crea automáticamente una identidad en Azure AD basada en su dominio de correo electrónico.
* **Directorio de Azure AD no administrado**: directorio donde se crea la identidad. Un directorio no administrado es un directorio que no tiene administrador global.
* **Usuario comprobado por correo electrónico**: tipo de cuenta de usuario en Azure AD. Un usuario que tiene una identidad que se crean automáticamente después de suscribirse a una oferta de autoservicio se conoce como usuario comprobado por correo electrónico. Un usuario comprobado por correo electrónico es un miembro regular de un directorio etiquetado con creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>¿Cómo controlo la configuración de autoservicio?
Actualmente, los administradores tienen dos controles de autoservicio . Pueden controlar:

* Si los usuarios pueden unirse o no al directorio mediante el correo electrónico.
* Si los usuarios pueden concederse o no licencias para aplicaciones y servicios.

### <a name="how-can-i-control-these-capabilities"></a>¿Cómo puedo controlar estas capacidades?
Un administrador puede configurar estas capacidades con estos parámetros Set-MsolCompanySettings de cmdlet de Azure AD:

* **AllowEmailVerifiedUsers** controla si un usuario puede crear o unirse a un directorio no administrado. Si establece este parámetro en $false, ningún usuario comprobado por correo electrónico se puede unir al directorio.
* **AllowAdHocSubscriptions** controla la capacidad de los usuarios de realizar suscripciones de autoservicio. Si establece el parámetro en $false, ningún usuario puede realizar suscripciones de autoservicio. 
  
  > [!NOTE]
  > Los registros de evaluación de Flow y PowerApps no están controlados por el valor **AllowAdHocSubscriptions**. Para más información, consulte los siguientes artículos.
  > * [¿Cómo puedo impedir que mis usuarios existentes comiencen a utilizar Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Preguntas y respuestas sobre Flow en su organización](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>¿Cómo funciona los controles conjuntamente?
Estos dos parámetros se pueden usar juntos para definir un control más preciso de la suscripción de autoservicio. Por ejemplo, el comando siguiente permitirá a los usuarios realizar suscripciones de autoservicio pero solo si estos usuarios ya tienen una cuenta en Azure AD (en otras palabras, los usuarios que necesitan crear primero una cuenta comprobada por correo electrónico no pueden realizar suscripciones de autoservicio):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
En el siguiente diagrama se explican las distintas combinaciones de estos parámetros y las condiciones resultantes para el directorio y el registro de autoservicio.

![][1]

Para obtener más información y ejemplos de cómo usar estos parámetros, consulte [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>pasos siguientes
* [Incorporación de su nombre de dominio personalizado a Azure Active Directory](add-custom-domain.md)
* [Instalación y configuración de Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referencia de cmdlets de Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
