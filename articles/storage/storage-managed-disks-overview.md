---
title: "Información general de las versiones Premium y Estándar de Azure Managed Disks | Microsoft Docs"
description: "Introducción a Azure Managed Disks, que controla las cuentas de almacenamiento automáticamente cuando se usan máquinas virtuales de Azure"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: b9bc70ec9e271a8e0b34ed415e27cd350390b21d
ms.contentlocale: es-es
ms.lasthandoff: 08/05/2017

---

# <a name="azure-managed-disks-overview"></a>Introducción a Azure Managed Disks

Azure Managed Disks simplifica la administración de discos para las máquinas virtuales de Azure IaaS, ya que administra las [cuentas de almacenamiento](storage-introduction.md) asociadas a los discos de la máquina virtual. Solo tiene que especificar el tipo ([premium](storage-premium-storage.md) o [estándar](storage-standard-storage.md)) y el tamaño del disco que necesita, y Azure lo crea y administra automáticamente.

## <a name="benefits-of-managed-disks"></a>Ventajas de los discos administrados

Vea algunas de las ventajas que obtendrá al usar discos administrados, comenzando con este vídeo de Channel 9, [Better Azure VM Resiliency with Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency) (Mejorar la resistencia de las máquinas virtuales de Azure con discos administrados).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>La implementación de las máquina virtuales es simple y escalable

Managed Disks controla automáticamente el almacenamiento en segundo plano. Anteriormente, era preciso crear cuentas de almacenamiento que contuvieran los discos (archivos VHD) de las máquinas virtuales de Azure. Al escalar verticalmente, era preciso asegurarse de que se habían creado cuentas de almacenamiento adicionales, para que no se superase el límite de IOPS de almacenamiento con cualquiera de los discos. Si Managed Disks controla el almacenamiento, desaparecen los límites de la cuenta de almacenamiento (por ejemplo, 20 000 IOPS por cuenta). Tampoco es preciso copiar las imágenes personalizadas (archivos VHD) en varias cuentas de almacenamiento. Puede administrarlas en una ubicación central (una cuenta de almacenamiento por región de Azure) y utilizarlas para crear cientos de máquinas virtuales en una suscripción.

Managed Disks le permitirá crear un máximo de 10 000 **discos** de máquina virtual en suscripción, lo que le permitirá crear miles de **máquinas virtuales** en una sola suscripción. Esta característica también aumenta la escalabilidad de los [conjuntos de escalado de máquinas virtuales (VMSS)](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), ya que permite crear hasta mil máquinas virtuales en un VMSS con una imagen de Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Mayor confiabilidad para los conjuntos de disponibilidad

Managed Disks proporciona una mayor confiabilidad para los conjuntos de disponibilidad, ya que garantiza que los discos de las [máquinas virtuales de un conjunto de disponibilidad](../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) están suficientemente aislados entre sí para evitar puntos únicos de error. Para hacerlo, coloca automáticamente los discos en diferentes unidades de escalado de almacenamiento (marcas). Si se produce un error en una marca debido a un error de hardware o software, solo dejarán de funcionar las instancias de máquina virtual con discos de dichas marcas. Por ejemplo, suponga que tiene una aplicación que se ejecuta en cinco máquinas virtuales y estas están en un conjunto de disponibilidad. No todos los discos de dichas máquinas virtuales se almacenarán en la misma marca, por lo que, si se produce un error en una, no se detiene la ejecución de las restantes instancias de la aplicación.

### <a name="highly-durable-and-available"></a>Mayor durabilidad y disponibilidad

Los discos de Azure están diseñados para ofrecer una disponibilidad del 99,999 %. Descanse tranquilo sabiendo que tiene tres réplicas de sus datos que aportan alta durabilidad. Si una o incluso dos réplicas experimentan problemas, las réplicas restantes garantizan la persistencia de los datos y una gran tolerancia a errores. Esta arquitectura ha contribuido a que Azure destaque en el sector por ofrecer, de manera constante, durabilidad de nivel empresarial para discos IaaS, con una tasa de error anualizada del 0 %. 

### <a name="granular-access-control"></a>Control de acceso pormenorizado

Puede usar el [control de acceso basado en rol de Azure (RBAC)](../active-directory/role-based-access-control-what-is.md) para asignar permisos concretos a un disco administrado a uno o varios usuarios. Managed Disks expone varias operaciones, entre las que se incluyen la lectura, escritura (creación o actualización), eliminación y recuperación de un [identificador URI de la firma de acceso compartido (SAS) URI](storage-dotnet-shared-access-signature-part-1.md) para el disco. Puede conceder acceso solo a las operaciones necesarias para que una persona pueda realizar su trabajo. Por ejemplo, si no desea que una persona copie un disco administrado a una cuenta de almacenamiento, puede decidir no conceder acceso a la acción de exportación de dicho disco administrado. De igual forma, si no desea que una persona use URI de SAS para copiar un disco administrado, puede elegir no conceder dicho permiso al disco administrado.

