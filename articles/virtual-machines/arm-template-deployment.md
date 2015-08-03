<properties pageTitle="Implementación de recursos de Azure con una plantilla" description="Aprenda a usar algunos de los clientes disponibles en la biblioteca de administración de recursos de Azure para implementar una máquina virtual, una red virtual y una cuenta de almacenamiento" services="virtual-machines,virtual-networks,storage" documentationCenter="" authors="davidmu1" manager="timlt" editor="tysonn" tags="azure-resource-manager/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na" 
	ms.topic="article"
	ms.date="06/15/2015"
	ms.author="davidmu"/>

# Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla

Mediante el uso de plantillas y grupos de recursos, tendrá la posibilidad de administrar en conjunto todos los recursos que admiten su aplicación. Este tutorial muestra cómo usar algunos de los clientes disponibles en la biblioteca de administración de recursos de Azure y cómo crear una plantilla para implementar una máquina virtual, una red virtual y una cuenta de almacenamiento.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Para completar este tutorial, también necesita:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Cuenta de Almacenamiento de Azure](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/es-es/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/es-es/download/details.aspx?id=40855)
- [Azure PowerShell](../powershell-install-configure.md)

Tardará unos 30 minutos en realizar estos pasos.

## Paso 1: Adición de una aplicación a Azure AD y establecer permisos

Para usar Azure AD para autenticar las solicitudes con el Administrador de recursos de Azure, es necesario agregar una aplicación en el directorio predeterminado. Siga este procedimiento para agregar una aplicación:

1. Abra un símbolo del sistema de Azure PowerShell y, a continuación, ejecute este comando:

        Switch-AzureMode –Name AzureResourceManager

2. Establezca la cuenta de Azure que desea utilizar para este tutorial. Ejecute este comando y escriba las credenciales para la suscripción cuando se le solicite:

	    Add-AzureAccount

3. Reemplace {password} en el siguiente comando por la contraseña que desea utilizar y, a continuación, ejecútelo para crear la aplicación:

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. Anote el valor de ApplicationId en la respuesta del paso anterior. La necesitará más adelante en el tutorial:

	![Crear una aplicación de AD](./media/arm-template-deployment/azureapplicationid.png)

	>[AZURE.NOTE]También puede encontrar el identificador de la aplicación en el campo Id. de cliente de la aplicación en el Portal de administración.

5. Reemplace {application-id} por el identificador que acaba de anotar y, a continuación, cree la entidad de servicio correspondiente a la aplicación:

        New-AzureADServicePrincipal -ApplicationId {application-id}

6. Establezca el permiso para usar la aplicación:

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Paso 2: Crear el proyecto de Visual Studio, el archivo de plantilla y el archivo de parámetros

###Cree el archivo de plantilla

Las plantillas del Administrador de recursos de Azure le permiten implementar y administrar los distintos recursos de Azure en conjunto mediante una descripción de JSON de los recursos y los parámetros de configuración e implementación asociados. En Visual Studio, haga lo siguiente:

1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** > **Nuevo elemento**.

4.	En la ventana Agregar nuevo elemento, seleccione **Archivo de texto**, escriba *VirtualMachineTemplate.json* como el nombre y, a continuación, haga clic en **Agregar**.

5.	Abra el archivo VirtualMachineTemplate.json y, a continuación, agregue los corchetes de apertura y de cierre y el elemento de esquema y el elemento contentVersion necesarios:

        {
            "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
            "contentVersion": "1.0.0.0",
        }

6. Los [parámetros](https://msdn.microsoft.com/library/azure/dn835138.aspx#parameters) no siempre son necesarios, pero facilitan la administración de la plantilla. Describen el tipo del valor, el valor predeterminado, en caso de ser necesario, y, posiblemente, los valores permitidos del parámetro. En este tutorial, los parámetros que se usan para crear una máquina virtual, una cuenta de almacenamiento y una red virtual se agregan a la plantilla.

    Agregue el elemento parameters y sus elementos secundarios después del elemento contentVersion:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
        }

7.	Pueden usarse [variables](https://msdn.microsoft.com/library/azure/dn835138.aspx#variables) en una plantilla para especificar los valores que pueden cambiar con frecuencia o los valores que se deben crear a partir de una combinación de valores de parámetro.

    Agregue el elemento variables después de la sección parameters:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
        }

