---
title: Realizar operaciones de la API Table de Azure Cosmos DB con PowerShell | Microsoft Docs
description: "Cómo realizar operaciones de la API Table de Azure Cosmos DB con PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d0f835db8a9fbe3833a9c7931ad1d8b4a778f016
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Realizar operaciones de la API Table de Azure Cosmos DB con Azure PowerShell 

>[!NOTE]
>La API Table de Azure Cosmos DB proporciona características premium de almacenamiento de tablas, como la distribución global llave en mano, lecturas y escrituras de latencia baja, la indexación secundaria automática y el rendimiento dedicado. Los comandos de PowerShell de este artículo son válidos en la mayoría de los casos tanto para la API Table de Azure Cosmos DB como para el almacenamiento de tablas de Azure, pero en este artículo son específicos de la API Table de Azure Cosmos DB. Si usa el almacenamiento de tablas de Azure, vea [Perform Azure Table storage operations with Azure PowerShell](table-storage-how-to-use-powershell.md) (Realizar operaciones de Azure Table Storage con Azure PowerShell).
>

La API Table de Azure Cosmos DB sirve para almacenar y consultar conjuntos grandes de datos estructurados y no relacionales. Los componentes principales del servicio son tablas, entidades y propiedades. Una tabla es una colección de entidades. Una entidad es un conjunto de propiedades. Cada entidad puede tener hasta 252 propiedades, las cuales son todas pares nombre-valor. En este artículo se da por hecho que ya está familiarizado con los conceptos de la API Table de Azure Cosmos DB. Para obtener información detallada, vea [Introducción a la API Table de Azure Cosmos DB](table-introduction.md) y [Compilación de una aplicación de .NET mediante Table API](create-table-dotnet.md).

En este artículo de instrucciones se describen las operaciones habituales de la API Table. Aprenderá a: 

> [!div class="checklist"]
> * Creación de una tabla
> * Recuperar una tabla
> * Agregar entidades de tabla
> * Consultar una tabla
> * Eliminar entidades de tabla

## <a name="prerequisites"></a>Requisitos previos

Los ejemplos requieren la versión 4.4.0 o posterior del módulo de Azure PowerShell. En una ventana de PowerShell, ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión. Si no aparece nada o necesita una actualización, vea [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalar y configurar Azure PowerShell). 

Después de instalar o actualizar Azure PowerShell, hay que instalar el módulo **AzureRmStorageTable**, que contiene los comandos para administrar las entidades. Para instalarlo, ejecute PowerShell como administrador y use el comando **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

Después, instale los ensamblados de Azure Cosmos DB localmente con el fin de usar estos cmdlets de PowerShell. Para saber cómo hacerlo, vea la entrada de blog [Azure RM Storage Tables PowerShell module for Cosmos DB Tables](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/) (Módulo de PowerShell de tablas de almacenamiento de Azure RM para las tablas de Cosmos DB).

Para realizar los siguientes ejercicios, se necesita una cuenta de base de datos de Azure Cosmos DB. Si aún no tiene una, créela con [Azure Portal](https://portal.azure.com). Si necesita ayuda para crearla, vea [Azure Cosmos DB: creación de una cuenta de base de datos](create-table-dotnet.md#create-a-database-account).

Obtenga el grupo de recursos y el nombre de la cuenta de base de datos en el portal; necesitará incluir estos valores en el script para poder tener acceso a las tablas. 

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Crear una tabla o hacer referencia a una tabla

Para crear una tabla o hacer referencia a una tabla, use **Get-AzureStorageTableTable**. Si se llama a este cmdlet con el nombre de una tabla que no existe, se crea una tabla con ese nombre y se devuelve una referencia a dicha tabla nueva. Si la tabla existe, devuelve una referencia a ella.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

Con PowerShell no se puede obtener una lista de las tablas de la cuenta de Azure Cosmos DB, pero sí iniciar sesión en el portal y ver la tabla. Pasemos a ver ahora cómo administrar las entidades de la tabla.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Eliminación de una tabla 

No se pueden eliminar tablas de Azure Cosmos DB con PowerShell. Para eliminar una tabla, vaya a [Azure Portal](https://azure.portal.com), busque la cuenta de Azure Cosmos DB que esté usando y, después, busque y elimine la tabla. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Si creó un grupo de recursos y una cuenta de Azure Cosmos DB en ese grupo, puede quitar todos los recursos creados en este ejercicio quitando el grupo de recursos. Con este comando se eliminan todos los recursos incluidos en el grupo de recursos, además del grupo de recursos en sí.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo de instrucciones ha aprendido a realizar operaciones comunes de la API Table con PowerShell. Por ejemplo: 

> [!div class="checklist"]
> * Creación de una tabla
> * Recuperar una tabla
> * Agregar entidades de tabla
> * Consultar una tabla
> * Eliminar entidades de tabla

Para más información, consulte los siguientes artículos.

* [Working with Azure Storage Tables from PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/) (Trabajar con tablas de Azure Storage en PowerShell)

* El [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.