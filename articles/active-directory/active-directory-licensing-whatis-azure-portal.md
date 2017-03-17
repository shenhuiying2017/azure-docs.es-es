---

title: "¿En qué consisten las licencias basadas en grupos de Azure Active Directory? | Microsoft Docs"
description: "Descripción de las licencias basadas en grupo de Azure Active Directory, cómo funcionan, cómo comenzar a usarlas y procedimientos recomendados"
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 0591b536a9be901085074f4b49fc65b097382835
ms.lasthandoff: 03/01/2017


---

# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Aspectos básicos de las licencias basadas en grupos en Azure Active Directory

Los Servicios en la nube de Microsoft como Office 365, Enterprise Mobility + Security, Dynamics CRM y otros productos similares requieren la asignación de licencias a cada usuario que necesita acceder a estos servicios. La administración de licencias se expone a los administradores a través de uno de los portales de administración (Office o Azure) y los cmdlets de PowerShell. El estado de asignación de licencia se almacena en Azure Active Directory; la infraestructura subyacente compatible con la administración de identidades para todos los Servicios en la nube de Microsoft.

Hasta ahora, las licencias solo podían asignarse a nivel de cada usuario, lo que puede dificultar a los clientes una administración a gran escala. Por ejemplo, para agregar o quitar licencias de usuario en función de los cambios que se producen en la organización, por ejemplo, la incorporación o la baja de un usuario en la organización o en un departamento, un administrador a menudo debe escribir un script de PowerShell complejo para realizar llamadas individuales al servicio en la nube.

Para abordar estos desafíos, se ha introducido una nueva funcionalidad en el sistema de administración de licencias de Azure AD: licencias basadas en grupos. Ahora es posible asignar una o varias licencias de producto a un grupo. Azure AD se asegurará de que las licencias se asignen a todos los miembros del grupo. A todos los miembros nuevos que se unan al grupo se les asignarán las licencias correspondientes, y cuando dejen el grupo, dichas licencias se retirarán. De esta forma, ya no es necesario automatizar la administración de licencias a través de PowerShell para reflejar los cambios que se producen en la organización y en la estructura de departamento por cada usuario.

## <a name="features"></a>Características

A continuación se indican las características principales de la funcionalidad de licencias basadas en grupos:

- Se pueden asignar licencias a todos los grupos de seguridad en Azure AD. Los grupos de seguridad se pueden sincronizar desde el entorno local mediante Azure AD Connect, se pueden crear directamente en Azure AD (también denominados grupos solo de nube) o se pueden crear automáticamente a través de la característica de grupo dinámico de Azure AD.

- Cuando se asigna una licencia de producto a un grupo, el administrador puede deshabilitar uno o varios planes de servicio del producto. Normalmente, esto se hace cuando la organización todavía no está lista para empezar a usar un servicio incluido en un producto; por ejemplo, el administrador desea asignar el producto de Office 365 E3 a un departamento, pero desea deshabilitar temporalmente el servicio Yammer Enterprise.

- Se admiten todos los Servicios en la nube de Microsoft que requieren licencias a nivel de usuario. Se incluyen todos los productos de Office 365, Enterprise Mobility + Security, Dynamics CRM, etc.

- Las licencias basadas en grupos actualmente solo están disponibles a través de [Azure Portal](https://portal.azure.com). Los clientes que usan principalmente otros portales de administración para la administración de usuarios y grupos, por ejemplo, el portal de Office 365, pueden seguir haciéndolo, pero deben usar Azure Portal para administrar licencias a nivel de grupo.

- Azure AD administra automáticamente las modificaciones de licencia resultantes de los cambios de pertenencia a grupos. Por lo general, si un usuario se une a un grupo o lo deja, sus licencias se modificarán pocos minutos después de que se produzca el cambio de pertenencia.

- Un usuario puede ser miembro de varios grupos con directivas de licencia especificadas; también pueden tener algunas licencias asignadas directamente al usuario fuera de todos los grupos. El estado de usuario resultante es una combinación de todas las licencias de producto y servicio asignadas.

- En algunos casos, no se pueden asignar licencias a un usuario; por ejemplo, porque no hay licencias suficientes disponibles en el inquilino o porque se han asignado servicios conflictivos al mismo tiempo. Los administradores tienen acceso a información sobre usuarios para los que Azure AD no ha podido procesar íntegramente las licencias de grupo; por ello, deben aplicar medidas correctivas basadas en dicha información.

- Durante la versión preliminar pública, se requiere una suscripción de pago o de prueba a Azure AD Básico o superior en el inquilino para usar la administración de licencias basadas en grupos. Además, todos los usuarios que hereden alguna licencia de grupos deben tener asignada la licencia de la edición de pago de Azure AD.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias basadas en grupos, vea:

* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Asignación de licencias a un grupo en Azure Active Directory)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Escenarios adicionales de licencias basadas en grupos de Azure Active Directory](active-directory-licensing-group-advanced.md)

