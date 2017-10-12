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
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 1309985d7f1b93230b38ada2ee2687b1db10a791
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Incorporación de una aplicación de Java a Aplicaciones web del Servicio de aplicaciones de Azure
Cuando haya inicializado la aplicación web de Java en [Azure App Service][Azure App Service] tal como se documenta en [Creación de una aplicación web de Java en Azure App Service](app-service-web-get-started-java.md), podrá cargar su aplicación colocando su WAR en la carpeta **webapps**.

La ruta de acceso de navegación a la carpeta **webapps** varía en función de cómo haya configurado la instancia de Aplicaciones web.

* Si configura su aplicación web con Azure Marketplace, la ruta de acceso a la carpeta **webapps** tiene el formato **d:\home\site\wwwroot\bin\application\_server\webapps**, donde **application\_server** es el nombre del servidor de la aplicación vigente en su instancia de Web Apps. 
* Si configura su aplicación web con la interfaz de usuario de configuración de Azure, la ruta de acceso a la carpeta **webapps** tiene el formato **d:\home\site\wwwroot\webapps**. 

Tenga en cuenta que puede utilizar el control de código fuente para cargar la aplicación o páginas web, incluso en [escenarios de integración continua](app-service-continuous-deployment.md). FTP también es una opción para cargar la aplicación o las páginas web; para más información sobre cómo implementar las aplicaciones a través de FTP, consulte el artículo sobre la [implementación de aplicaciones mediante FTP](app-service-deploy-ftp.md).

Nota para aplicaciones web de Tomcat: después de cargar el archivo WAR a la carpeta **webapps** , el servidor de la aplicación Tomcat detectará que lo ha agregado y lo cargará automáticamente. Tenga en cuenta que si copia los archivos (excepto los archivos WAR) al directorio raíz, será necesario reiniciar el servidor de aplicaciones antes de utilizar esos archivos. La funcionalidad de carga automática para las aplicaciones web de Java de Tomcat que se ejecutan en Azure se basa en un nuevo archivo WAR que se agrega, o en archivos o directorios nuevos que se agregan a la carpeta **webapps** . 

<a name="see-also"></a>

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

[application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
