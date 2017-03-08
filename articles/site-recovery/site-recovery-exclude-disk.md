---
title: "Exclusión discos de la protección con Azure Site Recovery | Microsoft Docs"
description: "Describe por qué y cómo excluir discos de máquina virtual de la replicación en escenarios de VMware a Azure y de Hyper-V a Azure."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 1/24/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: af0d66d92ca542f415de779fb638db166ba5f26a
ms.openlocfilehash: 5e0527fb0a41d8892c9e22d6d6d2f252972e69d0
ms.lasthandoff: 02/17/2017


---
#<a name="exclude-disk-from-replication"></a>Exclusión de discos de la replicación
En este artículo se describe cómo excluir un disco de la replicación para optimizar el ancho de banda de replicación que consumen u optimizar los recursos del lado de destino que usan estos discos. La característica es compatible con escenarios de VMware a Azure y de Hyper-V a Azure.

##<a name="prerequisites"></a>Requisitos previos

De manera predeterminada, se replican todos los discos en una máquina. Si se va a replicar de **VMware a Azure**, para excluir un disco de la replicación, la instancia de Mobility Service debe instalarse manualmente en la máquina antes de habilitar la replicación.


## <a name="why-exclude-disks-from-replication"></a>¿Por qué excluir discos de la replicación?
A menudo es necesario excluir discos de replicación porque:

1. Los datos renovados en el disco excluido no son importantes o no es necesario replicarlos.

2. Los recursos de red y almacenamiento pueden guardarse sin replicarse la renovación.

##<a name="what-are-the-typical-scenarios"></a>¿Cuáles son los escenarios típicos?
Hay algunos ejemplos específicos de renovación de datos que se identifican fácilmente y son buenos candidatos para la exclusión, por ejemplo, escribe cualquier archivo de paginación, escrituras de tempdb de Microsoft SQL Server, etc. Según la carga de trabajo y el subsistema de almacenamiento, el archivo de paginación puede registrar una renovación considerable. Sin embargo, esta réplica de datos del sitio principal a Azure consumiría muchos recursos. Por lo tanto, la replicación de una máquina virtual con un único disco virtual con sistema operativo y archivo de paginación se puede optimizar del siguiente modo:

1. dividiendo el disco virtual en dos: uno con el sistema operativo y otro con el archivo de paginación y
2. excluyendo el disco del archivo de paginación de la replicación

De forma similar, para Microsoft SQL Server con la base de datos tempdb y el archivo de la base de datos del sistema en el mismo disco, la replicación se puede optimizar:

1. manteniendo la base de datos del sistema y tempdb en dos discos diferentes y
2. excluyendo el disco de tempdb de la replicación.

##<a name="how-to-exclude-disk-from-replication"></a>¿Cómo se excluyen discos de la replicación?

