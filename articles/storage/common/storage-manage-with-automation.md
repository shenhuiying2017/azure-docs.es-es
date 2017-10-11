---
title: "Administración de Almacenamiento de Azure mediante Automatización de Azure"
description: "Obtenga información acerca de cómo puede usarse el servicio Automatización de Azure para administrar Almacenamiento de Azure a escala."
services: storage, automation
documentationcenter: 
author: jodoglevy
manager: eamono
editor: 
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="managing-azure-storage-using-azure-automation"></a>Administración de Almacenamiento de Azure mediante Automatización de Azure
Esta guía le ofrece el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de blobs, tablas y colas de Almacenamiento de Azure.

## <a name="what-is-azure-automation"></a>¿Qué es Automatización de Azure?
[Automatización de Azure](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten con frecuencia para aumentar la confiabilidad y la eficiencia y reducir el tiempo de amortización para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades a medida que crece la organización. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Automatización de Azure.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>¿Cómo puede ayudar el servicio Automatización de Azure a administrar Almacenamiento de Azure?
Almacenamiento de Azure se puede administrar en Automatización de Azure mediante los cmdlets de PowerShell que están disponibles en [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatización de Azure tiene estos cmdlets de PowerShell de Almacenamiento disponibles directamente para que pueda realizar todas las tareas de administración de blobs, tablas y colas dentro del servicio. También puede emparejar estos cmdlets en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

La [Galería de runbooks de Automatización de Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene una gran variedad de runbooks de comunidad y equipo de producto para empezar a automatizar la administración de Almacenamiento de Azure, otros servicios de Azure y sistemas de terceros. Los runbooks de la Galería incluyen:

* [Quitar Blobs desde Almacenamiento de Azure que llevan varios días mediante el Servicio de automatización](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Descargar un Blob desde Almacenamiento de Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Hacer una copia de seguridad de todos los discos para una sola VM de Azure o para todas las VM en un servicio en la nube](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar blobs, tablas y colas de Almacenamiento de Azure, siga estos vínculos para obtener más información acerca de Automatización de Azure.

Consulte el tutorial de Automatización de Azure [Crear o importar un Runbook en Automatización de Azure](../../automation/automation-creating-importing-runbook.md).

