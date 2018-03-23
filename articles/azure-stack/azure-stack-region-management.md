---
title: Administración de regiones en Azure Stack | Microsoft Docs
description: Introducción a la administración de regiones en Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 0a19490ce276eec9a46bc0ea4343e3449a9dc93c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="region-management-in-azure-stack"></a>Administración de regiones en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Azure Stack tiene el concepto de regiones, que son entidades lógicas que constan de los recursos de hardware que conforman la infraestructura de Azure Stack. Dentro de Region Management (Administración de regiones), puede buscar todos los recursos necesarios para usar correctamente el ciclo de vida de la infraestructura de Azure Stack.

Una implementación de sistema integrada (lo que se conoce como una *nube de Azure Stack*) constituye una región única. Cada Kit de desarrollo de Azure Stack tiene una región, llamada **local**. Si implementa un segundo sistema integrado de Azure Stack o configura otra instancia del kit de desarrollo en otro hardware, esta nube de Azure Stack es una región diferente.

## <a name="information-available-through-the-region-management-tile"></a>Información disponible a través del icono Administración de regiones
Azure Stack tiene un conjunto de funciones de administración de regiones disponibles en la ventana **Region management** (Administración de regiones). Este icono está disponible para un nuevo operador de Azure Stack en el panel predeterminado del portal de administrador. A través de este icono, puede supervisar y actualizar su región de Azure Stack y sus componentes, que son específicos de cada región.

 ![Icono Region Management](media/azure-stack-manage-region/image1.png)

 Si hace clic en una región en el icono Region Management (Administración de regiones), puede tener acceso a la siguiente información:

  ![Descripción de los paneles en la hoja Region Management](media/azure-stack-manage-region/image2.png)

1. **El menú de recursos**. Aquí, puede tener acceso a áreas de administración de infraestructura específicas y ver y administrar recursos de usuario, como cuentas de almacenamiento y redes virtuales.

2. **Alertas**. Este icono muestra las alertas de todo el sistema y proporciona detalles sobre cada una.

3. **Actualizaciones**. En este icono, puede ver la versión actual de la infraestructura de Azure Stack, las actualizaciones disponibles y el historial de actualizaciones. También puede actualizar el sistema integrado.

4. **Proveedores de recursos**. Este es el lugar donde administrar la funcionalidad para los usuarios proporcionada por los componentes necesarios para ejecutar Azure Stack. Cada proveedor de recursos viene con una experiencia administrativa. Esta experiencia puede incluir las alertas para el proveedor específico, métricas y otras funcionalidades de administración concretas para el proveedor de recursos.

5. **Infrastructure roles** (Roles de infraestructura). Son los componentes necesarios para ejecutar Azure Stack. Se enumeran solo los roles de infraestructura que notifican alertas. Si hace clic en un rol, puede ver las alertas asociadas a él y las instancias de rol donde este rol se ejecuta.

## <a name="next-steps"></a>Pasos siguientes
[Supervisar el estado y las alertas en Azure Stack](azure-stack-monitor-health.md)

[Administrar las actualizaciones en Azure Stack](azure-stack-updates.md)
