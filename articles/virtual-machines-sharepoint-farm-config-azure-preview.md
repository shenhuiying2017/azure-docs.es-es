<properties title="SharePoint Server Farm Configuration Details" pageTitle="Detalles de configuración de una granja de servidores de SharePoint" description="Describes the default configuration of SharePoint farms" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="josephd" />


#Detalles de configuración de una granja de servidores de SharePoint#

Granja de servidores de SharePoint es una característica del Portal de vista previa de Microsoft Azure que crea automáticamente una granja de SharePoint Server 2013. Hay dos configuraciones de granja:

- Básica
- Alta disponibilidad

En las siguientes secciones se proporcionan detalles de configuración de cada granja.

Para obtener información adicional, consulte [Granja de servidores de SharePoint](../virtual-machines-sharepoint-farm-azure-preview/).

##Granja básica de SharePoint##

La granja básica de SharePoint consta de tres máquinas virtuales con la siguiente configuración:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png) 

A continuación se indican los detalles de configuración:

-	Suscripción de Azure: se especifica durante la configuración inicial.
-	Nombres de dominio de Azure (también conocido como servicios en la nube): se crean nombres de dominio independientes automáticamente para cada máquina virtual.
-	Cuenta de almacenamiento: se especifica durante la configuración inicial.
-	Red virtual 	
	-   Tipo: solo para la nube	
    -	Espacio de direcciones: 192.168.16.0/26    

- Máquinas virtuales
	-	HostNamePrefix-DC (controlador de dominio AD DS)
	-	HostNamePrefix-SQL (servidor SQL Server 2014)
	-	HostNamePrefix-SP (servidor SharePoint 2013)

- Controlador de dominio
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño A1 (predeterminado)
	-	Nombre de dominio: contoso.com (predeterminado)
	-	Nombre de cuenta de administrador de dominio: se especifica durante la configuración inicial.
	-	Contraseña de cuenta de administrador de dominio: se especifica durante la configuración inicial.

- SQL Server
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño A5 (predeterminado)
	-	Nombre de cuenta de acceso de la base de datos: se especifica durante la configuración inicial.
	-	Contraseña de cuenta de acceso de la base de datos: se especifica durante la configuración inicial.
	-	Nombre de cuenta de servicio de SQL Server: se especifica durante la configuración inicial.
	-	Contraseña de cuenta de servicio de SQL Server: se especifica durante la configuración inicial.

- Servidor SharePoint
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño A2 (predeterminado)
	-	Nombre de cuenta de granja de SharePoint: se especifica durante la configuración inicial.
	-	Contraseña de cuenta de granja de SharePoint: se especifica durante la configuración inicial.
	-	Frase de contraseña de granja de SharePoint: se especifica durante la configuración inicial.


##Alta disponibilidad##

La granja de SharePoint de alta disponibilidad consta de nueve máquinas virtuales con la siguiente configuración:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
A continuación se indican los detalles de configuración:

-	Suscripción de Azure: se especifica durante la configuración inicial.
-	Nombres de dominio de Azure (también conocido como servicios en la nube): se crean nombres de dominio independientes automáticamente para cada máquina virtual.
-	Cuenta de almacenamiento: se especifica durante la configuración inicial.
-	Red virtual	
	-	Tipo: solo para la nube
	-	Espacio de direcciones: 192.168.16.0/26	

-	Máquinas virtuales
	-	HostNamePrefix-DC1 (controlador de dominio AD DS)
	-	HostNamePrefix-DC2 (controlador de dominio AD DS)
	-	HostNamePrefix-SQL1 (servidor SQL Server 2014)
	-	HostNamePrefix-SQL2 (servidor SQL Server 2014)
	-	HostNamePrefix-SQL0 (servidor SQL Server 2014)
	-	HostNamePrefix-WEB1 (servidor SharePoint 2013)
	-	HostNamePrefix-WEB2 (servidor SharePoint 2013)
	-	HostNamePrefix-APP1 (servidor SharePoint 2013)
	-	HostNamePrefix-APP2 (servidor SharePoint 2013)

-	Controladores de dominio
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño A1 (predeterminado)
	-	Nombre de dominio: contoso.com (predeterminado)
	-	Nombre de cuenta de administrador de dominio: se especifica durante la configuración inicial.
	-	Contraseña de cuenta de administrador de dominio: se especifica durante la configuración inicial.

-	Servidores SQL Server
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño A5 (predeterminado)
	-	Nombre de cuenta de acceso de la base de datos: se especifica durante la configuración inicial.
	-	Contraseña de cuenta de acceso de la base de datos: se especifica durante la configuración inicial.
	-	Nombre de cuenta de servicio de SQL Server: se especifica durante la configuración inicial.
	-	Contraseña de cuenta de servicio de SQL Server: se especifica durante la configuración inicial.

-	Servidores de SharePoint
	-	Prefijo de nombre de host: se especifica durante la configuración inicial.
	-	Tamaño A2 (predeterminado)
	-	Nombre de cuenta de granja de SharePoint: se especifica durante la configuración inicial.
	-	Contraseña de cuenta de granja de SharePoint: se especifica durante la configuración inicial.		
	-	Frase de contraseña de granja de SharePoint: se especifica durante la configuración inicial.


<!--HONumber=35.1-->
