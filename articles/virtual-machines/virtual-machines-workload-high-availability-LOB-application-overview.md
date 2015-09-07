<properties 
	pageTitle="Implementación de una aplicación de línea de negocio | Microsoft Azure"
	description="Implemente una aplicación de línea de negocio de alta disponibilidad basada en web con grupos de disponibilidad AlwaysOn de SQL Server en Azure en cinco fases."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# Implementación de una aplicación de línea de negocio de alta disponibilidad en Azure

Este artículo contiene vínculos a las instrucciones detalladas para implementar una aplicación de línea de negocio basada en web solo de intranet con grupos de disponibilidad AlwaysOn de SQL Server en servicios de infraestructura de Azure. La aplicación se hospeda en estos equipos:

- Dos servidores web
- Dos servidores de bases de datos
- Un servidor de nodos de mayoría de clúster
- Dos controladores de dominio

Esta es la configuración, con los nombres de marcador de posición para cada servidor.

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
Dos equipos como mínimo para cada rol garantizan la alta disponibilidad. Todas las máquinas virtuales están en una sola ubicación de Azure (también llamada región). Cada grupo de máquinas virtuales para un rol específico está en su propio conjunto de disponibilidad.

Esta configuración se implementa en las siguientes fases:

- [Fase 1: Configuración de Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md). Cree una red virtual entre locales, conjuntos de disponibilidad y cuentas de almacenamiento.
- [Fase 2: Configuración de controladores de dominio](virtual-machines-workload-high-availability-LOB-application-phase2.md). Cree y configure los controladores de dominio de los Servicios de dominio de Active Directory (AD DS) de réplica.
- [Fase 3: Configuración de la infraestructura de SQL Server](virtual-machines-workload-high-availability-LOB-application-phase3.md). Cree y configure las máquinas virtuales que ejecutan SQL Server, cree el clúster y habilite los grupos de disponibilidad AlwaysOn de SQL Server.
- [Fase 4: Configuración de servidores web](virtual-machines-workload-high-availability-LOB-application-phase4.md). Cree y configure las dos máquinas virtuales de servidor web.
- [Fase 5: Adición de las bases de datos de la aplicación a un grupo de disponibilidad AlwaysOn de SQL Server](virtual-machines-workload-high-availability-LOB-application-phase5.md). Prepare las bases de datos de la aplicación de línea de negocio y agréguelas a un grupo de disponibilidad AlwaysOn de SQL Server.

Esta implementación está diseñada para complementar el [Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664) e incorpora las últimas recomendaciones.

Se trata de una arquitectura preceptiva predefinida. Tenga en cuenta lo siguiente:

- Si es un implementador experimentado de aplicaciones de línea de negocio basadas en web, no dude en adaptar las instrucciones indicadas en las fases de la 3 a la 5 para crear la infraestructura de la aplicación que mejor se adapte a sus necesidades. 
- Si ya cuenta con una implementación en la nube híbrida de Azure, puede adaptar u omitir las instrucciones de las fases 1 y 2 con el fin de hospedar las máquinas virtuales para la nueva aplicación en la subred adecuada.
- Todos los servidores se encuentran en una sola subred en la red virtual de Azure. Si desea proporcionar seguridad adicional equivalente al aislamiento de subred, puede usar los [grupos de seguridad de red](../virtual-networks/virtual-networks-nsg.md).

Para compilar un entorno de desarrollo o pruebas, o una prueba de concepto de esta configuración, vea [Configuración de una aplicación de LOB basada en web en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

Para obtener más información sobre el diseño de cargas de trabajo de TI en Azure, en [Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md).

## Paso siguiente

Para iniciar la configuración de esta carga de trabajo, vaya a [Fase 1: Configuración de Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md).

## Recursos adicionales

[Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664)

[Configuración de una aplicación de LOB basada en web en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabajo de servicios de infraestructura de Azure: granja de SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO9-->