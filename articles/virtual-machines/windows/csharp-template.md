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
ms.date: 03/01/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: daff6ab4c0eaf17d1cb488f1c16aa111b6ed9a88
ms.contentlocale: es-es
ms.lasthandoff: 06/23/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implementación de una máquina virtual de Azure con C# y una plantilla de Resource Manager
En este artículo se muestra cómo implementar una plantilla de Azure Resource Manager con C#. La [plantilla](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) implementa una sola máquina virtual que ejecuta Windows Server en una nueva red virtual con una sola subred.

Para obtener una descripción detallada del recurso de máquina virtual, consulte [Virtual machines in an Azure Resource Manager template](template-description.md) (Máquinas virtuales en una plantilla de Azure Resource Manager). Para obtener más información acerca de todos los recursos de una plantilla, consulte [Tutorial de la plantilla de Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Tardará unos 10 minutos en realizar estos pasos.

## <a name="step-1-create-a-visual-studio-project"></a>Paso 1: Creación de un proyecto de Visual Studio

En este paso, asegúrese de que está instalado Visual Studio y cree una aplicación de consola que se utilice para implementar la plantilla.

1. Si aún no lo ha hecho, instale [Visual Studio](https://www.visualstudio.com/).
2. En Visual Studio, haga clic en **Archivo** > **Nuevo** > **proyecto**.
3. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola (.NET Framework)**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

## <a name="step-2-install-libraries"></a>Paso 2: Instalación de las bibliotecas

Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar estos pasos. Debe instalar la biblioteca de Azure Resource Manager y la biblioteca de autenticación de Azure Active Directory para crear los recursos. Para obtener estas bibliotecas en Visual Studio, siga estos pasos:

1. Haga clic con el botón derecho en el Explorador de soluciones, haga clic en **Administrar paquetes NuGet para la solución** y, luego, haga clic en **Examinar**.
2. Escriba *Microsoft.IdentityModel.Clients.ActiveDirectory* en el cuadro de búsqueda, seleccione su proyecto y haga clic en **Instalar**. A continuación, siga las instrucciones para instalar el paquete.
3. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.ResourceManager* en el cuadro de búsqueda, haga clic en **Instalar** y siga las instrucciones para instalar el paquete.

Ahora está preparado para comenzar a usar las bibliotecas para crear su aplicación.

## <a name="step-3-create-credentials-used-to-authenticate-requests"></a>Paso 3: Creación de las credenciales que se utilizan para autenticar las solicitudes

Antes de empezar este paso, asegúrese de que tiene acceso a una [entidad de servicio de Active Directory](../../resource-group-authenticate-service-principal.md). Desde la entidad de servicio, adquiera un token para autenticar solicitudes a Azure Resource Manager.

1. Abra el archivo Program.cs del proyecto que ha creado y agregue las siguientes instrucciones using a las instrucciones existentes en la parte superior del archivo:

    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Rest;
    using System.IO;
    ```

2. Agregue este método a la clase Program para obtener el token necesario para crear las credenciales:

    ```
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("client-id", "client-secret");
      var context = new AuthenticationContext("https://login.windows.net/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token.");
      } 
      return token;
    }
    ```

    Reemplace estos valores:
    
    - *client-id* con el identificador de la aplicación de Azure Active Directory. Puede encontrar este identificador en la hoja Propiedades de la aplicación de AD. Para encontrar la aplicación de AD en Azure Portal, haga clic en **Azure Active Directory** en el menú de recursos y, luego, haga clic en **Registros de aplicación**.
    - *client-secret* con la clave de acceso de la aplicación de AD. Puede encontrar este identificador en la hoja Propiedades de la aplicación de AD.
    - *tenant-id* con el identificador de inquilino de la suscripción. Puede encontrar el identificador del inquilino en la hoja Propiedades de Azure Active Directory en Azure Portal. Se etiqueta como *identificación de directorio*.

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Guarde el archivo Program.cs.

## <a name="step-4-create-a-resource-group"></a>Paso 4: Creación de un grupo de recursos

Aunque puede crear un grupo de recursos desde una plantilla, la plantilla que utiliza desde la Galería no crea uno. En este paso, agregará el código para crear un grupo de recursos.

1. Con el fin de especificar valores para la aplicación, agregue las variables al método Main de la clase Program:

    ```
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var deploymentName = "deploymentName";
    var location = "location";
    ```

    Reemplace estos valores:
    
    - *myResourceGroup* con el nombre del grupo de recursos que se está creando.
    - *subscriptionId* con su identificador de suscripción. Puede encontrar el identificador de suscripción en la hoja Suscripciones de Azure Portal.
    - *deploymentName* con el nombre de la implementación.
    - *location* con la [región de Azure](https://azure.microsoft.com/regions/) donde desea crear los recursos.

2. Agregue este método a la clase Program para crear el grupo de recursos:

    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

    ```
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-5-create-a-parameters-file"></a>Paso 5: Creación del archivo de parámetros

