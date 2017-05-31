---
title: "Implementación de una aplicación .NET de un contenedor en Azure Service Fabric | Microsoft Docs"
description: "Se ofrece información sobre cómo empaquetar una aplicación .NET de Visual Studio en un contenedor de Docker. Esta nueva aplicación de &quot;contenedor&quot; se implementa después en un clúster de Service Fabric."
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
ms.date: 05/10/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a965fcb9dd5caf9656af5ae381b25baaaa01ec6d
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Implementación de una aplicación .NET de un contenedor en Azure Service Fabric

En este tutorial se muestra cómo implementar una aplicación ASP.NET existente en un contenedor de Windows con la versión preliminar de Visual Studio 2017 Update 3. A continuación, se explica cómo implementar el contenedor en Azure con Visual Studio Team Services y hospedar el contenedor en un clúster de Service Fabric.

## <a name="prerequisites"></a>Requisitos previos

1. Instale [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) para poder ejecutar contenedores en Windows 10.
2. Familiarícese con el [inicio rápido de contenedores de Windows 10][link-container-quickstart].
3. En este artículo, se usa **Fabrikam Fiber**, una aplicación de ejemplo que puede descargar [aquí][link-fabrikam-github].
4. [Azure PowerShell][link-azure-powershell-install]
5. [Extensión de Herramientas de entrega continua para Visual Studio 2017][link-visualstudio-cd-extension]

>[!NOTE]
>Si se trata de la primera vez que ejecuta imágenes de contenedor de Windows en el equipo, Docker CE debe extraer las imágenes base para los contenedores. Las imágenes utilizadas en este tutorial tienen un tamaño de 14 GB. Continúe y ejecute el siguiente comando de Powershell para extraer las imágenes base:
>
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

## <a name="containerize-the-application"></a>Incluir la aplicación en contenedores

Para empezar a ejecutar la aplicación en un contenedor, se debe agregar la **compatibilidad con Docker** al proyecto en Visual Studio. Al agregar la **Compatibilidad con Docker** en la aplicación, suceden dos cosas. En primer lugar, se agrega un archivo de _Docker_ al proyecto. En este nuevo archivo se describe cómo se va a generar la imagen de contenedor. En segundo lugar, se agrega un nuevo proyecto de _Docker Compose_ a la solución. Este nuevo proyecto contiene algunos archivos de Docker Compose, que se pueden usar para describir cómo se ejecuta el contenedor.

