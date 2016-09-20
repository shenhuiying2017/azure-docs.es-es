<properties 
	pageTitle="Configuración de una conexión desde un indexador de Azure Search a SQL Server en una máquina virtual de Azure | Microsoft Azure | Indexadores" 
	description="Habilite conexiones cifradas y configure el firewall para permitir conexiones a SQL Server en una máquina virtual de Azure a partir de un indexador de Azure Search." 
	services="search" 
	documentationCenter="" 
	authors="jack4it" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/06/2016" 
	ms.author="jackma"/>

# Configuración de una conexión desde un indexador de Azure Search a SQL Server en una máquina virtual de Azure

Como se indicó en [Conexión de Azure SQL Database a Azure Search con indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), se admite la creación de indexadores en **SQL Server en máquinas virtuales de Azure** (o **VM de SQL Azure** para abreviar) en Azure Search, pero hay dos requisitos previos relacionados con la seguridad de los que hay que ocuparse en primer lugar.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Habilitación de conexiones cifradas

Azure Search requiere una conexión segura para leer los datos de la base de datos. Esto significa que debe habilitar conexiones cifradas en la máquina virtual de SQL Azure mediante la configuración de un certificado SSL.

Los pasos para habilitar conexiones cifradas con SQL Server están documentados en el artículo [Habilitar conexiones cifradas en el motor de base de datos](https://msdn.microsoft.com/library/ms191192.aspx), pero para las conexiones desde la red pública de Internet como, por ejemplo, una conexión desde Azure Search a una máquina virtual de SQL Azure, hay algunos requisitos adicionales para que la conexión pueda funcionar.

### Especificación de un FQDN en el certificado SSL

El nombre del sujeto del certificado SSL debe ser el nombre de dominio completo (o **FQDN**) de la máquina virtual de SQL Azure. Es el mismo FQDN que especificará en la cadena de conexión de la base de datos al crear un origen de datos en el servicio de búsqueda. Un FQDN presenta el formato `<your-VM-name>.<region>.cloudapp.azure.com` en el caso de las máquinas virtuales de **Resource Manager**. En el caso de máquinas virtuales **clásicas**, el formato es `<your-cloud-service-name.cloudapp.net>`. Puede encontrar el FQDN de la máquina virtual de SQL Azure como nombre o etiqueta DNS en [Azure Portal](https://portal.azure.com/).

### Uso de REGEDIT para configurar el certificado SSL

El Administrador de configuración de SQL Server no puede mostrar el certificado SSL de FQDN en el menú desplegable **Certificado** como se describe en la documentación. La solución consiste en configurar el certificado SSL editando esta clave del Registro: **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server[MSSQL13.MSSQLSERVER]\\MSSQLServer\\SuperSocketNetLib\\Certificate**. La parte *[MSSQL13.MSSQLSERVER]* puede variar en función de la versión y nombre de instancia de SQL Server. Esta clave debe actualizarse mediante la **huella digital** del certificado SSL que instaló en la máquina virtual de SQL Azure.

### Concesión de permisos a la cuenta de servicio

Asegúrese de que a la cuenta de servicio de SQL Server se le concede el permiso adecuado en la clave privada del certificado SSL. Si pasa por alto este paso, SQL Server no se iniciará.

## Configuración del firewall para permitir conexiones de Azure Search

No es extraño configurar el firewall y el correspondiente punto de conexión o lista de control de acceso (ACL) de Azure para que la máquina virtual de Azure sea accesible desde otras partes. Lo más probable es que ya haya realizado esta configuración para permitir que su propia lógica de la aplicación se conecte a la máquina virtual de SQL Azure. Esto es igual para una conexión de Azure Search a la máquina virtual de SQL Azure. Si no ha realizado esto todavía, aquí tiene algunas buenas prácticas de seguridad que debe tener en cuenta.

Si utiliza máquinas virtuales de **Resource Manager**, consulte [Connect to a SQL Server Virtual Machine on Azure (Resource Manager)](../virtual-machines/virtual-machines-windows-sql-connect.md) (Conexión a una máquina virtual de SQL Server en Azure [Resource Manager]). Si sigue usando máquinas virtuales **clásicas**, consulte [Connect to a SQL Server Virtual Machine on Azure Classic](../virtual-machines/virtual-machines-windows-classic-sql-connect.md) (Conexión a una máquina virtual de SQL Server en Azure clásico).

### Restricción del acceso a la dirección IP del servicio de búsqueda

Para cualquier modelo de implementación, al configurar las conexiones de Azure Search, se recomienda restringir el acceso a la dirección IP del servicio de búsqueda en la ACL en lugar de abrir totalmente las máquinas virtuales de SQL Azure a cualquier solicitud de conexión. Puede averiguar fácilmente la dirección IP haciendo ping en el FQDN (por ejemplo, `<your-search-service-name>.search.windows.net`) del servicio de búsqueda.

### Configuración de un intervalo de direcciones IP

Tenga en cuenta que si el servicio de búsqueda tiene solo una unidad de búsqueda (es decir, una réplica y una partición), la dirección IP puede cambiar durante los reinicios rutinarios del servicio. Para evitar errores de conexión, debe especificar el intervalo de direcciones IP de la región de Azure en la que se aprovisiona el servicio de búsqueda. La lista de intervalos IP desde la que se asignan direcciones IP públicas a recursos de Azure está publicada en [Intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Inclusión de las direcciones IP del portal de Azure Search

Además, si utiliza Azure Portal para crear un indexador, la lógica del portal de Azure Search también necesitará acceso a la máquina virtual de SQL Azure durante el tiempo de creación. Las direcciones IP del portal de búsqueda de Azure se pueden encontrar haciendo ping en `stamp1.search.ext.azure.com` y `stamp2.search.ext.azure.com`.

## Pasos siguientes

Una vez que se han cumplido los anteriores requisitos de configuración, ya puede especificar un servidor SQL Server en la máquina virtual de Azure como origen de datos de un indexador de Azure Search. Consulte [Conexión de Azure SQL Database a Azure Search con indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) para más información.

<!---HONumber=AcomDC_0907_2016-->