8.	Los [recursos](https://msdn.microsoft.com/library/azure/dn835138.aspx#resources), como la máquina virtual, la red virtual y la cuenta de almacenamiento se definen a continuación en la plantilla.

    Agregue la sección resources después de la sección variables:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
          "resources": [ {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "location": "[parameters('location')]",
            "properties": { "accountType": "[parameters('storageAccountType')]" }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
              "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
              "dnsSettings": { "domainNameLabel": "[parameters('dnsName')]" }
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
              "addressSpace": { "addressPrefixes": [ "[parameters('addressPrefix')]" ] },
              "subnets": [ {
                "name": "[parameters('subnet1Name')]",
                "properties": { "addressPrefix": "[parameters('subnet1Prefix')]" }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties": { "addressPrefix": "[parameters('subnet2Prefix')]" }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
              "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
              "ipConfigurations": [ {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddressName'))]"
                  },
                  "subnet": { "id": "[variables('subnet1Ref')]" }
                }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
              "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
              "hardwareProfile": { "vmSize": "[parameters('vmSize')]" },
              "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]",
                "windowsProfile": { "provisionVMAgent": "true" }
              },
              "storageProfile": {
                "sourceImage": { "id": "[variables('sourceImageName')]" },
                "destinationVhdsContainer" : "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmStorageAccountContainerName'),'/')]"
              },
              "networkProfile": {
                "networkInterfaces" : [ {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                } ]
              }
            }
          } ]
        }

9.	Guarde el archivo de plantilla que creó.

###Cree el archivo de parámetros

Para especificar los valores de los parámetros del recurso que se definieron en la plantilla, debe crear un archivo de parámetros que contenga los valores y enviarlo al Administrador de recursos junto con la plantilla. En Visual Studio, haga lo siguiente:

1.	Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** y en **Nuevo elemento**.

2.	En la ventana Agregar nuevo elemento, seleccione **Archivo de texto**, escriba *Parameters.json* como el nombre y, a continuación, haga clic en **Agregar**.

