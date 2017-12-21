---
title: "Introducción a SQL Server en máquinas virtuales Linux de Azure | Microsoft Docs"
description: "Aprenda a ejecutar ediciones completas de SQL Server en máquinas virtuales Linux de Azure. Obtenga vínculos directos a todas las imágenes de máquina virtual de SQL Server y al contenido relacionado."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: f044fcba92eb7aa7f4ac44608571fab3db0e03f5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Introducción a SQL Server en SQL Server en máquinas virtuales de Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

En este tema se describen las opciones para ejecutar SQL Server en máquinas virtuales Linux de Azure (VM), junto con [vínculos a imágenes del portal](#option-1-create-a-sql-vm-with-per-minute-licensing).

> [!NOTE]
> Si ya conoce y ha usado SQL Server, y solo desea ver cómo implementar una máquina virtual Linux de SQL Server, consulte [Provision a Linux SQL Server VM in Azure](provision-sql-server-linux-virtual-machine.md) (Aprovisionamiento de una máquina virtual Linux de SQL Server en Azure Portal). O bien si desea crear una máquina virtual Windows con SQL Server, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Si es un administrador de base de datos o un desarrollador, las máquinas virtuales de Azure le proporcionan una manera de mover las cargas de trabajo y las aplicaciones de SQL Server local a la nube.

## <a name="scenarios"></a>Escenarios

Hay muchas razones por las que puede decidir alojar los datos en Azure. Si va a desarrollar o migrar una aplicación en Azure, esto mejora el rendimiento para buscar también los datos de back-end en Azure. Tendrá acceso automático a varios centros de datos para una recuperación ante desastres y una presencia global. Datos muy protegidos y duraderos.

La ejecución de SQL Server en una máquina virtual de Azure es una opción para el almacenamiento de datos relacionales en Azure. También tiene la opción de usar el servicio Azure SQL Database. Para más información acerca de cómo elegir entre SQL Server en máquinas virtuales y Azure SQL Database, consulte [Selección de una opción de SQL Server en la nube: Azure SQL (PaaS) Database o SQL Server en máquinas virtuales de Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a id="create"></a> Creación de una máquina virtual de SQL

Para obtener instrucciones detalladas sobre la creación de una nueva máquina virtual de SQL, consulte el tutorial [Provision a Linux SQL Server virtual machine in the Azure portal](provision-sql-server-linux-virtual-machine.md) (Aprovisionamiento de una máquina virtual Linux de SQL Server en Azure Portal).

En la tabla siguiente se ofrece una matriz de las más recientes imágenes de SQL Server en la galería de máquinas virtuales. Haga clic en cualquier vínculo para empezar a crear una nueva máquina virtual de SQL con la versión, la edición y el sistema operativo que especifique.

> [!TIP]
> Para conocer los precios de máquinas virtuales y SQL, consulte la [página de precios para máquinas virtuales Linux en SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versión | Sistema operativo | Edition |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Para ver las imágenes de máquinas virtuales Windows en SQL Server disponibles, consulte [Información general de SQL Server en máquinas virtuales de Azure](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Paquetes instalados

Al configurar SQL Server en Linux, instale el paquete del motor de base de datos y, después, varios paquetes opcionales en función de los requisitos. Las imágenes de máquina virtual Linux para SQL Server instalan automáticamente la mayoría de los paquetes. La siguiente tabla muestra qué paquetes se instalan para cada distribución.

| Distribución | [Motor de base de datos](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Herramientas](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agente SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Búsqueda de texto completo](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Complemento de alta disponibilidad](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de cómo configurar y usar SQL Server en Linux, consulte [Información general de SQL Server en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
