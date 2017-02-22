---
title: "Implementación de una máquina virtual con C# y una plantilla de Resource Manager | Microsoft Docs"
description: "Aprenda a usar C# y una plantilla de Resource Manager para implementar una máquina virtual de Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 0782000e87bed0d881be5238c1b91f89a970682c
ms.openlocfilehash: 8424fb5d107935833e9652ef86e03933ea14c26e


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implementación de una máquina virtual de Azure con C# y una plantilla de Resource Manager
Mediante plantillas y grupos de recursos, podrá administrar en conjunto todos los recursos que admita su aplicación. En este artículo se muestra cómo usar Visual Studio y C# para configurar la autenticación, crear una plantilla y luego implementar recursos de Azure mediante la plantilla que creó.

En primer lugar, debe asegurarse de haber realizado estos pasos de configuración:

* Instalar [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* Comprobar la instalación de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
* Obtener un [token de autenticación](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* Crear un grupo de recursos mediante [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md), [CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) o [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

Tardará unos 30 minutos en realizar estos pasos.

## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Paso 1: Creación del proyecto de Visual Studio, el archivo de plantilla y el archivo de parámetros
### <a name="create-the-template-file"></a>Cree el archivo de plantilla
Una plantilla de Azure Resource Manager permite implementar y administrar recursos de Azure conjuntamente. La plantilla es una descripción JSON de los recursos y los parámetros de implementación asociados.

En Visual Studio, siga estos pasos:

1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**.
2. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.
3. Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** > **Nuevo elemento**.
4. Haga clic en Web, seleccione el Archivo JSON, escriba *VirtualMachineTemplate.json* como nombre y, después, haga clic en **Agregar**.
5. En los corchetes de apertura y de cierre del archivo VirtualMachineTemplate.json, agregue el elemento de esquema y el elemento contentVersion necesarios:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. No siempre se necesitan [parámetros](../azure-resource-manager/resource-group-authoring-templates.md#parameters), pero proporcionan una manera de introducir valores al implementar la plantilla. Agregue el elemento parameters y sus elementos secundarios después del elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. [variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) en una plantilla para especificar los valores que pueden cambiar con frecuencia o los valores que se deben crear a partir de una combinación de valores de parámetro. Agregue el elemento variables después de la sección parameters:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. [recursos](../azure-resource-manager/resource-group-authoring-templates.md#resources) , como la máquina virtual, la red virtual y la cuenta de almacenamiento se definen a continuación en la plantilla. Agregue la sección resources después de la sección variables:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. Guarde el archivo de plantilla que creó.

### <a name="create-the-parameters-file"></a>Cree el archivo de parámetros
Para especificar los valores de los parámetros de recurso que se definieron en la plantilla, debe crear un archivo de parámetros que contenga los valores que se usaron al implementar la plantilla. En Visual Studio, siga estos pasos:

1. Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** > **Nuevo elemento**.
2. Haga clic en Web, seleccione Archivo JSON, escriba *Parameters.json* como nombre y, después, haga clic en **Agregar**.
3. Abra el archivo parameters.json y, a continuación, agregue este contenido JSON:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

   > [!NOTE]
   > En este artículo se crea una máquina virtual que ejecuta una versión del sistema operativo Windows Server. Para obtener más información sobre cómo seleccionar otras imágenes, consulte [Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   >
   >
4. Guarde el archivo de parámetros que creó.

## <a name="step-2-install-the-libraries"></a>Paso 2: Instalación de las bibliotecas
Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar este tutorial. Debe instalar la biblioteca de administración de recursos de Azure y la biblioteca de autenticación de Azure Active Directory para crear los recursos. Para obtener estas bibliotecas en Visual Studio, siga estos pasos:

1. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.
2. Escriba *Active Directory* en el cuadro de búsqueda, haga clic en **Instalar** para el paquete de la biblioteca de autenticación de Active Directory y, a continuación, siga las instrucciones para instalar el paquete.
3. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.ResourceManager* en el cuadro de búsqueda, haga clic en **Instalar** en las bibliotecas de administración de recursos de Microsoft Azure y después siga las instrucciones para instalar el paquete.

Ahora está preparado para comenzar a usar las bibliotecas para crear su aplicación.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Paso 3: Creación de las credenciales que se utilizan para autenticar las solicitudes
La aplicación de Azure Active Directory se crea y se instala la biblioteca de autenticación. Ahora, dará formato a la información de la aplicación para crear las credenciales que se usan para autenticar las solicitudes a Azure Resource Manager.

1. Abra el archivo Program.cs del proyecto que ha creado y agregue las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. Agregue este método a la clase Program para obtener el token necesario para crear las credenciales:

   ```
   private static async Task<AuthenticationResult> GetAccessTokenAsync()
   {
     var cc = new ClientCredential("{client-id}", "{client-secret}");
     var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
     var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
     if (token == null)
     {
       throw new InvalidOperationException("Could not get the token.");
     }
     return token;
   }
   ```

   Reemplace {client-id} con el identificador de la aplicación Azure Active Directory, {client-secret} con la clave de acceso de la aplicación de AD y {tenant-id} con el identificador del inquilino de su suscripción. Para encontrar el identificador del inquilino, ejecute Get-AzureSubscription. Puede encontrar la clave de acceso mediante el Portal de Azure.
3. Para crear las credenciales, agregue este código al método Main en el archivo Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Guarde el archivo Program.cs.

## <a name="step-4-deploy-the-template"></a>Paso 4: Implementación de la plantilla
En este paso, se usa el grupo de recursos creado anteriormente, pero también podría crear un grupo de recursos con las clases [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) y [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1. Agregue variables al método Main de la clase Program para especificar los nombres de los recursos que creó anteriormente, el nombre de la implementación y el identificador de suscripción:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Reemplace el valor de groupName por el nombre del grupo de recursos. Reemplace el valor de deploymentName por el nombre que quiere usar para la implementación. Para encontrar el identificador de la suscripción, ejecute Get-AzureRmSubscription.
2. Agregue este método a la clase Program para implementar los recursos en el grupo de recursos con la plantilla que definió:

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Si quiere implementar la plantilla desde una cuenta de almacenamiento, puede reemplazar la propiedad Template por la propiedad TemplateLink.
3. Para llamar al método que acaba de agregar, agregue este código al método Main:

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Paso 5: Eliminación de los recursos
Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. No es necesario eliminar por separado cada recurso de un grupo de recursos. Elimine el grupo de recursos y todos sus recursos se eliminarán automáticamente.

1. Para eliminar el grupo de recursos, agregue este método a la clase Program:

   ```
   public static async void DeleteResourceGroupAsync(
     TokenCredentials credential,
     string groupName,
     string subscriptionId)
   {
     Console.WriteLine("Deleting resource group...");
     var resourceManagementClient = new ResourceManagementClient(credential)
       { SubscriptionId = subscriptionId };
     await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
   }
   ```

2. Para llamar al método que acaba de agregar, agregue este código al método Main:

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-6-run-the-console-application"></a>Paso 6: Ejecución de la aplicación de la consola.
1. Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con las mismas credenciales que utiliza en su suscripción.
2. Presione **Entrar** cuando aparezca el estado Aceptado.

   Esta aplicación de consola tardará unos cinco minutos en ejecutarse completamente de principio a fin. Antes de presionar Entrar para comenzar la eliminación de recursos, puede dedicar unos minutos a comprobar la creación de los recursos en el Portal de Azure.
3. Para ver el estado de los recursos, vaya a los registros de auditoría en Azure Portal:

    ![Exploración de registros de auditoría en el Portal de Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Pasos siguientes
* Si se produjeron problemas durante la implementación, el paso siguiente sería consultar [Solución de errores comunes de implementación de Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).
* Aprenda a administrar la máquina virtual que ha creado. Para ello, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



<!--HONumber=Jan17_HO2-->


