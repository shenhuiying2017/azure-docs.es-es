---
title: "Consideraciones de diseño para Conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Conozca las consideraciones de diseño de los Conjuntos de escalado de máquinas virtuales de Azure"
keywords: "máquina virtual Linux,conjuntos de escalado de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: efb9f7f7daa5dbb8cd3120b21ef812106fdc7fb9
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="design-considerations-for-scale-sets"></a>Consideraciones de diseño para conjuntos de escalado
En este artículo se analizan consideraciones de diseño de Virtual Machine Scale Sets. Para información sobre qué son los conjuntos de escalado de máquinas virtuales, consulte [Información general de conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>¿Cuándo se usan conjuntos de escalado en lugar de máquinas virtuales?
Por lo general, los conjuntos de escala son útiles para implementar infraestructura altamente disponible donde un conjunto de máquinas tiene una configuración similar. Sin embargo, algunas características solo están disponibles en los conjuntos de escalado, mientras que otras características solo están disponibles en las máquinas virtuales. Con el fin de tomar una decisión fundamentada sobre cuándo usar cada tecnología, primero debe examinar algunas de las características más usadas que se encuentran en los conjuntos de escalado, pero no en las máquinas virtuales:

### <a name="scale-set-specific-features"></a>Características específicas de los conjuntos de escalado

- Una vez que especifique la configuración del conjunto de escalado, puede actualizar la propiedad de "capacidad" para implementar más máquinas virtuales en paralelo. Esto es mucho más simple que escribir un script para organizar la implementación de muchas máquinas virtuales individuales en paralelo.
- Puede [usar el escalado automático de Azure para escalar automáticamente un conjunto de escalado](./virtual-machine-scale-sets-autoscale-overview.md), pero no máquinas virtuales individuales.
- Puede [restablecer la imagen inicial de máquinas virtuales del conjunto de escalado](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm), pero [no las máquinas virtuales individuales](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- También puede [sobreaprovisionar](./virtual-machine-scale-sets-design-overview.md) máquinas virtuales del conjunto de escalado para lograr una mejor confiabilidad y tiempos de implementación más rápidos. No puede hacer esto con máquinas virtuales individuales a menos que escriba el código personalizado para hacerlo.
- Puede especificar una [directiva de actualización](./virtual-machine-scale-sets-upgrade-scale-set.md) para facilitar la implementación de actualizaciones en las máquinas virtuales del conjunto de escalado. Con las máquinas virtuales individuales, debe organizar usted mismo las actualizaciones.

### <a name="vm-specific-features"></a>Características específicas de máquinas virtuales

Algunas características solo están disponibles actualmente en las máquinas virtuales:

- Puede conectar discos de datos a máquinas virtuales individuales específicas, pero los discos de datos conectados están configurados para todas las máquinas virtuales en un conjunto de escalado.
- Puede conectar discos de datos no vacíos a máquinas virtuales individuales, pero no máquinas virtuales de un conjunto de escalado.
- Puede crear una instantánea de una máquina virtual individual, pero no una máquina virtual en un conjunto de escalado.
- Puede capturar una imagen desde una máquina virtual individual, pero no desde una máquina virtual en un conjunto de escalado.
- Puede migrar una máquina virtual individual desde discos nativos a discos administrados, pero no puede hacerlo para máquinas virtuales de un conjunto de escalado.
- Puede asignar direcciones IP públicas IPv6 a NIC de máquinas virtuales individuales, pero no puede hacerlo para máquinas virtuales de un conjunto de escalado. Puede asignar direcciones IP públicas IPv6 a equilibradores de carga delante de máquinas virtuales individuales o de máquinas virtuales de conjunto de escalado.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de escalado con Azure Managed Disks
Los conjuntos de escalado se pueden crear con [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) en lugar de las cuentas de Azure Storage tradicionales. Managed Disks ofrece las siguientes ventajas:
- No necesita crear previamente un conjunto de cuentas de Azure Storage para las VM de conjunto de escalado.
- Puede definir [discos de datos conectados](virtual-machine-scale-sets-attached-disks.md) para las VM del conjunto de escalado.
- Los conjuntos de escalado se pueden configurar para [que admitan hasta 1000 VM en un conjunto](virtual-machine-scale-sets-placement-groups.md). 

Si hay una plantilla existente, también puede [actualizarla para usar Managed Disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Almacenamiento administrado por el usuario
Un conjunto de escalado que no se definió con Azure Managed Disks se basa en las cuentas de almacenamiento creadas por el cliente para almacenar los discos del SO de las VM en el conjunto. Se recomienda una proporción de 20 VM por cuenta de almacenamiento (o menos) para alcanzar el E/S máximo y también aprovechar el _aprovisionamiento en exceso_ (ver a continuación). También se recomienda usar las distintas letras del alfabeto en los caracteres iniciales de los nombres de las cuentas de almacenamiento. Esto ayuda a distribuir la carga en diferentes sistemas internos. 


## <a name="overprovisioning"></a>Aprovisionamiento en exceso
Los conjuntos de escalado actualmente se establecen de manera predeterminada para "sobreaprovisionar" máquinas virtuales. Con el aprovisionamiento en exceso activado, el conjunto de escalado en realidad ejecuta más VM de las que se solicitan y, luego, elimina las VM adicionales una vez que las solicitadas se aprovisionan correctamente. El aprovisionamiento en exceso mejora las tasas de éxito y disminuye el tiempo de implementación. No se le cobrará por las VM adicionales y estas no cuentan en sus límites de cuota.

Aunque el aprovisionamiento en exceso mejora las tasas de éxito de aprovisionamiento, puede provocar un comportamiento confuso para una aplicación que no está diseñada para controlar VM adicionales que aparecen y desparecen. Para desactivar el sobreaprovisionamiento, asegúrese de que tiene la cadena siguiente en la plantilla: `"overprovision": "false"`. Puede encontrar más detalles en la [documentación de API de REST de conjuntos de escalado](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Si el conjunto de escalado usa almacenamiento administrado por el usuario y se desactiva el aprovisionamiento en exceso, puede tener más de 20 VM por cuenta de almacenamiento. Sin embargo, no se recomienda que supere las 40 para mantener un buen rendimiento de E/S. 

## <a name="limits"></a>límites
Un conjunto de escalado basado en una imagen de Marketplace (que también se conoce como una imagen de plataforma) y configurado para usar Azure Managed Disks admite una capacidad de hasta 1000 VM. Si configura el conjunto de escalado para que admita más de 100 VM, no todos los escenarios funcionan del mismo modo (por ejemplo, el equilibro de carga). Para más información, consulte [Uso de grandes conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-placement-groups.md). 

Un conjunto de escalado configurado con cuentas de almacenamiento administradas por el usuario actualmente tiene un límite de 100 VM (y se recomiendan 5 cuentas de almacenamiento para esta escala).

Un conjunto de escalado basado en una imagen personalizada (que usted haya creado) puede tener una capacidad de hasta 300 VM cuando está configurado con Azure Managed Disks. Si el conjunto de escalado está configurado con cuentas de almacenamiento administradas por el usuario, debe crear todos los VHD del disco del SO dentro de una cuenta de almacenamiento. Como resultado, el número máximo recomendado de VM de un conjunto de escalado basado en una imagen personalizada y en el almacenamiento administrado por el usuario es 20. Si se desactiva el aprovisionamiento en exceso, puede aumentar la cifra hasta 40.

Para poder tener más máquinas virtuales que las que permiten estos límites, debe implementar varios conjuntos de escalado, tal como se muestra en [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

