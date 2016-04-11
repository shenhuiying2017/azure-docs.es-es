<properties
	pageTitle="Uso de instantáneas incrementales para realizar operaciones de copia de seguridad y recuperación de máquinas virtuales de Azure | Microsoft Azure"
	description="Cree una solución personalizada para realizar operaciones de copia de seguridad y recuperación de los discos de máquina virtual de Azure mediante instantáneas incrementales."
	services="storage"
	documentationCenter="na"
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tamram;prkhad"/>


# Copias de seguridad de discos de máquinas virtuales de Azure con instantáneas incrementales

## Información general 

Almacenamiento de Azure permite realizar instantáneas de blobs. Dichas instantáneas capturan el estado del blob en el preciso momento en el que se realicen. En este artículo, se describirá cómo mantener copias de seguridad de discos de máquinas virtuales por medio de instantáneas. Podrá utilizar esta metodología cuando opte por no usar el servicio de recuperación y copia de seguridad de Azure y quiera crear una estrategia de copias de seguridad personalizada para los discos de sus máquinas virtuales.

Los discos de las máquinas virtuales de Azure se almacenan como blobs en páginas en Almacenamiento de Azure. Como en este artículo estamos viendo una estrategia de copias de seguridad para discos de máquinas virtuales, cuando hablemos de instantáneas, lo haremos en el contexto de blobs de páginas. Para obtener más información sobre las instantáneas, consulte [Creación de una instantánea a partir de un blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## ¿Qué es una instantánea?

Una instantánea de blob es una versión de solo lectura de un blob que se ha capturado en un momento dado. Una vez se crea la instantánea, puede leerla, copiarla o eliminarla, pero no modificarla. Las instantáneas le ofrecen una oportunidad de realizar una copia de seguridad de un blob en el momento en que éste aparezca. Hasta la versión XX de REST, podía copiar instantáneas completas. Con la versión YY de REST y posteriores, puede copiar instantáneas incrementales.

## Copia de instantáneas completas

Es posible copiar las instantáneas en otra cuenta de almacenamiento como un blob para guardar copias de seguridad del blob de base. También puede sobrescribir el blob de base con una instantánea suya, lo que equivale a restaurar el blob a una versión previa. Cuando una instantánea se copia de una cuenta de almacenamiento a otra, ocupará el mismo espacio que el blob en páginas de base en la cuenta de almacenamiento de destino. Por lo tanto, la copia de instantáneas completas de una cuenta de almacenamiento a otra será un proceso lento y también consumirá mucho espacio en la de destino.

>[AZURE.NOTE] Si copia el blob de base en otro destino, sus instantáneas no se copian junto con él. Igualmente, si sobrescribe un blob de base con una copia, las instantáneas asociadas a él no se ven afectadas, sino que permanecen intactas con el nombre del blob de base.

### Copia de seguridad de discos mediante instantáneas

Como una estrategia de copias de seguridad para los discos de sus máquinas virtuales, puede realizar instantáneas periódicas del disco o el blob en páginas y copiarlas en otra cuenta de almacenamiento mediante herramientas como la operación [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o [AzCopy](storage-use-azcopy.md). Puede copiar una instantánea en un blob en páginas de destino con un nombre diferente. El blob en páginas de destino resultante es un blob en páginas en el que se puede escribir y no una instantánea. Más adelante en este artículo, se describirán los pasos para realizar copias de seguridad de discos de máquinas virtuales mediante instantáneas.

### Restauración de discos mediante instantáneas

Cuando llegue el momento de restaurar el disco a una versión estable anterior capturada en una de las instantáneas de copia de seguridad, podrá sobrescribir el blob en páginas de base con ella. Una vez que se convierta la instantánea en el blob en páginas de base, esta se conservará, pero el origen se sobrescribe con una copia de lectura y escritura. Más adelante en este artículo, se describirán los pasos para restaurar una versión anterior de su disco a partir de su instantánea.

### Implementación de una copia una instantánea completa

Puede implementar una copia de una instantánea completa siguiendo estos pasos:

-   Primero, realice una instantánea del blob de base con la operación [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).

-   Después, copie la instantánea en una cuenta de almacenamiento de destino mediante [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).

&lt; Fragmentos de código que muestran las operaciones Create Snapshot y Copy Blob &gt;

## Copia de instantáneas incrementales

La nueva característica de la API GetPageRanges ofrece un modo mucho mejor de realizar copias de seguridad de las instantáneas de sus discos o blobs en páginas. La API devuelve la lista de cambios entre el blob de base y las instantáneas. Así se reduce la cantidad de espacio de almacenamiento empleado en la cuenta de la copia de seguridad. La API es compatible con blobs en páginas en Almacenamiento premium, así como en su vertiente estándar. Gracias a esta API, ya puede crear soluciones de copia de seguridad más rápidas y eficaces para las VM de Azure. Esto estará disponible con la versión xx de REST y posterior.

La copia de instantáneas incrementales permite realizar copiar de una cuenta de almacenamiento a otra la diferencia entre:

-   Un blob de base y su instantánea, o bien

-   Dos instantáneas cualesquiera del blob de base

**Nota**: Esta característica se encuentra disponible para los blobs en páginas de Azure premium y estándar.

Cuando se emplea una estrategia de copia de seguridad personalizada en la que se utilicen instantáneas, la copia de estas últimas de una cuenta de almacenamiento a otra puede ser muy lenta y consumir mucho espacio. En lugar de copiar toda la instantánea en una cuenta de almacenamiento de copia de seguridad, puede escribir la diferencia en un blob en páginas de copia de seguridad. De este modo, se reduce considerablemente el tiempo de copia y espacio necesario para almacenar las copias de seguridad.

### Implementación de la copia de instantáneas incrementales

Puede implementar la copia de instantáneas incrementales siguiendo estos pasos:

-   Realice una instantánea del blob de base mediante [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).

-   Copie el blob de base en la cuenta de almacenamiento de destino mediante [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).

-   Obtenga la diferencia entre el blob de base y la instantánea utilizando [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) con el primero. Utilice el nuevo parámetro **prevsnapshot** para especificar el valor DateTime de la instantánea que quiere utilizar para obtener la diferencia. Cuando este parámetro esté presente, la respuesta de REST solo incorporará las páginas que se cambiaron entre el blob de destino y la instantánea previa, incluidas las borradas.

-   Por último, utilice [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para aplicar estos cambios en el blob en páginas de copia de seguridad.

&lt; Fragmentos de código que muestran las operaciones GetPageRanges y Put Page &gt;

En la siguiente sección, se describirá en mayor profundidad cómo puede mantener copias de seguridad de discos mediante la copia de instantáneas incrementales.

## Escenario

En esta sección vamos a describir un escenario en el que se utiliza una estrategia de copia de seguridad personalizada para discos de máquinas virtuales que usan instantáneas.

Considere la posibilidad de usar una VM de Azure de serie DS con un disco P30 de almacenamiento premium conectado. El disco P30 llamado "mypremiumdisk" se almacena en una cuenta de almacenamiento premium denominada "mypremiumaccount". Una cuenta de almacenamiento estándar denominada "mybackupstdaccount" se usará para almacenar la copia de seguridad de mypremiumdisk. Queremos mantener una instantánea de mypremiumdisk cada 12 horas.

Para obtener información sobre cómo crear discos y cuentas de almacenamiento, consulte [About Azure storage accounts](storage-create-storage-account.md) (Acerca de las cuentas de almacenamiento de Azure).

Para obtener información sobre la realización de copias de seguridad de máquinas virtuales de Azure, consulte [Plan Azure VM backups](../backup/backup-azure-vms-introduction.md) (Planeación de copias de seguridad de máquinas virtuales de Azure).

## Pasos para mantener copias de seguridad de un disco con instantáneas incrementales

En los pasos que se describen a continuación se tomarán instantáneas de mypremiumdisk y se mantendrán las copias de seguridad en mybackupstdaccount. La copia de seguridad será un blob en páginas estándar denominado "mybackupstdpageblob" que, en cualquier momento, reflejará el mismo estado de la última instantánea de mypremiumdisk.

1.  Realice una copia del disco de almacenamiento premium en la cuenta de almacenamiento estándar. Copie mypremiumdisk desde mypremiumaccount a mybackupstdaccount como un blob en páginas nuevo denominado "mybackupstdpageblob".

2.  Tome una instantánea de mybackupstdpageblob llamada "mybackupstdpageblob\_ss1" mediante la operación Snapshot Blob y almacénela en mybackupstdaccount.

3.  En la ventana de copia de seguridad, cree una instantánea de mypremiumdisk, por ejemplo, mypremiumdisk\_ss1, y almacénela en mypremiumaccount.

4.  Obtenga los cambios incrementales entre mypremiumdisk y su primera instantánea mypremiumdisk\_ss1 utilizando GetPageRanges. Escriba estos cambios incrementales en el blob en páginas de copia de seguridad mybackupstdpageblob de mybackupstdaccount. Si hay intervalos eliminados en los cambios incrementales, deben borrarse desde el blob en páginas de copia de seguridad. Use Put Page para escribir los cambios incrementales en el blob en páginas de copia de seguridad.

5.  Tome una instantánea del blob en páginas de copia de seguridad mybackupstdpageblob llamada "mybackupstdpageblob\_ss2" y almacénela en mybackupstdaccount. Elimine la instantánea de la cuenta de almacenamiento premium mypremiumdisk\_ss1.

6.  Repita los pasos del 3 al 5 en cada ventana de copia de seguridad. De esta forma, puede mantener copias de seguridad de mypremiumdisk en una cuenta de almacenamiento estándar.

![Realización de copias de seguridad de discos usando instantáneas incrementales](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

& lt; Fragmento de código que muestra las operaciones Copy Blob, GetPageRanges y Put Page & gt;

## Pasos para restaurar un disco de las instantáneas

En los pasos descritos a continuación se restaurará el disco premium, mypremiumdisk, a una instantánea anterior de la cuenta de almacenamiento de copia de seguridad mybackupstdaccount.

1.  Identifique el momento al que desea restaurar el disco premium. Supongamos que es la instantánea mybackupstdpageblob\_ss1, que se almacena en la cuenta de almacenamiento de copia de seguridad mybackupstdaccount.

2.  En mybackupstdaccount, promueva la instantánea mybackupstdpageblob\_ss1 como el nuevo blob en páginas base de copia de seguridad mybackupstdpageblobrestored.

3.  Copie el blob en páginas base de copia de seguridad mybackupstdpageblobrestored de mybackupstdaccount a mypremiumaccount como el nuevo disco premium mypremiumdiskrestored.

4.  Seleccione la máquina virtual de serie DS en el disco restaurado mypremiumdiskrestored y desasocie el disco premium mypremiumdisk anterior de la máquina virtual.

5.  Inicie el proceso de copia de seguridad que se describe en la sección anterior en el disco restaurado mypremiumdiskrestored usando mybackupstdpageblobrestored como el blob en páginas de copia de seguridad.

![Restauración de discos usando instantáneas](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

& lt; Fragmento de código que muestra las operaciones Copy Blob, Attach Disk y Detach Disk gt;

## Consulte también

- [Crear una instantánea de un blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](../backup/backup-azure-vms-introduction.md)

<!---HONumber=AcomDC_0330_2016-->