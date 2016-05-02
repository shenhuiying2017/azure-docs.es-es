<properties
	pageTitle="Implementación de una máquina virtual con C# y una plantilla de Resource Manager | Microsoft Azure"
	description="Aprenda a usar C# y una plantilla de Resource Manager para implementar una máquina virtual de Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Implementación de una máquina virtual de Azure con C# y una plantilla de Resource Manager

Mediante el uso de plantillas y grupos de recursos, podrá administrar en conjunto todos los recursos que admite su aplicación. En este artículo se muestra cómo configurar la autenticación y el almacenamiento con Azure PowerShell y, después, crear e implementar una plantilla mediante C# para crear recursos de Azure.

Para completar este tutorial, necesita:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [Un token de autenticación](../resource-group-authenticate-service-principal.md)

Tardará unos 30 minutos en realizar estos pasos.

## Paso 1: Instalación de Azure PowerShell

Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta de Azure.
    
## Paso 2: Creación de un grupo de recursos para la plantilla de almacenamiento

Todos los recursos se deben implementar en un grupo de recursos. Para más información, consulte [Información general de Azure Resource Manager](../resource-group-overview.md).

1. Obtenga una lista de las ubicaciones donde se pueden crear los recursos.

	    Get-AzureLocation | sort Name | Select Name

2. Reemplace el valor de **$locName** por una ubicación de la lista, por ejemplo **Centro de EE. UU**. Cree la variable.

        $locName = "location name"
        
3. Reemplace el valor de **$rgName** por el nombre del nuevo grupo de recursos. Cree la variable y el grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Puede ver algo así:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
    
## Paso 3: Creación de una cuenta de almacenamiento y el contenedor de plantillas

Se necesita una cuenta de almacenamiento para almacenar la plantilla que va a crear e implementar.

1. Reemplace el valor de $stName por el nombre de la cuenta de almacenamiento (solo letras minúsculas y números). Compruebe que el nombre sea único.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    Si este comando devuelve **False**, el nombre propuesto es único.
    
2. Ahora, ejecute este comando para crear la cuenta de almacenamiento.
    
        New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
3. Reemplace {blob-storage-endpoint} por el punto de conexión del almacenamiento de blobs en su cuenta. Reemplace {storage-account-name} por el nombre de la cuenta de almacenamiento. Reemplace {primary-storage-key} por la clave de acceso principal. Ejecute estos comandos para crear el contenedor donde se almacenan los archivos. Puede obtener los valores del punto de conexión y de la clave en el Portal de Azure.

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint={blob-storage-endpoint};AccountName={storage-account-name};AccountKey={primary-storage-key}"
        $ctx = New-AzureStorageContext -ConnnectionString $ConnectionString
        New-AzureStorageContainer -Name "templates" -Permission Blob -Context $ctx

## Paso 3: Creación del proyecto de Visual Studio, el archivo de plantilla y el archivo de parámetros

### Cree el archivo de plantilla

Las plantillas del Administrador de recursos de Azure le permiten implementar y administrar los distintos recursos de Azure en conjunto mediante una descripción de JSON de los recursos y los parámetros de configuración e implementación asociados. La plantilla que cree en este tutorial será similar a cualquier plantilla que pueda encontrarse en la galería de plantillas. Para más información, consulte [Deploy a simple Windows VM in West US](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/) (Implementación de una máquina virtual con Windows sencilla en la región del oeste de EE. UU.).

En Visual Studio, haga esto:

1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** > **Nuevo elemento**.

4. Haga clic en Web, seleccione Archivo JSON, escriba *VirtualMachineTemplate.json* como nombre y, después, haga clic en **Agregar**.

