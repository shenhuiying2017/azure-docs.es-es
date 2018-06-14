---
title: 'Ejemplo de script de Azure PowerShell: suscripción a una cuenta de Blob Storage | Microsoft Docs'
description: 'Ejemplo de script de Azure PowerShell: suscripción a una cuenta de Blob Storage'
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 0d9af16e77f39859135fe18fc810d4fca6635e19
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31426033"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Suscripción a eventos de una cuenta de Blob Storage con PowerShell

Este script crea una suscripción de Event Grid a los eventos de una cuenta de Blob Storage.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Explicación del script

Este script usa el siguiente comando para crear la suscripción de eventos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Cree una suscripción de Event Grid. |

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](../overview.md).
* Para más información sobre PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
