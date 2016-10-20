<properties
	pageTitle="Uso de instantáneas incrementales para realizar operaciones de copia de seguridad y recuperación de máquinas virtuales de Azure | Microsoft Azure"
	description="Cree una solución personalizada para realizar operaciones de copia de seguridad y recuperación de los discos de máquina virtual de Azure mediante instantáneas incrementales."
	services="storage"
	documentationCenter="na"
	authors="aungoo-msft"
	manager="tadb"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="aungoo;tamram"/>


# Copias de seguridad de discos de máquinas virtuales de Azure con instantáneas incrementales

## Información general

Almacenamiento de Azure permite realizar instantáneas de blobs. Dichas instantáneas capturan el estado del blob en el preciso momento en el que se realicen. En este artículo, se describirá cómo mantener copias de seguridad de discos de máquinas virtuales por medio de instantáneas. Podrá utilizar esta metodología cuando opte por no usar el servicio de recuperación y copia de seguridad de Azure y quiera crear una estrategia de copias de seguridad personalizada para los discos de sus máquinas virtuales.

Los discos de las máquinas virtuales de Azure se almacenan como blobs en páginas en Almacenamiento de Azure. Como en este artículo estamos viendo una estrategia de copias de seguridad para discos de máquinas virtuales, cuando hablemos de instantáneas, lo haremos en el contexto de blobs de páginas. Para obtener más información sobre las instantáneas, consulte [Creación de una instantánea a partir de un blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## ¿Qué es una instantánea?

Una instantánea de blob es una versión de solo lectura de un blob que se ha capturado en un momento dado. Una vez se crea la instantánea, puede leerla, copiarla o eliminarla, pero no modificarla. Las instantáneas le ofrecen una oportunidad de realizar una copia de seguridad de un blob en el momento en que éste aparezca. Hasta la versión 2015-04-05 de REST, podía copiar instantáneas completas. Con la versión 2015-07-08 de REST y posteriores, también puede copiar instantáneas incrementales.

## Copia de instantáneas completas

Es posible copiar las instantáneas en otra cuenta de almacenamiento como un blob para guardar copias de seguridad del blob de base. También puede sobrescribir el blob de base con una instantánea suya, lo que equivale a restaurar el blob a una versión previa. Cuando una instantánea se copia de una cuenta de almacenamiento en otra, ocupa el mismo espacio que el blob en páginas de base. Por lo tanto, la copia de instantáneas completas de una cuenta de almacenamiento a otra será un proceso lento y también consumirá mucho espacio en la de destino.

>[AZURE.NOTE] Si copia el blob de base en otro destino, sus instantáneas no se copian junto con él. Igualmente, si sobrescribe un blob de base con una copia, las instantáneas asociadas a él no se ven afectadas, sino que permanecen intactas con el nombre del blob de base.

### Copia de seguridad de discos mediante instantáneas

Como una estrategia de copias de seguridad para los discos de sus máquinas virtuales, puede realizar instantáneas periódicas del disco o el blob en páginas y copiarlas en otra cuenta de almacenamiento mediante herramientas como la operación [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o [AzCopy](storage-use-azcopy.md). Puede copiar una instantánea en un blob en páginas de destino con un nombre diferente. El blob en páginas de destino resultante es un blob en páginas en el que se puede escribir y no una instantánea. Más adelante en este artículo, se describirán los pasos para realizar copias de seguridad de discos de máquinas virtuales mediante instantáneas.

### Restauración de discos mediante instantáneas

Cuando llegue el momento de restaurar el disco a una versión estable anterior capturada en una de las instantáneas de copia de seguridad, podrá sobrescribir el blob en páginas de base con ella. Una vez que se convierta la instantánea en el blob en páginas de base, esta se conservará, pero el origen se sobrescribe con una copia de lectura y escritura. Más adelante en este artículo, se describirán los pasos para restaurar una versión anterior de su disco a partir de su instantánea.

### Implementación de una copia una instantánea completa

Puede implementar una copia de una instantánea completa siguiendo estos pasos:

-   Primero, realice una instantánea del blob de base con la operación [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Después, copie la instantánea en una cuenta de almacenamiento de destino mediante [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Repita este proceso para mantener copias de seguridad de su blob de base.

## Copia de instantáneas incrementales

La nueva característica de la API [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) ofrece un modo mucho mejor de realizar copias de seguridad de las instantáneas de los discos o blobs en páginas. La API devuelve la lista de cambios entre el blob de base y las instantáneas. Así se reduce la cantidad de espacio de almacenamiento empleado en la cuenta de la copia de seguridad. La API es compatible con blobs en páginas en Almacenamiento premium, así como en su vertiente estándar. Gracias a esta API, ya puede crear soluciones de copia de seguridad más rápidas y eficaces para las VM de Azure. Estará disponible con la versión 2015-07-08 de REST y posteriores.

La copia de instantáneas incrementales permite realizar copiar de una cuenta de almacenamiento a otra la diferencia entre:

-   Un blob de base y su instantánea, o bien
-   Dos instantáneas cualesquiera del blob de base

Siempre que se cumplan las condiciones siguientes:

- El blob se creó a partir del 1 de enero de 2016.
- El blob no se sobrescribió con [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) ni [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) entre dos instantáneas.


**Nota**: Esta característica está disponible para los blobs en páginas de Azure premium y estándar.

Cuando se emplea una estrategia de copia de seguridad personalizada en la que se utilicen instantáneas, la copia de estas últimas de una cuenta de almacenamiento a otra puede ser muy lenta y consumir mucho espacio. En lugar de copiar toda la instantánea en una cuenta de almacenamiento de copia de seguridad, puede escribir la diferencia entre instantáneas consecutivas en un blob en páginas de copia de seguridad. De este modo, se reduce considerablemente el tiempo de copia y espacio necesario para almacenar las copias de seguridad.

### Implementación de la copia de instantáneas incrementales

Puede implementar la copia de instantáneas incrementales siguiendo estos pasos:

-   Realice una instantánea del blob de base mediante [Instantánea de blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Copie la instantánea en una cuenta de almacenamiento de copia de seguridad de destino mediante [Copiar blob](https://msdn.microsoft.com/library/azure/dd894037.aspx). Este será el blob en páginas de copia de seguridad. Realice una instantánea de este blob en páginas de copia de seguridad y almacénela en la cuenta de copia de seguridad.
-   Realice otra instantánea del blob de base mediante Instantánea de blob.
-   Obtenga la diferencia entre la primera y la segunda instantánea del blob de base mediante [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Use el nuevo parámetro **prevsnapshot** para especificar el valor DateTime de la instantánea que quiere usar para obtener la diferencia. Cuando este parámetro esté presente, la respuesta de REST solo incorporará las páginas que se cambiaron entre la instantánea de destino y la anterior, incluidas las borradas.
-   Use [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para aplicar estos cambios al blob en páginas de copia de seguridad.
-   Por último, realice una instantánea del blob en páginas de copia de seguridad y almacénela en la cuenta de almacenamiento de copia de seguridad.

En la siguiente sección, se describirá en mayor profundidad cómo puede mantener copias de seguridad de discos mediante la copia de instantáneas incrementales.

## Escenario

En esta sección vamos a describir un escenario en el que se utiliza una estrategia de copia de seguridad personalizada para discos de máquinas virtuales que usan instantáneas.

Considere la posibilidad de usar una VM de Azure de serie DS con un disco P30 de almacenamiento premium conectado. El disco P30 denominado *mypremiumdisk* se almacena en una cuenta de almacenamiento premium denominada *mypremiumaccount*. Se usará una cuenta de almacenamiento estándar denominada *mybackupstdaccount* para almacenar la copia de seguridad de *mypremiumdisk*. Nos gustaría mantener una instantánea de *mypremiumdisk* cada 12 horas.

Para obtener información sobre cómo crear discos y cuentas de almacenamiento, consulte [About Azure storage accounts](storage-create-storage-account.md) (Acerca de las cuentas de almacenamiento de Azure).

Para obtener información sobre la realización de copias de seguridad de máquinas virtuales de Azure, consulte [Plan Azure VM backups](../backup/backup-azure-vms-introduction.md) (Planeación de copias de seguridad de máquinas virtuales de Azure).

## Pasos para mantener copias de seguridad de un disco con instantáneas incrementales

En los pasos que se describen a continuación se realizarán instantáneas de *mypremiumdisk* y las copias de seguridad se mantendrán en *mybackupstdaccount*. La copia de seguridad será un blob en páginas estándar denominado *mybackupstdpageblob*. El blob en páginas de copia de seguridad siempre reflejará el mismo estado que la última instantánea de *mypremiumdisk*.

1.  En primer lugar, cree el blob en páginas de copia de seguridad del disco de almacenamiento premium. Para ello, realice una instantánea de *mypremiumdisk* denominada *mypremiumdisk\_ss1*.
2.  Copie esta instantánea en mybackupstdaccount como un blob en páginas denominado *mybackupstdpageblob*.
3.  Realice una instantánea de *mybackupstdpageblob* denominada *mybackupstdpageblob\_ss1* con [Instantánea de blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) y almacénela en *mybackupstdaccount*.
4.  En la ventana de copia de seguridad, cree otra instantánea de *mypremiumdisk*, por ejemplo, *mypremiumdisk\_ss2*, y almacénela en *mypremiumaccount*.
5.  Obtenga los cambios incrementales entre las dos instantáneas, *mypremiumdisk\_ss2* y *mypremiumdisk\_ss1*, con [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) en *mypremiumdisk\_ss2* con el parámetro **prevsnapshot** establecido en la marca de tiempo de *mypremiumdisk\_ss1*. Escriba estos cambios incrementales en el blob en páginas de copia de seguridad *mybackupstdpageblob* de *mybackupstdaccount*. Si hay intervalos eliminados en los cambios incrementales, deben borrarse desde el blob en páginas de copia de seguridad. Use [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para escribir los cambios incrementales en el blob en páginas de copia de seguridad.
6.  Realice una instantánea del blob en páginas de copia de seguridad *mybackupstdpageblob* denominada *mybackupstdpageblob\_ss2*. Elimine la instantánea anterior *mypremiumdisk\_ss1* de la cuenta de almacenamiento premium.
7.  Repita los pasos del 4 al 6 en cada ventana de copia de seguridad. De esta forma, puede mantener copias de seguridad de *mypremiumdisk* en una cuenta de almacenamiento estándar.

![Realización de copias de seguridad de discos usando instantáneas incrementales](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## Pasos para restaurar un disco de las instantáneas

En los pasos descritos a continuación se restaurará el disco premium, *mypremiumdisk*, a una instantánea anterior desde la cuenta de almacenamiento de copia de seguridad *mybackupstdaccount*.

1.  Identifique el momento al que desea restaurar el disco premium. Supongamos que es la instantánea *mybackupstdpageblob\_ss2*, que se almacena en la cuenta de almacenamiento de copia de seguridad *mybackupstdaccount*.
2.  En mybackupstdaccount, promueva la instantánea *mybackupstdpageblob\_ss2* como el nuevo blob en páginas de base de copia de seguridad *mybackupstdpageblobrestored*.
3.  Realice una instantánea de este blob en páginas de copia de seguridad restaurado denominada *mybackupstdpageblobrestored\_ss1*.
4.  Copie el blob en páginas restaurado *mybackupstdpageblobrestored* desde *mybackupstdaccount* en *mypremiumaccount* como el nuevo disco premium *mypremiumdiskrestored*.
5.  Realice una instantánea de *mypremiumdiskrestored* denominada *mypremiumdiskrestored\_ss1* para realizar futuras copias de seguridad incrementales.
6.  Seleccione la máquina virtual de serie DS en el disco restaurado *mypremiumdiskrestored* y desasocie el *mypremiumdisk* anterior de la máquina virtual.
7.  Inicie el proceso de copia de seguridad que se describe en la sección anterior para el disco restaurado *mypremiumdiskrestored* con *mybackupstdpageblobrestored* como el blob en páginas de copia de seguridad.

![Restauración de discos usando instantáneas](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## Pasos siguientes

Obtenga más información sobre cómo crear instantáneas de un blob y planear la infraestructura de copia de seguridad de máquinas virtuales mediante los vínculos siguientes.

- [Crear una instantánea de un blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](../backup/backup-azure-vms-introduction.md)

<!---HONumber=AcomDC_0928_2016-->