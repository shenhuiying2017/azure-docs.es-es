---
title: Guía de inicio rápido para crear una aplicación web de Java en Azure App Service en Linux
description: En esta guía de inicio rápido, implementará su primera aplicación Hola mundo de Java en Azure App Service en Linux en cuestión de minutos.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 2018f5b7051f2b6906372dad3319c763974b93b1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355192"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Guía de inicio rápido: Creación de una aplicación web de Java en App Service en Linux

App Service en Linux proporciona en la actualidad una característica en vista previa compatible con las aplicaciones web de Java. Para más información sobre las versiones preliminares, consulte [Términos de uso complementarios para versiones en vista previa de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

[App Service en Linux](app-service-linux-intro.md) proporciona un servicio de hospedaje web muy escalable y con aplicación automática de revisiones que usa el sistema operativo Linux. En esta guía de inicio rápido se muestra cómo utilizar la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con el [complemento Maven para Azure Web Apps (versión preliminar)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implementar una aplicación web de Java con una imagen Linux integrada.

![Aplicación de ejemplo que se ejecuta en Azure](media/quickstart-java/java-hello-world-in-browser.png)

El artículo [Implementación de una aplicación web Hola mundo en un contenedor Linux en la nube con el kit de herramientas de Azure para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux) ofrece un método alternativo para implementar las aplicaciones de Java en su propio contenedor.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido: 

* [CLI de Azure 2.0 o posterior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) instalado localmente.
* [Apache Maven](http://maven.apache.org/).



## <a name="create-a-java-app"></a>Creación de una aplicación Java

Ejecute el siguiente comando mediante Maven, con el fin de crear una nueva aplicación web *helloworld*:  

    mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp

Cambie al directorio del nuevo proyecto *helloworld* y compile todos los módulos con el comando siguiente:

    mvn verify

Este comando comprobará y creará todos los módulos, incluido el archivo *helloworld.war*, en el subdirectorio *helloworld/target*.


## <a name="deploying-the-java-app-to-app-service-on-linux"></a>Implementación de la aplicación Java en App Service en Linux

Hay varias opciones para implementar las aplicaciones web Java en App Service en Linux. Entre estas opciones se incluyen:

* [Implementación a través del complemento Maven para Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)
* [Implementación a través de ZIP o WAR](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip)
* [Implementación a través de FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)

En esta guía de inicio rápido, utilizará el complemento Maven para Azure Web Apps. Sus ventajas son que se usa con facilidad desde Maven y que crea los recursos de Azure necesarios (grupo de recursos, plan de App Service y aplicación web).

### <a name="deploy-with-maven"></a>Implementación con Maven

Para realizar la implementación desde Maven, agregue la definición del siguiente complemento al elemento `<build>` del archivo *pom.xml*:

```xml
    <plugins>
      <plugin>
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-webapp-maven-plugin</artifactId> 
        <version>1.1.0</version>
        <configuration> 
          <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup> 
          <appName>YOUR_WEB_APP</appName> 
          <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>
          <deploymentType>ftp</deploymentType> 
          <resources> 
              <resource> 
                  <directory>${project.basedir}/target</directory> 
                  <targetPath>webapps</targetPath> 
                  <includes> 
                      <include>*.war</include> 
                  </includes> 
                  <excludes> 
                      <exclude>*.xml</exclude> 
                  </excludes> 
              </resource> 
          </resources> 
        </configuration>
      </plugin>
    </plugins>
```    

Actualice los siguientes marcadores de posición en la configuración del complemento:

| Placeholder | DESCRIPCIÓN |
| ----------- | ----------- |
| `YOUR_RESOURCE_GROUP` | Nombre del nuevo grupo de recursos en el que se va a crear la aplicación web. Al colocar todos los recursos de una aplicación en un grupo, puede administrarlos juntos. Por ejemplo, si elimina el grupo de recursos también se eliminarán todos los recursos asociados con la aplicación. Actualice este valor con un nombre único de un nuevo grupo de recursos, por ejemplo, *TestResources*. Este nombre lo utilizará para limpiar todos los recursos de Azure en una sección posterior. |
| `YOUR_WEB_APP` | El nombre de la aplicación formará parte del nombre de host de la aplicación web cuando se implemente en Azure (SU_APLICACIÓN_WEB.azurewebsites.net). Actualice este valor con un nombre único para la nueva aplicación web de Azure, que hospedará la aplicación Java, por ejemplo *contoso*. |

El elemento `linuxRuntime` de la configuración controla qué imagen de Linux integrada se utiliza con la aplicación.

Ejecute el comando siguiente y siga las instrucciones para realizar la autenticación en la CLI de Azure:

    az login

Implemente la aplicación de Java en la aplicación web mediante el siguiente comando:

    mvn clean package azure-webapp:deploy


Una vez que se haya completado la implementación, vaya a la aplicación implementada mediante la siguiente dirección URL en el explorador web.

```bash
http://<app_name>.azurewebsites.net/helloworld
```

El código de ejemplo de Java se está ejecutando en una aplicación web con una imagen integrada.

![Aplicación de ejemplo que se ejecuta en Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**¡Enhorabuena!** Ha implementado su primera aplicación Java en App Service en Linux.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha usado Maven para crear una aplicación web Java y luego ha implementado dicha aplicación en App Service en Linux. Para más información acerca del uso de Java con Azure, haga clic en el vínculo.

> [!div class="nextstepaction"]
> [Azure para desarrolladores de Java](https://docs.microsoft.com/java/azure/)

