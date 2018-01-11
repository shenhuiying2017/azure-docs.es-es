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
ms.date: 10/16/2017
ms.author: mimig
ms.openlocfilehash: de892cc631585c55b0c15f4efe1e06ad55afdce5
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Ejemplos de Azure PowerShell para Azure Cosmos DB

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

En la tabla siguiente se incluyen vínculos a scripts de Azure PowerShell de ejemplo para Azure Cosmos DB. En este momento, solo puede administrar el nivel de cuenta de Azure Cosmos DB mediante PowerShell. Los demás recursos como bases de datos y colecciones no se pueden administrar con PowerShell.

| |  |
|---|---|
|**Creación de una cuenta de Azure Cosmos DB**||
|[Creación de una cuenta de API de SQL](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea una sola cuenta de Azure Cosmos DB para usarla con la API de SQL. |
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
