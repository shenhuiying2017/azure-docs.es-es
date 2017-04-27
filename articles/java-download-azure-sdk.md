---
title: Descarga del SDK de Azure para Java | Microsoft Docs
description: "Obtenga información acerca de cómo descargar el SDK de Azure para Java, con código de ejemplo proporcionado para los proyectos de Maven."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8
ms.lasthandoff: 01/14/2017


---
# <a name="download-the-azure-sdk-for-java"></a>Descarga del SDK de Azure para Java
Este artículo contiene instrucciones para descargar e instalar las bibliotecas de administración de Azure para Java.

> [!NOTE]
> Las bibliotecas de administración de Azure para Java se distribuyen en la [licencia de Apache, versión 2.0][license].
>

## <a name="azure-libraries-for-java---manual-download"></a>Bibliotecas de Azure para Java: descarga manual
Para instalar manualmente las bibliotecas de administración de Azure para Java, haga clic en <http://go.microsoft.com/fwlink/?LinkId=690320> para descargar un archivo ZIP que contenga todas las bibliotecas y todas sus dependencias.

Una vez haya descargado el archivo zip en su equipo, extraiga el contenido y use una de las opciones siguientes para agregar los archivos JAR a su proyecto:

* Importe los archivos JAR a su proyecto de Java en Eclipse o IntelliJ.
* Configure las rutas de acceso de compilación para los proyectos de Java en Eclipse o IntelliJ para incluir la ruta de acceso a los archivos JAR.

> [!NOTE]
> Consulte los archivos license.txt y ThirdPartyNotices.txt que se encuentran dentro del ZIP para obtener información sobre la licencia y sobre otras cuestiones.
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>Bibliotecas de administración de Azure para Java: compilación con Maven
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Paso 1: configurar el proyecto para usar Maven para la compilación
Para crear proyectos Maven en Eclipse que usan las bibliotecas de administración de Azure para Java, siga las instrucciones del artículo [Getting Started with Azure Management Libraries for Java][maven-getting-started] (Introducción a las bibliotecas de administración de Azure para Java).

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Paso 2: establecer la configuración de Maven con las dependencias necesarias
Una vez que el proyecto se haya configurado para usar Maven para la compilación, puede agregar el las dependencias necesarias al archivo pom.xml mediante una sintaxis similar a la del ejemplo siguiente. Tenga en cuenta que no es necesario agregar todas las dependencias que se muestran en el ejemplo siguiente; solo necesitará agregar las dependencias específicas que requiere el proyecto.

> [!NOTE]
> En cada elemento `<version>` del ejemplo anterior, reemplace los marcadores de posición "n.n.n" de este ejemplo por números de versión válidos, que pueden obtenerse del [repositorio de bibliotecas de Azure en Maven].
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="see-also"></a>Otras referencias
Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

* [Kit de herramientas de Azure para Eclipse]
  * [Instalación del Kit de herramientas de Azure para Eclipse]
  * [Creación de una aplicación web Hello World para Azure en Eclipse]
  * [Novedades del kit de herramientas de Azure para Eclipse]
* [Kit de herramientas de Azure para IntelliJ]
  * [Instalación del kit de herramientas de Azure para IntelliJ]
  * [Creación de una aplicación web Hello World para Azure en IntelliJ]
  * [Novedades del kit de herramientas de Azure para IntelliJ]

Para más información sobre el uso de Azure con Java, vea el [Centro de desarrolladores de Java de Azure].

> [!NOTE]
> Para obtener información detallada acerca de cómo configurar las rutas de acceso de compilación en Eclipse, consulte el artículo [Ruta de acceso de compilación de Java] en el sitio web de Eclipse.
>

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Creación de una aplicación web Hello World para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creación de una aplicación web Hello World para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalación del Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalación del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novedades del kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro de desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[repositorio de bibliotecas de Azure en Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Ruta de acceso de compilación de Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

