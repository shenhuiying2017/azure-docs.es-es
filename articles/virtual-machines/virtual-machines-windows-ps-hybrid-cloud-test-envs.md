<properties
	pageTitle="Entornos de prueba de nube híbrida de Azure | Microsoft Azure"
	description="Busque los artículos que describen cómo crear pruebas y desarrollo o entornos de profesionales de TI de prueba de concepto para la nube híbrida basada en Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="04/01/2016"
	ms.author="josephd"/>

# Entornos de prueba de nube híbrida de Azure

Para desarrollo/prueba o una prueba de concepto, los entornos de prueba de nube híbrida usan la conexión local a Internet y una de las direcciones IP públicas y le guiarán durante el proceso de configuración de una red virtual de Azure (VNet) entre locales y en funcionamiento. Cuando haya finalizado, puede desarrollar y probar aplicaciones, experimentar con cargas de trabajo de TI simplificadas y medir el rendimiento de una conexión de red privada virtual (VPN) de sitio a sitio con respecto a su ubicación en Internet.

## Configuración básica de nube híbrida

La [configuración básica de nube híbrida](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md) consta de:

- Una red local simplificada con cuatro máquinas virtuales (un controlador de dominio, un servidor de aplicaciones, un equipo cliente y un dispositivo VPN que ejecutan Windows Server y enrutamiento y acceso remoto)
- Una red virtual de Azure con un controlador de dominio de réplica
- Una conexión VPN de sitio a sitio

## Granja de servidores de intranet de SharePoint en una nube híbrida

La [granja de servidores de intranet de SharePoint en un entorno de prueba de nube híbrida](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md) agrega un servidor SQL Server 2014 y un servidor de SharePoint Server 2013 a la configuración básica de nube híbrida. Esto crea una granja de servidores de SharePoint de dos capas a la que puede tener acceso desde el equipo cliente en la red local simplificada.

## Aplicación de línea de negocio (LOB) basada en web en una nube híbrida

La [aplicación de LOB basada en web en un entorno de pruebas de nube híbrida](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) agrega un servidor SQL Server 2014 y un servidor de Servicios de Internet Information Server (IIS) para la configuración básica de nube híbrida. Crea la infraestructura en la que puede implementar y probar una aplicación LOB en capas basada en web.

## Servidor de sincronización de directorios (DirSync) de Office 365 en una nube híbrida

El [servidor de sincronización de directorios de Office 365 en un entorno de pruebas de nube híbrida](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md) agrega un servidor de sincronización de directorios a la configuración básica de nube híbrida y muestra la sincronización de directorios de Office 365 con la sincronización de contraseña en una suscripción de prueba de Office 365.

## Entorno de prueba de nube híbrida simulada

Para aquellas organizaciones y usuarios sin acceso fácil a una conexión directa a Internet y a la dirección IP pública, el [entorno de prueba de nube híbrida simulada](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md) refuerza la red local simplificada en una red virtual de Azure independiente y después conecta las dos redes virtuales con una conexión VPN de red virtual a red virtual.


## Paso siguiente

- Obtenga información sobre las [directrices de implementación](virtual-machines-linux-infrastructure-service-guidelines.md) para diseñar una implementación personalizada de desarrollo, prueba o producción en los servicios de infraestructura de Azure.

<!---HONumber=AcomDC_0601_2016-->