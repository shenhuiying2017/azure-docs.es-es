---
title: "Implementación de una aplicación .NET de un contenedor en Azure Service Fabric | Microsoft Docs"
description: "Se ofrece información sobre cómo empaquetar una aplicación .NET de Visual Studio en un contenedor de Docker. Esta nueva aplicación de \"contenedor\" se implementa después en un clúster de Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 484db494e7975df950543d19bf841a4df7cdd139
ms.contentlocale: es-es
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Implementación de una aplicación .NET de un contenedor de Windows en Azure Service Fabric

En este tutorial se muestra cómo implementar una aplicación ASP.NET existente en un contenedor de Windows en Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de un proyecto de Docker en Visual Studio
> * Inclusión de una aplicación existente en un contenedor
> * Configuración de la integración continua con Visual Studio y VSTS

## <a name="prerequisites"></a>Requisitos previos

1. Instale [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) para poder ejecutar contenedores en Windows 10.
2. Familiarícese con el [inicio rápido de contenedores de Windows 10][link-container-quickstart].
3. Descargue la aplicación de ejemplo [Fabrikam Fiber CallCenter][link-fabrikam-github].
4. Instale [Azure PowerShell][link-azure-powershell-install].
5. Instale la [extensión Herramientas de entrega continua para Visual Studio 2017][link-visualstudio-cd-extension].
6. Cree una [suscripción de Azure][link-azure-subscription] y una [cuenta de Visual Studio Team Services][link-vsts-account]. 
7. [Creación de un clúster en Azure](service-fabric-tutorial-create-cluster-azure-ps.md)

## <a name="containerize-the-application"></a>Incluir la aplicación en contenedores

Ahora que tiene un [clúster de Service Fabric que se ejecuta en Azure](service-fabric-tutorial-create-cluster-azure-ps.md), está listo para crear e implementar una aplicación incluida en un contenedor. Para empezar a ejecutar la aplicación en un contenedor, se debe agregar la **compatibilidad con Docker** al proyecto en Visual Studio. Al agregar la **Compatibilidad con Docker** en la aplicación, suceden dos cosas. En primer lugar, se agrega un _Dockerfile_ al proyecto. En este nuevo archivo se describe cómo se va a generar la imagen de contenedor. En segundo lugar, se agrega un nuevo proyecto de _Docker Compose_ a la solución. El nuevo proyecto contiene algunos archivos de Docker Compose. Los archivos de Docker Compose pueden utilizarse para describir cómo se ejecuta el contenedor.

Más información sobre cómo trabajara con las [herramientas de contenedor de Visual Studio][link-visualstudio-container-tools].

>[!NOTE]
>Si se trata de la primera vez que ejecuta imágenes de contenedor de Windows en el equipo, Docker CE debe extraer las imágenes base para los contenedores. Las imágenes utilizadas en este tutorial tienen un tamaño de 14 GB. Siga adelante y ejecute el siguiente comando de terminal para extraer las imágenes base:
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Agregue compatibilidad con Docker

Abra el archivo [FabrikamFiber.CallCenter.sln][link-fabrikam-github] en Visual Studio.

Haga clic con el botón derecho en el proyecto **FabrikamFiber.Web** > **Agregar** > **Compatibilidad con Docker**.

### <a name="add-support-for-sql"></a>Agregar compatibilidad con SQL

Esta aplicación usa SQL como el proveedor de datos, por lo que se necesita una instancia de SQL Server para ejecutar la aplicación. Haga referencia a una imagen del contenedor de SQL Server en el archivo docker-compose.override.yml.

En Visual Studio, abra el **Explorador de soluciones**, busque **docker-compose** y abra el archivo **docker-compose.override.yml**.

Vaya al nodo `services:` y agregue un nodo denominado `db:` que defina la entrada de SQL Server para el contenedor.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Puede utilizar cualquier servidor SQL Server que prefiera para la depuración local, siempre que sea accesible desde el host. Sin embargo, **localdb** no admite la comunicación `container -> host`.

>[!WARNING]
>La ejecución de SQL Server en un contenedor no admite el almacenamiento de datos. Cuando el contenedor se detiene, los datos se borran. No utilice esta configuración para producción.

