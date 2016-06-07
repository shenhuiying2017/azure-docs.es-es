<properties
   pageTitle="Guía técnica sobre resistencia en la recuperación desde local a Azure | Microsoft Azure"
   description="Artículo sobre la descripción y diseño de sistemas recuperación desde infraestructuras locales a Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Guía técnica sobre resistencia en Azure: recuperación desde un centro de datos local a Azure

Azure proporciona un completo conjunto de servicios para habilitar la extensión de un centro de datos local a Azure con fines de alta disponibilidad y recuperación ante desastres:

  * __Redes__: con la red privada virtual puede ampliar de forma segura la red local en la nube.
  * __Proceso__: los clientes que usan Hyper-V en local pueden "Levantar y mover" las máquinas virtuales existentes a Azure.
  * __Almacenamiento__: StorSimple extiende el sistema de archivos al Almacenamiento de Azure. El servicio de Copia de seguridad de Azure proporciona la copia de seguridad de los archivos y Bases de datos SQL de Azure en el Almacenamiento de Azure.
  * __Replicación de base de datos__: con los grupos de disponibilidad de SQL 2014 (o posterior) puede implementar las características de alta disponibilidad y de recuperación ante desastres para los datos locales.

##Redes
Red virtual de Azure permite crear en Azure una sección aislada lógicamente y conectarla de forma segura al centro de datos local o a un único equipo cliente mediante una conexión IPsec. Red virtual hace que sea fácil sacarle todo el partido a la infraestructura escalable a petición de Azure sin dejar de ofrecer conectividad a los datos y aplicaciones locales, incluidos los sistemas que se ejecutan en Windows Server, en grandes sistemas y en UNIX. Para más información, consulte la [documentación de redes de Azure](../virtual-network/virtual-networks-overview.md).

##Proceso
Si está utilizando Hyper-V de forma local puede "levantar y mover" las máquinas virtuales (VM) existentes en Azure junto con los proveedores de servicios que ejecutan Windows Server 2012 (o posterior), sin realizar cambios en la máquina virtual ni convertir sus formatos. Para más información, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales de Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##Azure Site Recovery
Si desea obtener la recuperación ante desastres como servicio (DRaaS), Azure proporciona [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/). Azure Site Recovery ofrece protección completa para servidores de VMware, Hyper-V y físicos. Azure Site Recovery permite utilizar otro servidor local o el mismo Azure como sitio de recuperación. Para más información sobre Azure Site Recovery, consulte la [documentación de Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/).

##Almacenamiento
Hay varias opciones para usar Azure como sitio de copia de seguridad para los datos locales.

