---
title: "Sincronización de contenido de una carpeta de nube a Azure App Service"
description: "Descubra cómo implementar su aplicación en Azure App Service mediante una sincronización de contenido desde una carpeta de la nube."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronización de contenido de una carpeta de nube a Azure App Service
En este tutorial se muestra cómo sincronizar el contenido en [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) desde servicios de almacenamiento en nube populares como Dropbox y OneDrive. 

## <a name="overview"></a>Información general sobre la implementación de la sincronización de contenido
La implementación de la sincronización de contenido a petición funciona con el [motor de implementación Kudu](https://github.com/projectkudu/kudu/wiki) integrado con App Service. En el [Azure Portal](https://portal.azure.com), puede designar una carpeta especial en el almacenamiento en la nube, trabajar con el código de la aplicación y el contenido de dicha carpeta, y sincronizar con App Service con solo hacer clic en un botón. La sincronización de contenido utiliza el proceso de Kudu para la compilación e implementación. 

## <a name="contentsync"></a>Habilitación de la implementación de la sincronización de contenido
Para habilitar la sincronización de contenido en el [Azure Portal](https://portal.azure.com), siga estos pasos:

1. En la página de su aplicación de Azure Portal, haga clic en **Configuración** > **Origen de implementación**. Haga clic en **Elegir origen** y, después, seleccione **OneDrive** o **Dropbox** como origen de la implementación. 
   
    ![Sincronización de contenido](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > Debido a diferencias subyacentes en las API, **OneDrive para la empresa** no se admite en este momento. 
   > 
   > 
2. Complete el flujo de trabajo de autorización para permitir que App Service acceda a una ruta de acceso designada y predefinida específica para OneDrive o Dropbox en la que se almacenará todo su contenido de App Service. Tras la autorización, la plataforma App Service le ofrecerá la opción de crear una carpeta de contenido en la ruta de acceso de contenido designada o de elegir una carpeta existente en dicha ruta. Las rutas de contenido designadas en sus cuentas de almacenamiento en nube utilizadas para la sincronización de App Service son las siguientes:  
   
   * **OneDrive**: `Apps\Azure Web Apps` 
   * **Dropbox**: `Dropbox\Apps\Azure`
3. Después de la sincronización de contenido inicial, se puede iniciar la sincronización a petición desde Azure Portal. El historial de implementación está disponible en la página **Implementaciones**.
   
    ![Historial de implementaciones](./media/app-service-deploy-content-sync/onedrive_sync.png)

Puede encontrar información adicional sobre la implementación desde Dropbox en [Implementación desde Dropbox] (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/).

