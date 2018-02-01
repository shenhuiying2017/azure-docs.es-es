---
title: "Administración del acceso a la administración de Azure con acceso condicional en Azure Active Directory"
description: "Aprenda a usar el acceso condicional en Azure AD para administrar el acceso a la administración de Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: bryanla
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.openlocfilehash: 22d0e53c201853e2c316089479ffbd4d9e5d92be
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Administración el acceso a la administración de Azure con acceso condicional

El acceso condicional en Azure Active Directory (Azure AD) controla el acceso a las aplicaciones en la nube en función de las condiciones concretas que especifique. Para permitir el acceso, cree directivas de acceso condicional que permitan o bloqueen el acceso en función de si se cumplen los requisitos de la directiva. 

Normalmente, se utiliza el acceso condicional para controlar el acceso a las aplicaciones en la nube. También puede configurar directivas para controlar el acceso a la administración de Azure en función de ciertas condiciones (como el riesgo de inicio de sesión, la ubicación o el dispositivo) y para exigir requisitos como la autenticación multifactor.

Para crear una directiva de administración de Azure, seleccione **Microsoft Azure Management** en **Aplicaciones en la nube** al elegir la aplicación a la que se va a aplicar la directiva.

![Acceso condicional para la administración de Azure](./media/conditional-access-azure-mgmt.png)

La directiva que se crea se aplica a todos los puntos de conexión de la administración de Azure, incluidos el Portal de Azure clásico, Azure Portal, el proveedor de Azure Resource Manager, las instancias clásicas de Service Management API y Azure PowerShell.

> [!CAUTION]
> Asegúrese de comprender cómo funciona el acceso condicional antes de configurar una directiva para administrar el acceso a la administración de Azure. Asegúrese de no crear condiciones que pudieran bloquear su propio acceso al portal.

Para obtener más información sobre cómo configurar y usar el acceso condicional, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access-azure-portal.md).