###<a name="vmware-to-azure"></a>VMware en Azure
Siga el flujo de trabajo [Habilitación de la replicación](site-recovery-vmware-to-azure.md#enable-replication) para proteger una máquina virtual desde el portal de Azure Site Recovery. En el paso 4 de Habilitación de la replicación, la columna **DISCOS PARA REPLICAR** puede utilizarse para excluir el disco de la replicación. De forma predeterminada se seleccionan todos los discos. Anule la selección del disco que desee excluir de la replicación y complete los pasos para habilitar esta. 

![Habilitar replicación](./media/site-recovery-exclude-disk/v2a-enable-replication-exclude-disk1.png)
    
    
>[!NOTE]
> 
> * Solo puede excluir discos que ya tienen instalado Mobility Service. Mobility Service se instala manualmente, ya que la instalación solo se puede hacer con el mecanismo de notificación push una vez habilitada la replicación.
> * Solo se pueden excluir los discos básicos de la replicación. No se pueden excluir los discos dinámicos ni del sistema operativo.
> * Una vez habilitada la replicación, no puede agregar ni quitar discos para la replicación. Si desea agregar o excluir un disco, deberá deshabilitar la protección de la máquina y volver a habilitarla.
> * Si excluye un disco necesario para que una aplicación funcione, después de la conmutación por error a Azure, tendrá que crearlo manualmente en Azure para poder ejecutar la aplicación replicada. También puede integrar Azure Automation en un plan de recuperación para crear el disco durante la conmutación por error de la máquina.
> * Máquina virtual Windows: no se producirá una conmutación por recuperación de los discos creados manualmente en Azure. Por ejemplo, si realiza la conmutación por error de tres discos y crea dos directamente en una máquina virtual de Azure, solo los tres discos que se conmutaran por error se conmutarán por recuperación. No puede incluir los discos creados manualmente en la conmutación por recuperación o volver a protegerlos desde un entorno local a Azure.
> * Máquina virtual Linux: se producirá una conmutación por recuperación de los discos creados manualmente en Azure. Por ejemplo, si realiza una conmutación por error de tres discos y crea dos directamente en Azure, los cinco experimentarán una conmutación por recuperación. No se pueden excluir discos creados manualmente de la conmutación por recuperación.
> 

###<a name="hyper-v-to-azure"></a>Hyper-V en Azure
Siga el flujo de trabajo [Habilitación de la replicación](site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication) para proteger una máquina virtual desde el portal de Azure Site Recovery. En el paso 4 de Habilitación de la replicación, la columna **DISCOS PARA REPLICAR** puede utilizarse para excluir discos de la replicación. De forma predeterminada se seleccionan todos los discos para la replicación. Anule la selección del disco que desee excluir de la replicación y complete los pasos para habilitar esta. 

![Habilitar replicación](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)
    
>[!NOTE]
> 
> * Solo se pueden excluir los discos básicos de la replicación. No se puede excluir el disco del sistema operativo y no se recomienda excluir los discos dinámicos. ASR no puede identificar qué disco VHD es básico o dinámico dentro de la máquina virtual invitada.  Si no se excluye ningún disco del volumen dinámico dependientes, el disco dinámico protegido aparecerá como erróneo en la máquina virtual de conmutación por error y no se podrá acceder a los datos de ese disco.    
> * Una vez habilitada la replicación, no puede agregar ni quitar discos para la replicación. Si quiere agregar o excluir un disco, tendrá que deshabilitar la protección de la máquina y volver a habilitarla.
> * Si excluye un disco necesario para que una aplicación funcione, después de la conmutación por error a Azure, debe crearlo manualmente en Azure para poder ejecutar la aplicación replicada. También puede integrar Azure Automation en un plan de recuperación para crear el disco durante la conmutación por error de la máquina.
> * No se producirá una conmutación por recuperación de los discos creados manualmente en Azure. Por ejemplo, si realiza la conmutación por error de tres discos y crea dos directamente en una máquina virtual de Azure, solo tres discos que se conmutaran por error se conmutarán por recuperación desde Azure hasta Hyper-V. No puede incluir los discos creados manualmente en la conmutación por recuperación o en la replicación inversa de Hyper-V a Azure.
 


##<a name="end-to-end-scenarios-of-exclude-disks"></a>Escenarios completos de exclusión de discos
Veamos dos escenarios para una mejor comprensión de la característica de exclusión de discos.

1. Disco de la base de datos tempdb de SQL Server
2. Disco del archivo de paginación

###<a name="excluding-the-sql-server-tempdb-disk"></a>Exclusión del disco de la base de datos tempdb de SQL Server
Veamos una máquina virtual de SQL Server con una base de datos tempdb que se puede excluir.

Nombre de la máquina virtual: discos SalesDB en la máquina virtual de origen:


**Nombre del disco** | **N.º del disco de SO invitado** | **Unidad** | **Tipo de datos en el disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco del sistema operativo
DB-Disk1| Disk1 | D:\ | Base de datos del sistema SQL y Database1 del usuario
DB-Disk2 (disco excluido de la protección) | Disk2 | E:\ | Archivos temporales
DB-Disk3 (disco excluido de la protección) | Disk3 | F:\ | Base de datos tempdb de SQL (ruta de acceso de carpeta): F:\MSSQL\Data --> anote la ruta de acceso de la carpeta antes de la conmutación por error\)
DB-Disk4 | Disk4 |G:\ |Database2 del usuario

Puesto que la renovación de los datos en dos discos de la máquina virtual son temporales por naturaleza, se protege la máquina virtual de SalesDB, y se excluyen "Disk2" y "Disk3" de la replicación. Azure Site Recovery no replicará esos discos y no estarán presentes en la conmutación por error de la máquina virtual de Azure.

Discos en la máquina virtual de Azure después de la conmutación por error:

**N.º del disco de SO invitado** | **Unidad** | **Tipo de datos en el disco**
--- | --- | ---
DISK0 |    C:\ | Disco del sistema operativo
Disk1 |    E:\ | Almacenamiento temporal (Azure agrega este disco y asigna la primera unidad disponible)
Disk2 | D:\ | Base de datos del sistema SQL y Database1 del usuario
Disk3 | G:\ | Database2 del usuario

