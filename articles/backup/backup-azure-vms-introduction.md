---
title: "Planeación de la infraestructura de copia de seguridad de máquinas virtuales en Azure | Microsoft Docs"
description: "Consideraciones importantes al planear la realización de copias de seguridad de máquinas virtuales en Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "copias de seguridad de máquinas virtuales, realizar copias de seguridad de máquinas virtuales"
ms.assetid: 19d2cf82-1f60-43e1-b089-9238042887a9
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: 66b64c803dfea6a1e4c7795d10e4b4ba064f1cf7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planeación de la infraestructura de copia de seguridad de máquinas virtuales en Azure
En este artículo se proporcionan sugerencias de recursos y rendimiento para ayudarle a planear la infraestructura de copia de seguridad de máquina virtual. También se definen los aspectos clave del servicio Backup; estos aspectos pueden ser críticos a la hora de determinar la arquitectura, el planeamiento de la capacidad y la programación. Si ha [preparado el entorno](backup-azure-arm-vms-prepare.md), este es el paso siguiente antes de comenzar a realizar la [copia de seguridad de las máquinas virtuales](backup-azure-arm-vms.md). Si necesita más información sobre Azure Virtual Machines, vea la [Documentación sobre Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>¿Cómo realiza Azure la copia de seguridad de las máquinas virtuales?
El servicio Azure Backup inicia el trabajo de copia de seguridad a la hora programada y desencadena la extensión de copia de seguridad para tomar una instantánea de un momento en el tiempo. El servicio Azure Backup usa la extensión _VMSnapshot_ en Windows y la extensión _VMSnapshotLinux_ en Linux. La extensión se instala durante la primera copia de seguridad de la máquina virtual. Para instalar la extensión, la máquina virtual debe estar ejecutándose. Si no se está ejecutando la máquina virtual, el servicio Azure Backup toma una instantánea del almacenamiento subyacente (ya que no se produce ninguna escritura de la aplicación mientras se detiene la máquina virtual).

Cuando se toma una instantánea de las máquinas virtuales de Windows, el servicio Azure Backup se coordina con el servicio de instantáneas de volumen (VSS) para obtener una instantánea coherente de los discos de la máquina virtual. Si hace una copia de seguridad de máquinas virtuales de Linux, puede escribir sus propias secuencias de comandos personalizadas para garantizar la coherencia al tomar una instantánea de la máquina virtual. Más adelante en este artículo se proporcionan más detalles sobre cómo invocar estas secuencias de comandos.

Después de que el servicio Azure Backup toma la instantánea, se transfieren los datos al almacén. Para que el proceso resulte más eficaz, el servicio identifica y transfiere únicamente los bloques de datos que han cambiado desde la última copia de seguridad.

![Arquitectura de copia de seguridad de máquinas virtuales de Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Cuando finaliza la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación.

> [!NOTE]
> 1. Durante el proceso de copia de seguridad, Azure Backup no incluye el disco temporal asociado a la máquina virtual. Para obtener más información, consulte la publicación del blog sobre [almacenamiento temporal](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Dado que Azure Backup toma una instantánea a nivel de almacenamiento y la transfiere al almacén, no cambie las claves de la cuenta de almacenamiento hasta que finalice el trabajo de copia de seguridad.
> 3. Para las máquinas virtuales Premium, se copia la instantánea en la cuenta de almacenamiento. Esto es para asegurarse de que el servicio Azure Backup obtiene suficientes E/S por segundo para la transferencia de datos al almacén. Esta copia adicional de almacenamiento se cobra según el tamaño de la máquina virtual asignada. 
>

### <a name="data-consistency"></a>Coherencia de datos
La copia de seguridad y restauración de datos críticos para el negocio resulta complicada por el hecho de que es necesario realizar la copia de seguridad mientras se ejecutan las aplicaciones que producen los datos. Para solucionar este problema, Azure Backup admite las copias de seguridad coherentes con la aplicación para máquinas virtuales con Windows y con Linux.
#### <a name="windows-vm"></a>Máquina virtual de Windows
Azure Backup realiza copias de seguridad completas de VSS en máquinas virtuales de Windows (más información sobre [copia de seguridad completa de VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Para habilitar las copias de seguridad de VSS, hay que establecer la siguiente clave del Registro en la máquina virtual.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Máquinas virtuales con Linux
Azure Backup proporciona un marco de trabajo para las secuencias de comandos. Para garantizar la coherencia de la aplicación cuando realice la copia de seguridad de las máquinas virtuales de Linux, cree secuencias de comandos personalizadas anteriores y posteriores que controlen el flujo de trabajo y el entorno de la copia de seguridad. Azure Backup invoca la secuencia de comandos anterior antes de tomar la instantánea de la máquina virtual y una vez completado el trabajo de instantáneas de la máquina virtual, invoca la secuencia de comandos posterior. Para obtener más información, consulte sobre [copias de seguridad de máquinas virtuales coherentes con la aplicación mediante las secuencias de comandos anteriores y posteriores](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).
> [!NOTE]
> Azure Backup solo invoca las secuencias de comandos anteriores y posteriores escritas por el cliente. Azure Backup marcará el punto de recuperación como coherente con la aplicación si la ejecución de las secuencias de comandos anteriores y posteriores se realiza correctamente. Sin embargo, el cliente es el responsable final de la coherencia con la aplicación cuando se utilizan secuencias de comandos personalizadas.
>


En esta tabla se describen los tipos de coherencia y las condiciones en las que se producen durante los procedimientos de copia de seguridad y restauración de máquinas virtuales de Azure.

| Coherencia | Con base en VSS | Explicación y detalles |
| --- | --- | --- |
| Coherencia de las aplicaciones |Sí para Windows|Se trata del tipo de coherencia ideal para las cargas de trabajo, ya que garantiza que:<ol><li> la máquina virtual *arranca*. <li>No hay *daños*. <li>No hay *pérdida de datos*.<li> Los datos son coherentes con la aplicación que usa los datos, implicando a la aplicación en el momento de realizar la copia de seguridad, mediante VSS o los script anterior y posterior.</ol> <li>*Máquinas virtuales con Windows*: la mayoría de cargas de trabajo de Microsoft tienen escritores VSS que realizan acciones específicas de carga de trabajo relacionadas con la coherencia de los datos. Por ejemplo, Microsoft SQL Server tiene un escritor VSS que garantiza que las escrituras en el archivo de registro de transacciones y en la base de datos se realizan correctamente. En las copias de seguridad de máquinas virtuales de Azure con Windows, para crear un punto de recuperación coherente con la aplicación hay que invocar el flujo de trabajo VSS y completarlo antes de completar la instantánea de la máquina virtual. Para que la instantánea de la máquina virtual de Azure sea precisa, también deben completarse los escritores de VSS de todas las aplicaciones de máquinas virtuales de Azure. (Aprenda los [conceptos básicos de VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) y profundice en los detalles de [cómo funciona](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Máquinas virtuales de Linux*: los clientes pueden ejecutar [secuencias de comandos anteriores y posteriores personalizadas para garantizar la coherencia con la aplicación](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Coherencia del sistema de archivos |Sí, para equipos basados en Windows |Hay dos escenarios en los que el punto de recuperación puede ser *coherente con el sistema de archivos*:<ul><li>Copias de seguridad de máquinas virtuales con Linux en Azure, sin los script anterior y posterior, o en caso de que estos generen errores. <li>Error de VSS durante la copia de seguridad de máquinas virtuales Windows en Azure.</li></ul> En ambos casos, lo mejor que se puede hacer es asegurarse de que: <ol><li> la máquina virtual *arranca*. <li>No hay *daños*.<li>No hay *pérdida de datos*.</ol> Las aplicaciones deben implementar su propio mecanismo de "reparación" en los datos restaurados. |
| Coherencia de bloqueos |Sin  |Esta situación es equivalente a aquellos casos en que una máquina experimenta un "bloqueo" (a través de un restablecimiento parcial o completo). La coherencia de bloqueos suele ocurrir cuando la máquina virtual de Azure se apaga en el momento de realizar la copia de seguridad. Un punto de recuperación coherente con el bloqueo no ofrece ninguna garantía sobre la coherencia de los datos en el medio de almacenamiento, ni desde la perspectiva del sistema operativo ni desde la de la aplicación. Solamente se capturan y se hace una copia de seguridad de los datos que ya existen en el disco en el momento de la copia de seguridad. <br/> <br/> Aunque no hay ninguna garantía, normalmente se inicia el sistema operativo, seguido de un procedimiento de comprobación de disco como chkdsk para corregir los errores por daños. Se perderán los datos o las escrituras en memoria que no se hayan transferido al disco. Normalmente, la aplicación sigue con su propio mecanismo de comprobación en caso de que se deba realizar una reversión de datos. <br><br>Por ejemplo, si el registro de transacciones tiene entradas que no están presentes en la base de datos, el software de la base de datos realiza una reversión hasta que los datos sean coherentes. Cuando los datos se reparten en varios discos virtuales (por ejemplo, volúmenes distribuidos), un punto de recuperación coherente con el bloqueo no ofrece ninguna garantía sobre la corrección de los datos. |

## <a name="performance-and-resource-utilization"></a>Rendimiento y uso de recursos
Al igual que el software de copia de seguridad que se implementa de forma local, debe planear las necesidades de capacidad y utilización de recursos al realizar la copia de seguridad de máquinas virtuales en Azure. Los [límites de Azure Storage](../azure-subscription-service-limits.md#storage-limits) definirán cómo se estructuran las implementaciones de máquinas virtuales para obtener un rendimiento máximo con un impacto mínimo en las cargas de trabajo en ejecución.

Preste atención a los siguientes límites de Azure Storage al planear el rendimiento de copia de seguridad:

* Salida máxima por cuenta de almacenamiento
* Tasa de solicitud total por cuenta de almacenamiento

### <a name="storage-account-limits"></a>Límites de la cuenta de almacenamiento
Cuando los datos de copia de seguridad se copian de una cuenta de almacenamiento, se tienen en cuenta las métricas de salida (o rendimiento) y de las operaciones de entrada y salida por segundo (IOPS) de la cuenta de almacenamiento. Al mismo tiempo, las máquinas virtuales también consumen IOPS y el rendimiento. El objetivo es asegurarse de que el tráfico de copia de seguridad y máquina virtual no supere los límites de la cuenta de almacenamiento.

### <a name="number-of-disks"></a>Número de discos
El proceso de copia de seguridad intenta realizar el trabajo lo más rápido posible. Con ello, se consume tantos recursos como es posible. Pero todas las operaciones de E/S están limitadas por la *capacidad de proceso de destino de un blob*, que tiene un límite de 60 MB por segundo. En un intento por maximizar la velocidad, el proceso de copia de seguridad trata de hacer copia de seguridad de cada uno de los discos de la máquina virtual *en paralelo*. Si una máquina virtual tiene cuatro discos, el servicio tratará de hacer copia de seguridad de los cuatro discos en paralelo. El factor más importante que determina el tráfico de copia de seguridad de la cuenta de almacenamiento es el **número de discos** de los que se va a realizar una copia de seguridad.

### <a name="backup-schedule"></a>Programación de copia de seguridad
Un factor secundario que afecta al rendimiento es la **programación de copia de seguridad**. Si configura las directivas para que se realice la copia de seguridad de todas las máquinas virtuales al mismo tiempo, habrá programado un atasco de tráfico. El proceso de copia de seguridad intenta realizar la copia de seguridad de todos los discos en paralelo. Para reducir el tráfico de copia de seguridad de una cuenta de almacenamiento, realice copias de seguridad de las máquinas virtuales en distintos momentos del día, sin superponerse.

## <a name="capacity-planning"></a>Planificación de capacidad
Debe planear las necesidades de uso de la cuenta de almacenamiento teniendo en cuenta todos los factores anteriores. Descargue la [hoja cálculo de Excel de planeación de la capacidad de copia de seguridad de máquinas virtuales](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) para ver el impacto de las opciones de programación de las copias de seguridad y los discos.

### <a name="backup-throughput"></a>Rendimiento de la copia de seguridad
Para cada disco cuya copia de seguridad se realiza, Azure Backup lee los bloques en el disco y solo almacena los datos cambiados (copia de seguridad incremental). En la siguiente tabla se muestran los valores promedio de rendimiento del servicio Backup. Con los siguientes datos, puede calcular la cantidad de tiempo que tarda en realizarse una copia de seguridad de un disco de un tamaño determinado.

| Operación de copia de seguridad | Mejor rendimiento |
| --- | --- |
| Copia de seguridad inicial |160 Mbps |
| Copia de seguridad incremental (DR) |640 Mbps  <br><br> El rendimiento disminuye de forma significativa si los datos modificados (de los que hay que realizar copias de seguridad) están dispersos por el disco.|

## <a name="total-vm-backup-time"></a>Tiempo total de copia de seguridad de máquinas virtuales
Aunque la mayoría del tiempo de copia de seguridad se dedica a leer y copiar los datos, hay otras operaciones que contribuyen al tiempo total necesario para la copia de seguridad de una máquina virtual:

* Tiempo necesario para [instalar o actualizar la extensión de copia de seguridad](backup-azure-arm-vms.md)
* Hora de la instantánea: tiempo dedicado a desencadenar una instantánea. Las instantáneas se desencadenan cerca de la hora de copia de seguridad programada.
* Tiempo de espera en la cola. Puesto que el servicio Copia de seguridad procesa las copias de seguridad de varios clientes, la copia de datos de copia de seguridad de la instantánea al almacén de copia de seguridad o de Recovery Services podría no iniciarse inmediatamente. En los momentos de carga máxima, los tiempos de espera pueden ampliarse hasta ocho horas debido al número de copias de seguridad que se procesan. Sin embargo, el tiempo total de la copia de seguridad de máquina virtual será de menos de 24 horas para las directivas de copia de seguridad diarias.
* Tiempo de transferencia de datos, el tiempo necesario para que el servicio de copia de seguridad calcule los cambios incrementales de la copia de seguridad anterior y transfiera esos cambios al almacén de almacenamiento.

### <a name="why-am-i-observing-longer12-hours-backup-time"></a>¿Por qué veo un tiempo de copia de seguridad más largo (>12 horas)?
La copia de seguridad consta de dos fases: toma de la instantánea y transferencia de instantáneas al almacén. El servicio Backup optimiza el almacenamiento. Al transferir los datos de instantánea a un almacén, el servicio solo transfiere cambios incrementales de la instantánea anterior.  Para determinar los cambios incrementales, el servicio calcula la suma de comprobación de los bloques. Si se cambia un bloque, el bloque se identifica como un bloque que se envía al almacén. Después, el servicio irá profundizando en cada uno de los bloques identificados buscando oportunidades para minimizar los datos que se van a transferir. Después de evaluar todos los bloques cambiados, el servicio combina los cambios y los envía al almacén. En algunas aplicaciones heredadas, las escrituras pequeñas y fragmentadas no son óptimas para el almacenamiento. Si la instantánea contiene muchas escrituras pequeñas y fragmentadas, el servicio tarda más tiempo en procesar los datos que escriben las aplicaciones. Se recomienda que el bloque de escritura de las aplicaciones de Azure que se ejecutan dentro de una máquina virtual sea de 8 KB como mínimo. Si la aplicación utiliza un bloque de menos de 8 KB, se realiza el rendimiento de copia de seguridad. Consulte [Optimización de aplicaciones de alto rendimiento con Azure Storage](../virtual-machines/windows/premium-storage-performance.md) para ayudarlo a optimizar la aplicación con el fin de mejorar el rendimiento de copia de seguridad. Si bien en el artículo sobre el rendimiento de copia de seguridad se usan ejemplos de Premium Storage, la guía es aplicable a los discos de almacenamiento estándar.

## <a name="total-restore-time"></a>Tiempo total de restauración
Una operación de restauración consta de dos tareas secundarias principales: copiar datos desde el almacén a la cuenta de almacenamiento del cliente seleccionada y crear la máquina virtual. La copia datos desde el almacén depende de dónde se almacenan internamente las copias de seguridad en Azure y donde se almacena la cuenta de almacenamiento del cliente. El tiempo necesario para copiar los datos depende de:
* Tiempo de espera en cola: debido a que el servicio procesa restauraciones desde varios clientes al mismo tiempo, las solicitudes de restauración se ponen en cola.
* Tiempo de copia de datos: los datos se copian primero desde el almacén en la cuenta de almacenamiento del cliente. El tiempo de restauración depende del IOPS y el rendimiento que el servicio Azure Backup obtenga en la cuenta de almacenamiento del cliente seleccionado. Para reducir el tiempo de copia durante el proceso de restauración, seleccione una cuenta de almacenamiento que no esté cargada con otras lecturas y escrituras de aplicaciones.

## <a name="best-practices"></a>Procedimientos recomendados
Se recomienda seguir estos procedimientos recomendados al configurar copias de seguridad para máquinas virtuales:

* No programe la copia de seguridad de más de 10 máquinas virtuales clásicas desde el mismo servicio en la nube al mismo tiempo. Si quiere realizar la copia de seguridad de varias máquinas virtuales desde el mismo servicio en la nube, escalone las horas de inicio de cada una de ellas con una hora de diferencia.
* No programa la copia de seguridad de más de 40 máquinas virtuales implementadas al mismo tiempo.
* Programe las copias de seguridad de máquinas virtuales durante horas de poca actividad. De esta forma, el servicio Backup utiliza IOPS para transferir datos desde la cuenta de almacenamiento del cliente al almacén.
* Asegúrese de que una directiva se aplique en máquinas virtuales entre distintas cuentas de almacenamiento. Se recomienda que la misma programación de copia de seguridad proteja hasta 20 discos como máximo de una única cuenta de almacenamiento. Si tiene más de 20 discos en una cuenta de almacenamiento, distribuya esas máquinas virtuales entre varias directivas para obtener el número de IOPS necesario durante la fase de transferencia del proceso de copia de seguridad.
* No restaure una máquina virtual que se ejecuta en almacenamiento Premium en la misma cuenta de almacenamiento. Si el proceso de la operación de restauración coincide con la operación de copia de seguridad, se reduce el número de IOPS disponible para copia de seguridad.
* Para una copia de seguridad de la máquina virtual Premium, asegúrese de que la cuenta de almacenamiento que hospeda los discos Premium tenga al menos 50 % de espacio libre para que la instantánea de almacenamiento provisional realice una copia de seguridad correcta. 
* Asegúrese de que la versión de python en máquinas virtuales Linux habilitadas para la copia de seguridad sea 2.7

## <a name="data-encryption"></a>Cifrado de datos
Azure Backup no cifra los datos como parte del proceso de copia de seguridad. Pero se pueden cifrar datos dentro de la máquina virtual y los datos protegidos de copia de seguridad sin ningún problema (vea más información sobre [copia de seguridad de datos cifrados](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Cálculo del costo de instancias protegidas
Las máquinas virtuales de Azure cuya copia de seguridad se realiza mediante el servicio Azure Backup estarán sujetas a los [precios de Azure Backup](https://azure.microsoft.com/pricing/details/backup/). El cálculo de instancias protegidas se basa en el tamaño *real* de la máquina virtual, que es la suma de todos los datos de la máquina virtual, excepto el "disco de recursos".

Los precios de la copia de seguridad de máquinas virtuales *no* se basa en el tamaño máximo admitido para cada disco de datos conectado a la máquina virtual. Los precios se basan en los datos almacenados en el disco de datos. De forma similar, la factura de almacenamiento de copia de seguridad se basa en la cantidad de datos almacenados en Azure Backup, que es la suma de los datos reales de cada punto de recuperación.

Por ejemplo, veamos una máquina virtual de tamaño estándar A2 con dos discos de datos adicionales con un tamaño máximo de 1 TB cada uno. La tabla siguiente proporciona los datos almacenados en cada uno de estos discos:

| Tipo de disco | Tamaño máximo | Datos reales presentes |
| --- | --- | --- |
| Disco del sistema operativo |1023 GB |17 GB |
| Disco local o disco de recursos |135 GB |5 GB (no incluidos en la copia de seguridad) |
| Disco de datos 1 |1023 GB |30 GB |
| Disco de datos 2 |1023 GB |0 GB |

El tamaño *real* en este caso es el tamaño de la máquina virtual de 17 GB + 30 GB + 0 GB = 47 GB. Este tamaño de instancias protegidas (47 GB) se convierte en la base de la factura mensual. A medida que crece la cantidad de datos en la máquina virtual, el tamaño de instancia protegida usado para la facturación también cambia en consecuencia.

La facturación no se inicia hasta que se completa la primera copia de seguridad correcta. En este punto, se iniciará la facturación del almacenamiento y las instancias protegidas. La facturación continúa mientras haya *datos de cualquier copia de seguridad almacenados en un almacén* para la máquina virtual. Si detiene la protección en la máquina virtual, pero existen datos de copia de seguridad de la máquina virtual en un almacén, continúa la facturación.

La facturación de una máquina virtual especificada se suspenderá solo si se detiene la protección *y* se eliminan los datos de la copia de seguridad. Cuando no hay ningún trabajo de copia de seguridad activo y se ha detenido la protección, el tamaño de la última copia de seguridad correcta de la máquina virtual se convierte en el tamaño de instancia protegida en el que se basa la factura mensual.

## <a name="questions"></a>¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>pasos siguientes
* [Copia de seguridad de máquinas virtuales](backup-azure-arm-vms.md)
* [Administrar copia de seguridad de máquina virtual](backup-azure-manage-vms.md)
* [Restauración de máquinas virtuales](backup-azure-arm-restore-vms.md)
* [Solución de problemas de copia de seguridad de máquinas virtuales](backup-azure-vms-troubleshoot.md)
