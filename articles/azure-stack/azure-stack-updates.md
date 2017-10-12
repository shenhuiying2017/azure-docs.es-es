---
title: "Introducción a la administración de actualizaciones en Azure Stack | Microsoft Docs"
description: "Conozca sobre la administración de actualizaciones en sistemas integrados de Azure Stack."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3d0d5ea6cc3f3cc7bc0550b83dabbf0ae6af8a27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>Introducción a la administración de actualizaciones en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Microsoft lanzará actualizaciones para sistemas integrados de Azure Stack de manera periódica. Cada lanzamiento de actualizaciones de software de Microsoft software viene como una única actualización. Como operador de Azure Stack, puede importar, instalar y supervisar fácilmente el progreso de la instalación de estas actualizaciones desde el portal de administrador. 

El proveedor de hardware OEM (fabricante de equipo original) también lanzará actualizaciones, como las de controladores y firmware. proporcionará como paquetes independientes y luego Microsoft se encargará de administrarlas aparte.

Para que el sistema esté cubierto por el soporte técnico, Azure Stack debe mantenerse actualizado hasta un nivel de versión específico. Asegúrese de revisar la [directiva de servicio de Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> No es posible aplicar actualizaciones de Azure Stack a Azure Stack Development Kit. Las actualizaciones están diseñadas para sistemas integrados.

## <a name="the-update-resource-provider"></a>El proveedor de recursos de actualización

Azure Stack incluye un proveedor de recursos de actualización que organiza la aplicación de las actualizaciones de software de Microsoft. Este proveedor garantiza que las actualizaciones se aplican en todos los hosts físicos, entornos en tiempo de ejecución y aplicaciones de Service Fabric, y en todas las máquinas virtuales de la infraestructura y sus servicios asociados.

Cuando se instalan las actualizaciones, puede ver fácilmente el estado de alto nivel a medida que el proceso de actualización alcanza los diversos subsistemas de Azure Stack (por ejemplo, hosts físicos y máquinas virtuales de la infraestructura).

## <a name="plan-for-updates"></a>Planeamiento de las actualizaciones

Se recomienda firmemente notificar a los usuarios cualquier operación de mantenimiento, así como programar ventanas de mantenimiento normal durante el horario no laborable tanto como sea posible. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo de inquilino como a las operaciones del portal.

## <a name="using-the-update-tile-to-manage-updates"></a>Uso del icono Actualización para administrar las actualizaciones
La administración de actualizaciones desde el portal de administrador es un proceso sencillo. Un operador de Azure Stack puede desplazarse al icono Actualización del panel para:

- ver información importante, como la versión actual.
- instalar actualizaciones y supervisar el progreso.
- revisar el historial de actualización de las actualizaciones instaladas anteriormente.
 
## <a name="determine-the-current-version"></a>Determinar la versión actual

El icono Actualizar muestra la versión actual de Azure Stack. Para acceder a él, puede usar cualquiera de los siguientes métodos del portal de administrador:

- En el panel, vea la versión actual en el icono **Actualización**.
 
   ![Icono de las actualizaciones en el panel predeterminado](./media/azure-stack-updates/image1.png)
 
- En el icono **Administración de regiones**, haga clic en el nombre de la región. Vea la versión actual en el icono **Actualización**.

## <a name="next-steps"></a>Pasos siguientes

- [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md) 
- [Administración de regiones en Azure Stack](azure-stack-region-management.md)     


