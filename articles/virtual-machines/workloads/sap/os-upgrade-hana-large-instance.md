---
title: Actualización del sistema operativo para SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Realizar la actualización del sistema operativo para SAP HANA en Azure (instancias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077750"
---
# <a name="operating-system-upgrade"></a>Actualización del sistema operativo
Este documento describe los detalles acerca de las actualizaciones del sistema operativo en las instancias grandes HANA.

>[!NOTE]
>La actualización del SO es responsabilidad de los clientes. El soporte técnico de las operaciones de Microsoft puede guiarle a las áreas clave que debe tener en cuenta durante la actualización. Debe consultar con el proveedor del sistema operativo antes de planear una actualización.

En el momento del aprovisionamiento de la unidad HLI, el equipo de operaciones de Microsoft instala el sistema operativo. Con el tiempo, se le pide realizar mantenimiento al sistema operativo (por ejemplo: aplicación de revisiones, optimizaciones, actualizaciones, etc.) en la unidad HLI.

Antes de realizar cambios principales en el sistema operativo (por ejemplo, actualizar un SO), se **debe** tener en cuenta la siguiente matriz de compatibilidad. Además, **debe** ponerse en contacto con el equipo de operaciones de Microsoft mediante un vale de soporte para consultar antes de iniciar las actividades principales del sistema operativo, como la actualización.

Para la matriz de compatibilidad de las distintas versiones de SAP HANA con las diferentes versiones de Linux, consulte la [Nota de SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).

Se ha probado la siguiente compatibilidad para las HLI. Si el servidor HLI está fuera de la matriz de compatibilidad, póngase en contacto con el soporte técnico de operaciones de Microsoft.

## <a name="for-type-i-class-sku-category"></a>Para la categoría de clase SKU de tipo I

| Configuración | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Firmware del servidor | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| Versión ENIC | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| Versión FNIC | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | Disabled | Disabled | Disabled | Disabled |
| Versión de kernel | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>Para la categoría de clase SKU de tipo II

| Configuración | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Versión de firmware RMC | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| Versión de Firmware BMC | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Versión de Software Foundation Server (SFS) | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| Versión i40e    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| Versión de kernel    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>Problemas conocidos

A continuación, se muestran algunos problemas habituales durante la actualización:
- En la clase SKU de tipo II, Software Foundation Server (SFS) se quita después de actualizar el SO. Debe reinstalar la versión de SFS compatible después de actualizar el SO.
- Reversión de los controladores de la tarjeta Ethernet (ENIC y FNIC) a la versión anterior. Después de la actualización, debe volver a instalar la versión compatible de los controladores.

## <a name="next-steps"></a>Pasos siguientes
- Consulte la sección de [copia de seguridad y restauración](hana-overview-high-availability-disaster-recovery.md) para obtener información sobre la copia de seguridad del SO de clase SKU de tipo I.
- Consulte [Copia de seguridad del SO de SKU tipo II](os-backup-type-ii-skus.md) para la clase SKU de tipo II.