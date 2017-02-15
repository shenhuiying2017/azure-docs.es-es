---
title: "¿Qué le ha ocurrido a mi proyecto WebJob (servicio conectado a Almacenamiento de Azure de Visual Studio)? | Microsoft Docs"
description: "Describe lo que ha ocurrido en un proyecto WebJob de Azure después de conectarse a una cuenta de almacenamiento de Azure mediante los servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e4ae7ff320791ff22a7b77a2ffb47b96506bdc06


---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>¿Qué le ha ocurrido a mi proyecto WebJob (servicio conectado a Almacenamiento de Azure de Visual Studio)?
## <a name="references-added"></a>Se han agregado referencias
El paquete de NuGet de Almacenamiento de Azure se agregó al proyecto de Visual Studio o se actualizó en él.  
Este paquete agrega las siguientes referencias. NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Se ha agregado la cadena de conexión para Almacenamiento de Azure.
En el archivo App.config del proyecto, se actualizaron las entradas **AzureWebJobsStorage** y **AzureWebJobsDashboard** con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [Recursos de documentación de WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=Nov16_HO3-->


