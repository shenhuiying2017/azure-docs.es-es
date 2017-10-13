---
title: "Grupos de disponibilidad de SQL Server - Azure Virtual Machines - Información general | Microsoft Docs"
description: "En este artículo se describen los grupos de disponibilidad de SQL Server en Azure Virtual Machines."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 2cbb9ff3b2d13996b1b8dc64aa833807c264c877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introducción a grupos de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines #

En este artículo se describen los grupos de disponibilidad de SQL Server en Azure Virtual Machines. 

Los grupos de disponibilidad AlwaysOn en Azure Virtual Machines son similares a los grupos de disponibilidad AlwaysOn locales. Para más información, consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

El diagrama muestra las partes de un grupo de disponibilidad de SQL Server completo en Azure Virtual Machines.

![Grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La diferencia clave para un grupo de disponibilidad en Azure Virtual Machines es que las máquinas virtuales de Azure requieren un [equilibrador de carga](../../../load-balancer/load-balancer-overview.md). El equilibrador de carga almacena las direcciones IP del agente de escucha del grupo de disponibilidad. Si tiene más de un grupo de disponibilidad, se necesita un agente de escucha por grupo. Un equilibrador de carga puede admitir varios agentes de escucha.

Cuando esté listo para crear un grupo de disponibilidad de SQL Server en Azure Virtual Machines, consulte estos tutoriales.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Creación automática de grupos de disponibilidad desde una plantilla

[Configure Always On availability group in Azure VM automatically - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) (Configuración automática de grupos de disponibilidad Always On de máquinas virtuales de Azure - Resource Manager)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Creación manual de grupos de disponibilidad en Azure Portal

También puede crear las máquinas virtuales sin la plantilla. En primer lugar, complete los requisitos previos y después cree el grupo de disponibilidad. Vea los siguientes temas: 

- [Configure prerequisites for SQL Server Always On availability groups on Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md) (Configuración de requisitos previos para grupos de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines)///

- [Creación de un grupo de disponibilidad AlwaysOn para mejorar la disponibilidad y recuperación ante desastres](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Pasos siguientes

[Configuración de un grupo de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines en regiones distintas](virtual-machines-windows-portal-sql-availability-group-dr.md)
