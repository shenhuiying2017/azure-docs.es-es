---
title: Hacer una copia de seguridad de los archivos y aplicaciones de Azure Stack | Microsoft Docs
description: Use Azure Backup para hacer una copia de seguridad de los archivos y aplicaciones de Azure Stack, así como para restaurarlos, en el entorno de Azure Stack.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 99ac43efa5d3211bbe2d790f28532e682058313c
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075884"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Copia de seguridad de archivos y aplicaciones en Azure Stack
Puede usar Azure Backup para proteger o realizar copias de seguridad de archivos y aplicaciones en Azure Stack. Para hacer copias de seguridad de archivos y aplicaciones, instale Microsoft Azure Backup Server como una máquina virtual que se ejecuta en Azure Stack. Puede proteger las aplicaciones que se ejecutan en cualquier servidor de Azure Stack en la misma red virtual. Una vez que haya instalado Azure Backup Server, agregue discos de Azure para aumentar el almacenamiento local disponible para los datos de copia de seguridad a corto plazo. Azure Backup Server usa almacenamiento de Azure para la retención a largo plazo.

> [!NOTE]
> Aunque Azure Backup Server y System Center Data Protection Manager (DPM) son similares, el uso de DPM no es compatible con Azure Stack.
>


## <a name="azure-backup-server-protection-matrix"></a>Matriz de protección de Azure Backup Server
Azure Backup Server protege las siguientes cargas de trabajo de máquina virtual de Azure Stack:

| Origen de datos protegido | Protección y recuperación |
| --------------------- | ----------------------- |
| Canal semianual de Windows Server: Datacenter/Enterprise/Standard | Volúmenes, archivos, carpetas |
| Windows Server 2016: Datacenter/Enterprise/Standard | Volúmenes, archivos, carpetas |
| Windows Server 2012 R2: Datacenter/Enterprise/Standard | Volúmenes, archivos, carpetas |
| Windows Server 2012: Datacenter/Enterprise/Standard | Volúmenes, archivos, carpetas |
| Windows Server 2008 R2: Datacenter/Enterprise/Standard | Volúmenes, archivos, carpetas |
| SQL Server 2016 | Base de datos |
| SQL Server 2014 | Base de datos |
| SQL Server 2012 SP1 | Base de datos |
| SharePoint 2013 | Granja, base de datos, front-end, servidor web |
| SharePoint 2010 | Granja, base de datos, front-end, servidor web |


## <a name="install-azure-backup-server"></a>Instalar Azure Backup Server
Para instalar Azure Backup Server en una máquina virtual de Azure Stack, consulte el artículo [Preparación para hacer una copia de seguridad de cargas de trabajo con Azure Backup Server](backup-azure-microsoft-azure-backup.md). Antes de instalar y configurar Azure Backup Server, tenga en cuenta lo siguiente:

### <a name="determining-size-of-virtual-machine"></a>Determinar el tamaño de la máquina virtual
Para ejecutar Azure Backup Server en una máquina virtual de Azure Stack, use el tamaño A2 o superior. Si quiere obtener ayuda para elegir un tamaño de máquina virtual, descargue la [Calculadora de tamaño de VM de Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Redes virtuales en máquinas virtuales de Azure Stack
Toda máquina virtual que se usa en una carga de trabajo de Azure Stack debe pertenecer a la misma red virtual de Azure y suscripción a Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Almacenamiento de datos de copia de seguridad en un disco local y en Azure
Azure Backup Server almacena los datos de copia de seguridad en discos de Azure conectados a la máquina virtual, para la recuperación operativa. Una vez que los discos y el espacio de almacenamiento están conectados a la máquina virtual, Azure Backup Server gestiona el almacenamiento por usted. La cantidad de almacenamiento de datos de copia de seguridad depende del número y tamaño de los discos conectados a cada [máquina virtual de Azure Stack](../azure-stack/user/azure-stack-storage-overview.md). Cada tamaño de VM de Azure Stack tiene un número máximo de discos que se pueden conectar a la máquina virtual. Por ejemplo, A2 son cuatro discos. A3 son ocho discos. A4 son 16 discos. De nuevo, el tamaño y número de discos determina el grupo de almacenamiento de copia de seguridad total.

> [!IMPORTANT]
> **No** debería conservar datos de recuperación operativa (copia de seguridad) en discos conectados directamente a Azure Backup Server durante más de cinco días.
>

El almacenamiento de datos de copia de seguridad de Azure reduce la infraestructura de copia de seguridad en Azure Stack. Si los datos tienen más de cinco días de antigüedad, se deben almacenar en Azure.

Para almacenar datos de copia de seguridad en Azure, cree o use un almacén de Recovery Services. Cuando se prepare para hacer una copia de seguridad de la carga de trabajo de Azure Backup Server, deberá [configurar el almacén de Recovery Services](backup-azure-microsoft-azure-backup.md#recovery-services-vault). Una vez configurado, cada vez que se ejecuta un trabajo de copia de seguridad, se crea un punto de recuperación en el almacén. Cada almacén de Recovery Services contiene hasta 9999 puntos de recuperación. Puede conservar los datos de copias de seguridad durante muchos años, en función del número de puntos de recuperación creados y cuánto tiempo se conservaron. Por ejemplo, podría crear puntos de recuperación mensuales y conservarlos durante cinco años.
 
### <a name="using-sql-server"></a>Uso de SQL Server
Si quiere utilizar un servidor SQL Server remoto para la base de datos de Azure Backup Server, seleccione solo una VM de Azure Stack que ejecute SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Rendimiento de VM de Azure Backup Server
Si se comparte con otras máquinas virtuales, el tamaño de la cuenta de almacenamiento y los límites de IOPS pueden afectar al rendimiento de la máquina virtual de Azure Backup Server. Por este motivo, debe utilizar una cuenta de almacenamiento independiente para la máquina virtual de Azure Backup Server. El agente de Azure Backup que se ejecuta en Azure Backup Server necesita almacenamiento temporal para:
    - su propio uso (una ubicación en caché),
    - datos restaurados de la nube (área de almacenamiento provisional local)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurar el almacenamiento temporal en el disco de Azure Backup
Cada máquina virtual de Azure Stack viene con un almacenamiento temporal en el disco, que está disponible para el usuario como volumen `D:\`. El área de almacenamiento provisional local necesaria para Azure Backup se puede configurar para residir en `D:\`, y la ubicación en caché puede colocarse en `C:\`. De esta manera, no es necesario reducir el almacenamiento de los discos de datos conectados a la máquina virtual de Azure Backup Server.

### <a name="scaling-deployment"></a>Escalado de la implementación
Si quiere escalar su implementación, tiene las siguientes opciones:
  - Escalar verticalmente: aumente el tamaño de la máquina virtual Azure Backup Server de la serie A a la serie D y aumente el almacenamiento local [según las instrucciones de la máquina virtual de Azure Stack](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Descargar datos: envíe datos antiguos a Azure Backup Server y conserve solo los datos más recientes en el almacenamiento conectado a Azure Backup Server.
  - Escalar horizontalmente: agregue más instancias de Azure Backup Server para proteger las cargas de trabajo.

## <a name="see-also"></a>Otras referencias
Para obtener información sobre cómo usar Azure Backup Server para proteger otras cargas de trabajo, consulte uno de los siguientes artículos:
- [Hacer una copia de seguridad de la granja de SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Hacer una copia de seguridad de SQL Server](backup-azure-sql-mabs.md)
