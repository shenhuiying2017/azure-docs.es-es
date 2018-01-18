---
title: "Uso de una identidad de servicio administrada de Azure en una máquina virtual con los SDK de Azure"
description: "Ejemplos de código de uso de los SDK de Azure con una MSI en una máquina virtual de Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 6c9bd57080c95e4aad45515a0814471194261286
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Uso de una identidad de servicio administrada (MSI) de Azure en una máquina virtual con los SDK de Azure 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
En este artículo se proporciona una lista de ejemplos de SDK que demuestran el uso de sus SDK de Azure respectivos con MSI.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - En todo el código y scripts de ejemplo de este artículo se da por supuesto que el cliente se ejecuta en una máquina virtual con MSI habilitado. Use la característica "Conectar" de la máquina virtual en Azure Portal para conectarse a la máquina virtual de forma remota. Para más información sobre la habilitación de MSI en una máquina virtual, consulte [Configuración de Managed Service Identity (MSI) en una máquina virtual mediante Azure Portal](msi-qs-configure-portal-windows-vm.md) o uno de los artículos variantes (mediante PowerShell, la CLI, una plantilla o un SDK de Azure). 

## <a name="sdk-code-samples"></a>Ejemplos de código de SDK

| SDK             | Código de ejemplo |
| --------------- | ----------- |
| .NET            | [Deploy an ARM template from a Windows VM using Managed Service Identity](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) (Implementación de una plantilla de ARM desde una máquina virtual Windows mediante Managed Service Identity) |
| .NET Core       | [Call Azure services from a Linux VM using Managed Service Identity](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) (Llamada a servicios de Azure desde una máquina virtual Linux mediante Managed Service Identity) |
| Node.js         | [Administración de recursos con Managed Service Identity](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Uso de MSI para una autenticación simple desde una máquina virtual](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Administración de recursos desde una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Contenido relacionado

- Consulte los [SDK de Azure](https://azure.microsoft.com/downloads/) para ver la lista completa de recursos de SDK de Azure, como las descargas de bibliotecas, la documentación y mucho más.
- Para habilitar la identidad de servicio administrada en una máquina virtual de Azure, consulte [Configuración de la identidad de servicio administrada (MSI) de máquina virtual con Azure Portal](msi-qs-configure-portal-windows-vm.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.








