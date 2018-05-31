---
title: archivo de inclusión
description: archivo de inclusión
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 3157f5db58be5735fa7b194393b0ab6d0e143a91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33830473"
---
## <a name="create-a-media-services-account"></a>Creación de una cuenta de Media Services

Primero debe crear una cuenta de Media Services. Esta sección muestra lo que necesita para la creación de la cuenta mediante la CLI 2.0.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el siguiente comando. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran recursos como las cuentas de Azure Media Services y las cuentas de almacenamiento asociadas.

```azurecli-interactive
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Al crear una cuenta de Media Services, debe proporcionar el nombre de un recurso de cuenta de Azure Storage. La cuenta de almacenamiento especificada está asociada a su cuenta de Media Services. 

Debe tener una cuenta de almacenamiento **Principal** y puede tener cualquier número de cuentas de almacenamiento **Secundarias** asociadas a su cuenta de Media Services. Media Services admite cuentas de **Uso general v2** (GPv2) o **Uso general v1** (GPv1). No se permiten cuentas de solo BLOB como **Principal**. Si quiere obtener más información sobre las cuentas de almacenamiento, consulte [Opciones de la cuenta de Azure Storage](../articles/storage/common/storage-account-options.md). 

Para más información sobre cómo se utilizan las cuentas de almacenamiento en Media Services, consulte [Cuentas de almacenamiento](../articles/media-services/latest/storage-account-concept.md).

El siguiente comando crea una cuenta de almacenamiento que se asociará a la cuenta de Media Services. En el siguiente script, puede sustituir `storageaccountforams` por su valor. El nombre de la cuenta debe tener una longitud inferior a 24.

```azurecli-interactive
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Creación de una cuenta de Media Services

El siguiente comando de la CLI de Azure crea una nueva cuenta de Media Services. Puede reemplazar los valores siguientes: `amsaccount` `storageaccountforams` (deben coincidir con el valor que especificó para la cuenta de almacenamiento) y `amsResourceGroup` (debe coincidir con el valor que especificó para el grupo de recursos).

```azurecli-interactive
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