3.	Abra el archivo parameters.json y, a continuación, agregue el siguiente contenido JSON:

        {
          "contentVersion": "1.0.0.0",
          "parameters": {
            "vmName": { "value": "mytestvm1" },
            "newStorageAccountName": { "value": "mytestsa1" },
            "storageAccountType": { "value": "Standard_LRS" },
            "publicIPaddressName": { "value": "mytestip1" },
            "location": { "value": "West US" },
            "vmStorageAccountContainerName": { "value": "vhds" },
            "vmSize": { "value": "Standard_A1" },
            "subscriptionId": { "value": "{subscription-id}" },
            "vmSourceImageName": { "value": "{source-image-name}" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "virtualNetworkName": { "value": "mytestvn1" },
            "dnsName": { "value": "mytestdns1" },
            "nicName": { "value": "mytestnic1" }
          }
        }

    >[AZURE.NOTE]Los nombres de imagen vhd cambian de manera constante en la galería de imágenes, por lo que necesita obtener un nombre de imagen actual para implementar la máquina virtual. Para ello, consulte [Administrar imágenes con Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx) y, a continuación, reemplace {source-image-name} por el nombre del archivo vhd que desea usar. Por ejemplo, "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd". Reemplace {subscription-id} por el identificador de la suscripción.


4.	Guarde el archivo de parámetros que creó.

Administrador de recursos de Azure obtiene acceso al archivo de plantilla y al archivo de parámetros desde una cuenta de almacenamiento de Azure. Realice los siguientes pasos para poner los archivos en almacenamiento:

1.	Abra el Explorador de servidores y vaya al contenedor existente en su cuenta de almacenamiento en el que desea colocar el archivo. Para efectos de este tutorial, el contenedor donde se ubica la plantilla se denomina plantillas.

2.	En la esquina superior derecha del panel del contenedor de plantillas, haga clic en el icono Cargar blob, vaya al archivo VirtualMachineTemplate.json que creó y, a continuación, haga clic en **Abrir**.

3. Haga clic nuevamente en el icono Cargar blob, vaya al archivo Parameters.json que creó y, a continuación, haga clic en **Abrir**.

##Paso 3: Instalar las bibliotecas

Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar este tutorial. Debe instalar la biblioteca de administración de recursos de Azure y la biblioteca de autenticación de Azure Active Directory. Para obtener estas bibliotecas en Visual Studio, haga lo siguiente:

1.	Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

2.	Escriba *Active Directory* en el cuadro de búsqueda, haga clic en **Instalar** para el paquete de la biblioteca de autenticación de Active Directory y, a continuación, siga las instrucciones para instalar el paquete.

3.	En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Administración de recursos de Azure* en el cuadro de búsqueda, haga clic en **Instalar** para las bibliotecas de administración de recursos de Microsoft Azure y, a continuación, siga las instrucciones para instalar el paquete.

Ahora está preparado para comenzar a usar las bibliotecas para crear la aplicación.

##Paso 4: Crear las credenciales que se utilizan para autenticar las solicitudes

Ahora que la aplicación de Azure Active Directory se creó y se instaló la biblioteca de autenticación, dé a la información de la aplicación formato de credenciales que se utilizan para autenticar las solicitudes en el Administrador de recursos de Azure. Haga lo siguiente:

1.	Abra el archivo Program.cs para el proyecto que ha creado y, a continuación, agregue las siguientes instrucciones using a la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;

2.	Agregue el método siguiente a la clase Program para obtener el token necesario para crear las credenciales:

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Reemplace {application-id} por el identificador de aplicación que anotó anteriormente, {password} por la contraseña que eligió para la aplicación AD y {tenant-id} por el identificador del inquilino de su suscripción. Puede encontrar el Id. del inquilino ejecutando Get-AzureSubscription.

3.	Agregue el código siguiente al método Main en el archivo Program.cs para crear las credenciales:

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

4.	Guarde el archivo Program.cs.


##Paso 5: Agregar el código para implementar la plantilla

Los recursos siempre se implementan a partir de una plantilla a un grupo de recursos. Utilice las clases [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) y [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) para crear el grupo de recursos en el que se implementan los recursos.

1.	Agregue el método siguiente a la clase Program para crear el grupo de recursos:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  var resourceGroup = new ResourceGroup { Location = "West US" };
		  using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", resourceGroup);
		    Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

		CreateResourceGroup(credential);
		Console.ReadLine();

3.	Agregue el método siguiente a la clase Program para implementar los recursos en el grupo de recursos con la plantilla que definió:

		public async static void CreateTemplateDeployment(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the template deployment...");
		  var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
		  {
		    Mode = DeploymentMode.Incremental,
		    TemplateLink = new TemplateLink
		    {
		      Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json")
			},
			ParametersLink = new ParametersLink
			{
			  Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/Parameters.json")
			}
		  };
		  using (var templateDeploymentClient = new ResourceManagementClient(credential))
		  {
		    var dpResult = await templateDeploymentClient.Deployments.CreateOrUpdateAsync("mytestrg1", "mytestdp1", deployment);
			Console.WriteLine(dpResult.StatusCode);
		  }
		}

	Reemplace {storage-account-name} por el nombre de la cuenta donde colocó anteriormente los archivos.

4.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

		CreateTemplateDeployment(credential);
		Console.ReadLine();

##Paso 6: Agregar el código para eliminar los recursos

Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. No es necesario eliminar por separado cada recurso de un grupo de recursos. Puede eliminar el grupo de recursos y se eliminarán automáticamente todos sus recursos.

1.	Agregue el método siguiente a la clase Program para eliminar el grupo de recursos:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
		{
		  using (var resourceGroupClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
			Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

		DeleteResourceGroup(credential);
		Console.ReadLine();

##Paso 7: Ejecutar la aplicación de consola

1.	Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

2.	Presione **Entrar** después de que se devuelva cada código de estado para crear cada recurso. Después de crear la máquina virtual, realice el siguiente paso antes de presionar Entrar para eliminar todos los recursos.

	Esta aplicación de consola tardará unos cinco minutos en ejecutarse completamente de principio a fin. Antes de presionar Entrar para comenzar la eliminación de recursos, puede dedicar unos minutos a comprobar la creación de los recursos en el Portal de vista previa de Azure antes de eliminarlos.

3. Vaya a los registros de auditoría en el Portal de vista previa de Azure para ver el estado de los recursos:

	![Crear una aplicación de AD](./media/arm-template-deployment/crpportal.png)

<!---HONumber=July15_HO4-->