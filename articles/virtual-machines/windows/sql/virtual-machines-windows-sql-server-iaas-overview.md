---
title: "Información general de SQL Server en máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo ejecutar las ediciones completas de SQL Server en máquinas virtuales de Azure. Obtenga vínculos directos a todas las imágenes de máquinas virtuales de SQL Server y al contenido relacionado."
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
ms.date: 09/12/2017
ms.author: jroth
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: b9d42e393e696187d2299e033402db8ee565593a
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Información general de SQL Server en máquinas virtuales de Azure
Este tema describe las opciones para ejecutar SQL Server en máquinas virtuales de Azure (VM), junto con [vínculos a imágenes del portal](#option-1-create-a-sql-vm-with-per-minute-licensing) e información general sobre las [tareas comunes](#manage-your-sql-vm).

> [!NOTE]
> Si ya está familiarizado con SQL Server y solo desea ver cómo implementar una máquina virtual de SQL Server, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

Si es un administrador de base de datos o un desarrollador, las máquinas virtuales de Azure le proporcionan una manera de mover las cargas de trabajo y las aplicaciones de SQL Server local a la nube.

## <a name="scenarios"></a>Escenarios
Hay muchas razones por las que puede decidir alojar los datos en Azure. Si la aplicación se traslada a Azure, mejorará el rendimiento si también mueve los datos. Pero hay otras ventajas. Tendrá acceso automático a varios centros de datos para una recuperación ante desastres y una presencia global. Datos muy protegidos y duraderos.

La ejecución de SQL Server en una máquina virtual de Azure es una opción para el almacenamiento de datos relacionales en Azure. Esta es una buena opción para varios escenarios. Por ejemplo, puede que desee configurar la máquina virtual de Azure de una forma lo más parecida posible a un equipo con SQL Server local. O puede que desee ejecutar aplicaciones y servicios adicionales en el mismo servidor de la base de datos. Hay dos recursos principales que pueden ayudarle a plantearse aún más escenarios y consideraciones:

* [SQL Server en máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) proporciona una visión general de los mejores escenarios para utilizar SQL Server en máquinas virtuales de Azure. 
* [Selección de una opción de SQL Server en la nube: Azure SQL (PaaS) Database o SQL Server en máquinas virtuales de Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) proporciona una comparación detallada entre la ejecución de SQL Database y SQL Server en una máquina virtual.

## <a name="create-a-new-sql-vm"></a>Creación de una máquina virtual de SQL
Las secciones siguientes proporcionan vínculos directos a Azure Portal para las imágenes de la galería de máquinas virtuales de SQL Server. Dependiendo de la imagen que seleccione, podrá pagar los costos de licencias de SQL Server por minuto o aportar su propia licencia (BYOL).

Para obtener instrucciones detalladas sobre la creación de una nueva máquina virtual de SQL, consulte el tutorial [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Además, revise [Procedimientos recomendados de rendimiento para máquinas virtuales de SQL Server](virtual-machines-windows-sql-performance.md), que explica cómo seleccionar el tamaño de máquina adecuado y otras características disponibles durante el aprovisionamiento.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opción 1: Crear una VM de SQL con licencias por minuto
En la tabla siguiente se ofrece una matriz de las más recientes imágenes de SQL Server en la galería de máquinas virtuales. Haga clic en cualquier vínculo para empezar a crear una nueva máquina virtual de SQL con la versión, la edición y el sistema operativo que especifique. 

> [!TIP]
> Para entender los precios de máquinas virtuales y SQL, consulte [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server en Azure).

| Versión | Sistema operativo | Edition |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |

Además de esta lista, existen otras combinaciones disponibles de sistemas operativos y versiones de SQL Server. Busque otras imágenes a través de una búsqueda de Marketplace en Azure Portal. 

## <a id="BYOL"></a> Opción 2: Crear una VM de SQL con una licencia ya existente
También puede traer su propia licencia (BYOL). En este escenario, solo paga por la máquina virtual sin ningún cargo adicional de licencia de SQL Server. Para utilizar su propia licencia, use la matriz de los sistemas operativos, ediciones y versiones de SQL Server que puede ver a continuación. En el portal, los nombres de las imágenes van precedidos de **{BYOL}**.

> [!TIP]
> Aportar su propia licencia puede ahorrar dinero con el tiempo en cargas de trabajo de producción continuas. Para más información, consulte [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server en Azure).

| Versión | Sistema operativos | Edition |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

Además de esta lista, existen otras combinaciones disponibles de sistemas operativos y versiones de SQL Server. Busque otras imágenes a través de una búsqueda de Marketplace en Azure Portal (busque "{BYOL} SQL Server").

> [!IMPORTANT]
> Para utilizar imágenes de máquinas virtuales BYOL, es preciso tener un contrato Enterprise con [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/). También necesita una licencia válida para la versión y edición de SQL Server que desea utilizar. Tiene que [proporcionar la información necesaria de BYOL a Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) dentro de un plazo de **10** días a partir del aprovisionamiento de la máquina virtual. 

> [!NOTE]
> No es posible cambiar el modelo de licencias de una máquina virtual con SQL Server de pago por minuto para usar su propia licencia. En este caso, debe crear una nueva máquina virtual BYOL y migrar las bases de datos a ella. 

## <a name="manage-your-sql-vm"></a>Administración de la máquina virtual de SQL
Después de aprovisionar la máquina virtual de SQL Server, hay varias tareas de administración opcionales. En muchos aspectos, SQL Server se configura y se administra exactamente igual que si se tratara de una instancia local. Sin embargo, algunas tareas son específicas de Azure. Las secciones siguientes resaltan algunas de estas áreas con vínculos a más información.

### <a name="connect-to-the-vm"></a>Conexión a la máquina virtual
Uno de los pasos más básicos de administración consiste en conectarse a la máquina virtual de SQL Server a través de herramientas, como SQL Server Management Studio (SSMS). Para obtener instrucciones sobre cómo conectarse a la nueva máquina virtual de SQL Server, consulte [Conexión a una máquina virtual de SQL Server en Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migración de los datos
Si tiene una base de datos existente, debe moverla a la máquina virtual de SQL recién aprovisionada. Para ver una lista de las opciones de migración e instrucciones, consulte [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configuración para alta disponibilidad
Si requiere alta disponibilidad, considere la posibilidad de configurar grupos de disponibilidad de SQL Server. Esto implica varias máquinas virtuales de Azure en una red virtual. Azure Portal dispone de una plantilla que define esta configuración. Para más información, consulte [Configuración de un grupo de disponibilidad AlwaysOn en máquinas virtuales de Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Si desea configurar manualmente el grupo de disponibilidad y el agente de escucha asociado, consulte [Configuración de un grupo de disponibilidad AlwaysOn en máquinas virtuales de Azure Resource Manager (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para otras consideraciones sobre alta disponibilidad, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Realización de una copia de seguridad de los datos
Las máquinas virtuales de Azure pueden aprovechar la [Copia de seguridad automatizada](virtual-machines-windows-sql-automated-backup.md), que crea de forma regular copias de seguridad de la base de datos en el almacenamiento de blobs. También puede utilizar esta técnica manualmente. Para más información, consulte [Uso de Azure Storage para la copia de seguridad y la restauración de SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obtener información general sobre las opciones de copia de seguridad y restauración, consulte [Copias de seguridad y restauración para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatización de las actualizaciones
Las máquinas virtuales de Azure pueden usar las [revisiones automatizadas](virtual-machines-windows-sql-automated-patching.md) para programar una ventana de mantenimiento para la instalación automática de actualizaciones de SQL Server y Windows.

### <a name="customer-experience-improvement-program-ceip"></a>Programa para la mejora de la experiencia del usuario (CEIP)
De manera predeterminada, el Programa para la mejora de la experiencia del cliente (CEIP) está habilitado. Esto permitirá enviar periódicamente informes a Microsoft para ayudar a mejorar SQL Server. No hay ninguna tarea de administración requerida relacionada con el CEIP, a menos que desee deshabilitarlo después del aprovisionamiento. Puede personalizar o deshabilitar el CEIP mediante la conexión a la máquina virtual a través del escritorio remoto. A continuación, ejecute la utilidad **Informes de uso y errores de SQL Server** . Siga las instrucciones para deshabilitar los informes. 

Para más información acerca de la recopilación de datos, vea la [declaración de privacidad de SQL Server](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx). 

## <a name="next-steps"></a>Pasos siguientes

Para preguntas sobre los precios, consulte [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server en Azure) y la página [Precios de máquinas virtuales Windows ](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Seleccione la edición de destino de SQL Server en la lista **SO/software**. A continuación, vea los precios de máquinas virtuales de tamaños diferentes.

¿Más preguntas? En primer lugar, consulte las [Preguntas más frecuentes sobre SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-faq.md). Pero también agregue sus preguntas o comentarios en la parte inferior de cualquiera de los temas de las máquinas virtuales de SQL para interactuar con Microsoft y la comunidad.

