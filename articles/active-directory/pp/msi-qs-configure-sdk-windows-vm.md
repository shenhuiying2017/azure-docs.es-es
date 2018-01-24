---
title: "Configuración de una MSI asignada por el usuario para una máquina virtual de Azure mediante un SDK de Azure"
description: "Instrucciones detalladas para configurar una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual de Azure, mediante un SDK de Azure."
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
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19b6179803b8de4102818c1522b00e6b4881d0f0
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Configuración de una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual, mediante un SDK de Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

La identidad de servicio administrada proporciona a los servicios de Azure una identidad administrada en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar y quitar una MSI asignada por el usuario de una máquina virtual de Azure mediante un SDK de Azure.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>SDK de Azure compatibles con MSI 

Azure admite varias plataformas de programación a través de una serie de [SDK de Azure](https://azure.microsoft.com/downloads). Varios se han actualizado para admitir MSI y proporcionan los ejemplos correspondientes para mostrar el uso. Esta lista se actualiza a medida que se agrega compatibilidad adicional:

| SDK | Muestra |
| --- | ------ | 
| Python | [Creación de una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Creación de una máquina virtual de Azure con MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>pasos siguientes

- Consulte los artículos relacionados en "Configuración de una MSI para una máquina virtual de Azure" para obtener información sobre cómo configurar una identidad de servicio administrada asignada por el usuario en una máquina virtual de Azure.

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