Puesto que el Disk2 y Disk3 se excluyeron de la máquina virtual de SalesDB, E: es la primera unidad disponible de la lista. Azure asigna E: al volumen de almacenamiento temporal. Para todos los discos replicados, la unidad permanece igual.

El disco Disk3 (el de la base de datos tempdb de SQL, cuya ruta de acceso de carpeta es F:\MSSQL\Data, excluido de la replicación\) no está disponible en la máquina virtual de conmutación por error. Como resultado, el servicio SQL está detenido y necesita la ruta de acceso F:\MSSQL\Data.

Hay dos maneras de crear esta ruta de acceso.

1. Agregar un disco nuevo y asignar la ruta de acceso de carpeta de tempdb o
2. Usar el disco de almacenamiento temporal existente para la ruta de acceso de carpeta de tempdb

####<a name="add-a-new-disk"></a>Incorporación de un disco nuevo:

1. Anote la ruta de acceso de tempdb.mdf y tempdb.ldf de SQL antes de la conmutación por error.
2. Desde Azure Portal, agregue un disco nuevo a la máquina virtual de conmutación por error con el mismo o mayor tamaño que el del disco de la base de datos tempdb de SQL de origen (Disk3).
3. Inicie sesión en la máquina virtual de Azure. Desde la consola de administración de disco (diskmgmt.msc), inicialice y formatee el disco recién agregado.
4. Asigne la misma unidad del disco de la base de datos tempdb de SQL (F:).
5. Cree la carpeta para tempdb en el volumen F: (F:\MSSQL\Data).
6. Inicie el servicio SQL desde la consola de servicio.

####<a name="use-existing-temporary-storage-disk-for-sql-tempdb-folder-path"></a>Uso del disco de almacenamiento temporal existente para la ruta de acceso de carpeta de tempdb de SQL:

1. Abra una consola de línea de comandos.
2. Ejecute SQL Server en modo de recuperación desde la consola de línea de comandos.

        Net start MSSQLSERVER /f / T3608

3. Ejecute el siguiente sqlcmd para cambiar la ruta de acceso de tempdb a la nueva ruta de acceso.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;        
        GO        
        ALTER DATABASE tempdb        
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO        
        ALTER DATABASE tempdb        
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');        
        GO


4. Detenga el servicio Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Inicie el servicio Microsoft SQL Server.

        Net start MSSQLSERVER

Consulte la siguiente directriz de Azure para el disco de almacenamiento temporal

* Uso de SSDs en máquinas virtuales de Azure para almacenar TempDB de SQL Server y extensiones del grupo de búferes
* Procedimientos recomendados para mejorar el rendimiento de SQL Server en Máquinas virtuales de Azure

###<a name="failback-from-azure-to-on-premises"></a>Conmutación por recuperación (de Azure a un entorno local)
Ahora debemos tener claro que todos los discos se replicarán al realizar la conmutación por error de Azure al host de Hyper-V o VMware locales. Los discos creados manualmente en Azure no se replicarán. Por ejemplo, si realiza la conmutación por error de tres discos y crea dos directamente en una máquina virtual de Azure, solo tres discos que se conmutaran por error se conmutarán por recuperación. No puede incluir los discos creados manualmente en la conmutación por recuperación ni volver a protegerlos de un entorno local a Azure. Tampoco se replicará el disco de almacenamiento temporal en el entorno local.

####<a name="failback-to-original-location-recovery-olr"></a>Conmutación por recuperación en ubicación original (OLR)

Configuración del disco de máquina virtual de Azure en el ejemplo anterior:

**N.º del disco de SO invitado** | **Unidad** | **Tipo de datos en el disco** 
--- | --- | --- 
DISK0 | C:\ | Disco del sistema operativo
Disk1 |    E:\ | Almacenamiento temporal (Azure agrega este disco y asigna la primera unidad disponible)
Disk2 |    D:\ | Base de datos del sistema SQL y Database1 del usuario
Disk3 |    G:\ | Database2 del usuario


####<a name="vmware-to-azure"></a>VMware en Azure
Cuando se realiza la conmutación por recuperación en la ubicación original, la configuración del disco de máquina virtual de conmutación por recuperación no tiene exclusiones. Esto significa que los discos que se hayan excluido de VMware a Azure, no estarán disponibles en la máquina virtual de conmutación por recuperación. 

Discos de la máquina virtual de VMWare (ubicación original) después de la conmutación por error planeada de Azure a VMware local:

