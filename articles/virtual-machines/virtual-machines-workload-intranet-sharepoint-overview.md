<properties
	pageTitle="Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure"
	description="Puede implementar SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure en cinco fases."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="josephd"/>

# Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure

Este tema contiene vínculos a las instrucciones detalladas para implementar una granja de servidores de SharePoint 2013 solo de intranet con grupos de disponibilidad AlwaysOn de SQL Server con administración de servicios de Azure. La granja de servidores contiene estos equipos:

- Dos servidores web de SharePoint
- Dos servidores de aplicaciones de SharePoint
- Dos servidores de bases de datos
- Un servidor de nodos de mayoría de clúster
- Dos controladores de dominio

Esta es la configuración, con los nombres de marcador de posición para cada servidor:

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

Dos equipos para cada rol garantizan la alta disponibilidad. Todas las máquinas virtuales están en una sola región. Cada grupo de máquinas virtuales para un rol específico está en su propio conjunto de disponibilidad.

Esta configuración se implementa en las siguientes fases:

- [Fase 1: Configuración de Azure](virtual-machines-workload-intranet-sharepoint-phase1.md). Cree una cuenta de almacenamiento, servicios en la nube y una red virtual entre locales.
- [Fase 2: Configuración de los controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md). Cree y configure los controladores de dominio de los Servicios de dominio de Active Directory (AD DS) de réplica.
- [Fase 3: Configuración de la infraestructura de SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md). Cree y configure las máquinas virtuales de SQL Server, prepárelas para su uso con SharePoint y cree el clúster.
- [Fase 4: Configuración de los servidores de SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md). Cree y configure las cuatro máquinas virtuales de SharePoint.
- [Fase 5: Creación del grupo de disponibilidad y adición de las bases de datos de SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md). Prepare las bases de datos y cree un grupo de disponibilidad AlwaysOn de SQL Server.

Esta implementación de SharePoint con AlwaysOn de SQL Server está diseñada para acompañar la [infografía de SharePoint con AlwaysOn de SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788) e incorporar las últimas recomendaciones.

Esta configuración es una guía preceptiva fase por fase para una arquitectura predefinida destinada para crear una granja de servidores de SharePoint de intranet funcional y de alta disponibilidad en los servicios de infraestructura de Azure. Para obtener orientación sobre la arquitectura para implementar SharePoint 2013 en Azure, consulte [Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Tenga en cuenta lo siguiente:

- Si es un implementador experimentado de SharePoint, no dude en adaptar las instrucciones indicadas en las fases de la 3 a la 5 para crear la granja que mejor se adapte a sus necesidades.
- Si ya tiene una implementación en la nube híbrida de Azure, puede adaptar u omitir las instrucciones de las fases 1 y 2 para hospedar la nueva granja de servidores de SharePoint en la subred adecuada.
- Todos los servidores se encuentran en una sola subred en la red virtual de Azure. Si desea proporcionar seguridad adicional equivalente al aislamiento de subred, puede usar los [grupos de seguridad de red](https://msdn.microsoft.com/library/azure/dn848316.aspx).

Para compilar un entorno de desarrollo/pruebas o una prueba de concepto de esta configuración, vea [Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Para obtener información adicional acerca de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server, vea [Configurar grupos de disponibilidad AlwaysOn de SQL Server 2012 para SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Paso siguiente

Para iniciar la configuración de esta carga de trabajo, vaya a [Fase 1: Configuración de Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).


## Recursos adicionales

[Infografía de SharePoint con AlwaysOn de SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabajo de servicios de infraestructura de Azure: aplicación de línea de negocio de alta disponibilidad](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=August15_HO8-->