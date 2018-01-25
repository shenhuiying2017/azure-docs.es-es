---
title: Operaciones en Azure Table Storage con PowerShell | Microsoft Docs
description: Operaciones en Azure Table Storage con PowerShell
services: cosmos-db
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: robinsh
ms.openlocfilehash: 15a4ed2370598cb98565c48b4563bee3a4445827
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Operaciones en Azure Table Storage con Azure PowerShell 

>[!NOTE]
>La API de tabla de Azure Cosmos DB proporciona características premium de almacenamiento de tablas, como la distribución privada global, lecturas y escrituras de latencia baja, la indexación secundaria automática y el rendimiento dedicado. En la mayoría de los casos, los comandos de PowerShell de este artículo son válidos tanto para la API de tabla de Azure Cosmos DB como para Azure Table Storage, pero los de este artículo son específicos de Azure Table Storage. Si utiliza la API de tabla de Azure Cosmos DB, consulte el artículo sobre las [operaciones de API de tabla de Azure Cosmos DB con Azure PowerShell](table-powershell.md).
>

Azure Table Storage es un almacén de datos NoSQL que puede utilizar para almacenar y consultar conjuntos grandes de datos estructurados y no relacionales. Los componentes principales del servicio son tablas, entidades y propiedades. Una tabla es una colección de entidades. Una entidad es un conjunto de propiedades. Cada entidad puede tener hasta 252 propiedades, las cuales son todas pares nombre-valor. En este artículo supondremos que ya está familiarizado con los conceptos del servicio Azure Table Storage. Para más información, consulte [Introducción al modelo de datos de Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) e [Introducción a Azure Table Storage mediante .NET](table-storage-how-to-use-dotnet.md).

En este artículo de instrucciones se describen las operaciones de Azure Table Storage más habituales. Aprenderá a: 

> [!div class="checklist"]
> * Creación de una tabla
> * Recuperar una tabla
> * Agregar entidades de tabla
> * Consultar una tabla
> * Eliminar entidades de tabla
> * Eliminación de una tabla

En este artículo de instrucciones se muestra cómo crear una nueva cuenta de almacenamiento en un nuevo grupo de recursos, de modo que pueda eliminarla fácilmente cuando haya terminado. Si prefiere utilizar una cuenta de almacenamiento existente, puede hacerlo.

Los ejemplos requieren la versión 4.4.0 o posterior del módulo de Azure PowerShell. En una ventana de PowerShell, ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión. Si no aparece nada o necesita una actualización, vea [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalar y configurar Azure PowerShell). 

Después de instalar o actualizar Azure PowerShell, hay que instalar el módulo **AzureRmStorageTable**, que contiene los comandos para administrar las entidades. Para instalarlo, ejecute PowerShell como administrador y use el comando **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperación de la lista de ubicaciones

Si no sabe qué ubicación desea usar, puede enumerar las ubicaciones disponibles. Cuando se muestre la lista, busque la que desee usar. En estos ejemplos se utiliza **eastus**. Guarde este valor en la variable **location** para usarlo más adelante.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup). 

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Guarde el nombre del grupo de recursos en una variable para usarlo más adelante. En este ejemplo se crea un grupo de recursos denominado *pshtablesrg* en la región *eastus*.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Crear cuenta de almacenamiento

Cree una cuenta de almacenamiento genérica estándar con almacenamiento con redundancia local (LRS) mediante [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Obtenga el contexto de la cuenta de almacenamiento que define la cuenta de almacenamiento que se usará. Cuando actúa en una cuenta de almacenamiento, hace referencia al contexto en lugar de proporcionar varias veces las credenciales.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Creación de una nueva tabla

Para crear una tabla, utilice el cmdlet [New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable). En este ejemplo, la tabla se denomina `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Recuperación de una lista de tablas de la cuenta de almacenamiento

Recupere una lista de tablas de la cuenta de almacenamiento mediante [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Recuperación de una referencia a una tabla específica

Para llevar a cabo operaciones en una tabla, se necesita una referencia a la tabla concreta. Obtenga una referencia mediante [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Eliminación de una tabla

Para eliminar una tabla, use [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Este cmdlet elimina la tabla, incluidos todos sus datos.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ha creado un nuevo grupo de recursos y una cuenta de almacenamiento al principio de este artículo de ayuda, puede quitar el grupo de recursos para eliminar todos los recursos creados en este ejercicio. Con este comando se eliminan todos los recursos incluidos en el grupo de recursos, además del grupo de recursos en sí.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>pasos siguientes

En este artículo de instrucciones ha aprendido a realizar operaciones habituales de Azure Table Storage con PowerShell, por ejemplo: 

> [!div class="checklist"]
> * Creación de una tabla
> * Recuperar una tabla
> * Agregar entidades de tabla
> * Consultar una tabla
> * Eliminar entidades de tabla
> * Eliminación de una tabla

Para obtener más información, consulte los siguientes artículos:

* [Cmdlets de PowerShell de almacenamiento](/powershell/module/azurerm.storage#storage)

* [Working with Azure Storage Tables from PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/) (Trabajar con tablas de Azure Storage en PowerShell)

* El [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.