**N.º del disco de SO invitado** | **Unidad** | **Tipo de datos en el disco** 
--- | --- | --- 
DISK0 | C:\ | Disco del sistema operativo
Disk1 |    D:\ | Base de datos del sistema SQL y Database1 del usuario
Disk2 |    G:\ | Database2 del usuario

####<a name="hyper-v-to-azure"></a>Hyper-V en Azure
Cuando se realiza la conmutación por recuperación en la ubicación original, la configuración de disco de máquina virtual de conmutación por recuperación es la misma que la original del disco de máquina virtual para Hyper-V. Esto significa que los discos que se hayan excluido de Hyper-V a Azure, estarán disponibles en la máquina virtual de conmutación por recuperación.

Discos de la máquina virtual de Hyper-V (ubicación original) después de la conmutación por error planeada de Azure a Hyper-V local:

**Nombre del disco** | **N.º del disco de SO invitado** | **Unidad** | **Tipo de datos en el disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |    C:\ | Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Base de datos del sistema SQL y Database1 del usuario
DB-Disk2 (disco excluido) | Disk2 | E:\ | Archivos temporales
DB-Disk3 (disco excluido) | Disk3 | F:\ | Base de datos tempdb de SQL (ruta de acceso de carpeta): F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | Database2 del usuario


####<a name="exclude-paging-file-disk"></a>Exclusión del disco del archivo de paginación

Veamos una máquina virtual con un disco del archivo de paginación que se puede excluir.
Hay dos casos:

####<a name="case-1-pagefile-is-configured-on-the-d-drive"></a>Caso 1: El archivo de paginación está configurado en la unidad D:
Configuración del disco:


**Nombre del disco** | **N.º del disco de SO invitado** | **Unidad** | **Tipo de datos en el disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco del sistema operativo
DB-Disk1 (disco excluido de la protección) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Datos del usuario 1
DB-Disk3 | Disk3 | F:\ | Datos del usuario 2

Configuración del archivo de paginación en la máquina virtual de origen:

![Habilitar replicación](./media/site-recovery-exclude-disk/pagefile-on-d-drive-sourceVM.png)
    

Después de la conmutación por error de la máquina virtual de VMware a Azure o de Hyper-V a Azure, estos son los discos de la máquina virtual de Azure:
**Nombre del disco** | **N.º del disco de SO invitado** | **Unidad** | **Tipo de datos en el disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Almacenamiento temporal –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | Datos del usuario 1
DB-Disk3 | Disk3 | F:\ | Datos del usuario 2

Puesto que se ha excluido Disk1 (D:), D: es la primera unidad disponible en la lista y Azure la asigna al volumen de almacenamiento temporal.  Puesto que D: está disponible en la máquina virtual de Azure, la configuración de archivo de paginación de la máquina virtual permanece intacta.

Configuración del archivo de paginación en la máquina virtual de Azure:

![Habilitar replicación](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

####<a name="case-2-pagefile-file-is-configured-on-any-other-driveother-than-d-drive"></a>Caso 2: El archivo de archivo de paginación está configurado en cualquier otra unidad (que no sea D:)

Configuración del disco de la máquina virtual de origen:

**Nombre del disco** | **N.º del disco de SO invitado** | **Unidad** | **Tipo de datos en el disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco del sistema operativo
DB-Disk1 (disco excluido de la protección) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Datos del usuario 1
DB-Disk3 | Disk3 | F:\ | Datos del usuario 2

Configuración del archivo de paginación en la máquina virtual local:

![Habilitar replicación](./media/site-recovery-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Después de la conmutación por error de la máquina virtual de VMware/Hyper-V a Azure, estos son los discos de la máquina virtual de Azure:

**Nombre del disco**| **N.º del disco de SO invitado**| **Unidad** | **Tipo de datos en el disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Disco del sistema operativo
DB-Disk1 | Disk1 | D:\ | Almacenamiento temporal –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | Datos del usuario 1
DB-Disk3 | Disk3 | F:\ | Datos del usuario 2

Como D: es la primera unidad disponible en la lista, Azure la asigna al volumen de almacenamiento temporal. Para todos los discos replicados, la unidad permanece igual. Como G: disco no está disponible, el sistema usará la unidad C: para el archivo de paginación.

Configuración del archivo de paginación en la máquina virtual de Azure:

![Habilitar replicación](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Pasos siguientes
Después de que la implementación esté configurada y en ejecución, [obtenga más información](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error.

