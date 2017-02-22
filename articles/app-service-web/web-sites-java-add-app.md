---
title: "Incorporación de una aplicación de Java a Aplicaciones web del Servicio de aplicaciones de Azure"
description: "Este tutorial muestra cómo agregar una página o aplicación a su instancia de Aplicaciones web del Servicio de aplicaciones de Azure que ya está configurado para usar Java."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 627930ca68a94ecc56e7ef9ac9435f4b5f3f41c7
ms.openlocfilehash: 61466be17a52f1f230207b71bb94e10f88ee075c


---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Incorporación de una aplicación de Java a Aplicaciones web del Servicio de aplicaciones de Azure
Cuando haya inicializado la aplicación web de Java en [Azure App Service][Azure App Service] tal como se documenta en [Creación de una aplicación web de Java en Azure App Service](web-sites-java-get-started.md), podrá cargar su aplicación colocando su WAR en la carpeta **webapps**.

La ruta de acceso de navegación a la carpeta **webapps** varía en función de cómo haya configurado la instancia de Aplicaciones web.

* Si configura su aplicación web con Azure Marketplace, la ruta de acceso a la carpeta **webapps** tiene el formato **d:\home\site\wwwroot\bin\application\_server\webapps**, donde **application\_server** es el nombre del servidor de la aplicación vigente en su instancia de Web Apps. 
* Si configura su aplicación web con la interfaz de usuario de configuración de Azure, la ruta de acceso a la carpeta **webapps** tiene el formato **d:\home\site\wwwroot\webapps**. 

Tenga en cuenta que puede utilizar el control de código fuente para cargar la aplicación o páginas web, incluso en [escenarios de integración continua](app-service-continuous-deployment.md). FTP también es una opción para cargar la aplicación o las páginas web; para más información sobre cómo implementar las aplicaciones a través de FTP, consulte [Documentación de implementación de Azure App Service].

Nota para aplicaciones web de Tomcat: después de cargar el archivo WAR a la carpeta **webapps** , el servidor de la aplicación Tomcat detectará que lo ha agregado y lo cargará automáticamente. Tenga en cuenta que si copia los archivos (excepto los archivos WAR) al directorio raíz, será necesario reiniciar el servidor de aplicaciones antes de utilizar esos archivos. La funcionalidad de carga automática para las aplicaciones web de Java de Tomcat que se ejecutan en Azure se basa en un nuevo archivo WAR que se agrega, o en archivos o directorios nuevos que se agregan a la carpeta **webapps** . 

<a name="see-also"></a>

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

[!INCLUDE [application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)]

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Documentación de implementación de Azure App Service]: ./web-sites-deploy.md



<!--HONumber=Jan17_HO2-->