Más información sobre cómo trabajara con las [herramientas de contenedor de Visual Studio][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Agregue compatibilidad con Docker

1. Abra el archivo **FabrikamFiber.CallCenter.sln** en Visual Studio.

2. Haga clic con el botón derecho en el proyecto **FabrikamFiber.Web** > **Agregar** > **Compatibilidad con Docker**.

### <a name="add-support-for-sql"></a>Agregar compatibilidad con SQL

Esta aplicación usa SQL como el proveedor de datos, por lo que se necesita una instancia de SQL Server para ejecutar la aplicación. En este tutorial, se usa SQL Server en ejecución en un contenedor.
Para indicar a Docker que se va a ejecutar un servidor SQL Server en un contenedor, se puede hacer referencia a una imagen de contenedor de SQL Server en el archivo docker-compose.override.yml del proyecto de Docker Compose. De este modo, la instancia de SQL Server en ejecución en el contenedor se usa al depurar la aplicación en Visual Studio.

1. Abra el **Explorador de soluciones**.

2. Abra **docker-compose** > **docker-compose.yml** > **docker-compose.override.yml**.

3. En el nodo `services:`, agregue un nodo nuevo denominado `db:`. Este nodo se declara para ejecutar un servidor SQL Server en un contenedor.

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

   >[!NOTE]
   >Si siempre desea ejecutar SQL Server en un contenedor, puede optar por agregar lo anterior al archivo docker-compose.yml en lugar de al archivo docker-compose.override.yml.


4. Modifique el nodo `fabrikamfiber.web` y agregue un nuevo nodo secundario denominado `depends_on:`. Esto garantiza que el servicio `db` (el contenedor de SQL Server) se inicie antes que la aplicación web (fabrikamfiber.web).

   ```yml
     fabrikamfiber.web:
       ports:
         - "80"
       depends_on:
         - db
   ```

5. En el proyecto **FabrikamFiber.Web**, actualice la cadena de conexión en el archivo **web.config**, para que apunte a la instancia de SQL Server del contenedor.

   ```xml
   <add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   
   <add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   ```

   >[!NOTE]
   >Si desea utilizar un servidor SQL Server distinto para compilar una versión de compilación de la aplicación web, agregue otra cadena de conexión al archivo web.release.config.

6. Presione **F5** para ejecutar y depurar la aplicación en el contenedor.

   Edge abre la página de inicio definida de la aplicación mediante la dirección IP del contenedor en la red NAT interna (normalmente, 172.x.x.x). Para más información sobre la depuración de aplicaciones en contenedores con Visual Studio de 2017, vea [este artículo][link-debug-container].

   ![ejemplo de fabrikam en un contenedor][image-web-preview]

La aplicación ahora está lista para compilarse y empaquetarse en un contenedor. Cuando la imagen de contenedor esté en la máquina, puede insertarla en cualquier registro de contenedor y arrastrarla a cualquier host para que se ejecute.

En el resto de este tutorial, se usa Visual Studio Team Services para generar e implementar el contenedor, insertarlo en Azure Container Registry e implementarlo en Service Fabric, que se ejecuta en Azure.

## <a name="create-a-service-fabric-cluster"></a>Creación de un clúster de Service Fabric

Si ya tiene un clúster de Service Fabric en el que implementar la aplicación, puede omitir este paso. En caso contrario, continúe y cree un clúster de Service Fabric.

>[!NOTE]
>El procedimiento siguiente crea un clúster de Service Fabric, protegido mediante un certificado autofirmado, que se ubica en una instancia de Key Vault, creada como parte de la implementación. Para más información sobre el uso de la autenticación de Azure Active Directory, vea el artículo [Creación de un clúster de Service Fabric con Azure Resource Manager][link-servicefabric-create-secure-clusters].

1. Descargue una copia local de los archivos de plantilla y parámetros de Azure a los que se hace referencia a continuación.
    * [Plantilla de Azure Resource Manager para Service Fabric][link-sf-clustertemplate]: la plantilla de Resource Manager que define un clúster de Service Fabric.
    * [Archivo de parámetros de plantilla][link-sf-clustertemplate-parameters]: un archivo de parámetros para personalizar la implementación del clúster.
2. Personalice los parámetros siguientes en el archivo de parámetros:
  
   | Parámetro       | Descripción |
   | --------------- | ----------- |
   | clusterName     | Nombre del clúster. |
   | adminUserName   | Cuenta de administrador local en las máquinas virtuales del clúster. |
   | adminPassword   | Contraseña de la cuenta de administrador local en las máquinas virtuales del clúster. |
   | clusterLocation | Región de Azure en la que se implementa el clúster. |
   | vmInstanceCount | Número de nodos del clúster (puede ser 1). |

3. Abra **PowerShell**.
4. **Inicie sesión** en Azure.

   ```powershell
   Login-AzureRmAccount
   ```

5. Seleccione la **suscripción** en la que desea implementar el clúster.

   ```powershell
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

6. Cree y **cifre una contraseña** para el certificado usado por Service Fabric.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```

7. **Cree el clúster** mediante la ejecución del siguiente comando:

   ```powershell
   New-AzureRmServiceFabricCluster 
       -TemplateFile C:\users\me\downloads\PreviewSecureClusters.json `
       -ParameterFile C:\users\me\downloads\myCluster.parameters.json `
       -CertificateOutputFolder C:\users\me\desktop\ `
       -CertificatePassword $pwd `
       -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
       -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >El parámetro `-CertificateSubjectName` debe alinearse con el parámetro clusterName, que se ha especificado en el archivo de parámetros, y el dominio debe enlazarse con la región de Azure que elija, como: `clustername.eastus.cloudapp.azure.com`.
   
    Una vez realizada la configuración, se mostrará información sobre el clúster creado en Azure y, además, se entregará una copia del certificado en el directorio -CertificateOutputFolder.

8. **Haga doble clic** en el certificado que va a instalar en la máquina local.

## <a name="deploy-with-visual-studio"></a>Implementación con Visual Studio

Para configurar la implementación con Visual Studio Team Services, debe instalar la [extensión de Herramientas de entrega continua para Visual Studio 2017][link-visualstudio-cd-extension]. Esta extensión facilita la implementación en Azure mediante la configuración de Visual Studio Team Services y logra implementar la aplicación en el clúster de Service Fabric.

Para empezar, el código se debe hospedar en control de código fuente. En el resto de esta sección, se supone que se usa **Git**.

1. En la esquina inferior derecha de Visual Studio, haga clic en **Agregar a control de código fuente** > **Git** (o cualquier opción que prefiera).

   ![Presione el botón de control de código fuente][image-source-control]

2. En el panel _Team Explorer_, presione **Publicar repositorio GIT**.

3. Seleccione el nombre del repositorio VSTS y presione **Repositorio**.

   ![publicar el repositorio en VSTS][image-publish-repo]

Ahora que el código está sincronizado con un repositorio de código fuente VSTS, puede configurar la integración continua y la entrega continua.

1. En el _Explorador de soluciones_, haga clic con el botón derecho en la **solución** > **Configurar la entrega continua**.

2. Seleccione la suscripción de Azure.

3. Establezca la opción **Tipo de host** en **Clúster de Service Fabric**.

   >[!NOTE]
   >En función de los tipos de contenedores que vas a crear, se agregarán más opciones para que hospedes la aplicación en contenedores de Azure. 

4. Establezca la opción **Host de destino** en el clúster de Service Fabric que ha creado en la sección anterior.

5. Elija un **Registro de contenedor** en el que publicar el contenedor.

   >[!TIP]
   >Use el botón **Editar** para crear un registro de contenedor.
    
6. Presione **Aceptar**.

   ![Configuración de la integración continua para Service Fabric][image-setup-ci]
   
   Una vez completada la configuración, el contenedor se implementará en Service Fabric cada vez que inserte actualizaciones en el repositorio.

7. **Empiece a compilar** con **Team Explorer** y observe cómo se ejecuta la aplicación de contenedor en Service Fabric.

Ahora que ya ha incluido la solución Fabrikam CallCenter en un contenedor y la ha implementado, puede abrir [Azure Portal][link-azure-portal] y ver la aplicación en ejecución en Service Fabric. Para probar la aplicación, abra un explorador web y vaya a la dirección URL del clúster de Service Fabric.

## <a name="next-steps"></a>Pasos siguientes

- [Herramientas de contenedor en Visual Studio][link-visualstudio-container-tools]
- [Introducción a los contenedores en Service Fabric][link-servicefabric-containers]
- [Creación de aplicaciones de Service Fabric][link-servicefabric-createapp]

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
[link-sf-clustertemplate-parameters]: https://aka.ms/securepreviewonelineclusterparameters

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