### <a name="azure-backup-service-support"></a>Soporte técnico del servicio Azure Backup
Utilice el servicio Azure Backup con Managed Disks para crear un trabajo de copia de seguridad con copias de seguridad basadas en tiempo, fácil restauración de la máquina virtual y directivas de retención de copia de seguridad. Managed Disks solo admite el almacenamiento con redundancia local (LRS) como opción de replicación, lo que significa que mantiene tres copias de los datos en una única región. Para recuperación ante desastres regionales, debe realizar una copia de los discos de máquina virtual en una región distinta con el [servicio Azure Backup](../backup/backup-introduction-to-azure-backup.md) y una cuenta de almacenamiento GRS como almacén de copia de seguridad. Actualmente Azure Backup admite tamaños de disco de datos de hasta 1 TB para copia de seguridad. En [Uso del servicio de Azure Backup para máquinas virtuales con Managed Disks](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup) puede encontrar más información al respecto.

## <a name="pricing-and-billing"></a>Precios y facturación

Al usar Managed Disks, se aplican las siguientes consideraciones de facturación:
* Tipo de almacenamiento

* Tamaño del disco

* Número de transacciones

* Transferencias de datos de salida

* Instantáneas de disco administradas (copia de disco completo)

Veámoslas más detalladamente.

**Tipo de almacenamiento:** Managed Disks ofrece 2 niveles de rendimiento: [premium](storage-premium-storage.md) (basado en SSD) y [estándar](storage-standard-storage.md) (basado en HDD). La facturación de un disco administrado depende del tipo de almacenamiento que se haya seleccionado para el disco.


**Tamaño del disco**: la facturación de los discos administrados depende del tamaño aprovisionado del disco. Azure asigna el tamaño aprovisionado (redondeado) a la opción de disco de Managed Disks más cercana, como se especifica en las tablas siguientes. Cada disco administrado se asigna a uno de los tamaños aprovisionados admitidos y se factura según corresponda. Por ejemplo, si crea un disco administrado estándar y especificar un tamaño aprovisionado de 200 GB, se le facturará según los precios del tipo disco S20.

Estos son los tamaños de disco disponibles para un disco administrado premium:

| **Tipo de disco <br>administrado premium** | **P4** | **P6** |**P10** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|----------------|----------------|----------------|  
| Tamaño del disco        | 32 GB   | 64 GB   | 128 GB  | 512 GB  | 1.024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 


Estos son los tamaños de disco disponibles para un disco administrado estándar:

| **Tipo de disco <br>administrado estándar** | **S4** | **S6** | **S10** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|----------------|----------------|----------------| 
| Tamaño del disco        | 32 GB   | 64 GB   | 128 GB | 512 GB | 1.024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 


**Número de transacciones**: se le factura por el número de transacciones que realiza en un disco administrado estándar. Las transacciones de un disco administrado premium no tienen coste.

