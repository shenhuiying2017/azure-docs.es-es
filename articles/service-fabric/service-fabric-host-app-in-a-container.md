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
ms.date: 05/19/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 56788914452c0f3a7072d6b2805866072b9b7fea
ms.contentlocale: es-es
ms.lasthandoff: 05/23/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Implementación de una aplicación .NET de un contenedor en Azure Service Fabric

En este tutorial se muestra cómo implementar una aplicación ASP.NET existente en un contenedor de Windows con la versión preliminar de Visual Studio 2017 Update 3. A continuación, se explica cómo implementar el contenedor en Azure con Visual Studio Team Services y hospedar el contenedor en un clúster de Service Fabric.

## <a name="prerequisites"></a>Requisitos previos

1. Instale [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) para poder ejecutar contenedores en Windows 10.
2. Familiarícese con el [inicio rápido de contenedores de Windows 10][link-container-quickstart].
3. En este tutorial, se usa **Fabrikam Fiber CallCenter**, una aplicación de ejemplo que puede descargar [aquí][link-fabrikam-github].
4. [Azure PowerShell][link-azure-powershell-install]
5. [Extensión de Herramientas de entrega continua para Visual Studio 2017][link-visualstudio-cd-extension]
6. Una [suscripción de Azure][link-azure-subscription] y una [cuenta de Visual Studio Team Services][link-vsts-account]. Puede seguir este tutorial utilizando niveles Gratis de todos los servicios.

>[!NOTE]
>Si se trata de la primera vez que ejecuta imágenes de contenedor de Windows en el equipo, Docker CE debe extraer las imágenes base para los contenedores. Las imágenes utilizadas en este tutorial tienen un tamaño de 14 GB. Continúe y ejecute el siguiente comando de Powershell para extraer las imágenes base:
>1. docker pull microsoft/mssql-server-windows-developer
>2. docker pull microsoft/aspnet:4.6.2

## <a name="containerize-the-application"></a>Incluir la aplicación en contenedores

Para empezar a ejecutar la aplicación en un contenedor, se debe agregar la **compatibilidad con Docker** al proyecto en Visual Studio. Al agregar la **Compatibilidad con Docker** en la aplicación, suceden dos cosas. En primer lugar, se agrega un archivo de _Docker_ al proyecto. En este nuevo archivo se describe cómo se va a generar la imagen de contenedor. En segundo lugar, se agrega un nuevo proyecto de _Docker Compose_ a la solución. El nuevo proyecto contiene algunos archivos de Docker Compose. Los archivos de Docker Compose pueden utilizarse para describir cómo se ejecuta el contenedor.

