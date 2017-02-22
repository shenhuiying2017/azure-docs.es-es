---
title: "Preguntas más frecuentes (P+F) sobre los discos de máquina virtual de IaaS de Azure | Microsoft Docs"
description: "Preguntas más frecuentes sobre los discos de máquina virtual de IaaS de Azure y los discos premium (administrados y no administrados)"
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: 0746c954e669bd739b8ecfcddaf287cb5172877f
ms.openlocfilehash: 95b627738726f3c108fff38bfeda413303b2c718


---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Preguntas más frecuentes sobre los discos de máquina virtual de IaaS de Azure y los discos premium administrados y no administrados

En este artículo, se repasan algunas de las preguntas más frecuentes acerca de Managed Disks y Premium Storage.

## <a name="managed-disks"></a>Managed Disks

**¿Qué es Azure Managed Disks?**

Managed Disks es una característica que simplifica la administración de discos para máquinas virtuales de IaaS de Azure al controlar automáticamente la administración de la cuenta de almacenamiento. Para más información, consulte [Introducción a Managed Disks](storage-managed-disks-overview.md).

**Si creo un disco administrado estándar a partir un disco duro virtual existente con 80 GB de tamaño, ¿cuánto me costará?**

Un disco administrado estándar creado a partir de un disco duro virtual de 80 GB se tratará como el siguiente tamaño de disco premium disponible, es decir, S10. Se le cobrará según el precio de los discos S10. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage) para más información.

**¿Existen costes de transacción para los discos administrados estándar?**

Sí, se le cobrará por cada transacción. Consulte la [página de precios] (https://azure.microsoft.com/pricing/details/storage) para más información.

**Para un disco administrado estándar, ¿se me cobrará por el tamaño real de los datos en el disco o la capacidad aprovisionada del disco?**

Se le cobra según la capacidad aprovisionada del disco. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage) para más información.

**¿En qué se diferencian los precios de los discos administrados premium de los de los discos no administrados?**

Los precios de los discos administrados premium son iguales que los de los discos no administrados premium.

**¿Puedo cambiar el tipo de cuenta de almacenamiento (Estándar o Premium) de mis discos administrados?**

Sí. Puede cambiar el tipo de cuenta de almacenamiento de los discos administrados mediante Azure Portal, PowerShell o la CLI de Azure.

**¿Hay alguna manera de copiar o exportar un disco administrado a una cuenta de almacenamiento privada?**

Sí, puede exportar los discos administrados mediante Azure Portal, PowerShell o la CLI de Azure.

**¿Puedo usar un archivo de disco duro virtual en una cuenta de almacenamiento de Azure para crear un disco administrado en una suscripción distinta?**

No.

**¿Puedo usar un archivo de disco duro virtual en una cuenta de almacenamiento de Azure para crear un disco administrado en una región diferente?**

No.

**¿Habrá alguna limitación de escala para los clientes que usen discos administrados?**

Managed Disks elimina los límites asociados a las cuentas de almacenamiento. Aun así, el número de discos administrados por suscripción está limitado a 2000 de forma predeterminada. Si desea aumentar este número, llame al soporte técnico.

**¿Puedo tomar una instantánea incremental de un disco administrado?**

No. La funcionalidad de instantánea actual realiza una copia completa de un disco administrado. Sin embargo, está previsto admitir instantáneas incrementales en el futuro.

**¿Pueden las máquinas virtuales de un conjunto de disponibilidad estar compuestas de una mezcla de discos administrados y no administrados?**

No, las máquinas virtuales de un conjunto de disponibilidad deben utilizar únicamente discos administrados o no administrados. Cuando cree un conjunto de disponibilidad, puede elegir qué tipo de discos desea usar.

**¿Es Managed Disks la opción predeterminada en Azure Portal?**

No actualmente, pero lo será en el futuro.

**¿Puedo crear un disco administrado vacío?**

Sí, puede crear un disco vacío. Un disco administrado se puede crear de forma independiente de una máquina virtual, es decir, sin conectarlo a una máquina virtual.

**¿Qué número de dominios de error se admite para los conjuntos de disponibilidad que usen Managed Disks?**

El número de dominios de error admitido es 2 o 3 para los conjuntos de disponibilidad que usen Managed Disks, en función de la región en la que se encuentren.

