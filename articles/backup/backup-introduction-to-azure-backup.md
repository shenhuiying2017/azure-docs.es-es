<properties
	pageTitle="¿Qué es Copia de seguridad de Azure? | Microsoft Azure"
	description="Con los servicios de recuperación y Copia de seguridad de Azure puede realizar una copia de seguridad y restaurar los datos y aplicaciones de servidores Windows, equipos cliente de Windows, servidores SCDPM o máquinas virtuales de Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="copia de seguridad y restauración; servicios de recuperación"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="trinadhk;jimpark"/>

# ¿Qué es la Copia de seguridad de Azure?
Copia de seguridad de Azure es el servicio usado para hacer copias de seguridad y restauraciones de los datos en la nube de Microsoft. Reemplaza su solución de copia de seguridad local o remota existente por una solución confiable, segura y rentable basada en la nube. También protege los recursos que se ejecutan en la nube. Copia de seguridad de Azure proporciona servicios de recuperación basados en una infraestructura de clase mundial escalable, duradera y de alta disponibilidad.

[Ver un vídeo de información general de Copia de seguridad de Azure](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## ¿Por qué usar Copia de seguridad de Azure?
Las soluciones de copia de seguridad tradicionales han evolucionado para tratar la nube como un punto de conexión similar al disco o la cinta. Aunque este enfoque es simple, es limitado y no aprovecha una plataforma en la nube subyacente. Esto se traduce en una solución ineficaz y costosa. Por otra parte, Copia de seguridad de Azure ofrece todas las ventajas de una solución de copia de seguridad en la nube eficaz y asequible. A continuación se mencionan algunas de las ventajas clave que ofrece Copia de seguridad de Azure:

| CARACTERÍSTICA | VENTAJA |
| ------- | ------- |
| Administración automática del almacenamiento | No se necesita inversión de capital para los dispositivos de almacenamiento locales. Copia de seguridad de Azure asigna y administra almacenamiento de copia de seguridad automáticamente, con un modelo de consumo de pago por uso. |
| Escalado ilimitado | Garantías de alta disponibilidad sin la sobrecarga de mantenimiento y supervisión. Copia de seguridad de Azure utiliza la eficacia y escala subyacentes de la nube de Azure, con funcionalidades de escalado automático no intrusivo. |
| Varias opciones de almacenamiento | Elija el almacenamiento de copia de seguridad según las necesidades: <li>Blob en bloques de almacenamiento con redundancia local (LRS) es ideal para los clientes a los que les preocupa el precio y, al mismo tiempo, protege los datos contra errores de hardware locales. <li>Blob en bloques de almacenamiento replicación geográfica (GRS) proporciona 3 copias adicionales en un centro de datos asociado, lo que garantiza que los datos de copia de seguridad están altamente disponibles incluso en el caso de un desastre que afecte al sitio de Azure. |
| Transferencia de datos ilimitada | No tiene que pagar ningún costo de transferencia de datos de salida (saliente) durante una operación de restauración desde el almacén de Copia de seguridad de Azure. La entrada de datos a Azure también es gratis. |
| Administración central | Simplicidad y familiaridad del Portal de Azure. A medida que evoluciona el servicio, las características como la administración central le permitirán administrar su infraestructura de copia de seguridad desde una sola ubicación. |
| Cifrado de datos | Transmisión y almacenamiento seguros de los datos de cliente en la nube pública. La frase de contraseña de cifrado se almacena en el origen y nunca se transmite o se almacena en Azure. La clave de cifrado es necesaria para restaurar datos, y solo el cliente tiene acceso completo a los datos en el servicio. |  
| Copias de seguridad coherentes con la aplicación | Las copias de seguridad coherentes con la aplicación en Windows garantizan que no se necesita una corrección en el momento de la restauración. Esto reduce el RTO y permite a los clientes devolver un estado de ejecución más rápido. |
| Retención a largo plazo | En lugar de pagar soluciones de copia de seguridad de cinta fuera del sitio, los clientes pueden hacer la copia de seguridad en Azure, lo que proporciona una atractiva solución similar a la de la cinta, pero con un costo muy bajo. |

## Componentes de Copia de seguridad de Azure
Dado que Copia de seguridad de Azure es una solución de copia de seguridad híbrida, consta de varios componentes que trabajan conjuntamente para habilitar los flujos de trabajo de copia de seguridad y restauración integrales.

![Componentes de Copia de seguridad de Azure](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## Escenarios de implementación

| Componente | ¿Se puede implementar en Azure? | ¿Se puede implementar de forma local? | Almacenamiento de destino admitido|
| --- | --- | --- | --- |
| Agente de copia de seguridad de Azure | <p>**Sí**</p> <p>El agente de Copia de seguridad de Azure se puede implementar en cualquier VM de Windows Server que se ejecute en Azure.</p> | <p>**Sí**</p> <p>El agente de Copia de seguridad de Azure se puede implementar en cualquier VM de Windows Server o máquina física.</p> | <p>Almacén de copia de seguridad de Azure</p> |
| System Center Data Protection Manager (SCDPM) | <p>**Sí**</p> <p>Obtenga más información sobre la [protección de cargas de trabajo en Azure con SCDPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Sí**</p> <p>Obtenga más información sobre la [protección de cargas de trabajo y VM en su centro de datos](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Disco conectado localmente,</p> <p>almacén de Copia de seguridad de Azure,</p> <p>Cinta (solo local)</p> |
| Servidor de Copia de seguridad de Azure | <p>**Sí**</p> <p>Obtenga más información sobre la [protección de cargas de trabajo en Azure con el Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>**Sí**</p> <p>Obtenga más información sobre la [protección de cargas de trabajo en Azure con el Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>Almacén de copia de seguridad de Azure</p> |
| Copia de seguridad de Azure (extensión de máquina virtual) | <p>Sí</p> <p>Especializado para [copia de seguridad de máquinas virtuales de IaaS de Azure](backup-azure-vms-introduction.md).</p> | <p>**No**</p> <p>Use SCDPM para hacer copias de seguridad de máquinas virtuales en su centro de datos.</p> | <p>Almacén de copia de seguridad de Azure</p> |

## ¿Qué aplicaciones y cargas de trabajo puedo incluir en las copias de seguridad?

| Carga de trabajo | Máquina de origen | Solución de Copia de seguridad de Azure |
| --- | --- |---|
| Archivos y carpetas | Windows Server | <p>[Agente de Copia de seguridad de Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Archivos y carpetas | Cliente Windows | <p>[Agente de Copia de seguridad de Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Máquina virtual de Hyper-V (Windows) | Windows Server | <p>System Center DPM,</p> <p>[Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Máquina virtual de Hyper-V (Linux) | Windows Server | <p>System Center DPM,</p> <p>[Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Máquinas virtuales de IaaS de Azure (Windows)| - | [Copia de seguridad de Azure (Extensión de máquina virtual)](backup-azure-vms-introduction.md) | | Máquinas virtuales de IaaS de Azure (Linux) | - | [Copia de seguridad de Azure (Extensión de máquina virtual)](backup-azure-vms-introduction.md) |

## Funcionalidad
En estas tablas se resume cómo se controla la funcionalidad Copia de seguridad de Azure en cada componente:

### 1\. Almacenamiento

| Característica | Agente de copia de seguridad de Azure | SCDPM | Servidor de Copia de seguridad de Azure | Copia de seguridad de Azure (extensión de máquina virtual) |
| ------- | --- | --- | --- | ---- |
| Almacén de copia de seguridad de Azure | ![Sí][green] | ![Sí][green] | ![Sí][green] | ![Sí][green] |
| Almacenamiento en disco | | ![Sí][green] | ![Sí][green] | |
| Almacenamiento en cinta | | ![Sí][green] | | |
| Compresión (en almacén de copia de seguridad) | ![Sí][green] | ![Sí][green]| ![Sí][green] | |
| Copia de seguridad incremental | ![Sí][green] | ![Sí][green] | ![Sí][green] | ![Sí][green] |
| Desduplicación de disco | | ![Parcialmente][yellow] | ![Parcialmente][yellow]| | |

El almacén de Copia de seguridad de Azure es el destino de almacenamiento preferido en todos los componentes. SCDPM y el Servidor de Copia de seguridad de Azure proporcionan la opción de tener también una copia en disco local, pero solamente SCDPM ofrece la opción de escribir datos en un dispositivo de almacenamiento en cinta.

#### Copia de seguridad incremental
Independiente del almacenamiento de destino (disco, cinta o almacén de copia de seguridad), cada componente admite copias de seguridad incrementales. Esto garantiza que las copias de seguridad son eficientes desde el punto de vista del almacenamiento y el tiempo, ya que toman solo los cambios incrementales desde la última copia de seguridad y los transfieren al almacenamiento de destino. Además, las copias de seguridad se comprimen para reducir la superficie de almacenamiento.

El componente que no realiza compresión es la extensión de VM. Todos los datos de copia de seguridad se copian desde la cuenta de almacenamiento del cliente en el almacén de copia de seguridad en la misma región sin comprimir. Aunque esto aumenta el almacenamiento consumido un poco, el almacenamiento de los datos sin compresión permite tiempos de restauración más rápidos.

#### Desduplicación
Se admite desduplicación para SCDPM y el Servidor de Copia de seguridad de Azure cuando se [implementan en una máquina virtual de Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). La desduplicación se realiza en el ámbito de host aprovechando la característica de desduplicación de Windows Server (en los VHD conectados como almacenamiento de copia de seguridad a la máquina virtual).

>[AZURE.WARNING]¡La desduplicación no está disponible en Azure para ninguno de los componentes de Copia de seguridad de Azure! Cuando SCDPM y el Servidor de Copia de seguridad de Azure se implementan en Azure, los discos de almacenamiento conectados a la VM no se pueden desduplicar.

### 2\. Seguridad

| Característica | Agente de copia de seguridad de Azure | SCDPM | Servidor de Copia de seguridad de Azure | Copia de seguridad de Azure (extensión de máquina virtual) |
| ------- | --- | --- | --- | ---- |
| Seguridad de red (para Azure) | ![Sí][green] |![Sí][green] | ![Sí][green] | ![Parcialmente][yellow]|
| Seguridad de red (en Azure) | ![Sí][green] |![Sí][green] | ![Sí][green] | ![Parcialmente][yellow]|

Todo el tráfico de copia de seguridad desde los servidores hacia el almacén de Copia de seguridad de Azure se cifra mediante AES256 y los datos se envían a través de un vínculo HTTPS seguro. Los datos de copia de seguridad también se almacenan en el almacén de copia de seguridad en su forma cifrada. Solo el cliente tiene la frase de contraseña para desbloquear estos datos (Microsoft no puede descifrar los datos de copia de seguridad en ningún momento).

>[AZURE.WARNING]La clave usada para cifrar los datos de copia de seguridad solo está presente con el cliente. Microsoft no mantiene una copia en Azure y no tiene acceso a la clave. Si la clave se pierde, Microsoft no puede recuperar los datos de copia de seguridad.

Para la copia de seguridad de VM de Azure, debe configurar explícitamente el cifrado *dentro* de la máquina virtual. Use BitLocker en máquinas virtuales Windows y dm-crypt en máquinas virtuales Linux. Copia de seguridad de Azure no cifra automáticamente los datos de copia de seguridad que vienen a través de esta ruta.

### 3\. Cargas de trabajo compatibles

| Característica | Agente de copia de seguridad de Azure | SCDPM | Servidor de Copia de seguridad de Azure | Copia de seguridad de Azure (extensión de máquina virtual) |
| ------- | --- | --- | --- | ---- |
| Máquina de Windows Server: archivos y carpetas | ![Sí][green] | ![Sí][green] | ![Sí][green] | |
| Máquina cliente de Windows: archivos y carpetas | ![Sí][green] | ![Sí][green] | ![Sí][green] | |
| Máquina virtual de Hyper-V (Windows) | | ![Sí][green] | ![Sí][green] | |
| Máquina virtual de Hyper-V (Linux) | | ![Sí][green] | ![Sí][green] | |
| Microsoft SQL Server | | ![Sí][green] | ![Sí][green] | |
| Microsoft SharePoint | | ![Sí][green] | ![Sí][green] | |
| Microsoft Exchange | | ![Sí][green] | ![Sí][green] | |
| Máquina virtual de Azure (Windows) | | | | ![Sí][green] |
| Máquina virtual de Azure (Linux) | | | | ![Sí][green] |

### 4\. Red

| Característica | Agente de copia de seguridad de Azure | SCDPM | Servidor de Copia de seguridad de Azure | Copia de seguridad de Azure (extensión de máquina virtual) |
| ------- | --- | --- | --- | ---- |
| Compresión de red (para el servidor de copia de seguridad) | | ![Sí][green] | ![Sí][green] | |
| Compresión de red (para el almacén de copia de seguridad) | ![Sí][green] | ![Sí][green] | ![Sí][green] | |
| Protocolo de red (para el servidor de copia de seguridad) | | TCP | TCP | |
| Protocolo de red (para el almacén de copia de seguridad) | HTTPS | HTTPS | HTTPS | HTTPS |

Dado que la extensión de VM lee directamente los datos de la cuenta de Almacenamiento de Azure a través de la red de almacenamiento, no es necesario optimizar este tráfico. Dado que el tráfico fluye a través de la red de almacenamiento local en el controlador de dominio de Azure, existe una mínima necesidad de compresión que surge debido a consideraciones de ancho de banda.

Para clientes que protegen datos en un servidor de copia de seguridad (SCDPM o el Servidor de Copia de seguridad de Azure), el tráfico que fluye desde el servidor principal al servidor de copia de seguridad también se puede comprimir para ahorrar uso de ancho de banda.

### 5\. Copia de seguridad y retención

| | Agente de copia de seguridad de Azure | SCDPM y el Servidor de copia de seguridad de Azure | Copia de seguridad de Azure (extensión de máquina virtual) |
| --- | --- | --- | --- |
| Frecuencia de copia de seguridad (para el almacén de Azure) | 3 copias de seguridad por día | 2 copias de seguridad por día | 1 copia de seguridad por día |
| Frecuencia de copia de seguridad (para el disco) | No aplicable | <p>Cada 15 minutos para SQL Server</p> <p>Cada hora para otras cargas de trabajo</p> | No aplicable |
| Opciones de retención | Diariamente, semanalmente, mensualmente y anualmente | Diariamente, semanalmente, mensualmente y anualmente | Diariamente, semanalmente, mensualmente y anualmente |
| Período de retención | Hasta 99 años | Hasta 99 años | Hasta 99 años |
| Puntos de recuperación en el almacén de Azure | sin límite | sin límite | sin límite |
| Puntos de recuperación en disco local | No aplicable | No aplicable | No aplicable |
| Puntos de recuperación en cinta | No aplicable | No aplicable | No aplicable |

## ¿Qué diferencias hay entre Copia de seguridad de Azure y Azure Site Recovery?
Muchos clientes confunden la copia de seguridad y la recuperación ante desastres. Ambas capturan datos y proporcionan la semántica de restauración, pero la propuesta de valor principal es diferente para cada una de ellas.

Copia de seguridad de Azure realiza una copia de seguridad de datos localmente o en la nube. Azure Site Recovery coordina la replicación de la máquina virtual y el servidor físico, la conmutación por error y la conmutación por recuperación. Necesita ambas para una solución de recuperación ante desastres completa porque su estrategia de recuperación ante desastres necesita mantener los datos seguros y recuperables (Copia de seguridad de Azure) Y mantener las cargas de trabajo disponibles y accesibles (Azure Site Recovery) cuando se producen apagones.

Para tomar decisiones relacionadas con la copia de seguridad y la recuperación ante desastres, debe entender algunos conceptos importantes:

| CONCEPTO | DETALLES | COPIA DE SEGURIDAD | RECUPERACIÓN ANTE DESASTRES (DR) |
| ------- | ------- | ------ | ----------------- |
| Objetivo de punto de recuperación (RPO) | Cantidad de pérdida de datos que es aceptable en caso de que se debe hacer una recuperación. | Las soluciones de copia de seguridad tienen una gran variación en el RPO que es aceptable. Las copias de seguridad de máquina virtual normalmente tienen un RPO de 1 día, mientras que las copias de seguridad de base de datos tienen RPO bajos, de hasta 15 minutos. | Las soluciones de recuperación ante desastres tienen RPO muy bajos. La copia de DR puede estar detrás de pocos segundos o unos minutos. |
| Objetivo de tiempo de recuperación (RTO) | Cantidad de tiempo que se tarda en completar una recuperación o restauración. | Debido a un RPO mayor, la cantidad de datos que una solución de copia de seguridad debe procesar es normalmente mucho mayor, lo que da lugar a RTO más largos. Por ejemplo, la restauración de datos de cintas puede tardar días, dependiendo del tiempo necesario para transportar la cinta desde una ubicación externa. | Las soluciones de recuperación ante desastres tienen RTO más pequeños, ya que están más en sincronización con el origen y se necesitan procesar menos cambios. |
| Retención | ¿Cuánto tiempo necesitan almacenarse los datos? | <p>Para escenarios que requieren recuperación de operaciones (daños en los datos, eliminación involuntaria de archivos o errores del SO), los datos de copia de seguridad normalmente se conservan durante 30 días o menos.</p> <p>Desde el punto de vista del cumplimiento, los datos podrían tener que almacenarse durante meses o incluso años. Los datos de copia de seguridad son ideales para archivado en tales casos.</p> | La recuperación ante desastres solo necesita datos de recuperación operativos (normalmente unas pocas horas o hasta un día). Debido a la captura de datos específicos que se usa en soluciones de recuperación ante desastres, la retención a largo plazo no se recomienda si se usan datos de recuperación ante desastres. |

## Pasos siguientes

- [Probar Copia de seguridad de Azure](backup-try-azure-backup-in-10-mins.md)
- La pregunta frecuente sobre el servicio de Copia de seguridad de Azure aparece [aquí](backup-azure-backup-faq.md).
- Visite el [Foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0121_2016-->