Más información sobre cómo trabajara con las [herramientas de contenedor de Visual Studio][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Agregue compatibilidad con Docker

1. Abra el archivo **FabrikamFiber.CallCenter.sln** en Visual Studio.

2. Haga clic con el botón derecho en el proyecto **FabrikamFiber.Web** > **Agregar** > **Compatibilidad con Docker**.

### <a name="add-support-for-sql"></a>Agregar compatibilidad con SQL

Esta aplicación usa SQL como el proveedor de datos, por lo que se necesita una instancia de SQL Server para ejecutar la aplicación. En este tutorial, se usa SQL Server en un contenedor para depuración local.
Para ejecutar un servidor SQL Server en un contenedor, al realizar la depuración se puede hacer referencia a una imagen de contenedor de SQL Server en el archivo docker-compose.override.yml. 

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

   >[!WARNING]
   >Ejecutar SQL Server en un contenedor no admite datos persistentes cuando se detiene el contenedor. No utilice esta configuración para producción.

4. Modifique el nodo `fabrikamfiber.web` y agregue un nuevo nodo secundario denominado `depends_on:`. Esto garantiza que el servicio `db` (el contenedor de SQL Server) se inicie antes que la aplicación web (fabrikamfiber.web).

   ```yml
     fabrikamfiber.web:
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

En el resto de este tutorial, se usa Visual Studio Team Services para implementar el contenedor en Service Fabric, que se ejecuta en Azure.

## <a name="create-a-service-fabric-cluster"></a>Creación de un clúster de Service Fabric

Si ya tiene un clúster de Service Fabric en el que implementar la aplicación, puede omitir este paso. En caso contrario, continúe y cree un clúster de Service Fabric.

>[!NOTE]
>El procedimiento siguiente crea un clúster de versión preliminar de Service Fabric de un solo nodo (máquina virtual única), protegido mediante un certificado autofirmado, que se ubica en una instancia de Key Vault.
>Los clústeres de un solo nodo no se pueden escalar más allá de una máquina virtual y los clústeres de versión preliminar no se pueden actualizar a versiones más recientes.
>Para calcular el costo producido por la ejecución de un clúster de Service Fabric en Azure, use la [Calculadora de precios de Azure][link-azure-pricing-calculator].
>Para obtener más información sobre la creación de clústeres de Service Fabric, vea el artículo [Creación de un clúster de Service Fabric con Azure Resource Manager][link-servicefabric-create-secure-clusters].

1. Descargue una copia local de la plantilla de Azure Resource Manager y el archivo de parámetros de este repositorio de GitHub:
    * [Plantilla de Azure Resource Manager para Service Fabric][link-sf-clustertemplate]
       - **azuredeploy.json**: plantilla de Azure Resource Manager que define un clúster de Service Fabric.
       - **azuredeploy.parameters.json**: un archivo de parámetros para personalizar la implementación del clúster.
2. Personalice los parámetros siguientes en el archivo de parámetros:
  
   | Parámetro       | Descripción | Valor sugerido |
   | --------------- | ----------- | --------------- |
   | clusterLocation | Región de Azure en la que se implementa el clúster. | *Por ejemplo, westeurope, eastasia, eastus* |
   | clusterName     | Nombre del clúster. | *Por ejemplo, bobs-sfpreviewcluster* |
   | adminUserName   | Cuenta de administrador local en las máquinas virtuales del clúster. | *Cualquier nombre de usuario válido de Windows Server* |
   | adminPassword   | Contraseña de la cuenta de administrador local en las máquinas virtuales del clúster. | *Cualquier contraseña válida de Windows Server* |
   | clusterCodeVersion | La versión de Service Fabric que se ejecutará. (255.255.X.255 son versiones preliminares). | **255.255.5713.255** |
   | vmInstanceCount | El número de máquinas virtuales en el clúster (puede ser 1 o 3−99). | **1** |

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
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >El parámetro `-CertificateSubjectName` debe alinearse con el parámetro clusterName, que se ha especificado en el archivo de parámetros, y el dominio debe enlazarse con la región de Azure que elija, como: `clustername.eastus.cloudapp.azure.com`.
   
    Una vez realizada la configuración, se mostrará información sobre el clúster creado en Azure y, además, se entregará una copia del certificado en el directorio -CertificateOutputFolder.

8. **Haga doble clic** en el certificado para abrir el Asistente para importar certificados.

9. Utilice los valores predeterminados, pero asegúrese de activar la casilla **Marcar esta clave como exportable** en el paso de **protección de clave privada**. Visual Studio debe exportar el certificado al configurar Azure Container Registry para la autenticación de clúster de Service Fabric más adelante en este tutorial.

10. Ahora puede abrir Service Fabric Explorer en un explorador. La dirección URL es el **ManagementEndpoint** en la salida desde el CmdLet de PowerShell, por ejemplo, *https://mycluster.westeurope.cloudapp.azure.com:19080* 

>[!NOTE]
>Al abrir Service Fabric Explorer, verá un error de certificado, ya que está usando un certificado autofirmado. En Edge, debe hacer clic en *Detalles* y, luego, en el vínculo *Acceder a la página web*. En Chrome, debe hacer clic en *Avanzadas* y, luego, en el vínculo *continuar*.

>[!NOTE]
>Si se produce un error en la creación del clúster, siempre puede volver a ejecutar el comando, lo que actualiza los recursos ya implementados. Si se creó un certificado como parte de la implementación con errores, se genera uno nuevo. Para solucionar problemas de creación del clúster, visite el artículo [Creación de un clúster de Service Fabric con Azure Resource Manager][link-servicefabric-create-secure-clusters].

## <a name="getting-the-application-ready-for-the-cloud"></a>Preparación de la aplicación para la nube

Para preparar la aplicación para la ejecución en Service Fabric en Azure, es preciso realizar dos pasos:

1. Exponer el puerto donde desea tener acceso a la aplicación web en el clúster de Service Fabric
2. Proporcionar una base de datos SQL preparada para producción para nuestra aplicación

### <a name="1-exposing-the-web-application-in-service-fabric"></a>1. Exponer una aplicación web en Service Fabric
El clúster de Service Fabric que hemos configurado tiene abierto el puerto 80 de forma predeterminada en Azure Load Balancer, lo que equilibra el tráfico entrante al clúster. Podemos exponer nuestro contenedor en este puerto mediante nuestro archivo docker.compose.yml.

1. Abra el **Explorador de soluciones**.

2. Abra **docker-compose** > **docker-compose.yml**.

3. Modifique el nodo `fabrikamfiber.web` y agregue un nuevo nodo secundario denominado `ports:` y la cadena`- "80:80"`. El archivo docker.compose.yml completo debe tener este aspecto:

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

### <a name="2-provide-a-production-ready-sql-database-for-our-application"></a>2. Proporcionar una base de datos SQL preparada para producción para nuestra aplicación
Cuando incluimos esta aplicación en un contenedor y habilitamos la depuración local, la configuramos para ejecutar SQL Server en un contenedor. Este enfoque es una buena solución cuando se depura su aplicación localmente, ya que no es preciso persistir los datos de la base de datos. Sin embargo, cuando se ejecuta en producción, es necesario que nuestros datos persistan en nuestra base de datos. Actualmente no hay ninguna manera para garantizar la persistencia de datos en un contenedor; por lo tanto, no se pueden almacenar datos de producción de SQL Server en un contenedor.

Como resultado, si el servicio requiere una base de datos SQL persistente, se recomienda utilizar una base de datos Azure SQL Database. Para configurar y ejecutar un servidor administrado de SQL Server en Azure, visite el artículo [Inicios rápidos de Azure SQL Database] [[link-azure-sql]].

>[!NOTE]
>Recuerde modificar la cadena de conexión al servidor SQL Server en el archivo web.release.config en el proyecto FabrikamFiber.Web.
>Esta aplicación genera errores leves si no se puede acceder a ninguna base de datos SQL. Puede elegir implementar la aplicación sin ningún servidor SQL.

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
   
   Una vez completada la configuración, el contenedor se implementa en Service Fabric. Cada vez que inserta actualizaciones en el repositorio, se ejecuta una nueva compilación y versión.
   
   >[!NOTE]
   >La creación de las imágenes de contenedor tarda aproximadamente 15 minutos.
   >La primera implementación en el clúster de Service Fabric hace que se descarguen las imágenes base del contenedor de Windows Server Core. La descarga tarda entre 5 y 10 minutos más en completarse.

7. Vaya a la aplicación de Fabrikam Call Center mediante la dirección URL del clúster: por ejemplo, *http://mycluster.westeurope.cloudapp.azure.com*

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
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /sql-database

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
