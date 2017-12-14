---
title: Administrar el acceso a recursos de Azure mediante Azure Active Directory
description: "Aprenda a administrar el acceso a los recursos de Azure mediante las diversas características que le ofrece Azure Active Directory."
services: active-directory
documentationcenter: 
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: eee4353c183aeec19f72f8e1dec6c20b6c5bb226
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Administrar el acceso a recursos de Azure mediante Azure Active Directory

La administración de identidades y acceso de los recursos en la nube es una función importantísima para cualquier organización que use la nube. Azure Active Directory (Azure AD) es el sistema de identificación y acceso de Microsoft Azure.  

Antes de explorar las áreas de características de Azure AD, consulte el vídeo siguiente: "Bloquear el acceso a la nube de Azure mediante el uso de SSO, el Control de acceso basado en roles y el condicional." En él, aprenderá sobre los siguientes temas:

- Prácticas recomendadas para configurar el inicio de sesión único en Azure Portal con Active Directory local.
- Uso de RBAC de Azure para el control de acceso específico a los recursos de las suscripciones.
- Aplicación de reglas de autenticación fuerte con el acceso condicional de Azure AD.
- El concepto de Managed Service Identity, con el que los recursos de Azure pueden autenticarse automáticamente en los servicios de Azure y no es necesario que los desarrolladores posean claves de API ni secretos.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Áreas de características
Azure AD le proporciona las siguientes funcionalidades que le permitirán administrar el acceso a los recursos de Azure:

|||
|---|---|
| [Relación entre las suscripciones y los inquilinos de Azure AD](active-directory-understanding-resource-access.md) | Descubra por qué Azure AD es la fuente de confianza de aquellos usuarios y grupos que tienen una suscripción de Azure. |
| [Control de acceso basado en roles (RBAC)](role-based-access-control-what-is.md) | Ofrece la posibilidad de administrar al detalle el acceso de usuarios, grupos o entidades de servicio mediante roles asignados. |
| [Privileged Identity Management con RBAC](pim-azure-resource.md) | Permite controlar el acceso altamente privilegiado asignando roles con privilegios al momento. |
| [Acceso condicional para la administración de Azure](conditional-access-azure-management.md) | Permite configurar directivas de acceso condicional para permitir o bloquear el acceso a los puntos de conexión de la administración de Azure. |
| [Identidad de servicio administrada (MSI)](msi-overview.md) | Proporciona a ciertos recursos de Azure (como las máquinas virtuales) su propia identidad de modo que no sea necesario escribir las credenciales en el código. |

 