<properties
	pageTitle="Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure"
	description="Busque artículos clave que describan cómo configurar una granja de servidores de desarrollo/prueba o de producción de SharePoint 2013 en los servicios de infraestructura de Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure

Configure la primera o siguiente granja de servidores de desarrollo/prueba de SharePoint en los servicios de infraestructura de Microsoft Azure, donde puede aprovecharse de la facilidad de configuración y de la capacidad ampliar rápidamente la granja de servidores para incluir la nueva capacidad o la optimización de funcionalidad clave.

## Granja de servidores de desarrollo/prueba básica de SharePoint

Para las máquinas virtuales creadas en la administración de servicios, use la función [Granja de servidores de SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) del Portal de vista previa de Azure para crear una granja de servidores de desarrollo/prueba básica para un sitio web de SharePoint orientado a Internet.

El entorno creado automáticamente consta de tres servidores para un controlador de dominio, un servidor SQL Server y el servidor de SharePoint en una red virtual de Azure solo en la nube.

Para crear una configuración similar con máquinas virtuales creadas en el Administrador de recursos, use una plantilla. Consulte [Implementar una granja de tres servidores de SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

## Granja de desarrollo/prueba de SharePoint de alta disponibilidad

Para las máquinas virtuales creadas en la administración de servicios, use la función [Granja de servidores de SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) del Portal de vista previa de Azure para crear una granja de servidores de desarrollo/prueba de SharePoint de alta disponibilidad para un sitio web de SharePoint orientado a Internet.

El entorno creado automáticamente que consta de nueve servidores en una red virtual de Azure solo en la nube: dos para los controladores de dominio, tres para un clúster de SQL Server, dos servidores de SharePoint de capa de aplicación y dos servidores de SharePoint de capa de web.

Para crear una configuración similar con máquinas virtuales creadas en el Administrador de recursos, use una plantilla. Consulte [Implementar una granja de nueve servidores de SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

## Granja de servidores de desarrollo/prueba de nube híbrida

Con la [granja de servidores de intranet de SharePoint en entorno de prueba o desarrollo de nube híbrida](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), se crea una configuración de nube híbrida simulada que hospeda una granja de servidores de SharePoint simple, de dos capas, que puede utilizar para probar una granja de servidores de SharePoint de intranet hospedada en Azure desde su ubicación en Internet.

Esta configuración usa máquinas virtuales creadas en la administración de servicios.

## Granja de servidores de producción de SharePoint de intranet de alta disponibilidad

Con la implementación de [SharePoint 2013 con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md), cree una granja de servidores de SharePoint Server 2013 de intranet en Azure.

Esta configuración usa máquinas virtuales creadas en la administración de servicios.

## Recursos adicionales

Consulte estos recursos para obtener información y configuraciones adicionales acerca de SharePoint en Azure:

- [Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

- [Sitios de Internet de Microsoft Azure mediante SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

- [Recuperación ante desastres de SharePoint Server 2013 en Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

- [Uso de Microsoft Azure Active Directory para la autenticación de SharePoint 2013](https://technet.microsoft.com/library/dn635311.aspx)

- [Implementación de la sincronización de directorios de Office 365 (DirSync) en Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=August15_HO6-->