---
title: "¿Qué es la Copia de seguridad de Azure? | Microsoft Docs"
description: "Con los servicios de recuperación y Copia de seguridad de Azure puede realizar una copia de seguridad y restaurar los datos y aplicaciones de servidores Windows, equipos cliente de Windows, servidores de System Center DPM y máquinas virtuales de Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: tysonn
keywords: "copia de seguridad y restauración; servicios de recuperación; soluciones de copia de seguridad"
ms.assetid: 0d2a7f08-8ade-443a-93af-440cbf7c36c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2016
ms.author: jimpark; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: cf3930f209e84ee9b14b56566ca19d31382946aa
ms.openlocfilehash: cefb405b4f30ca5fe20f6acfaee5ebba2690990b


---
# <a name="what-is-azure-backup"></a>¿Qué es la Copia de seguridad de Azure?
Azure Backup es el servicio de Azure que puede usar para realizar una copia de seguridad de los datos (protegerlos) y recuperarlos en la nube de Microsoft. Reemplaza su solución de copia de seguridad local o remota existente por una solución confiable, segura y rentable basada en la nube. Azure Backup ofrece varios componentes que se descargan e implementan en el equipo o servidor adecuados, o en la nube. El componente, o agente, que se implemente depende de lo que quiera proteger. Todos los componentes de Azure Backup (sin importar si va a proteger los datos de forma local o en la nube) se pueden usar para realizar una copia de seguridad de los datos en un almacén de copia de seguridad de Azure. Para más información sobre qué componente usar para proteger datos, aplicaciones o cargas de trabajo específicos, consulte la [tabla de componentes de Azure Backup](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use) (más adelante en este artículo).

