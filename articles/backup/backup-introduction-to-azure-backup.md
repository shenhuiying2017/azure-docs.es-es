---
title: "¿Qué es Azure Backup? | Microsoft Docs"
description: "Use Azure Backup para realizar copias de seguridad y restaurar los datos y cargas de trabajo de servidores de Windows, estaciones de trabajo de Windows, servidores de System Center DPM y máquinas virtuales de Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "copia de seguridad y restauración; servicios de recuperación; soluciones de copia de seguridad"
ms.assetid: 0d2a7f08-8ade-443a-93af-440cbf7c36c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/5/2018
ms.author: markgal;trinadhk;anuragm
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 59beaac1d8619c3f4afa1c75074546a849dfce6b
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="overview-of-the-features-in-azure-backup"></a>Introducción a las características de Azure Backup
Azure Backup es el servicio de Azure que puede usar para realizar una copia de seguridad de los datos (protegerlos) y recuperarlos en la nube de Microsoft. Reemplaza su solución de copia de seguridad local o remota existente por una solución confiable, segura y rentable basada en la nube. Azure Backup ofrece varios componentes que se descargan e implementan en el equipo o servidor adecuados, o en la nube. El componente, o agente, que se implemente depende de lo que quiera proteger. Todos los componentes de Azure Backup (sin importar si va a proteger los datos de forma local o en la nube) se pueden usar para realizar una copia de seguridad de datos en un almacén de Azure Recovery Services. Para más información sobre qué componente usar para proteger datos, aplicaciones o cargas de trabajo específicos, consulte la [tabla de componentes de Azure Backup](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use) (más adelante en este artículo).