###StorSimple
StorSimple permite integrar de forma segura y transparente el almacenamiento en la nube para aplicaciones locales y ofrece un único dispositivo que proporciona almacenamiento local en capas de alto rendimiento y almacenamiento en la nube, archivado activo, protección de datos basada en la nube y recuperación ante desastres. Para más información, consulte la [página de productos de StorSimple](https://azure.microsoft.com/services/storsimple/).

###Copia de seguridad de Azure
Copia de seguridad de Azure permite las copias de seguridad en la nube mediante las conocidas herramientas de copia de seguridad de Windows Server 2012 (o posterior), Windows Server 2012 Essentials (o posterior) y System Center 2012 Data Protection Manager (o posterior). Estas herramientas proporcionan un flujo de trabajo para la administración de copias de seguridad que es independiente de la ubicación de almacenamiento de estas, ya se trate de un disco local o del Almacenamiento de Azure. Una vez que se realiza la copia de seguridad de los datos en la nube, los usuarios autorizados pueden recuperar fácilmente copias de seguridad en cualquier servidor.

Con las copias de seguridad incrementales, solo se transfieren a la nube los cambios en los archivos. Esto permite usar eficazmente el espacio de almacenamiento, reducir el consumo del ancho de banda y admitir la recuperación de estados anteriores de varias versiones de los datos. También puede usar características adicionales, como las directivas de retención de datos, la compresión de datos y la limitación de la transferencia de datos. El uso de Azure como ubicación de las copias de seguridad tiene la ventaja obvia de que las copias de seguridad están automáticamente en una ubicación remota. Esto elimina los requisitos adicionales para proteger los medios de copia de seguridad in situ. Para más información, consulte [¿Qué es la Copia de seguridad de Azure?](../backup/backup-introduction-to-azure-backup.md) y [Configuración de copia de seguridad de Azure para los datos DPM](https://technet.microsoft.com/library/jj728752.aspx).

##Base de datos
Puede tener una solución de recuperación ante desastres para las bases de datos de SQL Server en un entorno de TI híbrida con grupos de disponibilidad AlwaysOn, creación de reflejo de la base de datos, trasvase de registros y copias de seguridad y restauración con el almacenamiento de blobs de Azure. Todas estas soluciones utilizan SQL Server que se ejecuta en Máquinas virtuales de Azure. Los grupos de disponibilidad AlwaysOn pueden utilizarse en un entorno de TI híbrida donde las réplicas de la base de datos existen tanto de forma local como en la nube. Esto puede verse en el siguiente diagrama, extraído del artículo [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Grupos de disponibilidad AlwaysOn de SQL en una arquitectura en la nube híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png "Grupos de disponibilidad AlwaysOn de SQL en una arquitectura en la nube híbrida")

La creación de reflejo de base de datos también puede abarcar servidores locales y en la nube en una instalación basada en certificados. En el siguiente diagrama, se ilustra este concepto.

![Reflejo de base de datos SQL en una arquitectura en la nube híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png "Reflejo de base de datos SQL en una arquitectura en la nube híbrida")

El trasvase de registros se puede usar para sincronizar una base de datos local con una base de datos de SQL Server en una Máquina Virtual de Azure.

![Trasvase de registros de SQL en una arquitectura en la nube híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png "Trasvase de registros de SQL en una arquitectura en la nube híbrida")

Por último, puede hacer una copia de una base de datos local y enviarla directamente al Almacenamiento de blobs de Azure.

![Copia de seguridad de SQL Server en un blob de Almacenamiento de Azure en una arquitectura en la nube híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png "Copia de seguridad de SQL Server en un blob de Almacenamiento de Azure en una arquitectura en la nube híbrida")

Para más información, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) y [Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##Listas de comprobación para la recuperación local en Microsoft Azure

##Lista de comprobación de redes
  1. Revise la sección [Redes](#networking) de este documento.
  2. Use la red virtual para conectar de forma segura la ubicación local a la nube.

##Lista de comprobación de proceso
  1. Revise la sección [Proceso](#compute) de este documento.
  2. Reubique las máquinas virtuales entre Hyper-V y Azure.

##Lista de comprobación de almacenamiento
  1. Revise la sección [Almacenamiento](#storage) de este documento.
  2. Aproveche las ventajas de los servicios de StorSimple para usar el almacenamiento en la nube.
  3. Use los servicios de Copia de seguridad de Azure.

##Lista de comprobación de la base de datos
  1. Revise la sección [Base de datos](#database) de este documento.
  2. Considere la posibilidad de usar SQL Server en máquinas virtuales de Azure como copia de seguridad.
  3. Configure los grupos de disponibilidad AlwaysOn.
  4. Configure el reflejo de base de datos basado en certificados.
  5. Use el trasvase de registros.
  6. Realice una copia de seguridad de la base de datos local en el Almacenamiento de blobs de Azure.

##Pasos siguientes
Este artículo forma parte de una serie que se centra en la [Guía técnica sobre resistencia en Azure](./resiliency-technical-guidance.md). El siguiente artículo de esta serie se centra en la [recuperación ante datos dañados](./resiliency-technical-guidance-recovery-data-corruption.md).

<!---HONumber=AcomDC_0525_2016-->