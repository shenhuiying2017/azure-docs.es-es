---
title: "Almacenar archivos en PowerShell en Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Tutorial de cómo Azure Cloud Shell persiste archivos."
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: d0bc16bc951fce17235d8070012de44ebab89888
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Cómo funciona PowerShell en Azure Cloud Shell (versión preliminar)
PowerShell en Cloud Shell (versión preliminar) conserva los archivos a través del método siguiente: 
* Montaje del recurso compartido de archivos de Azure especificado como `clouddrive` en el directorio `$Home` para la interacción directa del recurso compartido de archivos.

## <a name="list-cloud-drive-azure-file-shares"></a>Enumeración de los recursos compartidos de archivos de Azure de Cloud Drive
El comando `Get-CloudDrive` recupera la información del recurso compartido de archivos de Azure que Cloud Drive actualmente monta en Cloud Shell. <br>
![Ejecución de Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Desmontaje de Cloud Drive
Puede desmontar un recurso compartido de archivos de Azure montado en Cloud Shell en cualquier momento. Si se quitó el recurso compartido de archivos de Azure, se le pedirá crear y montar uno nuevo en la sesión siguiente.

El comando `Dismount-CloudDrive` desmonta un recurso compartido de archivos de Azure desde la cuenta de almacenamiento actual. Desmontar Cloud Drive finaliza la sesión actual. Se le pedirá al usuario que cree y monte un recurso compartido de archivos de Azure nuevo durante la sesión siguiente.
![Ejecución de Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Pasos siguientes
[Guía de inicio rápido de PowerShell](quickstart-powershell.md) <br>
[Información acerca de Azure Files](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Información sobre las etiquetas de almacenamiento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>