Vaya al nodo `fabrikamfiber.web:` y agregue un nodo secundario denominado `depends_on:`. Esto garantiza que el servicio `db` (el contenedor de SQL Server) se inicie antes que la aplicación web (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Actualización de web config

En el proyecto **FabrikamFiber.Web**, actualice la cadena de conexión en el archivo **web.config** para que apunte a la instancia de SQL Server del contenedor.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Si desea utilizar un servidor SQL Server distinto para compilar una versión de compilación de la aplicación web, agregue otra cadena de conexión al archivo web.release.config.

### <a name="test-your-container"></a>Prueba del contenedor

Presione **F5** para ejecutar y depurar la aplicación en el contenedor.

Edge abre la página de inicio definida de la aplicación mediante la dirección IP del contenedor en la red NAT interna (normalmente, 172.x.x.x). Para más información sobre la depuración de aplicaciones en contenedores con Visual Studio de 2017, vea [este artículo][link-debug-container].

![ejemplo de fabrikam en un contenedor][image-web-preview]

El contenedor ahora está listo para compilarse y empaquetarse en una aplicación de Service Fabric. Cuando la imagen de contenedor esté en la máquina, puede insertarla en cualquier registro de contenedor y arrastrarla a cualquier host para que se ejecute.

## <a name="get-the-application-ready-for-the-cloud"></a>Preparación de la aplicación para la nube

Para preparar la aplicación para la ejecución en Service Fabric en Azure, es preciso realizar dos pasos:

1. Exponer el puerto donde quiere tener acceso a la aplicación web en el clúster de Service Fabric
2. Proporcionar una base de datos SQL preparada para producción para nuestra aplicación

### <a name="expose-the-port-for-the-app"></a>Exposición del puerto para la aplicación
El clúster de Service Fabric que se ha configurado tiene abierto el puerto *80* de forma predeterminada en Azure Load Balancer, lo que equilibra el tráfico entrante al clúster. Podemos exponer nuestro contenedor en este puerto mediante nuestro archivo docker.compose.yml.

En Visual Studio, abra el **Explorador de soluciones**, busque **docker-compose** y abra el archivo **docker-compose.override.yml**.

Modifique el nodo `fabrikamfiber.web:` y agregue un nodo secundario denominado `ports:`.

Agregue una entrada de cadena `- "80:80"`.

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Uso de una base de datos SQL de producción
Cuando se ejecutan en producción, es necesario que nuestros datos se conserven en nuestra base de datos. Actualmente no hay ninguna forma de garantizar la persistencia de datos en un contenedor; por lo tanto, no se pueden almacenar datos de producción de SQL Server en un contenedor.

Se recomienda usar una instancia de Azure SQL Database. Para configurar y ejecutar un servidor administrado de SQL Server en Azure, consulte el artículo [Inicios rápidos de Azure SQL Database][link-azure-sql].

>[!NOTE]
>Recuerde modificar la cadena de conexión al servidor SQL Server en el archivo **web.release.config** del proyecto **FabrikamFiber.Web**.
>
>Esta aplicación genera errores leves si no se puede acceder a ninguna base de datos SQL. Puede elegir implementar la aplicación sin ningún servidor SQL.

## <a name="deploy-with-visual-studio-team-services"></a>Implementación con Visual Studio Team Services

Para configurar la implementación con Visual Studio Team Services, debe instalar la [extensión de Herramientas de entrega continua para Visual Studio 2017][link-visualstudio-cd-extension]. Esta extensión facilita la implementación en Azure mediante la configuración de Visual Studio Team Services y logra implementar la aplicación en el clúster de Service Fabric.

Para empezar, el código se debe hospedar en control de código fuente. En el resto de esta sección, se supone que se usa **Git**.

### <a name="set-up-a-vsts-repo"></a>Configuración de un repositorio de VSTS
En la esquina inferior derecha de Visual Studio, haga clic en **Agregar a control de código fuente** > **Git** (o cualquier opción que prefiera).

![Presione el botón de control de código fuente][image-source-control]

En el panel _Team Explorer_, presione **Publicar repositorio GIT**.

Seleccione el nombre del repositorio VSTS y presione **Repositorio**.

![publicar el repositorio en VSTS][image-publish-repo]

Ahora que el código está sincronizado con un repositorio de código fuente VSTS, puede configurar la integración continua y la entrega continua.

### <a name="setup-continuous-delivery"></a>Configuración de la entrega continua

En el _Explorador de soluciones_, haga clic con el botón derecho en la **solución** > **Configurar la entrega continua**.

Seleccione la suscripción de Azure.

Establezca la opción **Tipo de host** en **Clúster de Service Fabric**.

Establezca la opción **Host de destino** en el clúster de Service Fabric que ha creado en la sección anterior.

Elija un **Registro de contenedor** en el que publicar el contenedor.

>[!TIP]
>Use el botón **Editar** para crear un registro de contenedor.

Presione **Aceptar**.

![Configuración de la integración continua para Service Fabric][image-setup-ci]
   
   Una vez completada la configuración, el contenedor se implementa en Service Fabric. Cada vez que inserta actualizaciones en el repositorio, se ejecuta una nueva compilación y versión.
   
   >[!NOTE]
   >La creación de imágenes de contenedor tarda aproximadamente 15 minutos.
   >La primera implementación en el clúster de Service Fabric hace que se descarguen las imágenes base del contenedor de Windows Server Core. La descarga tarda entre 5 y 10 minutos más en completarse.

Vaya a la aplicación de Fabrikam Call Center mediante la dirección URL del clúster: por ejemplo, *http://mycluster.westeurope.cloudapp.azure.com*

Ahora que ya ha incluido la solución Fabrikam CallCenter en un contenedor y la ha implementado, puede abrir [Azure Portal][link-azure-portal] y ver la aplicación en ejecución en Service Fabric. Para probar la aplicación, abra un explorador web y vaya a la dirección URL del clúster de Service Fabric.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Creación de un proyecto de Docker en Visual Studio
> * Inclusión de una aplicación existente en un contenedor
> * Configuración de la integración continua con Visual Studio y VSTS

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