[Ver un vídeo de información general de Copia de seguridad de Azure](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## <a name="why-use-azure-backup"></a>¿Por qué usar Copia de seguridad de Azure?
Las soluciones de copia de seguridad tradicionales han evolucionado para tratar la nube como un punto de conexión, o destino de almacenamiento estático, similar a un disco o una cinta. Aunque este enfoque es simple, es limitado y no aprovecha una plataforma de nube subyacente, lo que se traduce en una solución ineficaz y costosa. Algunas soluciones son costosas porque se acaba pagando por el tipo incorrecto de almacenamiento o almacenamiento que no necesita. Otras soluciones a menudo son ineficaces porque no ofrecen el tipo o la cantidad de almacenamiento que necesita, o las tareas administrativas exigen demasiado tiempo. En cambio, Azure Backup proporciona las siguientes ventajas principales:

**Administración de almacenamiento automática**: los entornos híbridos requieren con frecuencia almacenamiento heterogéneo, unos local y otros en la nube. Con Azure Backup, no hay ningún costo por el uso de dispositivos de almacenamiento local. Azure Backup asigna y administra automáticamente almacenamiento de copia de seguridad y emplea un modelo de pago por uso. Esto significa que solo paga por el almacenamiento que consume. Para más información, consulte el [artículo sobre precios de Azure](https://azure.microsoft.com/pricing/details/backup).

**Escalado ilimitado**: Azure Backup usa la eficacia subyacente y la escala ilimitada de la nube de Azure para proporcionar alta disponibilidad, sin sobrecarga de mantenimiento o supervisión. Puede configurar alertas para proporcionar información sobre los eventos, pero no tiene que preocuparse por la alta disponibilidad de los datos en la nube.

**Varias opciones de almacenamiento**: un aspecto de alta disponibilidad es la replicación del almacenamiento. Azure Backup ofrece dos tipos de replicación: [almacenamiento con redundancia local](../storage/storage-redundancy.md#locally-redundant-storage) y [almacenamiento con replicación geográfica](../storage/storage-redundancy.md#geo-redundant-storage). Elija la opción de almacenamiento de copia de seguridad más acorde con sus necesidades:

* El almacenamiento con redundancia local (LRS) replica los datos tres veces (crea tres copias de los datos) en un centro de datos emparejado de la misma región. LRS es una opción de bajo costo y resulta perfecto para los clientes a quienes les preocupa el precio porque protege los datos frente a los errores de hardware locales.
* El almacenamiento de replicación geográfica (GRS) replica los datos en una región secundaria (a cientos de kilómetros de la ubicación principal del origen de datos). GRS cuesta más que LRS, pero proporciona un mayor nivel de durabilidad de los datos, incluso si hay una interrupción regional.

**Transferencia de datos ilimitada**: Azure Backup no limita la cantidad de datos de entrada y salida que se transfieren. Tampoco cobra por los datos que se transfieren. Sin embargo, si usa el servicio Azure Import/Export para importar grandes cantidades de datos, existe un costo asociado con los datos de entrada. Para más información sobre este costo, consulte [Flujo de trabajo de copia de seguridad sin conexión en Azure Backup](backup-azure-backup-import-export.md). Los datos de salida hacen referencia a los datos transferidos desde un almacén de copia de seguridad durante una operación de restauración.

**Cifrado de datos**: el cifrado de datos permite la transmisión y el almacenamiento seguros de los datos en la nube pública. La frase de contraseña de cifrado se almacena localmente y nunca se transmite ni se almacena en Azure. Si es necesario restaurar algunos de los datos, solo cuenta con la frase de contraseña o clave de cifrado.

**Copia de seguridad coherente con la aplicación**: tanto si va a realizar una copia de seguridad de un servidor de archivos, una máquina virtual o una base de datos SQL, debe saber que un punto de recuperación tiene todos los datos necesarios para restaurar la copia de seguridad. Azure Backup proporciona copias de seguridad coherentes con la aplicación, lo que garantiza que no se necesitan correcciones adicionales para restaurar los datos. La restauración de datos coherentes con la aplicación reduce el tiempo de restauración, lo que le permite volver rápidamente a un estado de ejecución.

**Retención a largo plazo**: cree una copia de seguridad de los datos en Azure durante 99 años. En lugar de pasar las copias de seguridad de disco a cinta y luego trasladar la cinta a una ubicación externa para almacenamiento a largo plazo, puede usar Azure para la retención a corto y largo plazo.

## <a name="which-azure-backup-components-should-i-use"></a>¿Qué componentes de Azure Backup debo usar?
Si no está seguro de qué componentes de Azure Backup vienen bien para sus necesidades, consulte la siguiente tabla para información sobre lo que puede proteger con cada uno. Azure Portal proporciona un asistente, que está integrado en el portal, para guiarle a través de la elección de los componentes para descargar e implementar. El asistente, que forma parte de la creación del almacén de Recovery Services, le lleva por los pasos para seleccionar un objetivo de copia de seguridad y elegir los datos o la aplicación para proteger.

| Componente | Ventajas | límites | ¿Qué se protege? | ¿Dónde se almacenan las copias de seguridad? |
| --- | --- | --- | --- | --- |
| Agente de Copia de seguridad de Azure (MARS) |<li>Copia de seguridad de archivos y carpetas en el sistema operativo Windows físico o virtual (las máquinas virtuales pueden estar en el entorno local o en Azure)<li>No se necesita ningún servidor de copia de seguridad independiente. |<li>Copia de seguridad tres veces al día <li>No es compatible con la aplicación; restauración solo a nivel de archivo, carpeta y volumen. <li>  No se admite Linux. |<li>Archivos <li>Carpetas |Almacén de copia de seguridad de Azure |
| System Center DPM |<li>Instantáneas compatibles con la aplicación (VSS)<li>Total flexibilidad para realizar copias de seguridad<li>Granularidad en la recuperación (todo)<li>Puede usar el almacén de Azure Backup<li>Compatibilidad con Linux (si se hospeda en Hyper-V) <li>Protección de máquinas virtuales de VMware con DPM 2012 R2 |No admite copia de seguridad de la carga de trabajo de Oracle |<li>Archivos <li>Carpetas<li> Volúmenes <li>Máquinas virtuales<li> Aplicaciones<li> Cargas de trabajo |<li>Almacén de Azure Backup<li> Disco conectado localmente<li>  Cinta (solo local) |
| Servidor de Copia de seguridad de Azure |<li>Instantáneas compatibles con la aplicación (VSS)<li>Total flexibilidad para realizar copias de seguridad<li>Granularidad en la recuperación (todo)<li>Puede usar el almacén de Azure Backup<li>Compatibilidad con Linux (si se hospeda en Hyper-V)<li>No requiere licencia de System Center |<li>Falta de soporte heterogéneo (copia de seguridad de máquina virtual de VMware, copia de seguridad de carga de trabajo de Oracle).<li>Siempre requiere una suscripción de Azure activa<li>No se admite la copia de seguridad en cinta |<li>Archivos <li>Carpetas<li> Volúmenes <li>Máquinas virtuales<li> Aplicaciones<li> Cargas de trabajo |<li>Almacén de Azure Backup<li> Disco conectado localmente |
| Copia de seguridad de máquina virtual de IaaS de Azure |<li>Copias de seguridad nativas de Windows/Linux<li>No se requiere la instalación de ningún agente específico<li>Copia de seguridad de nivel de tejido sin necesidad de ninguna infraestructura de copia de seguridad |<li>Copia de seguridad de máquinas virtuales una vez al día <li>Restauración de máquinas virtuales solo en el nivel de disco<li>No puede realizar copias de seguridad locales |<li>Máquinas virtuales <li>Todos los discos (con PowerShell) |<p>Almacén de copia de seguridad de Azure</p> |

## <a name="what-are-the-deployment-scenarios-for-each-component"></a>¿Cuáles son los escenarios de implementación de cada componente?
| Componente | ¿Se puede implementar en Azure? | ¿Se puede implementar de forma local? | Almacenamiento de destino admitido |
| --- | --- | --- | --- |
| Agente de Copia de seguridad de Azure (MARS) |<p>**Sí**</p> <p>El agente de Azure Backup se puede implementar en cualquier máquina virtual Windows que se ejecute en Azure.</p> |<p>**Sí**</p> <p>El agente de Backup se puede implementar en cualquier máquina física o virtual de Windows Server.</p> |<p>Almacén de copia de seguridad de Azure</p> |
| System Center DPM |<p>**Sí**</p><p>Más información [sobre cómo proteger las cargas de trabajo en Azure mediante System Center DPM](backup-azure-dpm-introduction.md).</p> |<p>**Sí**</p> <p>Más información sobre [cómo proteger cargas de trabajo y máquinas virtuales en su centro de datos](https://technet.microsoft.com/en-us/system-center-docs/dpm/data-protection-manager).</p> |<p>Disco conectado localmente</p> <p>Almacén de Azure Backup</p> <p>Cinta (solo local)</p> |
| Servidor de Copia de seguridad de Azure |<p>**Sí**</p><p>Más información sobre [cómo proteger cargas de trabajo en Azure con el servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md).</p> |<p>**Sí**</p> <p>Más información sobre [cómo proteger cargas de trabajo en Azure con el servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md).</p> |<p>Disco conectado localmente</p> <p>Almacén de copia de seguridad de Azure</p> |
| Copia de seguridad de máquina virtual de IaaS de Azure |<p>**Sí**</p><p>Parte del tejido de Azure</p><p>Especializado para [copia de seguridad de máquinas virtuales de infraestructura como servicio (IaaS) de Azure](backup-azure-vms-introduction.md).</p> |<p>**No**</p> <p>Use System Center DPM para hacer copias de seguridad de máquinas virtuales en su centro de datos.</p> |<p>Almacén de copia de seguridad de Azure</p> |

## <a name="which-applications-and-workloads-can-be-backed-up"></a>¿De qué aplicaciones y cargas de trabajo puedo hacer copias de seguridad?
En la tabla siguiente se proporciona una matriz de los datos y las cargas de trabajo que se pueden proteger mediante Azure Backup. La columna de la solución de Azure Backup tiene vínculos a la documentación de implementación para esa solución. Cada componente de Azure Backup puede implementarse en un entorno de modelo de implementación clásico (implementación mediante Service Manager) o de Resource Manager.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

| Datos o carga de trabajo | Entorno de origen | Solución de Copia de seguridad de Azure |
| --- | --- | --- |
| Archivos y carpetas |Windows Server |<p>[Agente de Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ el agente de Azure Backup)</p> <p>[Servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Archivos y carpetas |Equipo de Windows |<p>[Agente de Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ el agente de Azure Backup)</p> <p>[Servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Máquina virtual de Hyper-V (Windows) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Máquina virtual de Hyper-V (Linux) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Microsoft SQL Server |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Microsoft SharePoint |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Microsoft Exchange |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ el agente de Azure Backup)</p> <p>[Servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md) (incluye el agente de Azure Backup)</p> |
| Máquinas virtuales de Azure IaaS (Windows) |Ejecución en Azure |[Copia de seguridad de Azure (extensión de máquina virtual)](backup-azure-vms-introduction.md) |
| Máquinas virtuales de IaaS de Azure (Linux) |Ejecución en Azure |[Copia de seguridad de Azure (extensión de máquina virtual)](backup-azure-vms-introduction.md) |

## <a name="linux-support"></a>Compatibilidad con Linux
En la siguiente tabla se muestran los componentes de Azure Backup que son compatibles con Linux.  

| Componente | Compatibilidad con Linux (reconocido por Azure) |
| --- | --- |
| Agente de Copia de seguridad de Azure (MARS) |No (solo agente basado en Windows) |
| System Center DPM |Solo copia de seguridad coherente con archivo en Hyper-V<br/> (no disponible para máquina virtual de Azure) |
| Servidor de Copia de seguridad de Azure |Solo copia de seguridad coherente con archivo en Hyper-V<br/> (no disponible para máquina virtual de Azure) |
| Copia de seguridad de máquina virtual de IaaS de Azure |Sí |

## <a name="using-premium-storage-vms-with-azure-backup"></a>Uso de máquinas virtuales de Premium Storage con Azure Backup
Azure Backup protege las máquinas virtuales con Premium Storage. Azure Premium Storage es almacenamiento basado en unidades de estado sólido (SSD) diseñado para admitir cargas de trabajo de E/S intensivas. Premium Storage es adecuado para cargas de trabajo de máquina virtual (VM). Para más información sobre Premium Storage, consulte el artículo [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md).

### <a name="back-up-premium-storage-vms"></a>Copia de seguridad de máquinas virtuales de almacenamiento premium
Durante la copia de seguridad de máquinas virtuales de almacenamiento premium, el servicio de copia de seguridad crea una ubicación de ensayo temporal en la cuenta de almacenamiento premium. La ubicación de ensayo, denominada "AzureBackup-" es igual al tamaño total de los datos de los discos premium conectados a la máquina virtual.

> [!NOTE]
> No modifique ni edite la ubicación de ensayo.
>
>

Una vez finalizado el trabajo de copia de seguridad, se elimina la ubicación de ensayo. El precio del almacenamiento utilizado para la ubicación de ensayo es coherente con todos los [precios de almacenamiento premium](../storage/storage-premium-storage.md#pricing-and-billing).

### <a name="restore-premium-storage-vms"></a>Restauración de máquinas virtuales de almacenamiento premium
Las máquinas virtuales de Premium Storage se pueden restaurar a cualquier almacenamiento premium o normal. El proceso habitual de restauración consiste en restaurar un punto de recuperación de la máquina virtual de almacenamiento premium a almacenamiento premium. Sin embargo, puede ser más rentable restaurar un punto de recuperación de la máquina virtual de almacenamiento premium a almacenamiento estándar. Este tipo de restauración se puede utilizar si necesita un subconjunto de archivos de la máquina virtual.

## <a name="what-are-the-features-of-each-backup-component"></a>¿Cuáles son las características de cada componente de Backup?
En las secciones siguientes se proporcionan tablas que resumen la disponibilidad o la compatibilidad de diversas características de cada componente de Azure Backup. Consulte la siguiente información de cada tabla para obtener ayuda o información adicional.

### <a name="storage"></a>Almacenamiento
| Característica | Agente de copia de seguridad de Azure | System Center DPM | Servidor de Copia de seguridad de Azure | Copia de seguridad de máquina virtual de IaaS de Azure |
| --- | --- | --- | --- | --- |
| Almacén de copia de seguridad de Azure |![Sí][green] |![Sí][green] |![Sí][green] |![Sí][green] |
| Almacenamiento en disco | |![Sí][green] |![Sí][green] | |
| Almacenamiento en cinta | |![Sí][green] | | |
| Compresión <br/>(en el almacén de copia de seguridad) |![yes][green] |![Sí][green] |![Sí][green] | |
| Copia de seguridad incremental |![Sí][green] |![Sí][green] |![Sí][green] |![Sí][green] |
| Desduplicación de disco | |![Parcialmente][yellow] |![Parcialmente][yellow] | |

![clave de tabla](./media/backup-introduction-to-azure-backup/table-key.png)

El almacén de Copia de seguridad es el destino de almacenamiento preferido en todos los componentes. System Center DPM y el servidor de copia de seguridad de Azure también proporcionan la opción de tener una copia en el disco local. Sin embargo, solo System Center DPM ofrece la opción de escribir datos en un dispositivo de almacenamiento en cinta.

#### <a name="compression"></a>Compresión
Las copias de seguridad se comprimen para reducir el espacio de almacenamiento necesario. El único componente que no utiliza compresión es la extensión de VM. La extensión de máquina virtual copia todos los datos de copia de seguridad desde su cuenta de almacenamiento en el Almacén de copia de seguridad de la misma región. Al transferir los datos, no se usa ningún tipo de compresión. Cuando se transfieren los datos sin comprimir, aumenta ligeramente el almacenamiento usado. Sin embargo, el almacenamiento de datos sin compresión permite tiempos de restauración más rápidos, en caso de que necesite ese punto de recuperación.

#### <a name="incremental-backup"></a>Copia de seguridad incremental
Cada componente admite la copia de seguridad incremental independientemente del tipo de almacenamiento de destino (disco, cinta, almacén de copia de seguridad). La copia de seguridad incremental garantiza que las copias de seguridad sean eficaces desde el punto de vista del almacenamiento y del tiempo, transfiriendo solo los cambios realizados desde la última copia de seguridad.

#### <a name="disk-deduplication"></a>Desduplicación de disco
Puede aprovechar las ventajas de la desduplicación al implementar System Center DPM o Azure Backup Server [en una máquina virtual de Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). Windows Server realiza la desduplicación de los datos (en el nivel de host) en discos duros virtuales (VHD) que están conectados a la máquina virtual como almacenamiento de copia de seguridad.

> [!NOTE]
> La desduplicación no está disponible en Azure para ninguno de los componentes de Backup. Cuando System Center DPM y el Servidor de Copia de seguridad se implementan en Azure, los discos de almacenamiento conectados a la VM no se pueden desduplicar.
>
>

### <a name="security"></a>Seguridad
| Característica | Agente de copia de seguridad de Azure | System Center DPM | Servidor de Copia de seguridad de Azure | Copia de seguridad de máquina virtual de IaaS de Azure |
| --- | --- | --- | --- | --- |
| Seguridad de las redes<br/> (en Azure) |![Sí][green] |![Sí][green] |![Sí][green] |![Parcialmente][yellow] |
| Seguridad de los datos<br/> (en Azure) |![Sí][green] |![Sí][green] |![Sí][green] |![Parcialmente][yellow] |

![clave de tabla](./media/backup-introduction-to-azure-backup/table-key.png)

#### <a name="network-security"></a>Seguridad de las redes
Todo el tráfico de copia de seguridad de los servidores al almacén de copia de seguridad se cifra mediante el Estándar de cifrado avanzado 256. Los datos se envían a través de un vínculo HTTPS seguro. Los datos de copia de seguridad también se almacenan en el almacén de Copia de seguridad en su forma cifrada. Solo usted, el cliente de Azure, tiene la frase de contraseña para desbloquear estos datos. Microsoft no puede descifrar los datos de copia de seguridad en ningún momento.

> [!WARNING]
> Después de establecer el almacén de copia de seguridad, solo usted tiene acceso a la clave de cifrado. Microsoft nunca mantiene una copia de la clave de cifrado y no tiene acceso a la clave. Si la clave se pierde, Microsoft no puede recuperar los datos de copia de seguridad.
>
>

#### <a name="data-security"></a>Seguridad de los datos
La realización de copias de seguridad de máquinas virtuales de Azure requiere la configuración del cifrado *en* la máquina virtual. Use BitLocker en máquinas virtuales Windows y **dm-crypt** en máquinas virtuales Linux. Copia de seguridad de Azure no cifra automáticamente los datos de copia de seguridad que circulan a través de esta ruta.

### <a name="network"></a>Red
| Característica | Agente de copia de seguridad de Azure | System Center DPM | Servidor de Copia de seguridad de Azure | Copia de seguridad de máquina virtual de IaaS de Azure |
| --- | --- | --- | --- | --- |
| Compresión de red <br/>(en el **servidor de copia de seguridad**) | |![Sí][green] |![yes][green] | |
| Compresión de red <br/>(en el **almacén de copia de seguridad**) |![Sí][green] |![Sí][green] |![Sí][green] | |
| Protocolo de red <br/>(en el **servidor de copia de seguridad**) | |TCP |TCP | |
| Protocolo de red <br/>(en el **almacén de copia de seguridad**) |HTTPS |HTTPS |HTTPS |HTTPS |

![clave de tabla](./media/backup-introduction-to-azure-backup/table-key-2.png)

Como la extensión de VM (en la máquina virtual IaaS) lee directamente los datos de la cuenta de almacenamiento de Azure a través de la red de almacenamiento, no es necesario comprimir este tráfico.

Si la copia de seguridad de los datos se va a realizar en System Center DPM o en un servidor de copia de seguridad de Azure, comprima los datos que van del servidor principal al servidor de copia de seguridad. La compresión de los datos antes de realizar una copia de seguridad en DPM o el servidor de copia de seguridad de Azure ahorra ancho de banda.

#### <a name="network-throttling"></a>Limitación de la red
El agente de Azure Backup ofrece velocidad moderada de la red, que le permite controlar cómo se usa el ancho de banda de red durante la transferencia de datos. La limitación puede resultar útil si necesita realizar una copia de seguridad de datos durante horas de trabajo, pero no desea que el proceso de copia de seguridad interfiera con otro tráfico de Internet. La limitación en la transferencia de datos se aplica a actividades de copia de seguridad y restauración.

### <a name="backup-and-retention"></a>Copia de seguridad y retención
|  | Agente de copia de seguridad de Azure | System Center DPM | Servidor de Copia de seguridad de Azure | Copia de seguridad de máquina virtual de IaaS de Azure |
| --- | --- | --- | --- | --- |
| Frecuencia de copia de seguridad<br/> (en el almacén de copia de seguridad) |Tres copias de seguridad por día |Dos copias de seguridad por día |Dos copias de seguridad por día |Una copia de seguridad por día |
| Frecuencia de copia de seguridad<br/> (en el disco) |No aplicable |<li>Cada 15 minutos para SQL Server <li>Cada hora para otras cargas de trabajo |<li>Cada 15 minutos para SQL Server <li>Cada hora para otras cargas de trabajo</p> |No aplicable |
| Opciones de retención |Diariamente, semanalmente, mensualmente y anualmente |Diariamente, semanalmente, mensualmente y anualmente |Diariamente, semanalmente, mensualmente y anualmente |Diariamente, semanalmente, mensualmente y anualmente |
| Período de retención |Hasta 99 años |Hasta 99 años |Hasta 99 años |Hasta 99 años |
| Puntos de recuperación en el almacén de Copia de seguridad |Sin límite |Sin límite |Sin límite |Sin límite |
| Puntos de recuperación en disco local |No aplicable |<li>64 en el caso de servidores de archivos,<li>448 en el caso de servidores de aplicaciones |<li>64 en el caso de servidores de archivos,<li>448 en el caso de servidores de aplicaciones |No aplicable |
| Puntos de recuperación en cinta |No aplicable |Sin límite |No aplicable |No aplicable |

## <a name="what-is-the-vault-credential-file"></a>¿Qué es el archivo de credenciales de almacén?
El archivo de credenciales de almacén es un certificado generado por el portal para cada almacén de copia de seguridad. Luego el portal carga la clave pública en el Servicio de control de acceso (ACS). La clave privada se proporciona al descargar las credenciales, y se usa para registrar los equipos que se protegen. La clave privada es lo que le permite autenticar los servidores o equipos para enviar datos de copia de seguridad a un determinado almacén de copia de seguridad.

La credencial de almacén solo se usa para registrar los servidores o los equipos. Sin embargo, tenga cuidado con las credenciales de almacén; si se pierden o las consiguen otros usuarios, se pueden usar para registrar otras máquinas con el mismo almacén. Puesto que los datos de copia de seguridad se cifran con una frase de contraseña, a la que solo usted puede acceder, los datos de copia de seguridad existentes no pueden estar en peligro. Las credenciales de almacén expiran a las 48 horas. Aunque puede descargar las credenciales de almacén del almacén de copia de seguridad con tanta frecuencia como desee, solo las más recientes se pueden usar para el registro.

## <a name="how-does-azure-backup-differ-from-azure-site-recovery"></a>¿Qué diferencias hay entre Copia de seguridad de Azure y Azure Site Recovery?
Azure Backup y Azure Site Recovery están relacionados porque ambos servicios realizan una copia de seguridad de los datos y pueden restaurarlos, pero sus propuestas clave de valor son distintas.

Azure Backup protege los datos locales y en la nube. Azure Site Recovery coordina la replicación de la máquina virtual y el servidor físico, la conmutación por error y la conmutación por recuperación. Ambos servicios son importantes porque la solución de recuperación ante desastres debe mantener los datos seguros y recuperables (Copia de seguridad) *y* mantener las cargas de trabajo disponibles (Recuperación del sitio) cuando se producen interrupciones.

Los siguientes conceptos pueden ayudarle a tomar decisiones importantes en torno a la copia de seguridad y la recuperación ante desastres.

| Concepto | Detalles | Copia de seguridad | Recuperación ante desastres (DR) |
| --- | --- | --- | --- |
| Objetivo de punto de recuperación (RPO) |La cantidad aceptable de pérdida de datos si debe realizarse una recuperación. |Las soluciones de copia de seguridad tienen una gran variabilidad en su RPO aceptable. Las copias de seguridad de máquina virtual normalmente tienen un RPO de un día, mientras que las copias de seguridad de base de datos tienen RPO bajos, de hasta 15 minutos. |Las soluciones de recuperación ante desastres tienen RPO bajos. La copia de DR puede estar detrás de algunos segundos o minutos. |
| Objetivo de tiempo de recuperación (RTO) |La cantidad de tiempo que se tarda en completar una recuperación o restauración. |Debido a un RPO mayor, la cantidad de datos que una solución de copia de seguridad debe procesar es normalmente mucho mayor, lo que da lugar a RTO más largos. Por ejemplo, la restauración de datos de cintas puede tardar días, dependiendo del tiempo necesario para transportar la cinta desde una ubicación externa. |Las soluciones de recuperación ante desastres tienen RTO más pequeños, ya que están más en sincronización con el origen. Además, se necesita procesar menos cambios. |
| Retención |¿Cuánto tiempo necesitan almacenarse los datos? |En escenarios que requieren recuperación de operaciones (daños en los datos, eliminación involuntaria de archivos o errores del SO), los datos de copia de seguridad normalmente se conservan durante 30 días o menos.<br>Desde el punto de vista del cumplimiento de normativas, los datos podrían tener que almacenarse durante meses o incluso años. Los datos de copia de seguridad son perfectos para el archivado en tales casos. |La recuperación ante desastres solo necesita datos de recuperación operativos, lo que normalmente tarda unas pocas horas o hasta un día. Debido a la captura de datos específicos que se usa en soluciones de recuperación ante desastres, la retención a largo plazo no se recomienda si se usan datos de recuperación ante desastres. |

## <a name="next-steps"></a>Pasos siguientes
Use uno de los siguientes tutoriales para obtener instrucciones paso a paso para proteger los datos en Windows Server o proteger una máquina virtual (VM) en Azure:

* [Realizar copias de seguridad de archivos y carpetas](backup-try-azure-backup-in-10-mins.md)
* [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-first-look-arm.md)

Para más información sobre cómo proteger otras cargas de trabajo, pruebe uno de estos artículos:

* [Copia de seguridad de Windows Server](backup-configure-vault.md)
* [Copia de seguridad de cargas de trabajo de aplicaciones](backup-azure-microsoft-azure-backup.md)
* [Copia de seguridad de máquinas virtuales de IaaS de Azure](backup-azure-vms-prepare.md)

[verde]: ./media/backup-introduction-to-azure-backup/green.png
[amarillo]: ./media/backup-introduction-to-azure-backup/yellow.png
[rojo]: ./media/backup-introduction-to-azure-backup/red.png



<!--HONumber=Nov16_HO3-->