**¿Cómo se configura una cuenta de almacenamiento estándar para el diagnóstico?**

Se configura una cuenta de almacenamiento privada para el diagnóstico de máquinas virtuales. En el futuro, está previsto cambiar también el diagnóstico a Managed Disks.

**¿Qué tipo de compatibilidad con RBAC existe para Managed Disks?**

Managed Disks admite tres roles predeterminados fundamentales:

1.  Propietario: puede administrar todo, incluido el acceso.

2.  Colaborador: puede administrar todo, excepto el acceso.

3.  Lector: puede ver todo, pero no realizar cambios.

**¿Hay alguna manera de copiar o exportar un disco administrado a una cuenta de almacenamiento privada?**

Puede obtener un identificador URI de firma de acceso compartido (SAS) de solo lectura para el disco administrado y usarlo para copiar el contenido a una cuenta de almacenamiento privada o al almacenamiento local.

**¿Puedo crear una copia de mi disco administrado?**

Los clientes pueden crear una instantánea de sus discos administrados y usarla para crear otro disco administrado.

**¿Siguen siendo compatibles los discos no administrados?**

Sí, se admiten los discos administrados y los no administrados. Sin embargo, se recomienda que empiece a usar discos administrados por las cargas de trabajo nuevas y migre las cargas de trabajo actuales a Managed Disks.

**Si creo un disco de 128 GB de tamaño y después lo aumento a 130 GB, ¿se me cobrará por el siguiente tamaño de disco (512 GB)?**

Sí.

**¿Se pueden crear discos administrados con LRS, GRS y ZRS?**

Actualmente, Azure Managed Disks solo admite almacenamiento con redundancia local (LRS).

## <a name="premium-disks--both-managed-and-unmanaged"></a>Discos premium: tanto administrados como no administrados

**Si una máquina virtual usa una serie de tamaño que admite Premium Storage, como DSv2, ¿puedo conectar discos de datos tanto premium como estándar?** 

Sí.

**¿Puedo conectar discos de datos tanto premium como estándar a una serie de tamaño que no admita Premium Storage, por ejemplo, las series D, Dv2, G o F?**

No. Solo puede conectar discos de datos estándar a máquinas virtuales que no utilicen una serie de tamaño que admita Premium Storage.

**Si creo un disco de datos premium a partir un disco duro virtual existente con 80 GB de tamaño, ¿cuánto me costará?**

Un disco de datos premium creado a partir de un disco duro virtual de 80 GB se tratará como el siguiente tamaño de disco premium disponible, es decir, P10. Se le cobrará según el precio de los discos P10.

**¿Hay costes de transacción cuando se usa Premium Storage?**

Existe un coste fijo para cada tamaño de disco que esté aprovisionado con límites específicos de IOPS y rendimiento. Los demás costes son por el ancho de banda de salida y la capacidad para instantáneas, si corresponde. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage) para más información.

**¿Cuáles son los límites de IOPS y rendimiento que puedo obtener de la caché de disco?**

Los límites combinados de memoria caché y SSD local para la serie DS son 4000 IOPS por núcleo y 33 MB por segundo y núcleo. La serie GS ofrece 5000 IOPS por núcleo y 50 MB por segundo y núcleo.

**¿Es el SSD local compatible con máquinas virtuales de Managed Disks?**

El SSD local es un almacenamiento temporal que se incluye con una máquina virtual de discos administrados. No existe ningún costo extra para este almacenamiento temporal. Se recomienda que no use este SSD local para almacenar los datos de la aplicación, ya que no se conserva en Azure Blob Storage.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi pregunta no está respondida aquí. ¿Qué debo hacer?

Si su pregunta no aparece aquí, háganoslo saber y lo ayudaremos a encontrar una respuesta. Puede publicar una pregunta al final de este artículo en los comentarios o en el [foro de Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) en MSDN para ponerse en contacto con el equipo de Azure Storage y otros miembros de la comunidad sobre este artículo.

Para presentar una solicitud de característica, envíe sus solicitudes e ideas al [foro de comentarios de Azure Storage](https://feedback.azure.com/forums/217298-storage).


<!--HONumber=Feb17_HO2-->


