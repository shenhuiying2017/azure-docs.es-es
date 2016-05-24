<properties
	pageTitle="Información general de SQL Server en máquinas virtuales | Microsoft Azure"
	description="Introducción a las ejecución de bases de datos de SQL Server en la nube en Máquinas virtuales de Azure. El modelo de infraestructura como servicio (IaaS) permite ejecutar cargas de trabajo de SQL Server en Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/10/2016"
	ms.author="jroth"/>

# Información general sobre SQL Server en máquinas virtuales de Azure

[SQL Server ejecutándose en Máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite hospedar bases de datos de SQL Server en la nube. Por ejemplo, puede migrar una base de datos local a una máquina virtual de Azure preconfigurada con Windows Server 2012 R2 y SQL Server 2014 Enterprise Edition. Pero hay muchos otros escenarios posibles, como las configuraciones en varias máquinas que admiten alta disponibilidad o las arquitecturas híbridas con conectividad a redes locales.

Para una introducción excelente, vea el vídeo [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016) (VM de Azure es la mejor plataforma para SQL Server 2016).

## Ofertas de SQL

La ejecución de SQL Server en una máquina virtual de Azure es una opción para el almacenamiento de datos relacionales en Azure. En la tabla siguiente se resumen las diferentes ofertas.

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Oferta de SQL | Descripción |
|---:|---|---|
|![SQL Server en Máquinas virtuales de Azure](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[SQL Server en Máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Ejecute SQL Server en Máquinas virtuales de Azure. Administre directamente la máquina virtual y ejecute la base de datos en las versiones comerciales de SQL Server. |
|![Base de datos SQL](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[Base de datos SQL](https://azure.microsoft.com/services/sql-database/)|Use el servicio de Base de datos SQL para acceder y escalar la base de datos sin tener que administrar la infraestructura subyacente.|
|![Almacenamiento de datos SQL](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[Almacenamiento de datos SQL](https://azure.microsoft.com/services/sql-data-warehouse/)|Use Almacenamiento de datos SQL de Azure para procesar grandes cantidades de datos relacionales y no relacionales. Proporciona funcionalidades de almacenamiento de datos escalable, como si fuera un servicio.|
|![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/)|Ajuste dinámicamente los datos transaccionales locales de Microsoft SQL Server 2016 en Azure.|

>[AZURE.NOTE] Para ver una comparación detallada entre las máquinas virtuales de SQL y Base de datos SQL, consulte [Selección de una opción de SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en Máquinas virtuales de Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

## Implementación de una máquina virtual con SQL Server

Para crear una máquina virtual de SQL Server en Azure, primero debe obtener una suscripción a la plataforma de Azure. Puede adquirir una suscripción de Azure en [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/). Para probarla de manera gratuita, visite la [evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

Cuando se haya registrado para una suscripción, la manera más sencilla de implementar una máquina virtual con SQL Server en Azure es [aprovisionar una imagen de la galería de máquinas con SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md). Esas imágenes incluyen licencias de SQL Server en el precio de la máquina virtual.

Es importante tener en cuenta que hay dos modelos para crear y administrar máquinas virtuales de Azure: clásica y Administrador de recursos. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para obtener más información, vea [Descripción de la implementación del Administrador de recursos y la implementación clásica](../resource-manager-deployment-model.md). Cada tema debe indicar claramente su modelo de destino, a menos que se aplique tanto al clásico como al de Resource Manager, como este artículo.

## Elección de una imagen de máquina virtual de SQL
En la tabla siguiente se ofrece una matriz de imágenes de SQL Server disponibles en la galería de máquinas virtuales. Haga clic en cualquiera de los vínculos de la tabla para crear una máquina virtual para esa versión, edición y sistema operativo.

|Versión de SQL Server|Sistema operativos|Edición de SQL Server|
|---|---|---|
|**SQL Server 2016 RC**|Windows Server 2012 R2|[Evaluación](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rc3evaluationwindowsserver2012r2/)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1webwindowsserver2012r2/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1expresswindowsserver2012r2/)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2expresswindowsserver2012/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3enterprisewindowsserver2008r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3standardwindowsserver2008r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3webwindowsserver2008r2/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3expresswindowsserver2012/)|

>[AZURE.NOTE] De manera predeterminada, el Programa para la mejora de la experiencia del cliente (CEIP) está habilitado. Si es necesario, el CEIP se puede personalizar o deshabilitar después de aprovisionar la máquina virtual. Conéctese a la máquina virtual con Escritorio remoto y ejecute la utilidad **Informes de uso y errores de SQL Server**.

Además de estas imágenes preconfiguradas, también puede [crear una máquina virtual de Azure](virtual-machines-windows-hero-tutorial.md) sin que SQL Server esté preinstalado. Puede instalar cualquier instancia de SQL Server de la que tenga licencia. Migre su licencia a Azure para ejecutar SQL Server en una máquina virtual de Azure mediante la [Movilidad de licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/). En este escenario, solo se paga por los [costes](https://azure.microsoft.com/pricing/details/virtual-machines/) de almacenamiento y proceso de Azure asociados a la máquina virtual.

Para determinar la mejor configuración de la máquina virtual para la imagen de SQL Server, revise [Prácticas recomendadas para mejorar el rendimiento de SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md). Para cargas de trabajo de producción, **DS3** y **DS2** son los tamaños de máquina virtual mínimos recomendados para las ediciones SQL Server Enterprise y Standard, respectivamente.

## Migración de los datos

Una vez puesta en marcha la máquina virtual de SQL Server, podrá migrar bases de datos existentes al equipo. Para ver una lista de las opciones de migración y una guía al respecto, consulte [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

## Alta disponibilidad

Si requiere alta disponibilidad, considere la posibilidad de configurar grupos de disponibilidad de SQL Server. Esto implica varias máquinas virtuales de Azure en una red virtual. El Portal de Azure dispone de una plantilla que define esta configuración. Para más información, consulte [Configuración de un grupo de disponibilidad AlwaysOn en máquinas virtuales de Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

Si desea configurar manualmente un grupo de disponibilidad y el agente de escucha asociado, consulte [Configuración de Grupos de disponibilidad AlwaysOn en la máquina virtual de Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para otras consideraciones sobre alta disponibilidad, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-high-availability-dr.md).

## Copia de seguridad y restauración
Con las bases de datos locales, Azure puede actuar como un centro de datos secundario para almacenar los archivos de copia de seguridad de SQL Server. Para obtener información general de las opciones de copia de seguridad y restauración, consulte [Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-backup-recovery.md).

[La copia de seguridad de SQL Server a URL](https://msdn.microsoft.com/library/dn435916.aspx) almacena los archivos de copia de seguridad de Azure en el almacenamiento de blobs de Azure. [La copia de seguridad administrada de SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) le permite programar la copia de seguridad y la retención en Azure. Estos servicios pueden utilizarse con instancias locales de SQL Server o con SQL Server que se ejecuta en máquinas virtuales de Azure. Las máquinas virtuales de Azure también pueden sacar partido de la [copia de seguridad automatizada](virtual-machines-windows-classic-sql-automated-backup.md) y de las [revisiones automatizadas](virtual-machines-windows-classic-sql-automated-patching.md) para SQL Server.

## Pasos siguientes

En primer lugar, [cree su propia máquina virtual con SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md).

Luego, consulte [prácticas recomendadas para mejorar el rendimiento](virtual-machines-windows-sql-performance.md) y [técnicas de migración](virtual-machines-windows-migrate-sql.md) cuando considere la posibilidad de mover las cargas de trabajo de SQL Server a Máquinas virtuales de Azure.

Si tiene más dudas sobre SQL Server en Máquinas virtuales de Azure, consulte [Preguntas más frecuentes sobre SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-faq.md). O bien, agregue sus comentarios a la parte inferior de cualquiera de los temas de las máquinas virtuales de SQL para interactuar con Microsoft y la comunidad.

<!---HONumber=AcomDC_0511_2016-->