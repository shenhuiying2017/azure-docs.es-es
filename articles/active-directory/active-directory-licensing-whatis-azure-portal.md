---
title: "¿En qué consisten las licencias basadas en grupos de Azure Active Directory? | Microsoft Docs"
description: "Descripción de las licencias basadas en grupo de Azure Active Directory, cómo funcionan y procedimientos recomendados"
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 9cfa8c42c1f3955adad5e4ce596d96c4e98e40a1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Aspectos básicos de las licencias basadas en grupos en Azure Active Directory

El uso de los servicios de pago en la nube de Microsoft, como Office 365, Enterprise Mobility + Security, Dynamics CRM y otros productos similares, requiere licencias. Estas licencias se asignan a cada usuario que necesita acceso a estos servicios. Para administrar las licencias, los administradores usan uno de los portales de administración (ya sea Office o Azure) y los cmdlets de PowerShell. Azure Active Directory (Azure AD) es la infraestructura subyacente que admite la administración de identidades para todos los servicios en la nube de Microsoft. Azure AD almacena información sobre los estados de asignación de licencias para los usuarios.

Hasta ahora, las licencias solo podían asignarse a nivel de cada usuario, lo que puede dificultar la administración a gran escala. Por ejemplo, para agregar o quitar licencias de usuario en función de los cambios que se producen en la organización, por ejemplo, la incorporación o la baja de un usuario en la organización o en un departamento, un administrador a menudo debe escribir un script de PowerShell complejo. Este script hace llamadas individuales al servicio en la nube.

Para abordar esos desafíos, Azure AD incluye ahora las licencias basadas en grupo. Puede asignar una o varias licencias de producto a un grupo. Azure AD garantiza que las licencias se asignen a todos los miembros del grupo. A todos los miembros nuevos que se unan al grupo se les asignarán las licencias correspondientes. Cuando salen del grupo, se quitan esas licencias. De esta forma, ya no es necesario automatizar la administración de licencias a través de PowerShell para reflejar los cambios que se producen en la organización y en la estructura de departamento por cada usuario.

## <a name="features"></a>Características

A continuación se indican las características principales de las licencias basadas en grupos:

- Se pueden asignar licencias a todos los grupos de seguridad en Azure AD. Los grupos de seguridad se pueden sincronizar en el entorno local mediante Azure AD Connect. También puede crear grupos de seguridad directamente en Azure AD (también denominados grupos solo de nube) o de forma automática, a través de la característica de grupo dinámico de Azure AD.

- Cuando se asigna una licencia de producto a un grupo, el administrador puede deshabilitar uno o varios planes de servicio del producto. Habitualmente, esto se hace cuando la organización todavía no está preparada para comenzar a usar un servicio incluido en un producto. Por ejemplo, el administrador podría asignar Office 365 a un departamento y deshabilitar temporalmente el servicio Yammer.

- Se admiten todos los Servicios en la nube de Microsoft que requieren licencias a nivel de usuario. Se incluyen todos los productos de Office 365, Enterprise Mobility + Security y Dynamics CRM.

- Las licencias basadas en grupos actualmente solo están disponibles a través de [Azure Portal](https://portal.azure.com). Si usa principalmente otros portales de administración para la administración de usuarios y grupos, como el portal de Office 365, puede seguir haciéndolo. Sin embargo, debe usar Azure Portal para administrar las licencias en el nivel de grupo.

- Azure AD administra automáticamente las modificaciones de licencia resultantes de los cambios de pertenencia a grupos. Habitualmente, las modificaciones de licencia entran en vigor minutos después de un cambio en la pertenencia.

- Un usuario puede ser miembro de varios grupos con directivas de licencia especificadas. Un usuario también puede tener algunas licencias que se asignaron directamente, fuera de cualquier grupo. El estado de usuario resultante es una combinación de todas las licencias de producto y servicio asignadas.

- En algunos casos, las licencias no se pueden asignar a un usuario. Por ejemplo, es posible que no haya licencias disponibles suficientes en el inquilino o puede que se hayan asignado servicios en conflicto al mismo tiempo. Los administradores tienen acceso a información sobre usuarios para los que Azure AD no pudo procesar íntegramente las licencias de grupo. Pueden realizar acciones correctivas según esa información.

- Durante la versión preliminar pública, se requiere una suscripción de pago o de prueba a las ediciones básica o premium de Azure AD en el inquilino para usar la administración de licencias basadas en grupos.

## <a name="your-feedback-is-welcome"></a>Agradecemos sus comentarios.

Si tiene comentarios o solicitudes de características, compártalos con nosotros a través de [este foro](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias basadas en grupos, consulte:

* [Introducción a las licencias de Azure Active Directory](active-directory-licensing-get-started-azure-portal.md)
* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Asignación de licencias a un grupo en Azure Active Directory)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Escenarios adicionales de licencias basadas en grupos de Azure Active Directory](active-directory-licensing-group-advanced.md)
