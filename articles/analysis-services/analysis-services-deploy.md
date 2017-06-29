---
title: "Implementación en Azure Analysis Services mediante SSDT | Microsoft Docs"
description: Aprenda a implementar un modelo tabular en un servidor de Azure Analysis Services mediante SSDT.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 0b15399cade0a9dc21b2274a64172d65f2f4e877
ms.contentlocale: es-es
ms.lasthandoff: 06/03/2017


---
# <a name="deploy-a-model-from-ssdt"></a>Implementación de un modelo desde SSDT
Una vez que ha creado un servidor en su suscripción de Azure, está listo para implementar una base de datos de modelo tabular en él. Puede utilizar SQL Server Data Tools (SSDT) para compilar e implementar un proyecto de modelo tabular que esté trabajando. 

## <a name="before-you-begin"></a>Antes de empezar
Para empezar, necesitará lo siguiente:

* **Servidor de Analysis Services** en Azure. Para más información, consulte [Creación de un servidor de Azure Analysis Services en Azure Portal](analysis-services-create-server.md).
* **Proyecto de modelo tabular** en SSDT o un modelo tabular existente en el nivel de compatibilidad 1200 o posterior. ¿Nunca ha creado ninguno? Pruebe el [Tutorial de Adventure Works](https://msdn.microsoft.com/library/hh231691.aspx).
* **Puerta de enlace local**: si uno o varios orígenes de datos son locales en la red de su organización, debe instalar una [puerta de enlace de datos local](analysis-services-gateway.md). La puerta de enlace es necesaria para que el servidor en la nube se conecte a sus orígenes de datos locales para procesar y actualizar los datos en el modelo.

> [!TIP]
> Antes de implementarlo, asegúrese de que puede procesar los datos de las tablas. En SSDT, haga clic en **Modelo** > **Proceso** > **Procesar todo**. Si se produce un error de procesamiento, no se implementará correctamente.
> 
> 

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>Para implementar un modelo tabular desde SSDT

1. Antes de realizar la implementación, es preciso obtener el nombre del servidor. En **Azure Portal** > servidor > **Información general** > **Nombre de servidor**, copie el nombre del servidor.
   
    ![Obtención del nombre del servidor en Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. En SSDT > **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Propiedades**. A continuación, en **Implementación** > **Servidor**, pegue el nombre del servidor.   
   
    ![Pegar el nombre del servidor en la propiedad del servidor de implementación](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. En el **Explorador de soluciones**, haga clic con el botón derecho en **Propiedades** y haga clic en **Implementar**. Es posible que se le pida que inicie sesión en Azure.
   
    ![Implementación en el servidor](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    El estado de la implementación aparece en la ventana Salida y en Implementar.
   
    ![Estado de implementación](./media/analysis-services-deploy/aas-deploy-status.png)

Eso es todo.


## <a name="but-something-went-wrong"></a>Pero ha habido un problema.
Si se produce un error al implementar los metadatos, es probable que sea porque SSDT no se pudo conectar al servidor. Asegúrese de poder conectarse al servidor mediante SSMS. A continuación, asegúrese de que la propiedad de Servidor de implementación del proyecto es correcta.

Si se produce un error al realizar una implementación en una tabla, es probable que sea porque el servidor no pudo conectarse a un origen de datos. Si el origen de datos es local en la red de su organización, asegúrese de instalar una [puerta de enlace de datos local](analysis-services-gateway.md).

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene el modelo tabular implementado en el servidor, está listo para conectarse a él. Puede [conectarse a él con SSMS](analysis-services-manage.md) para administrarlo. Además, puede [conectarse a él mediante una herramienta cliente](analysis-services-connect.md) como Power BI, Power BI Desktop o Excel y empezar a crear informes.


