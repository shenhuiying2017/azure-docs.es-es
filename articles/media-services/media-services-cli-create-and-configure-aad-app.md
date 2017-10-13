---
title: "Uso de la CLI 2.0 para crear una aplicación de Azure AD y configurarla para acceder a la API de Azure Media Services | Microsoft Docs"
description: "En este tema se explica cómo usar la CLI 2.0 para crear una aplicación de Azure AD y configurarla para acceder a la API de Azure Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 01a2bb6d99776feec936315bc882c3097ce832d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Uso de la CLI 2.0 para crear una aplicación de AAD y configurarla para acceder a la API de Azure Media Services

En este tema se muestra cómo utilizar la CLI 2.0 para crear una aplicación de Azure Active Directory (Azure AD) y la entidad de servicio para acceder a recursos de Azure Media Services. 

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Para más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Una cuenta de Servicios multimedia. Para más información, vea [Creación de una cuenta de Azure Media Services mediante Azure Portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Uso de Azure Cloud Shell

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Inicie Cloud Shell desde el panel de navegación superior del portal.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Para más información, vea [Introducción a Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>Creación de una aplicación de Azure AD y configuración del acceso a la cuenta multimedia con la CLI 2.0
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create -- assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Por ejemplo:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

En este ejemplo, el **ámbito** es la ruta de acceso completa al recurso para la cuenta de Media Services. Sin embargo, el **ámbito** puede estar en cualquier nivel.

Por ejemplo, podría ser uno de los siguientes niveles:
 
* El nivel de **suscripción**.
* El nivel de **grupo de recursos**.
* El nivel de **recurso** (por ejemplo, una cuenta multimedia).

Para más información, vea [Creación de una entidad de servicio de Azure con la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Vea también [Administración del control de acceso basado en rol con la interfaz de la línea de comandos de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md). 

## <a name="next-steps"></a>Pasos siguientes

Empiece a trabajar en la [carga de archivos en la cuenta](media-services-portal-upload-files.md).
