---
title: Ejemplos de CLI de Azure para Azure Cosmos DB | Microsoft Docs
description: "Ejemplos de la CLI de Azure: creación y administración de cuentas, bases de datos, contenedores, regiones y firewalls de Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 11/02/2017
ms.author: mimig
ms.openlocfilehash: 989ef9915028c42a4da817bf2dd3aa5ad2beb2ef
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Ejemplos de la CLI de Azure para Azure Cosmos DB

En la tabla siguiente se incluyen vínculos a scripts de ejemplo de la CLI de Azure para Azure Cosmos DB. Existen páginas de referencia para todos los comandos de la CLI de Azure Cosmos DB en la [referencia de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/cosmosdb).

| |  |
|---|---|
|**Creación de cuentas, bases de datos y contenedores de Azure Cosmos DB**||
|[Create a DocumentDB API account](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json) (Creación de una cuenta de API de DocumentDB)| Cree una cuenta, base de datos y contenedor de API de Azure Cosmos DB para usarlos con la API DocumentDB. |
| [Creación de una cuenta de API de MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Creación de una única cuenta, base de datos y colección de API de MongoDB para Azure Cosmos DB. |
|**Escalado de Azure Cosmos DB**||
| [Escalado del rendimiento del contenedor](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cambio de la capacidad de proceso aprovisionada en un contenedor.|
|[Replicación de la cuenta de base de datos de Azure Cosmos DB en varias regiones y configuración de prioridades de conmutación por error](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replicación global de datos de la cuenta en varias regiones con una prioridad específica de conmutación por error.|
|**Protección de Azure Cosmos DB**||
| [Obtención de claves de cuenta](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtención de las claves de escritura maestras primaria y secundaria y de las claves de solo lectura primaria y secundaria de la cuenta.|
| [Obtención de la cadena de conexión de MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtención de la cadena de conexión para conectar su aplicación de MongoDB a su cuenta de Azure Cosmos DB.|
|[Regeneración de claves de cuenta](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Regeneración de la clave maestra o de solo lectura de la cuenta.|
|[Creación de un firewall](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Creación de una directiva de control de acceso de IP de entrada para limitar el acceso a la cuenta desde un conjunto aprobado de máquinas o servicios en la nube.|
|**Alta disponibilidad, recuperación ante desastres, copia de seguridad y restauración**||
|[Configuración de la directiva de conmutación por error](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Establecimiento de la prioridad de conmutación por error de cada región en la que se replica la cuenta.|
|**Conexión de Azure Cosmos DB a los recursos**||
|[Conexión de una aplicación web a Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Creación y conexión a una base de datos de Azure Cosmos DB y creación de una aplicación web de Azure.|
|||
