<properties 
	pageTitle="Información general sobre SQL Server en máquinas virtuales de Azure" 
	description="Este artículo proporciona una descripción general de SQL Server que hospeda las Máquinas virtuales IaaS de Azure. Esto incluye vínculos a contenido en profundidad." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Información general sobre SQL Server en máquinas virtuales de Azure

## Información general
Puede hospedar [SQL Server en Máquinas virtuales Azure](http://azure.microsoft.com/services/virtual-machines/sql-server/) en una variedad de configuraciones, que van desde un servidor de base de datos única para una configuración de varios equipo mediante grupos de disponibilidad AlwaysOn y red Virtual de Azure. Este tema intenta señalar algunos de los mejores recursos para obtener una introducción a la ejecución de SQL Server en una máquina virtual de Azure.

>[AZURE.NOTE]La ejecución de SQL Server en una máquina virtual de Azure es una opción para el almacenamiento de datos relacionales en Azure. También puede utilizar el servicio Base de datos SQL de Azure. Para obtener más información, consulte [Descripción de Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).
 
## Implementar una instancia de SQL Server en una única máquina virtual

La manera más fácil de implementar una máquina virtual de SQL Server en Azure es [aprovisionar una imagen de la galería de máquinas de SQL Server en el Portal de administración de Azure](virtual-machines-provision-sql-server.md). Esas imágenes incluyen licencias de SQL Server en el precio de la máquina virtual.

Sin embargo, también se puede [crear una máquina virtual de Azure](virtual-machines-windows-tutorial.md) sin que SQL Server esté preinstalado. Puede instalar cualquier instancia de SQL Server de la que tenga licencia.

Durante las primeras fases del aprovisionamiento y configuración, las tareas comunes son:

- [Revisar los procedimientos recomendados para mejorar el rendimiento de SQL Server en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [Revisar los procedimientos recomendados para mejorar la seguridad de SQL Server en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/dn133147.aspx)
- [Configurar la conectividad](virtual-machines-sql-server-connectivity.md).

## Implementar una configuración de alta disponibilidad con varias máquinas virtuales

Puede lograr una alta disponibilidad para SQL Server mediante el uso de grupos de disponibilidad AlwaysOn de SQL Server. Esto implica varias máquinas virtuales de Azure en una red virtual. El Portal de vista previa de Azure dispone de una plantilla que define esta configuración. Para obtener más información, consulte [Oferta de AlwaysOn de SQL Server en la Galería del Portal de Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Si desea configurar manualmente un grupo de disponibilidad y el agente de escucha asociado, consulte los artículos siguientes:

- [Configuración de los grupos de disponibilidad AlwaysOn en Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Ampliación de los grupos de disponibilidad AlwaysOn locales a Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Para otras consideraciones sobre alta disponibilidad, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Ejecutar Business intelligence, almacenamiento de datos y cargas de trabajo OLTP en Azure   
Puede ejecutar cargas de trabajo comunes de SQL Server en Máquinas virtuales de Azure. SQL Server cuenta con varias imágenes optimizadas de máquinas virtuales disponible en la galería. Se incluyen las imágenes para:

- [Inteligencia empresarial](https://msdn.microsoft.com/library/azure/jj992719.aspx)
- [Almacenamiento de datos](https://msdn.microsoft.com/library/azure/dn387396.aspx)
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx)

## Migración de los datos

Una vez puesta en marcha la máquina virtual de SQL Server, podrá migrar bases de datos existentes al equipo. Existen varias técnicas, pero el asistente para implementación en SQL Server Management Studio funciona bien en la mayoría de los escenarios. Para obtener una explicación de los escenarios y un tutorial del asistente, consulte [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md).

## Copia de seguridad y restauración
Con las bases de datos locales, Azure puede actuar como un centro de datos secundario para almacenar los archivos de copia de seguridad de SQL Server. Para obtener información general de las opciones de copia de seguridad y restauración, consulte [Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-backup-and-restore.md).

[La copia de seguridad de SQL Server a URL](https://msdn.microsoft.com/library/dn435916.aspx) almacena los archivos de copia de seguridad de Azure en el almacenamiento de blobs de Azure. [La copia de seguridad administrada de SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) le permite programar la copia de seguridad y la retención en Azure. Estos servicios pueden utilizarse con instancias locales de SQL Server o con SQL Server que se ejecuta en máquinas virtuales de Azure. Las máquinas virtuales de Azure también pueden sacar partido de la [copia de seguridad automatizada](virtual-machines-sql-server-automated-backup.md) y de las [revisiones automatizadas](virtual-machines-sql-server-automated-patching.md) para SQL Server.

<!---HONumber=August15_HO8-->