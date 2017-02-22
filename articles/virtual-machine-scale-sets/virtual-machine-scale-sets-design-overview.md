---
title: "Diseño de conjuntos de escalado de máquinas virtuales de Azure para escala | Microsoft Docs"
description: "Obtenga información sobre cómo diseñar los conjuntos de escalado de máquinas virtuales de Azure para escala"
keywords: "máquina virtual Linux,conjuntos de escalado de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: e869b06935736fae72bd3b5407ebab7c3830098d
ms.openlocfilehash: de3687a1bf36bf49db400a5660ac631f20b629d0


---
# <a name="designing-vm-scale-sets-for-scale"></a>Diseño de conjunto de escalado de VM para escala
Este tema analiza consideraciones de diseño para conjuntos de escalado de máquinas virtuales. Para información sobre qué son los conjuntos de escalado de máquinas virtuales, consulte [Información general de conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-overview.md).

## <a name="storage"></a>Almacenamiento

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de escalado con Azure Managed Disks
Los conjuntos de escalado ahora se pueden crear con [Azure Managed Disks](../storage/storage-managed-disks-overview.md). Managed Disks ofrece las siguientes ventajas:
- No necesita crear previamente un conjunto de cuentas de Azure Storage para las VM de conjunto de escalado.
- Puede definir [discos de datos conectados](virtual-machine-scale-sets-attached-disks.md) para las VM del conjunto de escalado.
- Los conjuntos de escalado se pueden configurar para [que admitan hasta 1000 VM en un conjunto](virtual-machine-scale-sets-placement-groups.md). 

Puede crear conjuntos de escalado con Managed Disks a partir de la versión "2016-04-30-preview" de la API de proceso de Azure. Para información sobre cómo convertir una plantilla de conjunto de escalado a Managed Disks, consulte [Conversión de una plantilla de conjunto de escalado en una plantilla de conjunto de escalado de un disco administrado](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Almacenamiento administrado por el usuario
Un conjunto de escalado que no se definió con Azure Managed Disks se basa en las cuentas de almacenamiento creadas por el cliente para almacenar los discos del SO de las VM en el conjunto. Se recomienda una proporción de 20 VM por cuenta de almacenamiento (o menos) para alcanzar el E/S máximo y también aprovechar el _aprovisionamiento en exceso_ (ver a continuación). También se recomienda usar las distintas letras del alfabeto en los caracteres iniciales de los nombres de las cuentas de almacenamiento. Esto ayuda a distribuir la carga en diferentes sistemas internos. 

>[!NOTE]
>La versión `2016-04-30-preview` de la API de conjuntos de escalado de VM admite el uso de Azure Managed Disks para el disco del sistema operativo y los discos de datos adicionales. Para más información, consulte [Managed Disks Overview](../storage/storage-managed-disks-overview.md) (Introducción a Managed Disks) y [Use Attached Data Disks](virtual-machine-scale-sets-attached-disks.md) (Uso de discos de datos conectados). 

## <a name="overprovisioning"></a>Aprovisionamiento en exceso
A partir de la versión de API "2016-03-30", los conjuntos de escalado de VM "aprovisionan en exceso" VM de forma predeterminada. Con el aprovisionamiento en exceso activado, el conjunto de escalado en realidad ejecuta más VM de las que se solicitan y, luego, elimina las VM adicionales una vez que las solicitadas se aprovisionan correctamente. El aprovisionamiento en exceso mejora las tasas de éxito y disminuye el tiempo de implementación. No se le cobrará por las VM adicionales y estas no cuentan en sus límites de cuota.

Aunque el aprovisionamiento en exceso mejora las tasas de éxito de aprovisionamiento, puede provocar un comportamiento confuso para una aplicación que no está diseñada para controlar VM adicionales que aparecen y desparecen. Para desactivar el aprovisionamiento en exceso, asegúrese de que tiene la cadena siguiente en la plantilla: "overprovision": "false". Puede encontrar más detalles en la [documentación de API de REST de conjuntos de escalado de máquinas virtuales](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Si el conjunto de escalado usa almacenamiento administrado por el usuario y se desactiva el aprovisionamiento en exceso, puede tener más de 20 VM por cuenta de almacenamiento. Sin embargo, no se recomienda que supere las 40 para mantener un buen rendimiento de E/S. 

## <a name="limits"></a>Límites
Un conjunto de escalado basado en una imagen de Marketplace (que también se conoce como una imagen de plataforma) y configurado para usar Azure Managed Disks admite una capacidad de hasta 1000 VM. Si configura el conjunto de escalado para que admita más de 100 VM, no todos los escenarios funcionan del mismo modo (por ejemplo, el equilibro de carga). Para más información, consulte [Uso de grandes conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-placement-groups.md). 

Un conjunto de escalado configurado con cuentas de almacenamiento administradas por el usuario actualmente tiene un límite de 100 VM (y se recomiendan 5 cuentas de almacenamiento para esta escala).

Un conjunto de escalado basado en una imagen personalizada (que usted haya creado) puede tener una capacidad de hasta 100 VM cuando está configurado con Azure Managed Disks. Si el conjunto de escalado está configurado con cuentas de almacenamiento administradas por el usuario, debe crear todos los VHD del disco del SO dentro de una cuenta de almacenamiento. Como resultado, el número máximo recomendado de VM de un conjunto de escalado basado en una imagen personalizada y en el almacenamiento administrado por el usuario es 20. Si se desactiva el aprovisionamiento en exceso, puede aumentar la cifra hasta 40.

Para poder tener más máquinas virtuales que las que permiten estos límites, debe implementar varios conjuntos de escalado, tal como se muestra en [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).




<!--HONumber=Feb17_HO2-->


