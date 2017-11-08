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
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: db849cd1e9da634064f79fbc041098542580ad02
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
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

> [!WARNING]
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

* [¿Cómo se realiza un lanzamiento correcto de SSPR?](active-directory-passwords-best-practices.md)
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md).
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Qué métodos de autenticación están disponibles para los usuarios?](active-directory-passwords-how-it-works.md#authentication-methods)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)