**Transferencias de datos de salida**: las [transferencias de datos de salida](https://azure.microsoft.com/pricing/details/data-transfers/) (datos que salen de los centros de datos de Azure) se facturan en función del uso de ancho de banda.

Para obtener información detallada acerca de los precios de Managed Disks, consulte [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Instantáneas de disco administradas

Una instantánea administrada es una copia completa de solo lectura de un disco administrado que se almacena como disco administrado estándar de forma predeterminada. Con las instantáneas, puede realizar una copia de seguridad de sus discos administrados en cualquier momento. Estas instantáneas existen independientemente del disco de origen y se pueden usar para una instancia de Managed Disks. Se facturan según el tamaño usado. Por ejemplo, si crea una instantánea de un disco administrado con capacidad aprovisionada de 64 GB y el tamaño de datos usado real es de 10 GB, solo se le cobrará por el tamaño de datos usado de 10 GB.  

Actualmente, las [instantáneas incrementales](storage-incremental-snapshots.md) no son compatibles con Managed Disks, pero lo serán en el futuro.

Para más información acerca de cómo crear instantáneas con Managed Disks, consulte estos recursos:

* [Creación de una copia del disco duro virtual que se almacene como un disco administrado mediante instantáneas en Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Creación de una copia del disco duro virtual que se almacene como un disco administrado mediante instantáneas en Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>Imágenes

Managed Disks también admite la creación de una imagen personalizada administrada. Puede crear una imagen desde un disco duro virtual personalizado en una cuenta de almacenamiento, o bien directamente desde una máquina virtual generalizada (con Sysprep). De esta forma se capturan en una sola imagen todos los discos administrados con una máquina virtual, entre los que se incluyen tanto el disco de datos como el del sistema operativo. Esto permite crear cientos de máquinas virtuales con la imagen personalizada sin necesidad de copiar o administrar cuentas de almacenamiento.

Para obtener información sobre la creación de imágenes, consulte los artículos siguientes:
* [How to capture a managed image of a generalized VM in Azure](../virtual-machines/windows/capture-image-resource.md) (Captura de una imagen administrada de una máquina virtual generalizada en Azure)
* [How to generalize and capture a Linux virtual machine using the Azure CLI 2.0](../virtual-machines/linux/capture-image.md) (Procedimiento de generalización y captura de una máquina virtual Linux con la CLI de Azure 2.0)

## <a name="images-versus-snapshots"></a>Imágenes frente a instantáneas

A menudo se ve la palabra "imagen" utilizada con máquinas virtuales, pero ahora también se puede ver "instantáneas". Es importante saber en qué se diferencian. Con Managed Disks, es posible tomar una imagen de una máquina virtual generalizada que se ha desasignado. Dicha imagen incluirá todos los discos conectados a la máquina virtual, se puede usar para crear una máquina virtual e incluirá todos los discos.

Una instantánea es una copia de un disco en un momento dado. Solo se aplica a un disco. Si tiene una máquina virtual con un solo disco (el del sistema operativo), puede tomar una instantánea o una imagen del mismo y crear una máquina virtual a partir de cualquiera de ellas.

¿Qué sucede si una máquina virtual tiene cinco discos y se seccionan? Puede tomar una instantánea de cada uno de los discos, pero en la máquina virtual no se conoce el estado de los discos (las instantáneas solo "conocen" un disco). En este caso, las instantáneas tendrían que coordinarse entre sí, algo que actualmente no se no se admite.

## <a name="managed-disks-and-encryption"></a>Managed Disks y cifrado

Hay dos tipos de cifrado que comentar en referencia a Managed Disks. El primero de ellos es el cifrado de servicio de almacenamiento (SSE), que se realiza mediante el servicio Storage. El segundo es Azure Disk Encryption, que se puede habilitar en los discos de datos y del sistema operativo de las máquinas virtuales.

### <a name="storage-service-encryption-sse"></a>cifrado del servicio de almacenamiento (SSE)

[Cifrado del servicio Azure Storage](storage-service-encryption.md) proporciona cifrado en reposo y protege sus datos con el fin de cumplir con los compromisos de cumplimiento y seguridad de su organización. SSE está habilitado de forma predeterminada para todos los discos administrados, instantáneas e imágenes en todas las regiones donde hay discos administrados. Desde el 10 de junio de 2017, todos los nuevos discos administrados, instantáneas e imágenes, así como los datos nuevos que se escriban en discos administrados existentes, se cifran automáticamente en reposo con claves administradas por Microsoft.  Visite la [página de preguntas más frecuentes sobre discos administrados](storage-faq-for-disks.md#managed-disks-and-storage-service-encryption) para obtener más detalles.


### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption le permite cifrar los discos de datos y del sistema operativo usados por una máquina virtual de IaaS. Esto incluye Managed Disks. Para Windows, las unidades se cifran mediante la tecnología de cifrado de BitLocker estándar del sector. Para Linux, los discos se cifran mediante la tecnología DM-Crypt. Se integra con Azure Key Vault para permitirle controlar y administrar las claves de cifrado del disco. Para más información, vea [Azure Disk Encryption para máquinas virtuales IaaS de Windows y Linux](../security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Managed Disks, consulte los siguientes artículos.

### <a name="get-started-with-managed-disks"></a>Introducción a Managed Disks

* [Creación de una máquina virtual con Resource Manager y PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

* [Creación de una máquina virtual con Linux desde cero con la CLI de Azure 2.0](../virtual-machines/linux/quick-create-cli.md)

* [Conexión de un disco de datos administrado a una máquina virtual Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md)

* [Adición de un disco administrado a una máquina virtual Linux](../virtual-machines/linux/add-disk.md)

* [Scripts d ejemplo de PowerShell de Managed Disks](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Usar Managed Disks en plantillas de Azure Resource Manager](storage-using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Comparación de las opciones de almacenamiento de Managed Disks

* [Discos y Premium Storage](storage-premium-storage.md)

* [Discos y almacenamiento estándar](storage-standard-storage.md)

### <a name="operational-guidance"></a>Guía de operaciones

* [Migración desde AWS y otras plataformas a Managed Disks en Azure](../virtual-machines/windows/on-prem-to-azure.md)

* [Conversión de máquinas virtuales de Azure a discos administrados en Azure](../virtual-machines/windows/migrate-to-managed-disks.md)

