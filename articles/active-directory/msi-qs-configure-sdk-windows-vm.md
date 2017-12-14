---
title: "Cómo configurar una máquina virtual de Azure con MSI habilitado mediante un SDK de Azure"
description: "Instrucciones paso a paso para configurar y utilizar Managed Service Identity (MSI) en una máquina virtual de Azure mediante un SDK de Azure."
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
ms.date: 09/28/2017
ms.author: bryanla
ms.openlocfilehash: d5fb9decd1417a51c85f23d6b178c8a86cf292bd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Configuración de Managed Service Identity (MSI) de una máquina virtual mediante un SDK de Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory (AD). Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar y eliminar MSI en una máquina virtual de Azure mediante un SDK de Azure.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>SDK de Azure compatibles con MSI 

Azure admite varias plataformas de programación a través de una serie de [SDK de Azure](https://azure.microsoft.com/downloads). Varios se han actualizado para admitir MSI y proporcionan los ejemplos correspondientes para mostrar el uso. Esta lista se actualiza a medida que se agrega compatibilidad adicional:

| SDK | Muestra |
| --- | ------ | 
| .NET   | [Administración de recursos desde una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Administración de almacenamiento desde una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Creación de una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Creación de una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Creación de una máquina virtual de Azure con MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Pasos siguientes

- Consulte los artículos relacionados en "Configuración de MSI para una máquina virtual de Azure", para obtener información sobre cómo puede usar también Azure Portal, PowerShell, la CLI y las plantillas de recursos.

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
