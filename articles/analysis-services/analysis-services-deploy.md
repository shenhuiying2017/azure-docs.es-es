---
title: "Implementación en Azure Analysis Services | Microsoft Docs"
description: Aprenda a implementar un modelo tabular en un servidor Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/17/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 766120913d419ec8090a4f25d304f2d3cf9b6693
ms.lasthandoff: 04/18/2017


---
# <a name="deploy-to-azure-analysis-services"></a>Implementación en Azure Analysis Services
Una vez que ha creado un servidor en su suscripción de Azure, está listo para implementar una base de datos de modelo tabular en él. Puede utilizar SQL Server Data Tools (SSDT) para compilar e implementar un proyecto de modelo tabular que esté trabajando. Si lo prefiere, puede usar SQL Server Management Studio (SSMS) para implementar una base de datos de modelo tabular desde una instancia de Analysis Services.

## <a name="before-you-begin"></a>Antes de empezar
Para empezar, necesitará lo siguiente:

* **Servidor de Analysis Services** en Azure. Para aprender más, consulte [Creación de un servidor de Analysis Services en Azure](analysis-services-create-server.md).
* **Proyecto de modelo tabular** en SSDT o de un modelo tabular existente en el nivel de compatibilidad 1200 en una instancia de Analysis Services. ¿Nunca ha creado ninguno? Pruebe el [Tutorial de Adventure Works](https://msdn.microsoft.com/library/hh231691.aspx).
* **Puerta de enlace local**: si uno o varios orígenes de datos son locales en la red de su organización, debe instalar una [puerta de enlace de datos local](analysis-services-gateway.md). La puerta de enlace es necesaria para que el servidor en la nube se conecte a sus orígenes de datos locales para procesar y actualizar los datos en el modelo.

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>Para implementar un modelo tabular desde SSDT
Para implementar desde SSDT, asegúrese de estar usando la [versión más reciente](https://msdn.microsoft.com/library/mt204009.aspx).

> [!TIP]
> Antes de implementarlo, asegúrese de que puede procesar los datos de las tablas. En SSDT, haga clic en **Modelo** > **Proceso** > **Procesar todo**. Si falla el procesamiento, la implementación también lo hará.
> 
> 

1. Antes de realizar la implementación, es preciso obtener el nombre del servidor. En **Azure Portal** > servidor > **Información general** > **Nombre de servidor**, copie el nombre del servidor.
   
    ![Obtención del nombre del servidor en Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. En SSDT > **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Propiedades**. A continuación, en **Implementación** > **Servidor**, pegue el nombre del servidor.   
   
    ![Pegar el nombre del servidor en la propiedad del servidor de implementación](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. En el **Explorador de soluciones**, haga clic con el botón derecho en **Propiedades** y haga clic en **Implementar**. Es posible que se le pida que inicie sesión en Azure.
   
    ![Implementación en el servidor](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    El estado de la implementación aparece en la ventana Salida y en Implementar.
   
    ![Estado de implementación](./media/analysis-services-deploy/aas-deploy-status.png)

Eso es todo.

## <a name="to-deploy-using-xmla-script"></a>Para implementar mediante el script XMLA
1. En SSMS, haga clic con el botón derecho en la base de datos de modelo tabular que desea implementar, haga clic en **Script** > **Incluir la base de datos como** > **CREATE to** y elija una ubicación.
2. Ejecute la consulta en la que desea implementar la instancia del servidor. Si va a implementarla en el mismo servidor, debe cambiar al menos la propiedad **name** en el script XMLA.  

## <a name="but-something-went-wrong"></a>Pero ha habido un problema.
Si se produce un error al implementar los metadatos, es probable que sea porque SSDT no se pudo conectar al servidor. Asegúrese de poder conectarse al servidor mediante SSMS. A continuación, asegúrese de que la propiedad de Servidor de implementación del proyecto es correcta.

Si se produce un error al realizar una implementación en una tabla, es probable que sea porque el servidor no pudo conectarse a un origen de datos. Si el origen de datos es local en la red de su organización, asegúrese de instalar una [puerta de enlace de datos local](analysis-services-gateway.md).

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene el modelo tabular implementado en el servidor, está listo para conectarse a él. Puede [conectarse a él con SSMS](analysis-services-manage.md) para administrarlo. Además, puede [conectarse a él mediante una herramienta cliente](analysis-services-connect.md) como Power BI, Power BI Desktop o Excel y empezar a crear informes.


