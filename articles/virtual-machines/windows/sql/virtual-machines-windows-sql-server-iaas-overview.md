---
title: "Introducción a SQL Server en máquinas virtuales Windows de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo ejecutar las ediciones completas de SQL Server en máquinas virtuales de Azure."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/14/2017
ms.author: jroth
ms.openlocfilehash: 95a18e4b2437ca628e84f6d6f580e12f2f26e111
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>¿Qué es SQL Server en máquinas virtuales de Azure? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[SQL Server en máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) le permite usar versiones completas de SQL Server en la nube sin tener que administrar todo el hardware local. Las máquinas virtuales con SQL Server también simplifican los costos de licencia cuando se paga por uso.

Las máquinas virtuales de Azure se ejecutan en distintas [regiones geográficas](https://azure.microsoft.com/regions/) en todo el mundo. También ofrecen diversos [tamaños de máquina](../sizes.md). La galería de imágenes de máquina virtual le permite crear una máquina virtual con SQL Server con la versión, la edición y el sistema operativo correctos. Esto hace que las máquinas virtuales sean una buena opción para muchas cargas de trabajo de SQL Server diferentes.

## <a name="automated-updates"></a>Actualizaciones automatizadas

Las máquinas virtuales de Azure con SQL Server pueden usar [Automated Patching](virtual-machines-windows-sql-automated-patching.md) para programar el intervalo de tiempo de mantenimiento para la instalación automática de actualizaciones de SQL Server y Windows.

## <a name="automated-backups"></a>Copias de seguridad automatizadas

Las máquinas virtuales de Azure con SQL Server pueden aprovechar [Automated Backup](virtual-machines-windows-sql-automated-backup-v2.md), que crea de forma regular copias de seguridad de la base de datos en el almacenamiento de blobs. También puede utilizar esta técnica manualmente. Para más información, consulte [Uso de Azure Storage para la copia de seguridad y la restauración de SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Alta disponibilidad

Si requiere alta disponibilidad, considere la posibilidad de configurar grupos de disponibilidad de SQL Server. Esto implica varias máquinas virtuales de Azure con SQL Server en una red virtual. Puede configurar manualmente la solución de alta disponibilidad, o puede usar plantillas en Azure Portal para configurarla automáticamente. Para una introducción a las opciones de alta disponibilidad, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Rendimiento

Las máquinas virtuales de Azure ofrecen tamaños de máquina diferentes para satisfacer las distintas demandas de carga de trabajo. Las máquinas virtuales de SQL también proporcionan la configuración automatizada de almacenamiento, que está optimizada para cumplir con los requisitos de rendimiento. Para más información acerca de cómo configurar el almacenamiento para las máquinas virtuales de SQL, consulte [Configuración del almacenamiento para máquinas virtuales de SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Para ajustar el rendimiento, consulte [Procedimientos recomendados para mejorar el rendimiento de SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Introducción a máquinas virtuales con SQL

Para empezar, elija una imagen de máquina virtual con SQL Server con la versión, la edición y el sistema operativo requeridos. Las secciones siguientes proporcionan vínculos directos a Azure Portal para las imágenes de la galería de máquinas virtuales de SQL Server.

> [!TIP]
> Para entender los precios de máquinas virtuales y SQL, consulte [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server en Azure).

> [!TIP]
> Para entender la directiva de ciclo de vida y actualización para las imágenes de la galería de máquinas virtuales de SQL Server, consulte [Preguntas más frecuentes sobre las máquinas virtuales de SQL Server](virtual-machines-windows-sql-server-iaas-faq.md#images).

### <a id="payasyougo"></a> Pago por uso
En la tabla siguiente se proporciona la matriz de imágenes de SQL SErver de pago por uso.

| Versión | Sistema operativo | Edition |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Para ver las imágenes de máquinas virtuales Linux con SQL Server disponibles, consulte [Overview of SQL Server on Azure Virtual Machines (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md) [Introducción a SQL Server en máquinas virtuales de Azure (Linux)].

### <a id="BYOL"></a> Traiga su propia licencia
También puede traer su propia licencia (BYOL). En este escenario, solo paga por la máquina virtual sin ningún cargo adicional de licencia de SQL Server.  Aportar su propia licencia puede ahorrar dinero con el tiempo en cargas de trabajo de producción continuas. Para más información, consulte [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guía de precios de máquinas virtuales de Azure con SQL Server).

| Versión | Sistema operativos | Edition |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

En el portal, los nombres de las imágenes van precedidos de **{BYOL}**.

### <a name="connect-to-the-vm"></a>Conexión a la máquina virtual
Después de crear la máquina virtual con SQL Server, conéctese a ella desde aplicaciones o herramientas tales como SQL Server Management Studio (SSMS). Consulte las instrucciones en [Conexión a una máquina virtual de Azure con SQL Server (Resource Manager)](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migración de los datos
Si tiene una base de datos existente, debe moverla a la máquina virtual de SQL recién aprovisionada. Para ver una lista de las opciones de migración e instrucciones, consulte [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

## <a name="customer-experience-improvement-program-ceip"></a>Programa para la mejora de la experiencia del usuario (CEIP)
De manera predeterminada, el Programa para la mejora de la experiencia del cliente (CEIP) está habilitado. Esto permitirá enviar periódicamente informes a Microsoft para ayudar a mejorar SQL Server. No hay ninguna tarea de administración requerida relacionada con el CEIP, a menos que desee deshabilitarlo después del aprovisionamiento. Puede personalizar o deshabilitar el CEIP mediante la conexión a la máquina virtual a través del escritorio remoto. A continuación, ejecute la utilidad **Informes de uso y errores de SQL Server** . Siga las instrucciones para deshabilitar los informes. Para más información acerca de la recopilación de datos, vea la [declaración de privacidad de SQL Server](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx).

## <a name="related-products-and-services"></a>Productos y servicios relacionados
### <a name="windows-virtual-machines"></a>Máquinas virtuales Windows
* [Información general de Virtual Machines](../overview.md)

### <a name="storage"></a>Storage
* [Introducción a Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Redes
* [Información general sobre Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [Direcciones IP en Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Creación de un nombre de dominio completo en Azure Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentación de SQL Server](https://docs.microsoft.com/sql/index)
* [Comparación de Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Pasos siguientes

Introducción a SQL Server en máquinas virtuales de Azure:

* [Creación de una máquina virtual con SQL Server en Azure Portal](quickstart-sql-vm-create-portal.md)

Obtenga respuestas a las preguntas más habituales acerca de las máquinas virtuales con SQL:

* [Preguntas más frecuentes sobre SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)
