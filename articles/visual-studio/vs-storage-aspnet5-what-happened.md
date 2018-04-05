---
title: ¿Qué le ha ocurrido a mi proyecto ASP.NET 5 (servicios conectados a Visual Studio)? | Microsoft Docs
description: Describe lo que sucede después de conectarse a una cuenta de almacenamiento de Azure en un proyecto de ASP.NET 5 de Visual Studio mediante los servicios conectados de Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 16da7e7a2d61cffb95d22a85669c8f91f28ae476
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>¿Qué le ha ocurrido a mi proyecto ASP.NET 5 (servicios conectados a Azure Storage de Visual Studio)?
## <a name="references-added"></a>Se han agregado referencias
El paquete NuGet de Azure Storage se agregó al proyecto de Visual Studio.  
Este paquete agrega las siguientes referencias. NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

Además, se agregó el paquete NuGet **Microsoft.Framework.Configuration.Json** .

## <a name="connection-string-for-azure-storage-added"></a>Se ha agregado la cadena de conexión para Azure Storage.
En el archivo config.json de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para más información, vea [ASP.NET 5](http://www.asp.net/vnext).

