<properties 
	pageTitle="Entornos de prueba de nube híbrida de Azure" 
	description="Acceda a los temas fundamentales que describen cómo crear entornos de prueba que se pueden utilizar para desarrollo/prueba o para prueba de concepto en la nube híbrida de Azure." 
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
	ms.date="04/07/2015" 
	ms.author="josephd"/>

# Entornos de prueba de nube híbrida de Azure

Para desarrollo/prueba o una prueba de concepto, los entornos de prueba de nube híbrida usan la conexión local a Internet y una de las direcciones IP públicas y le guiarán durante el proceso de configuración de una red virtual de Azure (VNet) entre locales y en funcionamiento. Cuando haya finalizado, puede hacer pruebas y desarrollo de aplicaciones, experimentar con cargas de trabajo de TI simplificadas y medir el rendimiento de una conexión de red privada virtual (VPN) de sitio a sitio con respecto a su ubicación en Internet.

## Configuración básica de nube híbrida

La [configuración básica de nube híbrida](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) consta de:

- Una red local simplificada con cuatro máquinas virtuales (un controlador de dominio, un servidor de aplicaciones, un equipo cliente y un dispositivo VPN que ejecutan Windows Server y enrutamiento y acceso remoto)
- Una red virtual de Azure con un controlador de dominio de réplica
- Una conexión VPN de sitio a sitio

## Granja de servidores de intranet de SharePoint en una nube híbrida

La [granja de servidores de intranet de SharePoint en un entorno de prueba de nube híbrida](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) agrega un servidor SQL Server 2014 y un servidor de SharePoint Server 2013 a la configuración básica de nube híbrida. Esto crea una granja de servidores de SharePoint de dos capas a la que puede tener acceso desde el equipo cliente en la red local simplificada.

## Aplicación de línea de negocio (LOB) basada en web en una nube híbrida

La [aplicación de LOB basada en web en un entorno de pruebas de nube híbrida](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md) agrega un servidor SQL Server 2014 y un servidor de Servicios de Internet Information Server (IIS) para la configuración básica de nube híbrida. Crea la infraestructura en la que puede implementar y probar una aplicación LOB en capas basada en web.

## Servidor de sincronización de directorios (DirSync) de Office 365 en una nube híbrida

El [servidor de sincronización de directorios de Office 365 en un entorno de pruebas de nube híbrida](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md) agrega un servidor de sincronización de directorios a la configuración básica de nube híbrida y muestra la sincronización de directorios de Office 365 con la sincronización de contraseña en una suscripción de prueba de Office 365.

## Entorno de prueba de nube híbrida simulada

Para aquellas organizaciones y usuarios sin acceso fácil a una conexión directa a Internet y a la dirección IP pública, el [entorno de prueba de nube híbrida simulada](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) refuerza la red local simplificada en una red virtual de Azure independiente y después conecta las dos redes virtuales con una conexión VPN de red virtual a red virtual.


## Recursos adicionales

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[PDF del plano de arquitectura de aplicaciones de línea de negocio 3D](http://download.microsoft.com/download/2/C/8/2C8EB75F-AC45-4A79-8A63-C1800C098792/MS_Arch_LOB_App_3D_pdf.pdf)

[Implementación de la sincronización de directorios de Office 365 (DirSync) en Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)


<!--HONumber=52-->
 