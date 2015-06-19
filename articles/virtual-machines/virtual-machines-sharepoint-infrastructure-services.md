<properties 
	pageTitle="Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure" 
	description="Obtener acceso a los temas que describen cómo configurar una granja de servidores de desarrollo/prueba o de producción de SharePoint 2013 en los servicios de infraestructura de Azure." 
	documentationCenter="" 
	services="virtual-machines"
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="josephd"/>

# Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure

Configure la primera o siguiente granja de servidores de desarrollo/prueba de SharePoint en los servicios de infraestructura de Microsoft Azure, donde puede aprovecharse de la facilidad de configuración y de la capacidad ampliar rápidamente la granja de servidores para incluir la nueva capacidad o la optimización de funcionalidad clave.

## Granja de servidores de desarrollo/prueba básica de SharePoint 

Puede utilizar la plantilla [Granja de servidores de SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) en el Portal de vista previa de Azure para crear una granja de servidores de desarrollo/prueba básica para un sitio web de SharePoint destinado a Internet.

El entorno creado automáticamente consta de tres servidores para un controlador de dominio, un servidor SQL Server y el servidor de SharePoint en una red virtual de Azure solo en la nube.

## Granja de servidores de desarrollo/prueba de SharePoint de alta disponibilidad

También puede utilizar la plantilla [Granja de servidores de SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) en el Portal de vista previa de Azure para crear una granja de servidores de desarrollo/prueba de SharePoint de alta disponibilidad para un sitio web de SharePoint destinado a Internet.

El entorno creado automáticamente que consta de nueve servidores en una red virtual de Azure solo en la nube: dos para los controladores de dominio, tres para un clúster de SQL Server, dos servidores de SharePoint de capa de aplicación y dos servidores de SharePoint de capa de web.

## Granja de servidores de desarrollo/prueba de nube híbrida

Con la [granja de servidores de intranet de SharePoint en entorno de prueba o desarrollo de nube híbrida](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), se crea una configuración de nube híbrida simulada que hospeda una granja de servidores de SharePoint simple, de dos capas, que puede utilizar para probar una granja de servidores de SharePoint de intranet hospedada en Azure desde su ubicación en Internet.

## Granja de servidores de producción de SharePoint de intranet, de alta disponibilidad

Con [Implementación de SharePoint 2013 con grupos de disponibilidad AlwaysOn de SQL Server en Azure](https://msdn.microsoft.com/library/dn275959.aspx), cree una granja de servidores de SharePoint Server 2013 de intranet en Azure.

## Recursos adicionales

[SharePoint Server en los servicios de infraestructura de Azure](https://msdn.microsoft.com/library/dn275955.aspx)

[Planeación para SharePoint 2013 en los servicios de infraestructura de Azure](https://msdn.microsoft.com/library/dn275958.aspx)

[Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=52-->
 