---
title: Ejemplos de plantillas de directivas | Microsoft Docs
description: Ejemplos de plantillas de directivas de Azure para Virtual Network.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2b8766a5353b015030872176e9032034afb7cb9d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Plantillas de ejemplo de directivas de Azure para Virtual Network.

En la tabla siguiente se incluyen vínculos a las plantillas de ejemplo de [Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Estos ejemplos se encuentran en el [repositorio de ejemplos de Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Red**||
| [NSG X en cada NIC](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Requiere que se use un grupo de seguridad de red específico con cada interfaz de red virtual. Se especifica el identificador del grupo de seguridad de red que se va a usar. |
| [NSG X en cada subred](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Requiere que se use un grupo de seguridad de red específico con cada subred virtual. Se especifica el identificador del grupo de seguridad de red que se va a usar. |
| [No hay tabla de rutas](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Prohíbe que se implementen redes virtuales con una tabla de rutas. |
| [Usar subred aprobada para las interfaces de red de máquinas virtuales](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Requiere que las interfaces de red usen una subred aprobada. Se especifica el identificador de la subred aprobada. |
| [Usar red virtual aprobada para las interfaces de red de máquinas virtuales](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Requiere que las interfaces de red usen una red virtual aprobada. Se especifica el identificador de la red virtual aprobada. |
|**Supervisión**||
| [Auditar la configuración de diagnóstico](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Audita si la configuración de diagnóstico no está habilitada para tipos de recursos especificados. Se especifica una matriz de tipos de recursos para comprobar si la configuración de diagnóstico está habilitada. |
|**Convenciones de nombre y texto**||
| [Permitir varios patrones de nombre](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Permite utilizar uno de varios patrones de nombre para los recursos. |
| [Requerir patrón Like](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Asegúrese de que los nombres de recursos cumplan la condición *like* para un patrón. |
| [Requerir patrón de coincidencia](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Asegúrese de que los nombres de recursos coincidan con un patrón de nomenclatura específico. |
| [Requerir patrón de coincidencia de etiqueta](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Asegúrese de que un valor de etiqueta coincida con un patrón de texto. |
|**Etiquetas**||
| [Iniciativa de directiva de etiquetas de facturación](../azure-policy/scripts/billing-tags-policy-init.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Requiere valores de etiqueta especificados para el nombre de producto y el centro de costos. Usa directivas integradas para aplicar las etiquetas requeridas. Se especifican los valores requeridos para las etiquetas.  |
| [Aplicar etiqueta y su valor en grupos de recursos](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Requiere una etiqueta y un valor en un grupo de recursos. Se especifica el valor y el nombre de etiqueta que se requieren.  |
| [Aplicar una etiqueta y su valor](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Requiere un valor y nombre de etiqueta especificada. Se especifica el valor y el nombre de la etiqueta que se va a aplicar.  |
| [Aplicar etiqueta y su valor predeterminado](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Anexa un nombre y valor de etiqueta especificado, si no se proporciona esa etiqueta. Se especifica el valor y el nombre de la etiqueta que se va a aplicar.  |
|**General**||
| [Ubicaciones permitidas](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Requiere que todos los recursos se implementen en las ubicaciones aprobadas. Se especifica una matriz de ubicaciones aprobadas.  |
| [Tipos de recursos permitidos](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Garantiza que solo se implementen los tipos de recursos aprobados. Se especifica una matriz de los tipos de recursos permitidos.  |
| [Tipos de recursos no permitidos](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Prohíbe la implementación de tipos de recursos especificados. Se especifica una matriz de los tipos de recurso que se bloquearán.  |