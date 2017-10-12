---
title: 'Licensing: Azure AD SSPR (Licencias: SSPR de Azure AD) | Microsoft Docs'
description: "Requisitos de concesión de licencias del autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 5445a479fd6893048eb8ff356fa829a3dcd5f7d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licensing requirements for Azure AD self-service password reset (Requisitos de concesión de licencias del autoservicio de restablecimiento de contraseña de Azure AD)

Para que el restablecimiento de contraseña de Azure AD funcione, **debe tener al menos una licencia asignada en su organización**. No aplicamos la concesión de licencias por usuario en la experiencia de restablecimiento de contraseña. A fin de que se siga cumpliendo el contrato de licencia de Microsoft, debe asignar licencias a los usuarios que usen características premium.

* **Solo usuarios en la nube**: Office 365 (O365), cualquier SKU de pago o Azure AD Basic
* **Usuarios en la nube** o **locales**: Azure AD Premium P1 o P2, Mobility + Security (EMS) o Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>Licencias necesarias para la escritura diferida de contraseñas

Para poder usar la escritura diferida de contraseñas, debe tener una de las siguientes licencias asignadas en el inquilino.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!NOTE]
> Los planes de licencias de Office 365 independientes **no admiten la escritura diferida de contraseñas** y requieren uno de los planes anteriores para que esta funcionalidad funcione.

En las páginas siguientes encontrará información adicional sobre licencias, incluidos los costos

* [Sitio sobre precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Características y funcionalidades de Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Habilitar licencias basadas en grupos o usuarios

Azure AD admite ahora licencias basadas en grupos que permiten a los administradores asignar licencias en bloque a un grupo de usuarios en lugar de asignarlas una a una. [Asignar, comprobar y resolver los problemas con licencias](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Algunos servicios de Microsoft no están disponibles en todas las ubicaciones. Antes de poder asignar una licencia a un usuario, el administrador tiene que especificar la propiedad “Ubicación de uso” en el usuario. La asignación de licencias puede hacerse en la sección Usuario > Perfil > Configuración de Azure Portal. **Cuando se utiliza la asignación de licencias de grupo, los usuarios sin ubicación de uso especificada heredan la ubicación del directorio.**

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planee e implemente SSPR en sus usuarios mediante las instrucciones que se encuentran aquí.
* [**Personalización**](active-directory-passwords-customize.md): personalización de la experiencia de SSPR para la empresa
* [**Informes**](active-directory-passwords-reporting.md): detectan si los usuarios acceden a la funcionalidad de SSPR, cuándo lo hacen y dónde.
* [**Profundización técnica**](active-directory-passwords-how-it-works.md): conozca lo que hay detrás para comprender cómo funciona.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR
