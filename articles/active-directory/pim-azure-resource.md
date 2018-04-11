---
title: Administración del acceso a los recursos de Azure con Privileged Identity Management (PIM)
description: Obtenga información acerca del uso de la administración de acceso basada en roles de PIM para tener acceso a recursos de Azure.
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 1e74579ef2f0e18f23a40dfc573177938b9b726f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Administración del acceso a recursos de Azure con Privileged Identity Management

Para proteger las cuentas con privilegios de ciberataques malintencionados, puede usar Azure Active Directory Privileged Identity Management (PIM) para reducir el tiempo de exposición de los privilegios y aumentar la visibilidad de su uso a través de alertas e informes. PIM lo hace limitando a los usuarios a que solo acepten sus privilegios "just-in-time" (JIT) o mediante la asignación de privilegios por un período de tiempo más corto, tras el cual se revocan automáticamente. 

Ya puede usar PIM con Control de acceso basado en roles de Azure (RBAC) para administrar, controlar y supervisar el acceso a recursos de Azure. PIM puede administrar la pertenencia de roles integrados y personalizados para ayudarle a: 

- Habilitar el acceso "just-in-time" y a petición a recursos de Azure
- Hacer expirar automáticamente el acceso a los recursos para usuarios y grupos asignados
- Asignar acceso temporal a recursos de Azure para tareas rápidas u horarios de guardia
- Recibir alertas cuando se asigne acceso a recursos para nuevos usuarios o grupos, así como cuando activen asignaciones aptas

Para obtener más información, consulte [Información general de control de acceso basado en roles de Azure PIM](privileged-identity-management/azure-pim-resource-rbac.md).