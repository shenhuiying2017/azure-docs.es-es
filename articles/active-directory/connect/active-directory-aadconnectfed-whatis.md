---
title: "Azure AD Connect y federación | Microsoft Docs"
description: "Esta página es la ubicación central para toda la documentación relacionada con las operaciones de AD FS que usan Azure AD Connect."
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: anandy
ms.openlocfilehash: 6822320c92d106d28607289a90f2f08a51e04070
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect y la federación
Connect de Azure Active Directory (Azure AD) le permite configurar la federación con Servicios de federación de Active Directory (AD FS) locales y Azure AD. Con el inicio de sesión federado, puede habilitar a los usuarios para que inicien sesión en los servicios basados en Azure AD con sus contraseñas locales y, mientras se encuentren en la red corporativa, sin necesidad de volver a escribir sus contraseñas. Mediante la opción de federación con AD FS, puede implementar una nueva instalación de AD FS o puede especificar una instalación existente en una granja de servidores de Windows Server 2012 R2.

Este tema constituye la principal referencia de las funcionalidades relacionadas con la federación correspondientes a Azure AD Connect. Ofrece vínculos a todos los demás temas relacionados. Para obtener vínculos para Azure AD Connect, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: temas de federación
| Tema. | ¿Qué aspectos cubre y cuándo debe leerlos? |
|:--- |:--- |
| **Opciones para el inicio de sesión de los usuarios en Azure AD Connect** | |
| [Descripción de las opciones de inicio de sesión de los usuarios](active-directory-aadconnect-user-signin.md) |Obtenga información sobre diferentes opciones de inicio de sesión de usuario y cómo afectan a la experiencia de inicio de sesión del usuario en Azure. |
| **Instalación de AD FS mediante Azure AD Connect** | |
| [Requisitos previos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Consulte los requisitos previos para una correcta instalación de AD FS a través de Azure AD Connect. |
| [Configuración de una granja de servidores de AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Instale una nueva granja de servidores de AD FS mediante Azure AD Connect. |
| [Federación con Azure AD mediante un identificador de inicio de sesión alternativo](active-directory-aadconnect-federation-management.md#alternateid) | Configuración de la federación con un identificador de inicio de sesión alternativo  |
| **Modificación de la configuración de AD FS** | |
| [Reparación de la confianza](active-directory-aadconnect-federation-management.md#repairthetrust) |Repare la confianza actual entre AD FS local y Office 365 o Azure. |
| [Adición de un nuevo servidor de AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Expansión de la granja de servidores de AD FS con un servidor de AD FS adicional después de la instalación inicial. |
| [Incorporación de un nuevo servidor WAP de AD FS](active-directory-aadconnect-federation-management.md#addwapserver) |Expanda una granja de servidores de AD FS con un servidor proxy de aplicación web (WAP) adicional después de la instalación inicial. |
| [Incorporación de un nuevo dominio federado](active-directory-aadconnect-federation-management.md#addfeddomain) |Agregue otro dominio para que se federe con Azure AD. |
| [Actualizar el certificado SSL](active-directory-aadconnectfed-ssl-update.md)| Actualice el certificado SSL para una granja de servidores de AD FS. |
| **Otros parámetros de configuración de la federación** | |
| [Federación de varias instancias de Azure AD con una instancia única de AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Federación de varias instancias de Azure AD con una granja única de servidores de AD FS| 
| [Incorporación de una ilustración o un logotipo personalizado de la compañía](active-directory-aadconnect-federation-management.md#customlogo) |Modifique la experiencia de inicio de sesión especificando el logotipo personalizado que se muestra en la página de inicio de sesión de AD FS. |
| [Adición de la descripción de inicio de sesión](active-directory-aadconnect-federation-management.md#addsignindescription) |Cambie la descripción de inicio de sesión en la página de inicio de sesión de AD FS. |
| [Modificación de las reglas de notificaciones de AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Modifique o agregue reglas de notificaciones de AD FS que se corresponden con la configuración de sincronización de Azure AD Connect. |


## <a name="additional-resources"></a>Recursos adicionales
* [Federación de dos instancias de Azure AD con una instancia única de AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md)
* [Implementación de AD FS en Azure de alta disponibilidad entre regiones geográficas con Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
