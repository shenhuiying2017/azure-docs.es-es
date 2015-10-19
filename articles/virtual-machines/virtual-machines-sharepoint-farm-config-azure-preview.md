<properties
	pageTitle="Configuración de una granja de servidores de SharePoint | Microsoft Azure"
	description="Obtenga información acerca de la configuración predeterminada de las granjas de SharePoint cuando se usa la función Granja de servidores SharePoint del Portal de vista previa de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="josephd"/>


# Detalles de configuración de Granja de servidores SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de recursos con el modelo de implementación clásica.

Granja de servidores SharePoint es una característica del Portal de vista previa de Azure que crea automáticamente una granja preconfigurada de SharePoint Server 2013. Hay dos configuraciones de granja:

- Básica
- Alta disponibilidad

En las siguientes secciones se proporcionan detalles de configuración de cada granja.

Para obtener información adicional, consulte [Granja de servidores SharePoint](virtual-machines-sharepoint-farm-azure-preview.md).

## Granja básica de SharePoint

La granja de SharePoint básica consta de tres máquinas virtuales con esta configuración:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

A continuación se indican los detalles de configuración:

-	Suscripción de Azure: se especifica en la configuración inicial.
-	Nombres de dominio de Azure (conocidos también como servicios en la nube):se crean automáticamente nombres de dominio independientes para cada máquina virtual.
-	Cuenta de almacenamiento: Se especifica durante la configuración inicial.
-	Red virtual:
	-   Tipo: Solo para la nube
    -	Espacio de direcciones: 10.0.0.0/26

- Máquinas virtuales:
	-	*HostNamePrefix*-DC (controlador de dominio de AD DS)
	-	*HostNamePrefix*-SQL (servidor SQL Server 2014)
	-	*HostNamePrefix*-SP (servidor de SharePoint 2013)

- Controlador de dominio:
	-	Imagen de máquina virtual: Windows Server 2012 R2.
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño: A1 (predeterminado).
	-	Nombre de dominio: contoso.com (predeterminado).
	-	Nombre de cuenta de administrador de dominio: Se especifica durante la configuración inicial.
	-	Contraseña de cuenta de administrador de dominio: Se especifica durante la configuración inicial.

- Servidor de bases de datos
	-	Imagen de máquina virtual: SQL Server 2014 RTM Enterprise en Windows Server 2012 R2.
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño: A5 (predeterminado).
	-	Nombre de cuenta de acceso de la base de datos: Se especifica durante la configuración inicial.
	-	Contraseña de cuenta de acceso de la base de datos: Se especifica durante la configuración inicial.
	-	Nombre de cuenta de servicio de SQL Server: sqlservice (predeterminado).
	-	Contraseña de cuenta de servicio de SQL Server: Se especifica durante la configuración inicial.

- Servidor SharePoint
	-	Imagen de máquina virtual: Versión de evaluación de SharePoint Server 2013.
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño: A2 (predeterminado).
	-	Nombre de cuenta de granja de SharePoint: sp\_farm (predeterminado).
	-	Contraseña de cuenta de granja de Sharepoint: Se especifica durante la configuración inicial.
	-	Frase de contraseña de granja de Sharepoint: Se especifica durante la configuración inicial.


## Granja de SharePoint de alta disponibilidad

La granja de servidores de SharePoint de alta disponibilidad consta de nueve máquinas virtuales con esta configuración:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)

A continuación se indican los detalles de configuración:

-	Suscripción de Azure: se especifica en la configuración inicial.
-	Nombres de dominio de Azure (conocidos también como servicios en la nube): se crean nombres de dominio independientes según la ilustración anterior.
-	Cuenta de almacenamiento: Se especifica durante la configuración inicial.
-	Red virtual:
	-	Tipo: Solo para la nube
	-	Espacio de direcciones: 10.0.0.0/26

-	Máquinas virtuales:
	-	*HostNamePrefix*-DC1 (controlador de dominio de AD DS)
	-	*HostNamePrefix*-DC2 (controlador de dominio de AD DS)
	-	*HostNamePrefix*-SQL1 (servidor SQL Server 2014)
	-	*HostNamePrefix*-SQL2 (servidor SQL Server 2014)
	-	*HostNamePrefix*-SQL0 (servidor Windows Server 2012 R2)
	-	*HostNamePrefix*-WEB1 (servidor de SharePoint 2013)
	-	*HostNamePrefix*-WEB2 (servidor de SharePoint 2013)
	-	*HostNamePrefix*-APP1 (servidor de SharePoint 2013)
	-	*HostNamePrefix*-APP2 (servidor de SharePoint 2013)

-	Controladores de dominio:
	-	Imagen de máquina virtual: Windows Server 2012 R2.
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño: A1 (predeterminado).
	-	Nombre de dominio: contoso.com (predeterminado).
	-	Nombre de cuenta de administrador de dominio: Se especifica durante la configuración inicial.
	-	Contraseña de cuenta de administrador de dominio: Se especifica durante la configuración inicial.

-	Servidores de bases de datos:
	-	Imagen de máquina virtual: SQL Server 2014 RTM Enterprise en Windows Server 2012 R2.
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño: A5 (predeterminado) para servidores de bases de datos, A0 (predeterminado) para el testigo del recurso compartido de archivos (SQL0).
	-	Nombre de cuenta de acceso de la base de datos: Se especifica durante la configuración inicial.
	-	Contraseña de cuenta de acceso de la base de datos: Se especifica durante la configuración inicial.
	-	Nombre de cuenta de servicio de SQL Server: sqlservice (predeterminado).
	-	Contraseña de cuenta de servicio de SQL Server: Se especifica durante la configuración inicial.

-	Servidores de SharePoint:
	-	Imagen de máquina virtual: Versión de evaluación de SharePoint Server 2013.
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño: A2 (predeterminado).
	-	Nombre de cuenta de granja de SharePoint: sp\_farm (predeterminado).
	-	Contraseña de cuenta de granja de Sharepoint: Se especifica durante la configuración inicial.
	-	Frase de contraseña de granja de Sharepoint: Se especifica durante la configuración inicial.

> [AZURE.NOTE]Los servidores de SharePoint se crean a partir de la imagen de la versión de evaluación de SharePoint Server 2013. Para seguir usando la máquina virtual después de que haya expirado la versión de evaluación, deberá convertir la instalación para que utilice una clave de licencia de minorista o de licencia por volumen para las ediciones Standard o Enterprise de SharePoint Server 2013.

## Administrador de recursos de Azure

La función Granja de servidores SharePoint del Portal de vista previa de Azure crea máquinas virtuales en Administración de servicios. Para crear granjas de SharePoint Server 2013 en el Administrador de recursos de Azure, vea [Implementación de granjas de servidores SharePoint con plantillas del Administrador de recursos de Azure](virtual-machines-workload-template-sharepoint.md).

## Recursos adicionales

[Granja de servidores SharePoint](virtual-machines-sharepoint-farm-azure-preview.md)

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=Oct15_HO2-->