Para especificar los valores de los parámetros de recurso que se definieron en la plantilla, debe crear un archivo de parámetros que contenga los valores. El archivo de parámetros se usa al implementar la plantilla. La plantilla que está usando desde la Galería espera valores para los parámetros *adminUserName*, *adminPassword* y *dnsLabelPrefix*.

En Visual Studio, siga estos pasos:

1. Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** > **Nuevo elemento**.
2. Haga clic en **Web**, seleccione **Archivo JSON**, escriba *Parameters.json* como nombre y, después, haga clic en **Agregar**.
3. Abra el archivo parameters.json y, a continuación, agregue este contenido JSON:

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "mytestacct1" },
        "adminPassword": { "value": "mytestpass1" },
        "dnsLabelPrefix": { "value": "mydns1" }
      }
    }
    ```

    Reemplace los valores de parámetro con valores que funcionan en su entorno.

4. Guarde el archivo Parameters.json.

## <a name="step-6-deploy-a-template"></a>Paso 6: Implementación de una plantilla

En este ejemplo, implementará una plantilla de la Galería de plantillas de Azure y proporcionará valores de parámetro desde el archivo local que ha creado. 

1. Agregue este método a la clase Program:

    ```
    public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
      TokenCredentials credential,
      string groupName,
      string deploymentName,
      string subscriptionId)
    {
    
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the template deployment...");
      var deployment = new Deployment();
      deployment.Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          TemplateLink = new TemplateLink("https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json"),
          Parameters = File.ReadAllText("..\\..\\Parameters.json")
        };
      
      return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
        groupName,
        deploymentName,
        deployment
      );
    }
    ```

    También puede implementar una plantilla desde una carpeta local mediante la propiedad de plantilla en lugar de la propiedad TemplateLink.

2. Para llamar al método que acaba de agregar, agregue este código al método Main:

    ```
    var dpResult = CreateTemplateDeploymentAsync(
      credential,
      groupName,
      deploymentName,
      subscriptionId);
    Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-7-delete-the-resources"></a>Paso 7: Eliminación de los recursos

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

## <a name="step-8-run-the-console-application"></a>Paso 8: Ejecución de la aplicación de consola

Esta aplicación de consola tardará unos cinco minutos en ejecutarse completamente de principio a fin. 

1. Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con las mismas credenciales que utiliza en su suscripción.

2. Presione **Entrar** cuando aparezca el estado *Correcto*. 

    También debería ver **1 Correcto** en Implementaciones en la hoja Introducción del grupo de recursos de Azure Portal.

3. Antes de presionar **Entrar** para comenzar la eliminación de recursos, puede dedicar unos minutos a comprobar la creación de los recursos en Azure Portal. Haga clic en el estado de implementación para ver información de la implementación.

## <a name="next-steps"></a>Pasos siguientes
* Si se produjeron problemas durante la implementación, el paso siguiente sería consultar [Solución de errores comunes de implementación de Azure con Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Obtenga información sobre cómo implementar una máquina virtual y los recursos de apoyo leyendo [Implementación de una máquina virtual de Azure con C#](csharp.md).

