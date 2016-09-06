<properties
	pageTitle="Información general de SQL Server en máquinas virtuales de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo ejecutar las ediciones completas de SQL Server en máquinas virtuales de Azure. Obtenga vínculos directos a todas las imágenes de máquinas virtuales de SQL Server y al contenido relacionado."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/29/2016"
	ms.author="jroth"/>

# Información general de SQL Server en máquinas virtuales de Azure

Este tema describe las opciones para ejecutar SQL Server en máquinas virtuales de Azure, junto con [vínculos a imágenes del portal](#option-1-deploy-a-sql-vm-per-minute-licensing) e información general sobre las [tareas comunes](#manage-your-sql-vm).

>[AZURE.NOTE] Si ya está familiarizado con SQL Server y solo desea ver cómo implementar una máquina virtual de SQL Server, consulte [Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md).

## Información general
Puede que sea un administrador de base de datos que desea mover las cargas de trabajo locales de SQL Server a la nube. O es posible que sea un desarrollador que está considerando la funcionalidad de base de datos relacional de SQL Server para la aplicación de Azure. ¿Cuál es la ventaja de ejecutar las cargas de trabajo de SQL Server en máquinas virtuales de Azure? El siguiente vídeo informativo analiza las ventajas y ofrece una introducción técnica.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Evaluación de las ventajas

Antes de comenzar, primero evalúe qué es lo que gana usando SQL Server en máquinas virtuales de Azure.

Si va a mover otras cargas de trabajo a Azure, como una aplicación empresarial, tiene sentido mover también cualquier base de datos SQL Server dependiente a Azure, para de esta forma mejorar el rendimiento. Pero el hospedaje de SQL Server en máquinas virtuales de Azure ofrece además otras ventajas. Por ejemplo, tiene acceso automático a varios centros de datos para una recuperación ante desastres y una presencia global. Para obtener una lista completa de escenarios y ventajas, consulte la [página de producto SQL Server en máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [AZURE.NOTE] Cuando realice la evaluación de SQL Server en máquinas virtuales de Azure, revise también las otras opciones de almacenamiento y SQL en Azure, como [Base de datos SQL](../sql-database/sql-database-technical-overview.md), [Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md), y [Stretch Database] (.. / sql-server-stretch-database/sql-server-stretch-database-overview.md). Para ver una comparación completa, consulte [Selección de una opción de SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en máquinas virtuales de Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

Una vez que haya decidido ejecutar SQL Server en máquinas virtuales de Azure, una de las primeras decisiones que tiene que tomar es si desea utilizar una imagen de máquina virtual que incluya los costos de licencia de SQL Server. La alternativa es usar la opción "traiga su propia licencia" (BYOL) y pagar por la máquina virtual. Las dos secciones siguientes describen estas opciones.

## Opción 1: Implementación de una máquina virtual de SQL (licencia por minuto)
En la tabla siguiente se ofrece una matriz de imágenes de SQL Server disponibles en la galería de máquinas virtuales. Haga clic en cualquier vínculo para empezar a crear una nueva máquina virtual de SQL con la versión, la edición y el sistema operativo que especifique. Todas las imágenes incluyen los [precios de las licencias de SQL Server para máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

El tutorial, [Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md), ofrece instrucciones detalladas. Además, revise [Procedimientos recomendados de rendimiento para máquinas virtuales de SQL Server](virtual-machines-windows-sql-performance.md), que explica cómo seleccionar el tamaño de máquina adecuado y otras características disponibles durante el aprovisionamiento.

|Versión|Sistema operativo|Edition|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## Opción 2: Implementación de una máquina virtual de SQL (BYOL)
La otra opción es "traiga su propia licencia" (BYOL). En este escenario, solo paga por la máquina virtual sin ningún cargo adicional de licencia de SQL Server. Para utilizar su propia licencia, use la matriz de los sistemas operativos, ediciones y versiones de SQL Server que puede ver a continuación. En el portal, los nombres de las imágenes van precedidos de **{BYOL}**.

> [AZURE.IMPORTANT] Para poder utilizar imágenes de máquina virtual BYOL, tiene que tener un Contrato Enterprise con [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/). También necesita una licencia válida para la versión y edición de SQL Server que desea utilizar. Tiene que [proporcionar la información necesaria de BYOL a Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) dentro de un plazo de **10** días a partir del aprovisionamiento de la máquina virtual.

Las instrucciones del tutorial [Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md) son aplicables, pero tiene que usar una de las siguientes opciones de imagen de **BYOL**. Además, revise [Procedimientos recomendados de rendimiento para máquinas virtuales de SQL Server](virtual-machines-windows-sql-performance.md), que explica cómo seleccionar el tamaño de máquina adecuado y otras características disponibles durante el aprovisionamiento.

|Versión|Sistema operativos|Edition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

## Administración de la máquina virtual de SQL
Después de aprovisionar la máquina virtual de SQL Server, hay varias tareas de administración opcionales. En algunos aspectos, SQL Server se configura y se administra exactamente igual que si lo hiciera en un entorno local. Pero algunas tareas son específicas de Azure. Las secciones siguientes resaltan algunas de estas áreas con vínculos a más información.

### Migración de los datos

Si tiene una base de datos existente, debe moverla a la máquina virtual de SQL recién aprovisionada. Para ver una lista de las opciones de migración e instrucciones, consulte [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

### Configuración para alta disponibilidad

Si requiere alta disponibilidad, considere la posibilidad de configurar grupos de disponibilidad de SQL Server. Esto implica varias máquinas virtuales de Azure en una red virtual. El Portal de Azure dispone de una plantilla que define esta configuración. Para más información, consulte [Configuración de un grupo de disponibilidad AlwaysOn en máquinas virtuales de Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Si desea configurar manualmente el grupo de disponibilidad y el agente de escucha asociado, consulte [Configuración de un grupo de disponibilidad AlwaysOn en máquinas virtuales de Azure Resource Manager (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para otras consideraciones sobre alta disponibilidad, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-high-availability-dr.md).

### Realización de una copia de seguridad de los datos
Las máquinas virtuales de Azure pueden aprovechar la [Copia de seguridad automatizada](virtual-machines-windows-sql-automated-backup.md), que crea de forma regular copias de seguridad de la base de datos en el almacenamiento de blobs. También puede utilizar esta técnica manualmente. Para más información, consulte [Uso de Almacenamiento de Azure para la copia de seguridad y la restauración de SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obtener información general sobre las opciones de copia de seguridad y restauración, consulte [Copias de seguridad y restauración para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-backup-recovery.md).

### Automatización de las actualizaciones
Las máquinas virtuales de Azure pueden usar las [revisiones automatizadas](virtual-machines-windows-sql-automated-patching.md) para programar una ventana de mantenimiento para la instalación automática de actualizaciones de SQL Server y Windows.

### Programa para la mejora de la experiencia del usuario (CEIP)
De manera predeterminada, el Programa para la mejora de la experiencia del cliente (CEIP) está habilitado. No se trata de una tarea de administración, a menos que desee deshabilitar el CEIP después del aprovisionamiento. Puede personalizar o deshabilitar el CEIP mediante la conexión a la máquina virtual a través del escritorio remoto. A continuación, ejecute la utilidad **Informes de uso y errores de SQL Server**. Siga las instrucciones para deshabilitar los informes.

## Pasos siguientes
[Explore la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para SQL Server en máquinas virtuales de Azure.

¿Más preguntas? En primer lugar, consulte las [Preguntas más frecuentes sobre SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-faq.md). Pero también agregue sus preguntas o comentarios en la parte inferior de cualquiera de los temas de las máquinas virtuales de SQL para interactuar con Microsoft y la comunidad.

<!---HONumber=AcomDC_0831_2016-->