5. En los corchetes de apertura y de cierre del archivo VirtualMachineTemplate.json, agregue el elemento de esquema y el elemento contentVersion necesarios:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. Los [parámetros](../resource-group-authoring-templates.md#parameters) no siempre son necesarios, pero facilitan la administración de la plantilla. Describen el tipo del valor, el valor predeterminado, en caso de ser necesario, y, posiblemente, los valores permitidos del parámetro. En este tutorial, se agregan a la plantilla los parámetros que se usan para crear una máquina virtual, una cuenta de almacenamiento y una red virtual. Agregue el elemento parameters y sus elementos secundarios después del elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
        }

7. Pueden usarse [variables](../resource-group-authoring-templates.md#variables) en una plantilla para especificar los valores que pueden cambiar con frecuencia o los valores que se deben crear a partir de una combinación de valores de parámetro. Agregue el elemento variables después de la sección parameters:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" },
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdiskforwindowssimple",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"  
          },
        }

8. Los [recursos](../resource-group-authoring-templates.md#resources), como la máquina virtual, la red virtual y la cuenta de almacenamiento se definen a continuación en la plantilla. Agregue la sección resources después de la sección variables:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "OSDiskName": "osdiskforwindowssimple",
            "windowsOSVersion": "2012-R2-Datacenter",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "apiVersion": "2015-06-15",
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [ "[variables('addressPrefix')]" ]
                },
                "subnets": [ {
                  "name": "[variables('subnetName')]",
                  "properties": {
                    "addressPrefix": "[variables('subnetPrefix')]"
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
                    },
                    "subnet": {
                      "id": "[variables('subnetRef')]"
                    }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computerName": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]",
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku": "[variables('windowsOSVersion')]",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                  } ]
                }
              }
            } ]
          }
      
9. Guarde el archivo de plantilla que creó.

### Cree el archivo de parámetros

Para especificar los valores de los parámetros del recurso que se definieron en la plantilla, debe crear un archivo de parámetros que contenga los valores y enviarlo al Administrador de recursos junto con la plantilla. En Visual Studio, haga esto:

1. Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** y en **Nuevo elemento**.

2. Haga clic en Web, seleccione Archivo JSON, escriba *Parameters.json* como nombre y, después, haga clic en **Agregar**.

3. Abra el archivo parameters.json y, a continuación, agregue este contenido JSON:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] En este artículo se crea una máquina virtual que ejecuta una versión del sistema operativo Windows Server. Para más información sobre cómo seleccionar otras imágenes, consulte [Navegación y selección de las imágenes de máquina virtual Linux en Azure con CLI o PowerShell](virtual-machines-linux-cli-ps-findimage.md).

4. Guarde el archivo de parámetros que creó.

### Carga de los archivos y establecimiento de los permisos para usarlos 

Administrador de recursos de Azure obtiene acceso al archivo de plantilla y al archivo de parámetros desde una cuenta de almacenamiento de Azure. Para poner los archivos en el primer almacenamiento que creó, haga lo siguiente:

1. Abra Cloud Explorer y vaya al contenedor de plantillas en la cuenta de almacenamiento que creó anteriormente.

2. En la esquina superior derecha de la ventana del contenedor de plantillas, haga clic en el icono Cargar blob, vaya al archivo VirtualMachineTemplate.json que creó y, después, haga clic en **Abrir**.

3. Haga clic nuevamente en el icono Cargar blob, vaya al archivo Parameters.json que creó y, a continuación, haga clic en **Abrir**.

## Paso 4: Instalación de las bibliotecas

Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar este tutorial. Debe instalar la biblioteca de administración de recursos de Azure y la biblioteca de autenticación de Azure Active Directory. Para obtener estas bibliotecas en Visual Studio, haga lo siguiente:

1. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

2. Escriba *Active Directory* en el cuadro de búsqueda, haga clic en **Instalar** para el paquete de la biblioteca de autenticación de Active Directory y, a continuación, siga las instrucciones para instalar el paquete.

4. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.Resources* en el cuadro de búsqueda, haga clic en **Instalar** para las bibliotecas de administración de recursos de Microsoft Azure y, después, siga las instrucciones para instalar el paquete.

