---
title: "¿Qué le ha ocurrido a mi proyecto ASP.NET? | Microsoft Docs"
description: "Describe lo que sucede después de agregar Almacenamiento de Azure a un proyecto ASP.NET con servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 680960f51201a98c81db4bce3615e1602e795651
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>¿Qué le ha ocurrido a mi proyecto ASP.NET (servicio conectado a Almacenamiento de Azure de Visual Studio)?
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

## <a name="connection-string-for-azure-storage-added"></a>Se ha agregado la cadena de conexión para Almacenamiento de Azure.
En el archivo web.config de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [ASP.NET](http://www.asp.net).

