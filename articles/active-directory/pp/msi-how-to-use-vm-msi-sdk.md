---
title: "Uso de una identidad de servicio administrada asignada por el usuario de los SDK de Azure en una máquina virtual"
description: "Códigos de ejemplo para usar los SDK de Azure con una MSI asignada por el usuario en una máquina virtual."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f9a31a0500a6f5f1c49fc45d5811e28788e6f2b1
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>Uso de los SDK de Azure con una identidad de servicio administrada (MSI) asignada por el usuario

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)] En este artículo se proporciona una lista de ejemplos de SDK que demuestran el uso de sus SDK de Azure respectivos con MSI asignadas por el usuario.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - En todo el código y scripts de ejemplo de este artículo se da por supuesto que el cliente se ejecuta en una máquina virtual con MSI habilitado. Use la característica "Conectar" de la máquina virtual en Azure Portal para conectarse a la máquina virtual de forma remota. Para más información sobre la habilitación de MSI en una máquina virtual, vea [Configuración de una identidad de servicio administrada (MSI) con una máquina virtual con la CLI de Azure](msi-qs-configure-cli-windows-vm.md) o uno de los artículos variantes (mediante PowerShell, Azure Portal, una plantilla o un SDK de Azure). 

## <a name="sdk-code-samples"></a>Ejemplos de código de SDK

| SDK             | Código de ejemplo |
| --------------- | ----------- |
| Python          | [Uso de MSI para una autenticación simple desde una máquina virtual](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Administración de recursos desde una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>pasos siguientes

- Consulte los [SDK de Azure](https://azure.microsoft.com/downloads/) para ver la lista completa de recursos de SDK de Azure, como las descargas de bibliotecas, la documentación y mucho más.

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.