Ahora está preparado para comenzar a usar las bibliotecas para crear su aplicación.

##Paso 5: Creación de las credenciales que se usan para autenticar las solicitudes

Ahora que la aplicación Azure Active Directory está creada y que la biblioteca de autenticación está instalada, la información de la aplicación se convierte en las credenciales que se usan para autenticar las solicitudes en Azure Resource Manager. Haga esto:

1. Abra el archivo Program.cs del proyecto que ha creado y agregue las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;
        using Microsoft.Rest;

2.	Agregue este método a la clase Program para obtener el token necesario para crear las credenciales:

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }

    Reemplace {application-id} por el identificador de aplicación que anotó anteriormente, {password} por la contraseña que eligió para la aplicación AD y {tenant-id} por el identificador del inquilino de su suscripción. Para encontrar el identificador del inquilino, ejecute Get-AzureSubscription.

3. Agregue este código al método Main en el archivo Program.cs para crear las credenciales:

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Guarde el archivo Program.cs.

## Paso 6: Incorporación del código para implementar la plantilla

En este paso, use las clases [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) y [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) para crear el grupo de recursos en el que se implementan los recursos.

1. Agregue variables al método Main de la clase Program para especificar los nombres que desea usar para los recursos, la ubicación de los recursos (por ejemplo, "Oeste de EE. UU."), la información de la cuenta de administrador y el identificador de la suscripción:

        var groupName = "resource group name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var deploymentName = "deployment name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    Reemplace todos los valores de variables por los nombres y el identificador que desea usar. Para encontrar el identificador de la suscripción, ejecute Get-AzureRmSubscription. El valor de la variable storageName es el nombre de la cuenta de almacenamiento donde se almacenó la plantilla.
    
2. Agregue este método a la clase Program para crear el grupo de recursos:

        public static void CreateResourceGroup(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var resourceGroup = new ResourceGroup {
            Location = location
          };
          var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
          Console.WriteLine(rgResult.Properties.ProvisioningState);
        }

2. Agregue este código al método Main para llamar al método que acaba de agregar:

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

3. Agregue este método a la clase Program para implementar los recursos en el grupo de recursos con la plantilla que definió:

        public static void CreateTemplateDeployment(
          TokenCredentials credential,
          string groupName,
          string storageName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/VirtualMachineTemplate.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/Parameters.json"
            }
          };
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
            groupName,
            deploymentName,
            deployment);
          Console.WriteLine(dpResult.Properties.ProvisioningState);
        }

4. Agregue este código al método Main para llamar al método que acaba de agregar:

        CreateTemplateDeployment(
          credential,
          groupName",
          storageName,
          deploymentName}",
          subscriptionId}");
        Console.ReadLine();

##Paso 7: Incorporación del código para eliminar los recursos

Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. No es necesario eliminar por separado cada recurso de un grupo de recursos. Al eliminar el grupo de recursos se eliminarán automáticamente todos sus recursos.

1.	Agregue este método a la clase Program para eliminar el grupo de recursos:

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceGroupClient = new ResourceManagementClient(credential);
          resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Agregue este código al método Main para llamar al método que acaba de agregar:

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

##Paso 8: Ejecución de la aplicación de consola

1.	Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

2.	Presione **Entrar** después de que aparezca el estado Aceptado.

	Esta aplicación de consola tardará unos cinco minutos en ejecutarse completamente de principio a fin. Antes de presionar Entrar para comenzar la eliminación de recursos, puede dedicar unos minutos a comprobar la creación de los recursos en el Portal de Azure.

3. Vaya a los registros de auditoría en el Portal de Azure para ver el estado de los recursos:

	![Exploración de registros de auditoría en el Portal de Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## Pasos siguientes

- Si hubo problemas durante la implementación, el paso siguiente sería mirar en [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Aprenda a administrar la máquina virtual que acaba de crear. Para ello, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0420_2016-->