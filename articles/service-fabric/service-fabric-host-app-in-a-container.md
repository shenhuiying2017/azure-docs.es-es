---
title: Implementación de una aplicación .NET de un contenedor en Azure Service Fabric | Microsoft Docs
description: Aprenda a incluir una aplicación .NET existente en un contenedor mediante Visual Studio y a depurar contenedores en Service Fabric localmente. La aplicación incluida en el contenedor se inserta en un registro de contenedor de Azure y se implementa en un clúster de Service Fabric. Cuando se implementa en Azure, la aplicación utiliza Azure SQL DB para conservar los datos.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/07/2018
ms.author: ryanwi,mikhegn
ms.openlocfilehash: 20600eda935d15b0554f6184b41caa45ee42fd14
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Tutorial: Implementación de una aplicación .NET de un contenedor de Windows en Azure Service Fabric

En este tutorial se muestra cómo incluir una aplicación ASP.NET existente en un contenedor y empaquetarla como una aplicación de Service Fabric.  Ejecute los contenedores localmente en el clúster de desarrollo de Service Fabric y, a continuación, implemente la aplicación en Azure.  La aplicación conserva los datos en [Azure SQL Database](/azure/sql-database/sql-database-technical-overview). 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Inclusión de una aplicación existente en un contenedor con Visual Studio
> * Crear una base de datos SQL de Azure
> * Creación de un registro de contenedor de Azure
> * Implementación de una aplicación de Service Fabric en Azure

## <a name="prerequisites"></a>requisitos previos

1. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Instale [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) para poder ejecutar contenedores en Windows 10.
3. Instale [la versión en tiempo de ejecución de Service Fabric 6.2 o posterior](service-fabric-get-started.md) y la [versión 3.1 del SDK de Service Fabric](service-fabric-get-started.md) o posterior.
4. Instale [Visual Studio 2017 versión 15.7](https://www.visualstudio.com/) o posterior con las cargas de trabajo de **desarrollo de Azure** y de **desarrollo de ASP.NET y web**.
5. Instale [Azure PowerShell][link-azure-powershell-install].
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Descarga y ejecución de Fabrikam Fiber CallCenter
Descargue la aplicación de ejemplo [Fabrikam Fiber CallCenter][link-fabrikam-github].  Haga clic en el vínculo para **descargar archivo**.  Desde el directorio *sourceCode* del archivo *fabrikam.zip*, extraiga el archivo *sourceCode.zip* y, a continuación, extraiga el directorio *VS2015* en el equipo.

Compruebe que la aplicación Fabrikam Fiber CallCenter se compila y se ejecuta sin error.  Inicie Visual Studio como **administrador** y abra la solución el archivo [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Presione F5 para depurar y ejecutar la aplicación.

![Ejemplo web de Fabrikam][fabrikam-web-page]

## <a name="containerize-the-application"></a>Incluir la aplicación en contenedores
Haga clic con el botón derecho en el proyecto **FabrikamFiber.Web** > **Agregar** > **Compatibilidad con el orquestador de contenedores**.  Seleccione **Service Fabric** como orquestador de contenedores y haga clic en **Aceptar**.

Se crea un nuevo proyecto de aplicación de Service Fabric **FabrikamFiber.CallCenterApplication** en la solución.  Se agrega un archivo Dockerfile al proyecto **FabrikamFiber.Web** existente.  También se agrega un directorio **PackageRoot** al proyecto **FabrikamFiber.Web**, que contiene el manifiesto del servicio y la configuración para el nuevo servicio de FabrikamFiber.Web. 

El contenedor ahora está listo para compilarse y empaquetarse en una aplicación de Service Fabric. Cuando la imagen de contenedor esté en la máquina, puede insertarla en cualquier registro de contenedor y arrastrarla a cualquier host para que se ejecute.

## <a name="create-an-azure-sql-db"></a>Creación de una instancia de Azure SQL DB
Cuando se ejecuta la aplicación de Fabrikam Fiber CallCenter en producción, los datos deben conservarse en una base de datos. Actualmente no hay ninguna forma de garantizar la persistencia de datos en un contenedor; por lo tanto, no se pueden almacenar datos de producción de SQL Server en un contenedor.

Recomendamos [Azure SQL Database](/azure/sql-database/sql-database-get-started-powershell). Para configurar y ejecutar una base de datos de SQL Server en Azure, ejecute el script siguiente.  Modifique las variables del script según sea necesario. *clientIP* es la dirección IP del equipo de desarrollo.  Si está detrás de un firewall corporativo, la dirección IP del equipo de desarrollo puede no ser una dirección IP expuesta a Internet.  También puede establecer la regla del firewall del servidor para la base de datos SQL a través de [Azure Portal](https://portal.azure.com), que muestra la dirección IP de su equipo.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "24.18.117.76"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

## <a name="update-the-web-config"></a>Actualización de web config
En el proyecto **FabrikamFiber.Web**, actualice la cadena de conexión en el archivo **web.config** para que apunte a la instancia de SQL Server del contenedor.  Actualice la parte *Server* de la cadena de conexión del servidor creado por el script anterior. 

```xml
<add name="FabrikamFiber-Express" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Puede utilizar cualquier servidor SQL Server que prefiera para la depuración local, siempre que sea accesible desde el host. Sin embargo, **localdb** no admite la comunicación `container -> host`. Si desea utilizar una base de datos SQL distinta para compilar una versión de compilación de la aplicación web, agregue otra cadena de conexión al archivo *web.release.config*.

## <a name="run-the-containerized-application-locally"></a>Ejecución de la aplicación en contenedores localmente
Presione **F5** para ejecutar y depurar la aplicación en un contenedor en el clúster de desarrollo local de Service Fabric.

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor
Ahora que la aplicación se ejecuta localmente, empiece a preparar la implementación en Azure.  Las imágenes de contenedor deben estar almacenadas en un registro de contenedor.  Cree una instancia de [Azure Container Registry](/azure/container-registry/container-registry-intro) mediante el script siguiente.  Antes de implementar la aplicación en Azure, inserte la imagen del contenedor en este registro.  Cuando la aplicación se implementa en el clúster de Azure, la imagen del contenedor se extrae de este registro.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Creación de un clúster de Service Fabric en Azure
Las aplicaciones de Service Fabric se ejecutan en un clúster o en un conjunto de máquinas físicas o virtuales conectadas a la red.  Antes de implementar la aplicación en Azure, cree un clúster de Service Fabric en Azure.

Puede:
- Crear un clúster de prueba desde Visual Studio. Esta opción permite crear un clúster seguro directamente desde Visual Studio con las configuraciones preferidas. 
- [Crear un clúster seguro a partir de una plantilla](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

Al crear el clúster, elija una SKU que admita la ejecución de contenedores (como, por ejemplo, Windows Server 2016 Datacenter con contenedores). Este tutorial crea un clúster desde Visual Studio, lo cual es ideal para escenarios de prueba. Si crea un clúster de alguna otra manera o utiliza un clúster existente, puede copiar y pegar el punto de conexión o seleccionarlo de la suscripción. 

1. Haga clic con el botón derecho en el proyecto de aplicación **FabrikamFiber.CallCenterApplication** en el Explorador de soluciones y elija **Publicar**.

2. Inicie sesión con su cuenta de Azure para poder acceder a sus suscripciones. 

3. Despliegue la lista **Punto de conexión** y seleccione la opción **Crear un clúster...**    
        
4. En el cuadro de diálogo **Crear clúster**, modifique los siguientes valores:

    1. Especifique el nombre del clúster en el campo **Nombre del clúster**, así como la suscripción y la ubicación que desee utilizar.
    2. Opcional: puede modificar el número de nodos. De forma predeterminada tiene tres nodos, lo mínimo necesario para probar escenarios de Service Fabric.
    3. Seleccione la pestaña **Certificado**. En ella, escriba la contraseña que se utilizará para proteger el certificado del clúster. Este certificado le ayuda a proteger el clúster. También puede modificar la ruta de acceso a la ubicación en que desea guardar el certificado. Visual Studio también puede importar el certificado automáticamente, ya que este es un paso necesario para publicar la aplicación en el clúster.
    4. Seleccione la pestaña **Detalle de máquina virtual**. Especifique la contraseña que desea usar para las máquinas virtuales (VM) que componen el clúster. El nombre de usuario y la contraseña se pueden utilizar para conectarse a las máquinas virtuales de forma remota. También debe seleccionar el tamaño de la máquina virtual y puede cambiar la imagen de la misma si fuera necesario.
    5. En la pestaña **Opciones avanzadas**, aparece el puerto de la aplicación que se abrirá en el equilibrador de carga cuando se implemente el clúster. En el Explorador de soluciones, abra FabrikamFiber.Web->PackageRoot->ServiceManifest.xml.  Aparece el puerto para el front-end web en **Punto de conexión**.  También puede agregar una clave de Application Insights existente que se va a utilizar para enrutar los archivos de registro de aplicaciones.
    6. Cuando haya acabado de modificar la configuración, seleccione el botón **Crear**. 
5. La creación tarda varios minutos en completarse; la ventana de salida indicará el momento en que el clúster está totalmente creado.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Concesión de permiso a la aplicación que se ejecuta en Azure para acceder a SQL Database
Anteriormente, creó una regla de firewall de SQL para conceder acceso a la aplicación que se ejecuta localmente.  A continuación, tendrá que permitir a la aplicación que se ejecuta en Azure que acceda a SQL Database.  Cree un [punto de conexión de servicio de red virtual](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) para el clúster de Service Fabric y, a continuación, cree una regla para permitir que ese punto de conexión tenga acceso a SQL Database.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "fabrikamfiber.callcenterapplication_RG"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Implementación de la aplicación en Azure
Ahora que la aplicación está lista, puede implementarla en un clúster de Azure directamente desde Visual Studio.  En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de aplicación **FabrikamFiber.CallCenterApplication** y elija **Publicar**.  En **Punto de conexión**, seleccione el punto de conexión del clúster que creó anteriormente.  En **Azure Container Registry**, seleccione el registro de contenedor que creó anteriormente.  Haga clic en **Publicar** para implementar la aplicación en el clúster de Azure.

![Publicación de la aplicación][publish-app]

Siga el progreso de la implementación en la ventana de salida.  Cuando la aplicación esté implementada, abra un explorador y escriba la dirección del clúster y el puerto de la aplicación. Por ejemplo, http://http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Ejemplo web de Fabrikam][fabrikam-web-page-deployed]

## <a name="clean-up-resources"></a>Limpieza de recursos
Si ha terminado, asegúrese de quitar todos los recursos que haya creado.  La manera más sencilla consiste en quitar los grupos de recursos que contienen el clúster de Service Fabric, Azure SQL DB y Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Inclusión de una aplicación existente en un contenedor con Visual Studio
> * Crear una base de datos SQL de Azure
> * Creación de un registro de contenedor de Azure
> * Implementación de una aplicación de Service Fabric en Azure

En la siguiente parte del tutorial, puede aprender a configurar la [supervisión del contenedor](service-fabric-tutorial-monitoring-wincontainers.md).


[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png