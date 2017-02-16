---
title: "Creación de una aplicación web de Azure que se ejecute en Linux | Microsoft Docs"
description: "Flujo de creación de aplicaciones web para App Service en Linux."
keywords: "azure app service, aplicación web, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: e062cb169782319bda14acfa4f2b7c8198f320f6
ms.openlocfilehash: e5c0547a4fb109560dbafbf84d17e0c3d28a7188


---
# <a name="create-an-azure-web-app-running-on-linux"></a>Creación de una aplicación web de Azure que se ejecute en Linux
## <a name="use-the-azure-portal-to-create-your-web-app"></a>Uso de Azure Portal para crear una aplicación web
Puede empezar a crear la aplicación web en Linux desde [Azure Portal](https://portal.azure.com) como se muestra en la siguiente imagen:

![Inicio de creación de una aplicación web en Azure Portal][1]

Aparecerá la hoja de **creación**, como se muestra en la siguiente imagen:

![Hoja de creación][2]

1. Asigne un nombre a la aplicación web.
2. Elija un grupo de recursos existente o cree uno. (Consulte las regiones disponibles en la [sección de limitaciones](app-service-linux-intro.md)).
3. Seleccione un plan de Azure App Service existente o cree uno. (Consulte las notas del plan de App Service en la [sección de limitaciones](app-service-linux-intro.md)).
4. Elija la pila de aplicaciones que se va a utilizar. Puede elegir entre varias versiones de Node.js y PHP.

Una vez que haya creado la aplicación, puede cambiar la pila de aplicaciones de la configuración de la aplicación como se muestra en la siguiente imagen:

![Configuración de la aplicación][3]

## <a name="deploy-your-web-app"></a>Implementación de la aplicación web
Elegir **Opciones de implementación** de Management Portal permite implementar la aplicación en un repositorio de Git o GitHub local. El resto de instrucciones son similares a las de una aplicación web fuera de Linux; puede seguir las instrucciones de nuestro artículo sobre [implementación local de Git](app-service-deploy-local-git.md) o [implementación continua](app-service-continuous-deployment.md) para GitHub.

También puede usar FTP para cargar la aplicación al sitio. En la sección de registros de diagnóstico puede obtener el punto de conexión FTP para la aplicación web, como se muestra en la siguiente imagen:

![Registros de diagnóstico][4]

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es App Service en Linux?](app-service-linux-intro.md)
* [Using PM2 Configuration for Node.js in Web Apps on Linux](app-service-linux-using-nodejs-pm2.md) (Uso de la configuración de PM2 para Node.js en Web Apps en Linux)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png



<!--HONumber=Jan17_HO4-->


