---
title: "Visualización del consumo de direcciones IP públicas en Azure Stack | Microsoft Docs"
description: "Los administradores pueden ver el consumo de direcciones IP públicas de una región"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Visualización del consumo de direcciones IP públicas en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Los administradores de la nube pueden ver el número de direcciones IP públicas que se han asignado a los inquilinos, el número de direcciones IP públicas que siguen estando disponibles para su asignación y el porcentaje de direcciones IP públicas que se han asignado en una ubicación.

El icono **Uso de grupos de IP públicas** muestra el número total de direcciones IP públicas que se han consumido en todos los grupos de direcciones IP públicas en el tejido, si se han utilizado para las instancias de máquina virtual IaaS de los inquilinos, los servicios de la infraestructura del tejido o los recursos de direcciones IP públicas que los inquilinos han creado explícitamente.

Este icono permite a los administradores de Azure Stack hacerse una idea del número total de direcciones IP públicas que se han consumido en una ubicación, lo que les ayuda a determinar si se están quedando sin algún recurso.

En la hoja **Proveedores de recursos**, **Red**, el elemento de menú **Direcciones IP públicas** de **Recursos de inquilinos** enumera solo las direcciones IP públicas que han *creado de manera explícita los inquilinos*. Como tal, el número de direcciones IP públicas **usadas** del icono **Uso de grupos de IP públicas** nunca coincide (es mayor) con el número del icono **Direcciones IP públicas** de **Recursos de inquilinos**.

## <a name="view-the-public-ip-address-usage-information"></a>Visualización de la información de uso de direcciones IP públicas
Para ver el número total de direcciones IP públicas que se han consumido en una región:

1. En el portal de administrador de Azure Stack, haga clic en **Más servicios** y en **Recursos administrativos**, haga clic en **Proveedores de recursos**.
2. En la lista de **proveedores de recursos**, seleccione **Red**.
3. La hoja **Red** muestra el icono **Uso de grupos de IP públicas** en la sección **Información general**.

![Hoja de proveedor de recursos de red](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Tenga en cuenta que el número que aparece debajo de **Usadas** representa el número de direcciones IP públicas de todos los grupos de direcciones IP públicas de la ubicación que están asignados. El número que aparece debajo de **Libres** representa el número de direcciones IP públicas de todos los grupos de direcciones IP públicas que no se ha asignado y siguen estando disponibles. El número que aparece al lado de **% usado** representa el número de direcciones usadas o asignadas, como porcentaje del número total de direcciones IP públicas de todos los grupos de direcciones IP públicas que hay en la ubicación.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visualización de las direcciones IP públicas que han creado las suscripciones de los inquilinos
Para ver la lista de direcciones IP públicas que las suscripciones de los inquilinos han creado de manera explícita en una región concreta, haga clic en **Direcciones IP públicas** en **Recursos de inquilinos**.

![Direcciones IP públicas de inquilinos](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Puede ver que algunas direcciones IP públicas que se han asignado dinámicamente aparecen en la lista pero aún no tiene una dirección asociada. Eso que debe a que el recurso de red ya se ha creado en el proveedor de recursos de red, pero aún no en la controladora de red.

La controladora de red no asigna una dirección a este recurso hasta que realmente está enlazado a una interfaz, una tarjeta de interfaz de red (NIC), un equilibrador de carga o una puerta de enlace de red virtual. Cuando la dirección IP pública está enlazada a una interfaz, la controladora de red le asigna una dirección IP y aparece en el campo **Dirección**.

## <a name="view-the-public-ip-address-information-summary-table"></a>Visualización de la tabla de resumen de la información acerca de las direcciones IP públicas
Hay una varios casos en los que se asignan direcciones IP públicas que determinan si la dirección aparece en una lista o en otra.

| **Caso de asignación de dirección IP pública** | **Aparece en resumen de uso** | **Aparece en lista de direcciones IP públicas de inquilinos** |
| --- | --- | --- |
| La dirección IP pública dinámica aún no está asignada a un NIC o equilibrador de carga (temporal). |No |Sí |
| La dirección IP pública dinámica está asignada a un NIC o equilibrador de carga. |Sí |Sí |
| La dirección IP pública estática está asignada a un NIC o equilibrador de carga del inquilino. |Sí |Sí |
| La dirección IP pública estática está asignada a un punto de conexión del servicio de la infraestructura del tejido. |Sí |No |
| Dirección IP pública creada de manera implícita para instancias de VM de IaaS y usada para NAT saliente en la red virtual. Se crean en segundo plano cada vez que un inquilino crea una instancia de máquina virtual para que las máquinas virtuales pueden enviar información a Internet. |Sí |No |

## <a name="next-steps"></a>Pasos siguientes
[Administración de cuentas de almacenamiento en Azure Stack](azure-stack-manage-storage-accounts.md)