[Ver un vídeo de información general de Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## <a name="why-use-azure-backup"></a>¿Por qué usar Azure Backup?
Las soluciones de copia de seguridad tradicionales han evolucionado para tratar la nube como un punto de conexión, o destino de almacenamiento estático, similar a un disco o una cinta. Aunque este enfoque es simple, es limitado y no aprovecha una plataforma de nube subyacente, lo que se traduce en una solución ineficaz y costosa. Algunas soluciones son costosas porque se acaba pagando por el tipo incorrecto de almacenamiento o almacenamiento que no necesita. Otras soluciones a menudo son ineficaces porque no ofrecen el tipo o la cantidad de almacenamiento que necesita, o las tareas administrativas exigen demasiado tiempo. En cambio, Azure Backup proporciona las siguientes ventajas principales:

**Administración de almacenamiento automática**: los entornos híbridos requieren con frecuencia almacenamiento heterogéneo, unos local y otros en la nube. Con Azure Backup, no hay ningún costo por el uso de dispositivos de almacenamiento local. Azure Backup asigna y administra automáticamente almacenamiento de copia de seguridad y emplea un modelo de pago por uso. Esto significa que solo paga por el almacenamiento que consume. Para más información, consulte el [artículo sobre precios de Azure](https://azure.microsoft.com/pricing/details/backup).

**Escalado ilimitado**: Azure Backup usa la eficacia subyacente y la escala ilimitada de la nube de Azure para proporcionar alta disponibilidad, sin sobrecarga de mantenimiento o supervisión. Puede configurar alertas para proporcionar información sobre los eventos, pero no tiene que preocuparse por la alta disponibilidad de los datos en la nube.

**Varias opciones de almacenamiento**: un aspecto de alta disponibilidad es la replicación del almacenamiento. Azure Backup ofrece dos tipos de replicación: [almacenamiento con redundancia local](../storage/common/storage-redundancy.md#locally-redundant-storage) y [almacenamiento con redundancia geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage). Elija la opción de almacenamiento de copia de seguridad más acorde con sus necesidades:

* El almacenamiento con redundancia local (LRS) replica los datos tres veces (crea tres copias de los datos) en un centro de datos emparejado de la misma región. LRS es una opción de bajo costo para proteger los datos contra errores de hardware local.

* El almacenamiento con redundancia geográfica (GRS) replica los datos en una región secundaria (a cientos de kilómetros de la ubicación principal del origen de datos). GRS cuesta más que LRS, pero proporciona un mayor nivel de durabilidad de los datos, incluso si hay una interrupción regional.

**Transferencia de datos ilimitada**: Azure Backup no limita la cantidad de datos de entrada y salida que se transfieren. Tampoco cobra por los datos que se transfieren. Sin embargo, si usa el servicio Azure Import/Export para importar grandes cantidades de datos, existe un costo asociado con los datos de entrada. Para más información sobre este costo, consulte [Flujo de trabajo de copia de seguridad sin conexión en Azure Backup](backup-azure-backup-import-export.md). Datos de salida hacen referencia a los datos transferidos desde un almacén de Recovery Services durante una operación de restauración.

**Cifrado de datos**: el cifrado de datos permite la transmisión y el almacenamiento seguros de los datos en la nube pública. La frase de contraseña de cifrado se almacena localmente y nunca se transmite ni se almacena en Azure. Si es necesario restaurar algunos de los datos, solo cuenta con la frase de contraseña o clave de cifrado.

**Copia de seguridad coherente con la aplicación**: tanto si va a realizar una copia de seguridad de un servidor de archivos, una máquina virtual o una base de datos SQL, debe saber que un punto de recuperación tiene todos los datos necesarios para restaurar la copia de seguridad. Azure Backup proporciona copias de seguridad coherentes con la aplicación, lo que garantiza que no se necesitan correcciones adicionales para restaurar los datos. La restauración de datos coherentes con la aplicación reduce el tiempo de restauración, lo que le permite volver rápidamente a un estado de ejecución.

**Retención a largo plazo**: en lugar de pasar las copias de seguridad de disco a cinta y luego trasladar la cinta a una ubicación externa, puede usar Azure para la retención a corto y largo plazo. Azure no limita la cantidad de tiempo que los datos permanecen en un almacén de Backup o Recovery Services. Los datos se pueden conservar en un almacén tanto tiempo como se desee. Azure Backup tiene un límite de 9999 puntos de recuperación por instancia protegida. Consulte la sección [Copia de seguridad y retención](backup-introduction-to-azure-backup.md#backup-and-retention) de este artículo para obtener una explicación de la forma en que este límite puede afectar a sus necesidades de copia de seguridad.  

## <a name="which-azure-backup-components-should-i-use"></a>¿Qué componentes de Azure Backup debo usar?
Si no está seguro de qué componentes de Azure Backup vienen bien para sus necesidades, consulte la siguiente tabla para información sobre lo que puede proteger con cada uno. Azure Portal proporciona un asistente, que está integrado en el portal, para guiarle a través de la elección de los componentes para descargar e implementar. El asistente, que forma parte de la creación del almacén de Recovery Services, le lleva por los pasos para seleccionar un objetivo de copia de seguridad y elegir los datos o la aplicación para proteger.

| Componente | Ventajas | límites | ¿Qué se protege? | ¿Dónde se almacenan las copias de seguridad? |
| --- | --- | --- | --- | --- |
| Agente de Azure Backup (MARS) |<li>Copia de seguridad de archivos y carpetas en el sistema operativo Windows físico o virtual (las máquinas virtuales pueden estar en el entorno local o en Azure)<li>No se necesita ningún servidor de copia de seguridad independiente. |<li>Copia de seguridad tres veces al día <li>No es compatible con la aplicación; restauración solo a nivel de archivo, carpeta y volumen. <li>  No se admite Linux. |<li>Archivos <li>Carpetas <li>Estado del sistema |Almacén de Recovery Services |
| System Center DPM |<li>Instantáneas compatibles con aplicación (VSS)<li>Total flexibilidad para realizar copias de seguridad<li>Granularidad en la recuperación (todo)<li>Puede usar un almacén de Recovery Services<li>Linux Support en máquinas virtuales de Hyper-V y VMware <li>Copia de seguridad y restauración de máquinas virtuales de VMware mediante DPM 2012 R2 |No puede realizar la copia de seguridad de una carga de trabajo de Oracle.|<li>Archivos <li>Carpetas<li> Volúmenes <li>Máquinas virtuales<li> Aplicaciones<li> Cargas de trabajo |<li>Almacén de Recovery Services,<li> Disco conectado localmente<li>  Cinta (solo local) |
| Servidor de Azure Backup |<li>Instantáneas compatibles con la aplicación (VSS)<li>Total flexibilidad para realizar copias de seguridad<li>Granularidad en la recuperación (todo)<li>Puede usar un almacén de Recovery Services<li>Linux Support en máquinas virtuales de Hyper-V y VMware<li>Creación de copias de seguridad y restauración de máquinas virtuales de VMware <li>No requiere licencia de System Center |<li>No puede realizar la copia de seguridad de una carga de trabajo de Oracle.<li>Siempre requiere una suscripción de Azure activa<li>No se admite la copia de seguridad en cinta |<li>Archivos <li>Carpetas<li> Volúmenes <li>Máquinas virtuales<li> Aplicaciones<li> Cargas de trabajo |<li>Almacén de Recovery Services,<li> Disco conectado localmente |
| Copia de seguridad de máquina virtual de IaaS de Azure |<li>Copias de seguridad nativas de Windows/Linux<li>No se requiere la instalación de ningún agente específico<li>Copia de seguridad de nivel de tejido sin necesidad de ninguna infraestructura de copia de seguridad |<li>Copia de seguridad de máquinas virtuales una vez al día <li>Restauración de máquinas virtuales solo en el nivel de disco<li>No puede realizar copias de seguridad locales |<li>Máquinas virtuales <li>Todos los discos (con PowerShell) |<p>Almacén de Recovery Services</p> |

## <a name="what-are-the-deployment-scenarios-for-each-component"></a>¿Cuáles son los escenarios de implementación de cada componente?
| Componente | ¿Se puede implementar en Azure? | ¿Se puede implementar de forma local? | Almacenamiento de destino admitido |
| --- | --- | --- | --- |
| Agente de Azure Backup (MARS) |<p>**Sí**</p> <p>El agente de Azure Backup se puede implementar en cualquier máquina virtual Windows que se ejecute en Azure.</p> |<p>**Sí**</p> <p>El agente de Backup se puede implementar en cualquier máquina física o virtual de Windows Server.</p> |<p>Almacén de Recovery Services</p> |
| System Center DPM |<p>**Sí**</p><p>Más información [sobre cómo proteger las cargas de trabajo en Azure mediante System Center DPM](backup-azure-dpm-introduction.md).</p> |<p>**Sí**</p> <p>Más información sobre [cómo proteger cargas de trabajo y máquinas virtuales en su centro de datos](https://technet.microsoft.com/system-center-docs/dpm/data-protection-manager).</p> |<p>Disco conectado localmente</p> <p>Almacén de Recovery Services,</p> <p>Cinta (solo local)</p> |
| Servidor de Azure Backup |<p>**Sí**</p><p>Más información sobre [cómo proteger cargas de trabajo en Azure con Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> |<p>**Sí**</p> <p>Más información sobre [cómo proteger cargas de trabajo en Azure con el Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> |<p>Disco conectado localmente</p> <p>Almacén de Recovery Services</p> |
| Copia de seguridad de máquina virtual de IaaS de Azure |<p>**Sí**</p><p>Parte del tejido de Azure</p><p>Especializado para [copia de seguridad de máquinas virtuales de infraestructura como servicio (IaaS) de Azure](backup-azure-vms-introduction.md).</p> |<p>**No**</p> <p>Use System Center DPM para hacer copias de seguridad de máquinas virtuales en su centro de datos.</p> |<p>Almacén de Recovery Services</p> |

## <a name="which-applications-and-workloads-can-be-backed-up"></a>¿De qué aplicaciones y cargas de trabajo puedo hacer copias de seguridad?
En la tabla siguiente se proporciona una matriz de los datos y las cargas de trabajo que se pueden proteger mediante Azure Backup. La columna de la solución de Azure Backup tiene vínculos a la documentación de implementación para esa solución. 

| Datos o carga de trabajo | Entorno de origen | Solución de Azure Backup |
| --- | --- | --- |
| Archivos y carpetas |Windows Server |<p>[Agente de Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ el agente de Azure Backup)</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Archivos y carpetas |Equipo de Windows |<p>[Agente de Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ el agente de Azure Backup)</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Máquina virtual de Hyper-V (Windows) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Máquina virtual de Hyper-V (Linux) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Máquina virtual de VMware |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Microsoft SQL Server |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Microsoft SharePoint |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Microsoft Exchange |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Máquinas virtuales de Azure IaaS (Windows) |Ejecución en Azure |[Azure Backup (extensión de máquina virtual)](backup-azure-vms-introduction.md) |
| Máquinas virtuales de IaaS de Azure (Linux) |Ejecución en Azure |[Azure Backup (extensión de máquina virtual)](backup-azure-vms-introduction.md) |

## <a name="linux-support"></a>Linux Support
En la siguiente tabla se muestran los componentes de Azure Backup que son compatibles con Linux.  

| Componente | Linux Support (reconocido por Azure) |
| --- | --- |
| Agente de Azure Backup (MARS) |No (solo agente basado en Windows) |
| System Center DPM |<li> Copia de seguridad coherente con archivo de máquinas virtuales invitadas de Linux en Hyper-V y VMWare<br/> <li> Restauración de máquinas virtuales invitadas de Linux en Hyper-V y VMWare </br> </br>  *Copia de seguridad coherente con archivo no disponible para la máquina virtual de Azure* <br/> |
| Servidor de Azure Backup |<li>Copia de seguridad coherente con archivo de máquinas virtuales invitadas de Linux en Hyper-V y VMWare<br/> <li> Restauración de máquinas virtuales invitadas de Linux en Hyper-V y VMWare </br></br> *Copia de seguridad coherente con archivo no disponible para la máquina virtual de Azure*  |
| Copia de seguridad de máquina virtual de IaaS de Azure |Copia de seguridad coherente con la aplicación mediante un [marco de scripts previos y posteriores](backup-azure-linux-app-consistent.md)<br/> [Recuperación de archivos pormenorizada](backup-azure-restore-files-from-vm.md)<br/> [Restauración de todos los discos de máquina virtual](backup-azure-arm-restore-vms.md#restore-backed-up-disks)<br/> [Restauración de máquina virtual](backup-azure-arm-restore-vms.md#create-a-new-vm-from-a-restore-point) |

## <a name="using-premium-storage-vms-with-azure-backup"></a>Uso de máquinas virtuales de Premium Storage con Azure Backup
Azure Backup protege las máquinas virtuales de Premium Storage. Azure Premium Storage es almacenamiento basado en unidades de estado sólido (SSD) diseñado para admitir cargas de trabajo de E/S intensivas. Premium Storage es adecuado para cargas de trabajo de máquina virtual (VM). Para más información sobre Premium Storage, consulte el artículo [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../virtual-machines/windows/premium-storage.md).

### <a name="back-up-premium-storage-vms"></a>Copia de seguridad de máquinas virtuales de Premium Storage
Durante la copia de seguridad de máquinas virtuales de Premium Storage, el servicio Backup crea una ubicación de ensayo temporal, llamada "AzureBackup-" en la cuenta de Premium Storage. El tamaño de la ubicación de ensayo equivale al de la instantánea del punto de recuperación. Asegúrese de que haya espacio disponible suficiente en la cuenta de Premium Storage para dar cabida a la ubicación de ensayo temporal. Para más información, consulte las [limitaciones de Premium Storage](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets). Una vez finalizado el trabajo de copia de seguridad, se elimina la ubicación de ensayo. El precio del almacenamiento utilizado para la ubicación de ensayo es coherente con todos los [precios de Premium Storage](../virtual-machines/windows/premium-storage.md#pricing-and-billing).

> [!NOTE]
> No modifique ni edite la ubicación de ensayo.
>
>

### <a name="restore-premium-storage-vms"></a>Restauración de máquinas virtuales de Premium Storage
Las máquinas virtuales de Premium Storage se pueden restaurar a cualquier almacenamiento premium o normal. El proceso habitual de restauración consiste en restaurar un punto de recuperación de la máquina virtual de Premium Storage a Premium Storage. Sin embargo, puede ser más rentable restaurar un punto de recuperación de la máquina virtual de Premium Storage a almacenamiento estándar. Este tipo de restauración se puede utilizar si necesita un subconjunto de archivos de la máquina virtual.

## <a name="using-managed-disk-vms-with-azure-backup"></a>Uso de máquinas virtuales de disco administrado con Azure Backup
Azure Backup protege las máquinas virtuales de disco administrado. Los discos administrados le liberan de la administración de cuentas de almacenamiento de máquinas virtuales simplifican en gran medida el aprovisionamiento de las máquinas virtuales.

### <a name="back-up-managed-disk-vms"></a>Copia de seguridad de máquinas virtuales de disco administrado
Realizar copias de seguridad de máquinas virtuales en discos administrados no es tan diferente a realizar copias de seguridad de máquinas virtuales de Resource Manager. En Azure Portal, puede configurar el trabajo de copia de seguridad directamente en la vista de máquina virtual o la vista del almacén de Recovery Services. Puede realizar la copia de seguridad de máquinas virtuales en discos administrados mediante colecciones de RestorePoint basadas en discos administrados. Azure Backup también admite la realización de copias de seguridad de máquinas virtuales de discos administrados cifradas mediante Azure Disk Encryption (ADE).

### <a name="restore-managed-disk-vms"></a>Restauración de máquinas virtuales de disco administrado
Azure Backup le permite restaurar una máquina virtual completa con discos administrados o restaurar discos administrados en una cuenta de almacenamiento. Azure administra los discos administrados durante el proceso de restauración. Usted (el cliente) administra la cuenta de almacenamiento creada como parte del proceso de restauración. Al restaurar máquinas virtuales cifradas administradas, las claves y los secretos de la máquina virtual deberían existir en el almacén de claves antes de iniciarse la operación de restauración.

## <a name="what-are-the-features-of-each-backup-component"></a>¿Cuáles son las características de cada componente de Backup?
En las secciones siguientes se proporcionan tablas que resumen la disponibilidad o la compatibilidad de diversas características de cada componente de Azure Backup. Consulte la siguiente información de cada tabla para obtener ayuda o información adicional.

### <a name="storage"></a>Storage
| Característica | Agente de Azure Backup | System Center DPM | Azure Backup Server | Copia de seguridad de máquina virtual de IaaS de Azure |
| --- | --- | --- | --- | --- |
| Almacén de Recovery Services |![Sí][green] |![Sí][green] |![Sí][green] |![Sí][green] |
| Almacenamiento en disco | |![Sí][green] |![Sí][green] | |
| Almacenamiento en cinta | |![Sí][green] | | |
| Compresión <br/>(en el almacén de Recovery Services) |![Sí][green] |![Sí][green] |![Sí][green] | |
| Copia de seguridad incremental |![Sí][green] |![Sí][green] |![Sí][green] |![Sí][green] |
| Desduplicación de disco | |![Parcialmente][yellow] |![Parcialmente][yellow] | | |

![clave de tabla](./media/backup-introduction-to-azure-backup/table-key.png)

El almacén de Recovery Services es el destino de almacenamiento preferido en todos los componentes. System Center DPM y el Azure Backup Server también proporcionan la opción de tener una copia en el disco local. Sin embargo, solo System Center DPM ofrece la opción de escribir datos en un dispositivo de almacenamiento en cinta.

#### <a name="compression"></a>Compresión
Las copias de seguridad se comprimen para reducir el espacio de almacenamiento necesario. El único componente que no utiliza compresión es la extensión de VM. La extensión de máquina virtual copia todos los datos de copia de seguridad de su cuenta de almacenamiento al almacén de Recovery Services de la misma región. Al transferir los datos, no se usa ningún tipo de compresión. Cuando se transfieren los datos sin comprimir, aumenta ligeramente el almacenamiento usado. Sin embargo, el almacenamiento de datos sin compresión permite tiempos de restauración más rápidos, en caso de que necesite ese punto de recuperación.


#### <a name="disk-deduplication"></a>Desduplicación de disco
Puede aprovechar las ventajas de la desduplicación al implementar System Center DPM o Azure Backup Server [en una máquina virtual de Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). Windows Server realiza la desduplicación de los datos (en el nivel de host) en discos duros virtuales (VHD) que están conectados a la máquina virtual como almacenamiento de copia de seguridad.

> [!NOTE]
> La desduplicación no está disponible en Azure para ninguno de los componentes de Backup. Cuando System Center DPM y el Servidor de Copia de seguridad se implementan en Azure, los discos de almacenamiento conectados a la VM no se pueden desduplicar.
>
>

### <a name="incremental-backup-explained"></a>Explicación de una copia de seguridad incremental
Cada componente de Azure Backup admite la copia de seguridad incremental, con independencia del almacenamiento de destino (disco, cinta, almacén de Recovery Services). La copia de seguridad incremental garantiza que las copias de seguridad sean eficaces desde el punto de vista del almacenamiento y del tiempo, transfiriendo solo los cambios realizados desde la última copia de seguridad.

#### <a name="comparing-full-differential-and-incremental-backup"></a>Comparación entre copia de seguridad completa, diferencial o incremental

El consumo de almacenamiento, el objetivo de tiempo de recuperación (RTO) y el consumo de red varían según el tipo de método de copia de seguridad. Para reducir el costo total de propiedad (TCO), debe comprender cómo elegir la mejor solución de copia de seguridad. En la imagen siguiente se comparan la copia de seguridad completa, la copia de seguridad diferencial y la copia de seguridad incremental. En la imagen, el origen de datos A se compone de 10 bloques de almacenamiento A1-A10, de los que se hace una copia de seguridad cada mes. Los bloques A2, A3, A4 y A9 cambian en el primer mes y el bloque A5 cambia en el siguiente mes.

![imagen que muestra las comparaciones de métodos de copia de seguridad](./media/backup-introduction-to-azure-backup/backup-method-comparison.png)

Con la **copia de seguridad completa**, cada copia de seguridad contiene el origen de datos completo. La copia de seguridad completa consume una gran cantidad de ancho de banda de red y almacenamiento, cada vez que se transfiere una copia de seguridad.

La **copia de seguridad diferencial** almacena solo los bloques que cambiaron desde la copia de seguridad completa inicial, lo que da lugar a un consumo menor de red y almacenamiento. Las copias de seguridad diferenciales no conservan copias redundantes de los datos sin modificar. Sin embargo, como los bloques de datos permanecen sin cambios entre las sucesivas copias de seguridad que se transfieren y almacenan, las copias de seguridad diferenciales no son eficaces. En el segundo mes, se realiza una copia de seguridad de los bloques modificados A2, A3, A4 y A9. En el tercer mes, se vuelve a hacer una copia de seguridad de estos mismos bloques, junto con el bloque A5 modificado. Los bloques modificados se siguen copiando hasta que tiene lugar la siguiente copia de seguridad completa.

La **copia de seguridad incremental** logra una alta eficacia de almacenamiento y red al almacenar solo los bloques de datos que cambiaron desde la copia de seguridad anterior. Con la copia de seguridad incremental, no hay ninguna necesidad de realizar copias de seguridad completas periódicas. En el ejemplo, después de realizar la copia de seguridad completa el primer mes, los bloques modificados A2, A3, A4 y A9 se marcan como modificados y se transfieren al segundo mes. En el tercer mes, solo el bloque A5 modificado se marca y se transfiere. Mover menos datos ahorra recursos de almacenamiento y red, lo que reduce el TCO.   

### <a name="security"></a>Seguridad
| Característica | Agente de Azure Backup | System Center DPM | Azure Backup Server | Copia de seguridad de máquina virtual de IaaS de Azure |
| --- | --- | --- | --- | --- |
| Seguridad de las redes<br/> (en Azure) |![Sí][green] |![Sí][green] |![Sí][green] |![Parcialmente][yellow] |
| Seguridad de los datos<br/> (en Azure) |![Sí][green] |![Sí][green] |![Sí][green] |![Parcialmente][yellow] |

![clave de tabla](./media/backup-introduction-to-azure-backup/table-key.png)

#### <a name="network-security"></a>Seguridad de las redes
Todo el tráfico de copia de seguridad de los servidores al almacén de Recovery Services se cifra mediante el Estándar de cifrado avanzado 256. Los datos se envían a través de un vínculo HTTPS seguro. Los datos de copia de seguridad también se almacenan en el almacén de Recovery Services en su forma cifrada. Solo usted, el cliente de Azure, tiene la frase de contraseña para desbloquear estos datos. Microsoft no puede descifrar los datos de copia de seguridad en ningún momento.

> [!WARNING]
> Después de establecer el almacén de Recovery Services, solo usted tiene acceso a la clave de cifrado. Microsoft nunca mantiene una copia de la clave de cifrado y no tiene acceso a la clave. Si la clave se pierde, Microsoft no puede recuperar los datos de copia de seguridad.
>
>

#### <a name="data-security"></a>Seguridad de los datos
La realización de copias de seguridad de máquinas virtuales de Azure requiere la configuración del cifrado *en* la máquina virtual. Use BitLocker en máquinas virtuales Windows y **dm-crypt** en máquinas virtuales Linux. Azure Backup no cifra automáticamente los datos de copia de seguridad que circulan a través de esta ruta.

### <a name="network"></a>Red
| Característica | Agente de Azure Backup | System Center DPM | Azure Backup Server | Copia de seguridad de máquina virtual de IaaS de Azure |
| --- | --- | --- | --- | --- |
| Compresión de red <br/>(en el **servidor de copia de seguridad**) | |![Sí][green] |![Sí][green] | |
| Compresión de red <br/>(en el **almacén de Recovery Services**) |![Sí][green] |![Sí][green] |![Sí][green] | |
| Protocolo de red <br/>(en el **servidor de copia de seguridad**) | |TCP |TCP | |
| Protocolo de red <br/>(en el **almacén de Recovery Services**) |HTTPS |HTTPS |HTTPS |HTTPS |

![clave de tabla](./media/backup-introduction-to-azure-backup/table-key-2.png)

Como la extensión de VM (en la máquina virtual IaaS) lee directamente los datos de la cuenta de almacenamiento de Azure a través de la red de almacenamiento, no es necesario comprimir este tráfico.

Si usa un servidor de System Center DPM o Azure Backup Server como servidor de copia de seguridad secundario, comprima los datos que van del servidor principal al de copia de seguridad. La compresión de los datos antes de realizar una copia de seguridad en DPM o el Azure Backup Server ahorra ancho de banda.

#### <a name="network-throttling"></a>Limitación de la red
El agente de Azure Backup ofrece velocidad moderada de la red, que le permite controlar cómo se usa el ancho de banda de red durante la transferencia de datos. La limitación puede resultar útil si necesita realizar una copia de seguridad de datos durante horas de trabajo, pero no desea que el proceso de copia de seguridad interfiera con otro tráfico de Internet. La limitación en la transferencia de datos se aplica a actividades de copia de seguridad y restauración.

## <a name="backup-and-retention"></a>Copia de seguridad y retención

Azure Backup tiene un límite de 9999 puntos de recuperación, también conocidos como copias de seguridad o instantáneas, por cada *instancia protegida*. Una instancia protegida es un equipo, un servidor (físico o virtual) o una carga de trabajo configurada para realizar copias de seguridad en Azure. Para más información, consulte la sección [Descripción de una instancia protegida](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Una instancia está protegida una vez que se ha guardado una copia de seguridad de los datos. La copia de seguridad de los datos es la protección. Si los datos de origen se pierden o dañan, la copia de seguridad puede restaurar los datos de origen. En la tabla siguiente se muestra la frecuencia máxima de copia de seguridad para cada componente. La configuración de la directiva de copia de seguridad determina la rapidez con que se consumen los puntos de recuperación. Por ejemplo, si crea un punto de recuperación cada día, puede conservarlos durante 27 años antes de que se agoten. Si crea un punto de recuperación cada mes, puede conservarlos durante 833 años antes de que se agoten. El servicio Backup no establece un límite de tiempo de expiración para un punto de recuperación.

|  | Agente de Azure Backup | System Center DPM | Azure Backup Server | Copia de seguridad de máquina virtual de IaaS de Azure |
| --- | --- | --- | --- | --- |
| Frecuencia de copia de seguridad<br/> (en el almacén de Recovery Services) |Tres copias de seguridad por día |Dos copias de seguridad por día |Dos copias de seguridad por día |Una copia de seguridad por día |
| Frecuencia de copia de seguridad<br/> (en el disco) |No aplicable |<li>Cada 15 minutos para SQL Server <li>Cada hora para otras cargas de trabajo |<li>Cada 15 minutos para SQL Server <li>Cada hora para otras cargas de trabajo</p> |No aplicable |
| Opciones de retención |Diariamente, semanalmente, mensualmente y anualmente |Diariamente, semanalmente, mensualmente y anualmente |Diariamente, semanalmente, mensualmente y anualmente |Diariamente, semanalmente, mensualmente y anualmente |
| Número máximo de puntos de recuperación por instancia protegida |9.999|9.999|9.999|9.999|
| Período de retención máximo |Depende de la frecuencia de la copia de seguridad |Depende de la frecuencia de la copia de seguridad |Depende de la frecuencia de la copia de seguridad |Depende de la frecuencia de la copia de seguridad |
| Puntos de recuperación en disco local |No aplicable |<li>64 en el caso de servidores de archivos,<li>448 en el caso de servidores de aplicaciones |<li>64 en el caso de servidores de archivos,<li>448 en el caso de servidores de aplicaciones |No aplicable |
| Puntos de recuperación en cinta |No aplicable |Ilimitado |No aplicable |No aplicable |

## <a name="what-is-a-protected-instance"></a>Descripción de una instancia protegida
Una instancia protegida es una referencia genérica a un equipo de Windows, un servidor (físico o virtual) o una instancia de SQL Database que se ha configurado para realizar una copia de seguridad en Azure. Una instancia está protegida después de configurar una directiva de copia de seguridad para el equipo, el servidor o la base de datos y crear una copia de seguridad de los datos. Las copias de seguridad de los datos posteriores para esa instancia protegida (denominadas puntos de recuperación), aumenta la cantidad de almacenamiento utilizada. Puede crear hasta 9999 puntos de recuperación para una instancia protegida. Si elimina un punto de recuperación del almacenamiento, no cuenta con respecto al total de puntos de recuperación (9999).
Algunos ejemplos comunes de instancias protegidas son máquinas virtuales, servidores de aplicaciones, bases de datos y equipos personales que ejecutan el sistema operativo Windows. Por ejemplo: 

* Una máquina virtual que ejecuta el tejido de hipervisor de Hyper-V o IaaS de Azure. Los sistemas operativos invitados de la máquina virtual puede ser Windows Server o Linux.
* Un servidor de aplicaciones: el servidor de aplicaciones puede ser una máquina física o virtual con Windows Server y las cargas de trabajo con los datos de los que es necesario realizar copias de seguridad. Las cargas de trabajo habituales son las siguientes: Microsoft SQL Server, Microsoft Exchange Server, Microsoft SharePoint Server y el rol Servidor de archivos en Windows Server. Para realizar copias de seguridad de estas cargas de trabajo se necesita System Center Data Protection Manager (DPM) o Azure Backup Server.
* Un equipo personal, estación de trabajo o portátil con el sistema operativo Windows.


## <a name="what-is-a-recovery-services-vault"></a>¿Qué es un almacén de Recovery Services?
Un almacén de Recovery Services es una entidad de almacenamiento en línea en Azure que se usa para contener datos, como copias de seguridad, puntos de recuperación y directivas de copia de seguridad. Puede usar almacenes de Recovery Services para almacenar datos de copia de seguridad de servicios de Azure, y de servidores y estaciones de trabajo locales. Los almacenes de Recovery Services facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reduce al mínimo su sobrecarga administrativa. Puede crear tantos almacenes de Recovery Services como desee, dentro de una suscripción.

Los almacenes de Backup, que se basan en Azure Service Management, fueron la primera versión del almacén. Los almacenes de Recovery Services, que agregan características del modelo de Azure Resource Manager, son la segunda versión del almacén. Consulte el [artículo de introducción al almacén de Recovery Services](backup-azure-recovery-services-vault-overview.md) para una descripción completa de las diferencias en cuanto a características. Los almacenes de Backup se siguen admitiendo, aunque ya no se pueda usar el portal para crearlos. Debe usar Azure Portal para administrar los almacenes de Backup.

> [!IMPORTANT]
> Puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> Después del 30 de noviembre de 2017, ya no podrá usar PowerShell para crear almacenes de Backup y los que queden se actualizarán automáticamente a almacenes de Recovery Services.

## <a name="how-does-azure-backup-differ-from-azure-site-recovery"></a>¿Qué diferencias hay entre Azure Backup y Azure Site Recovery?
Azure Backup y Azure Site Recovery tienen en común que ambos servicios realizan una copia de seguridad de los datos y pueden restaurar dichos datos. Sin embargo, estos servicios atienden a finalidades distintas para proporcionar recuperación ante desastres y continuidad empresarial en su organización. Use Azure Backup para proteger y restaurar datos en un nivel más pormenorizado. Por ejemplo, si una presentación en un equipo portátil resulta dañada, podría usar Azure Backup para restaurarla. Si desea replicar la configuración y los datos de una máquina virtual en otro centro de datos, utilice Azure Site Recovery.

Azure Backup protege los datos locales y en la nube. Azure Site Recovery coordina la replicación de la máquina virtual y el servidor físico, la conmutación por error y la conmutación por recuperación. Ambos servicios son importantes porque la solución de recuperación ante desastres debe mantener los datos seguros y recuperables (Backup) *y* mantener las cargas de trabajo disponibles (Site Recovery) cuando se producen interrupciones.

Los siguientes conceptos pueden ayudarle a tomar decisiones importantes en torno a la copia de seguridad y la recuperación ante desastres.

| Concepto | Detalles | Backup | Recuperación ante desastres (DR) |
| --- | --- | --- | --- |
| Objetivo de punto de recuperación (RPO) |La cantidad aceptable de pérdida de datos si debe realizarse una recuperación. |Las soluciones de copia de seguridad tienen una gran variabilidad en su RPO aceptable. Las copias de seguridad de máquina virtual normalmente tienen un RPO de un día, mientras que las copias de seguridad de base de datos tienen RPO bajos, de hasta 15 minutos. |Las soluciones de recuperación ante desastres tienen RPO bajos. La copia de DR puede estar detrás de algunos segundos o minutos. |
| Objetivo de tiempo de recuperación (RTO) |La cantidad de tiempo que se tarda en completar una recuperación o restauración. |Debido a un RPO mayor, la cantidad de datos que una solución de copia de seguridad debe procesar es normalmente mucho mayor, lo que da lugar a RTO más largos. Por ejemplo, la restauración de datos de cintas puede tardar días, dependiendo del tiempo necesario para transportar la cinta desde una ubicación externa. |Las soluciones de recuperación ante desastres tienen RTO más pequeños, ya que están más en sincronización con el origen. Además, se necesita procesar menos cambios. |
| Retención |¿Cuánto tiempo necesitan almacenarse los datos? |En escenarios que requieren recuperación de operaciones (daños en los datos, eliminación involuntaria de archivos o errores del SO), los datos de copia de seguridad normalmente se conservan durante 30 días o menos.<br>Desde el punto de vista del cumplimiento de normativas, los datos podrían tener que almacenarse durante meses o incluso años. Los datos de copia de seguridad son perfectos para el archivado en tales casos. |La recuperación ante desastres solo necesita datos de recuperación operativos, lo que normalmente tarda unas pocas horas o hasta un día. Debido a la captura de datos específicos que se usa en soluciones de recuperación ante desastres, la retención a largo plazo no se recomienda si se usan datos de recuperación ante desastres. |

## <a name="next-steps"></a>pasos siguientes
Use uno de los siguientes tutoriales para obtener instrucciones paso a paso para proteger los datos en Windows Server o proteger una máquina virtual (VM) en Azure:

* [Realizar copias de seguridad de archivos y carpetas](backup-try-azure-backup-in-10-mins.md)
* [Copia de seguridad de Azure Virtual Machines](backup-azure-vms-first-look-arm.md)

Para más información sobre cómo proteger otras cargas de trabajo, pruebe uno de estos artículos:

* [Copia de seguridad de Windows Server](backup-configure-vault.md)
* [Copia de seguridad de cargas de trabajo de aplicaciones](backup-azure-microsoft-azure-backup.md)
* [Copia de seguridad de máquinas virtuales de IaaS de Azure](backup-azure-arm-vms-prepare.md)

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
