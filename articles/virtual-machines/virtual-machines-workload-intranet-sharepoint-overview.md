<properties
	pageTitle="Implementación de una granja de SharePoint Server 2013 | Microsoft Azure"
	description="Implemente una granja de alta disponibilidad de SharePoint Server 2013 con los grupos de disponibilidad AlwaysOn de SQL Server en Azure en cinco fases."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Este tema contiene vínculos a las instrucciones detalladas para implementar una granja de servidores de SharePoint 2013 solo de intranet con grupos de disponibilidad AlwaysOn de SQL Server. La granja de servidores contiene estos equipos:

- Dos servidores web de SharePoint
- Dos servidores de aplicaciones de SharePoint
- Dos servidores de bases de datos
- Un servidor de nodos de mayoría de clúster
- Dos controladores de dominio

Esta es la configuración, con los nombres de marcador de posición para cada servidor.

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

Dos equipos para cada rol garantizan la alta disponibilidad. Todas las máquinas virtuales están en una sola región. Cada grupo de máquinas virtuales para un rol específico está en su propio conjunto de disponibilidad.

Esta configuración se implementa en las siguientes fases:

- [Fase 1: Configuración de Azure](virtual-machines-workload-intranet-sharepoint-phase1.md). Cree una cuenta de almacenamiento, conjuntos de disponibilidad y una red entre locales.
- [Fase 2: Configuración de los controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md). Cree y configure los controladores de dominio de los Servicios de dominio de Active Directory (AD DS) de réplica.
- [Fase 3: Configuración de la infraestructura de SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md). Cree y configure las máquinas virtuales de SQL Server, prepárelas para su uso con SharePoint y cree el clúster.
- [Fase 4: Configuración de los servidores de SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md). Cree y configure las cuatro máquinas virtuales de SharePoint.
- [Fase 5: Creación del grupo de disponibilidad y adición de las bases de datos de SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md). Prepare las bases de datos y cree un grupo de disponibilidad AlwaysOn de SQL Server.

Esta implementación de SharePoint con AlwaysOn de SQL Server está diseñada para acompañar la [infografía de SharePoint con AlwaysOn de SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788) e incorporar las últimas recomendaciones.

Esta configuración es una guía preceptiva fase por fase para una arquitectura predefinida destinada para crear una granja de servidores de SharePoint de intranet funcional y de alta disponibilidad en los servicios de infraestructura de Azure. Para obtener orientación sobre la arquitectura para implementar SharePoint 2013 en Azure, consulte [Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Tenga en cuenta lo siguiente:

- Si es un implementador experimentado de SharePoint, no dude en adaptar las instrucciones indicadas en las fases de la 3 a la 5 para crear la granja que mejor se adapte a sus necesidades.
- Si ya tiene una implementación en la nube híbrida de Azure, puede adaptar u omitir las instrucciones de las fases 1 y 2 para hospedar la nueva granja de servidores de SharePoint en la subred adecuada.
- Todos los servidores se encuentran en una sola subred en la red virtual de Azure. Si quiere ofrecer seguridad adicional equivalente al aislamiento de subred, puede usar los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).

Para compilar un entorno de desarrollo/pruebas o una prueba de concepto de esta configuración, vea [Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Para obtener información adicional acerca de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server, vea [Configurar grupos de disponibilidad AlwaysOn de SQL Server 2012 para SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

> [AZURE.NOTE]Microsoft ha publicado la versión de vista previa de TI de SharePoint Server 2016. Para facilitar la instalación y prueba de esta vista previa, puede utilizar una imagen de la galería de máquinas virtuales de Azure con la vista previa de TI de SharePoint Server 2016 y sus requisitos previos preinstalados. Para obtener más información, consulte [Prueba de la vista previa de TI de SharePoint 2016 en Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Paso siguiente

- Inicie la configuración de esta carga de trabajo con la [Fase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

<!---HONumber=AcomDC_1217_2015-->