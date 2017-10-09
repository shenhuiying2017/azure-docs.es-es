---
title: Requerir transferencia segura en Azure Storage | Microsoft Docs
description: "Obtenga información acerca de la característica \"Se requiere transferencia segura\" de Azure Storage y cómo habilitarla."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 5ec50ca23d9f7c92365492dfab42dc14a38699e2
ms.contentlocale: es-es
ms.lasthandoff: 09/28/2017

---
# <a name="require-secure-transfer-in-azure-storage"></a>Requerir transferencia segura en Azure Storage

La opción "Se requiere transferencia segura" mejora la seguridad de su cuenta de almacenamiento, ya que solo permite enviar solicitudes a la cuenta desde conexiones seguras. Por ejemplo, al llamar a las API de REST para acceder a una cuenta de almacenamiento, es preciso conectarse mediante HTTPS. "Se requiere transferencia segura" rechaza las solicitudes que utilizan HTTP.

Si se usa el servicio Azure Files, se produce un error en todas las conexiones sin cifrado cuando la opción "Se requiere transferencia segura" está habilitada. Aquí se incluyen los escenarios que usan SMB 2.1 y SMB 3.0 sin cifrado, y algunas versiones del cliente SMB de Linux. 

De forma predeterminada, la opción "Se requiere transferencia segura" está deshabilitada.

> [!NOTE]
> Dado que Azure Storage no admite HTTPS para los nombres de dominio personalizados, esta opción no se aplica cuando se utiliza un nombre de dominio personalizado.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Habilitación de "Se requiere transferencia segura" en Azure Portal

La opción de "Se requiere transferencia segura" se puede activar cuando se crea una cuenta de almacenamiento en [Azure Portal](https://portal.azure.com). También puede habilitarla para cuentas de almacenamiento existentes.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Solicitud de transferencia segura en una nueva cuenta de almacenamiento

1. Abra el panel **Crear cuenta de almacenamiento** en Azure Portal.
1. En **Se requiere transferencia segura**, seleccione **Habilitado**.

  ![Creación de una hoja de la cuenta de almacenamiento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Solicitud de transferencia segura en una cuenta de almacenamiento existente

1. Seleccione una cuenta de almacenamiento existente en Azure Portal.
1. En el panel de menú de la cuenta de almacenamiento, en **CONFIGURACIÓN**, seleccione **Configuración**.
1. En **Se requiere transferencia segura**, seleccione **Habilitado**.

  ![Panel de menú de la cuenta de almacenamiento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Habilitación de "Se requiere transferencia segura" mediante programación

Para requerir una transferencia segura mediante programación, use la configuración de _supportsHttpsTrafficOnly_ en propiedades de la cuenta de almacenamiento mediante la API de REST, herramientas o bibliotecas:

* [API de REST](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts) (versión: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (versión: 4.1.0)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (versión: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (versión: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (versión: 6.3.0)
* [SDK de Python](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (versión: 1.1.0)
* [SDK de Ruby](https://rubygems.org/gems/azure_mgmt_storage) (versión: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>Habilitación de la configuración "Se requiere transferencia segura" con la API de REST

Para simplificar las pruebas con la API de REST, puede usar [ArmClient](https://github.com/projectkudu/ARMClient) para llamar desde la línea de comandos.

 Utilice la siguiente línea de comandos siguiente para comprobar la configuración con la API de REST:

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

En la respuesta, puede encontrar el valor _supportsHttpsTrafficOnly_. Por ejemplo:

```Json

{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}

```

Utilice la siguiente línea de comandos siguiente para habilitar la configuración con la API de REST:

```

# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json

```

A continuación se muestra un ejemplo de Input.json:
```Json

{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}

```

## <a name="next-steps"></a>Pasos siguientes
Azure Storage proporciona un completo conjunto de funcionalidades de seguridad que, conjuntamente, permiten a los desarrolladores compilar aplicaciones seguras. Para obtener más detalles, vaya a la [Guía de seguridad para almacenamiento](storage-security-guide.md).

