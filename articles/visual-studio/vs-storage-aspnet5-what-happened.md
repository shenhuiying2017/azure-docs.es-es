---
title: "¿Qué le ha ocurrido a mi proyecto ASP.NET 5 (servicios conectados a Visual Studio)? | Microsoft Docs"
description: "Describe lo que sucede después de conectarse a una cuenta de almacenamiento de Azure en un proyecto de ASP.NET 5 de Visual Studio mediante los servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 2a25c24fd7625374d269622a805f386fcd52bb5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>¿Qué le ha ocurrido a mi proyecto ASP.NET 5 (servicios conectados a Almacenamiento de Azure de Visual Studio)?
## <a name="references-added"></a>Se han agregado referencias
El paquete NuGet de Almacenamiento de Azure se agregó al proyecto de Visual Studio.  
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

## <a name="connection-string-for-azure-storage-added"></a>Se ha agregado la cadena de conexión para Almacenamiento de Azure.
En el archivo config.json de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para más información, vea [ASP.NET 5](http://www.asp.net/vnext).

