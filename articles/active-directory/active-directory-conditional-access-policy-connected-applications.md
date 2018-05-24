---
title: Configuración de directivas de acceso condicional basadas en dispositivos de Azure Active Directory | Microsoft Docs
description: Obtenga información sobre cómo configurar directivas de acceso condicional basadas en dispositivos de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 07957d5ec843c414813d69b7084915bcd70a5a61
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configuración de directivas de acceso condicional basadas en dispositivos de Azure Active Directory

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede controlar el modo en que los usuarios autorizados pueden acceder a los recursos. Por ejemplo, puede limitar el acceso a determinados recursos a dispositivos administrados. Una directiva de acceso condicional que requiere un dispositivo administrado se conoce también como directiva de acceso condicional basada en dispositivos.

En este tema se explica cómo puede configurar directivas de acceso condicional basadas en dispositivos para aplicaciones de conectadas a Azure AD. 


## <a name="before-you-begin"></a>Antes de empezar

El acceso condicional basado en dispositivos une el **acceso condicional de Azure AD** y la **administración de dispositivos de Azure AD**. Si no está familiarizado con ninguna de estas áreas, debería leer los siguientes temas en primer lugar:

- **[Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)**: en este tema se proporcionan tanto información general conceptual de acceso condicional como la terminología relacionada.

- **[Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md)**: en este tema se proporciona información general de las diversas opciones que tiene para conectar dispositivos con Azure AD. 



## <a name="managed-devices"></a>Dispositivos administrados  

En un mundo Mobile First, Cloud First, Azure Active Directory permite el inicio de sesión único en dispositivos, aplicaciones y servicios desde cualquier parte. Para determinados recursos de su entorno, es posible que conceder acceso a los usuarios adecuados no sea una acción lo suficientemente buena. Además de los usuarios adecuados, también podría requerir que los intentos de acceso solo se puedan realizar mediante un dispositivo administrado.

Un dispositivo administrado es un dispositivo que cumple los estándares de seguridad y cumplimiento. En otras palabras, los dispositivos administrados son dispositivos que están bajo *algún tipo* de control de la organización. En Azure AD, el requisito previo para un dispositivo administrado es que se haya registrado en Azure AD. Al registrar un dispositivo, se crea una identidad para el dispositivo en forma de un objeto de dispositivo. Azure usa este objeto para realizar el seguimiento de información de estado sobre el dispositivo. Como administrador de Azure AD, ya puede utilizar este objeto para alternar (habilitar/deshabilitar) el estado de un dispositivo.
  
![Condiciones basadas en el dispositivo](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Para registrar un dispositivo en Azure AD, tiene tres opciones:

- **[Dispositivos registrados en Azure AD](device-management-introduction.md#azure-ad-registered-devices)**: registrar un dispositivo personal en Azure AD

- **[Dispositivos unidos a Azure AD](device-management-introduction.md#azure-ad-joined-devices)**: registrar en Azure AD un dispositivo Windows 10 organizativo que no está unido a una implementación local de AD. 

- **[Dispositivos unidos a Azure AD híbridos](device-management-introduction.md#hybrid-azure-ad-joined-devices)**: registrar en Azure AD un dispositivo Windows 10 que está unido a una implementación local de AD.

Para convertirse en dispositivo administrado, un dispositivo registrado puede ser un dispositivo unido a Azure AD híbrido o un dispositivo que se ha marcado como compatible.  

![Condiciones basadas en el dispositivo](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Requerir dispositivos unidos a Azure AD híbridos

En la directiva de acceso condicional, puede seleccionar **Requerir dispositivo unido a Azure AD híbrido** para indicar que solo se puede acceder a las aplicaciones de nube seleccionadas mediante un dispositivo administrado. 

![Condiciones basadas en el dispositivo](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Esta configuración solo se aplica a dispositivos Windows 10 que están unidos a Azure AD local. Estos dispositivos solo pueden registrarse en Azure AD con una unión a Azure AD híbrida, que es un [proceso automatizado](device-management-hybrid-azuread-joined-devices-setup.md) para registrar un dispositivo Windows 10. 

![Condiciones basadas en el dispositivo](./media/active-directory-conditional-access-policy-connected-applications/45.png)

¿Qué hace que un dispositivo unido a Azure AD híbrido sea un dispositivo administrado?  Para los dispositivos que están unidos a un AD local, se supone que el control de estos dispositivos se ejerce mediante soluciones de administración, como **System Center Configuration Manager (SCCM)** o **la directiva de grupo (GP)** para administrarlos. Dado que no hay ningún método para que Azure AD determine si alguno de estos métodos se ha aplicado a un dispositivo, requerir un dispositivo unido a Azure AD híbrido es un mecanismo relativamente débil para requerir un dispositivo administrado. Depende de usted como administrador juzgar si los métodos que se aplican a sus dispositivos locales unidos a un dominio son lo suficientemente estrictos para constituir un dispositivo administrado si tal dispositivo también es un dispositivo unido a Azure AD híbrido.


## <a name="require-device-to-be-marked-as-compliant"></a>Requerir que el dispositivo esté marcado como compatible

La opción de *requerir que un dispositivo esté marcado como compatibles* es la forma más eficaz para solicitar un dispositivo administrado.

![Condiciones basadas en el dispositivo](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Esta opción exige registrar un dispositivo en Azure AD y, también, que se marque como compatible por:
         
- Intune 
- Un sistema administrado de dispositivos móviles de terceros que administra los dispositivos de Windows 10 a través de la integración de Azure AD 
 
![Condiciones basadas en el dispositivo](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Para un dispositivo que está marcado como compatible, puede suponer que: 

- Los dispositivos móviles que los trabajadores usan para tener acceso a los datos de la empresa son administrados.
- Las aplicaciones móviles que los trabajadores usan son administradas.
- La información empresarial está protegida al ayudar a controlar la manera en que los trabajadores acceden a ella y la comparten.
- El dispositivo y sus aplicaciones son compatibles con los requisitos de seguridad de la empresa.




## <a name="next-steps"></a>Pasos siguientes

Antes de configurar una directiva de acceso condicional basada en dispositivos en su entorno, debería echar un vistazo a [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md).

