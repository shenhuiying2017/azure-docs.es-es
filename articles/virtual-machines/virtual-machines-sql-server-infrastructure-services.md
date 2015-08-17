<properties 
	pageTitle="SQL Server en Máquinas virtuales de Azure" 
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
	ms.date="07/17/2015"
	ms.author="jroth"/>

# SQL Server en Máquinas virtuales de Azure

## Información general
Puede hospedar [SQL Server en Máquinas virtuales Azure][sqlvmlanding] en una variedad de configuraciones, que van desde un servidor de base de datos única para una configuración de varios equipo mediante grupos de disponibilidad AlwaysOn y red Virtual de Azure.

> [AZURE.NOTE]La ejecución de SQL Server en una máquina virtual de Azure es una opción para el almacenamiento de datos relacionales en Azure. También puede utilizar el servicio Base de datos SQL de Azure. Para obtener más información, consulte [Descripción de Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure][sqldbcompared].
 
## Implementar una instancia de SQL Server en una única máquina virtual
Cuando [crea una máquina virtual de Azure mediante el Portal de Azure][createvmportal] o la automatización, puede instalar cualquier instancia de SQL Server para la que tenga licencia. Sin embargo, debe realizar pasos adicionales para [configurar la conectividad][setupconnectivity] entre el equipo de SQL Server y los equipos cliente.
 
También puede instalar una de las muchas imágenes de máquinas virtuales de SQL Server de la galería. Esas imágenes incluyen licencias de SQL Server en el precio de la máquina virtual. Para obtener más información y una conectividad paso a paso, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure][provisionsqlvm].

Una vez puesta en marcha la máquina virtual de SQL Server, podrá migrar bases de datos existentes al equipo. Para obtener más información sobre la migración de la base de datos, consulte [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md).

## Implementar una configuración de alta disponibilidad con varias máquinas virtuales
Puede lograr una alta disponibilidad para SQL Server mediante el uso de grupos de disponibilidad AlwaysOn de SQL Server. Esto implica varias máquinas virtuales de Azure en una red virtual. El Portal de vista previa de Azure dispone de una plantilla que define esta configuración. Para obtener más información, consulte [Oferta de AlwaysOn de SQL Server en la Galería del Portal de Microsoft Azure][sqlalwaysonportal]. O bien puede [configurar manualmente un grupo de disponibilidad AlwaysOn][sqlalwaysonmanual]. Para otras consideraciones sobre alta disponibilidad, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure][sqlhadr].

## Ejecutar Business intelligence, almacenamiento de datos y cargas de trabajo OLTP en Azure   
Puede ejecutar cargas de trabajo comunes de SQL Server en Máquinas virtuales de Azure. SQL Server cuenta con varias imágenes optimizadas de máquinas virtuales disponible en la galería. Incluyen imágenes para [Business Intelligence][sqlbi], [almacenamiento de datos][sqldw] y [OLTP][sqloltp].

## Migración de los datos
Una vez puesta en marcha la máquina virtual de SQL Server, podrá migrar bases de datos existentes al equipo. Existen varias técnicas, pero el asistente para implementación en SQL Server Management Studio funciona bien en la mayoría de los escenarios. Para obtener una explicación de los escenarios y un tutorial del asistente, consulte [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md).

## Copia de seguridad y restauración
Con las bases de datos locales, Azure puede actuar como un centro de datos secundario para almacenar los archivos de copia de seguridad de SQL Server. [La copia de seguridad de SQL Server a URL][backupurl] almacena los archivos de copia de seguridad de Azure en el almacenamiento de blobs de Azure. [La copia de seguridad administrada de SQL Server][managedbackup] le permite programar la copia de seguridad y la retención en Azure. Estos servicios pueden utilizarse con instancias locales de SQL Server o con SQL Server que se ejecuta en máquinas virtuales de Azure. Las máquinas virtuales de Azure también pueden sacar partido de la [copia de seguridad automatizada][autobackup] y de las [revisiones automatizadas][autopatching] para SQL Server. Para obtener más información, consulte [Tareas de administración de SQL Server en Máquinas virtuales de Azure][managementtasks].

## Recursos:
[SQL Server en máquinas virtuales de Azure][sqlmsdnlanding]

[Aprovisionamiento de una máquina virtual de SQL Server en Azure][provisionsqlvm]

[Introducción a SQL Server en Máquinas virtuales de Azure][sqlvmgetstarted]

[Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md)

[Procedimientos recomendados para SQL Server en Máquinas virtuales de Azure][sqlperf]

[Consideraciones de seguridad para SQL Server en Máquinas virtuales de Azure][sqlsecurity]

  [sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
  [sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
  [createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
  [setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
  [provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
  [sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
  [sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
  [sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
  [sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
  [sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
  [sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
  [migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
  [backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
  [managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
  [autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
  [autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
  [managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
  [sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
  [sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
  [sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
  [sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
  [technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx

<!---HONumber=August15_HO6-->