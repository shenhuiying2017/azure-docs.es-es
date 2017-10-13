---
title: Ejemplos de Azure PowerShell para Azure Cosmos DB | Microsoft Docs
description: 'Ejemplos de Azure PowerShell: scripts para ayudarle a crear y administrar cuentas de Azure Cosmos DB.'
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 7698e03c0dc8d1c6d1e926f45e903a909bfd0c93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Ejemplos de Azure PowerShell para Azure Cosmos DB

En la tabla siguiente se incluyen vínculos a scripts de Azure PowerShell de ejemplo para Azure Cosmos DB.

| |  |
|---|---|
|**Creación de una cuenta de Azure Cosmos DB**||
|[Create a DocumentDB API account](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Creación de una cuenta de API de DocumentDB)| Crea una sola cuenta de Azure Cosmos DB para usarla con la API de DocumentDB. |
|**Escalado de Azure Cosmos DB**||
|[Replicación de la cuenta de Azure Cosmos DB en varias regiones y configuración de prioridades de conmutación por error](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Replicación global de datos de la cuenta en varias regiones con una prioridad específica de conmutación por error.|
|**Protección de Azure Cosmos DB**||
| [Obtención de claves de cuenta](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtención de las claves de escritura maestras primaria y secundaria y de las claves de solo lectura primaria y secundaria de la cuenta.|
| [Obtención de la cadena de conexión de MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtención de la cadena de conexión para conectar su aplicación de MongoDB a su cuenta de Azure Cosmos DB.|
|[Regeneración de claves de cuenta](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Regeneración de la clave maestra o de solo lectura de la cuenta.|
|[Creación de un firewall](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creación de una directiva de control de acceso de IP de entrada para limitar el acceso a la cuenta desde un conjunto aprobado de máquinas o servicios en la nube.|
|**Alta disponibilidad, recuperación ante desastres, copia de seguridad y restauración**||
|[Configuración de la directiva de conmutación por error](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Establecimiento de la prioridad de conmutación por error de cada región en la que se replica la cuenta.|
|||