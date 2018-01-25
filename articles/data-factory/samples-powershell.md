---
title: Ejemplos de Azure PowerShell para Azure Data Factory | Microsoft Docs
description: "Ejemplos de Azure PowerShell: scripts para ayudarle a crear y administrar factorías de datos."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: spelluru
ms.openlocfilehash: 9e361f42b25a1b3a97eacfb1ec1bc82900881806
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Ejemplos de Azure PowerShell para Azure Data Factory

En la tabla siguiente se incluyen vínculos a scripts de ejemplo de Azure PowerShell para Azure Data Factory.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte los [ejemplos de Data Factory, versión 1](v1/data-factory-samples.md).

| |  |
|---|---|
|**Copia de datos**||
|[Copia de blobs de una carpeta a otra carpeta en Azure Blob Storage](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell copia blobs desde una carpeta de Azure Blob Storage a otra carpeta de la misma ubicación. |
|[Copia de datos de un servidor SQL Server local a Azure Blob Storage](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| El script de PowerShell copia datos de la base de datos SQL Server local en una instancia de Azure Blob Storage. |
|[Copia masiva](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell de ejemplo copia los datos de varias tablas en una Azure SQL Database en un Azure SQL Data Warehouse. |
|[Copia incremental](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell de ejemplo carga solo los registros nuevos o actualizados desde un almacén de datos de origen a un almacén de datos del receptor después de la copia completa inicial de datos del origen al receptor. |
|**Transformación de datos**||
|[Transformación de datos usando un clúster de Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell transforma datos mediante la ejecución de un programa en un clúster de Spark. |
|**Migración "Levantar y mover" de paquetes SSIS a Azure**||
|[Crear una instancia de Integration Runtime de Azure SSIS](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell aprovisiona un runtime de integración Azure-SSIS que ejecuta paquetes de SQL Server Integration Services (SSIS) en Azure. |



